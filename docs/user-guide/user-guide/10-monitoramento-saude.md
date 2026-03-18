# 10. Monitoramento de Saúde Proativo

O SmartReader Gateway inclui um sistema de **monitoramento proativo de saúde** que executa em segundo plano, coletando métricas dos leitores Identix EZ700 e publicando eventos MQTT quando mudanças importantes ocorrem.

Ao contrário de um sistema de polling contínuo, o monitor de saúde:
- **Publica apenas transições** — não gera spam quando as condições são estáveis
- **Honra as mudanças de estado** — três níveis de severidade para temperatura (normal → warning → critical)
- **Silencia na primeira execução** — evita flood de eventos ao iniciar o gateway

---

## 10.1 Como habilitar o monitoramento

Para habilitar o monitoramento de saúde proativo:

### Via arquivo de configuração

Edite `config.yaml` e defina:

```yaml
events:
  enabled: true                        # Habilita todos os monitores
  publish_health_alerts: true          # Publicar alertas de temperatura/VSWR
  publish_network_status: true         # Publicar mudanças de conectividade
  publish_antenna_status: true         # Publicar estado das antenas
  publish_inventory_status: true       # Publicar início/parada de inventário
```

### Via página de configuração

1. Acesse a página **Config** na Web UI
2. Role até a seção [**Events**](07-configuracao.md#eventos-de-saude)
3. Marque o checkbox `Enabled`
4. Selecione quais categorias de eventos deseja publicar
5. Clique em `Save Configuration`

> **Nota:** Ao ativar eventos, também configure os limiares de saúde na seção
> [**Monitoring**](07-configuracao.md#76-monitoramento-e-limiares-de-saude).

---

## 10.2 Eventos publicados

O monitor de saúde publica **quatro categorias de eventos**. Cada uma é publicada em um tópico MQTT separado e pode ser habilitada/desabilitada independentemente.

| Evento | Tópico MQTT | Disparado quando | Config |
|--------|-------------|------------------|--------|
| **Health Alert** | `healthAlerts` | Temperatura cruza limiar de warning ou critical | `publish_health_alerts` |
| **Network Status** | `networkStatus` | WiFi ou MQTT broker conectam/desconectam | `publish_network_status` |
| **Antenna Status** | `antennaStatus` | Antena apresenta/recupera de defeito | `publish_antenna_status` |
| **Inventory Status** | `inventoryStatus` | Inventário inicia ou para | `publish_inventory_status` |

### Comportamento de transições

- **Temperatura:** Publica em escaladas (normal → warning, warning → critical) e descidas (critical → warning, warning → normal).
- **Rede e Antenas:** Publica qualquer mudança no estado booleano.
- **Inventário:** Publica quando o estado `InventoryRunning` muda.

> **Nota:** Se a mesma condição de alerta é detectada novamente, não há republicação.

---

## 10.3 Estrutura dos payloads

Cada tipo de evento possui um formato JSON específico. Aqui estão exemplos reais:

### HealthAlertEvent

Publicado quando temperatura cruza um limiar (warning ou critical).

```json
{
  "eventType": "health-alert",
  "readerId": "F412FA828A40",
  "severity": "warning",
  "alertType": "temperature",
  "message": "temperature warning: PA=105.2°C",
  "timestamp": 1742300000000000000,
  "payload": {
    "pa": 105.2,
    "module": 98.1
  }
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `eventType` | String | Sempre `"health-alert"` |
| `readerId` | String | ID do leitor (ex: `F412FA828A40`) |
| `severity` | String | `"warning"` ou `"critical"` — determine a ação |
| `alertType` | String | Sempre `"temperature"` para este tipo de evento |
| `message` | String | Descrição legível (ex: "temperature warning: PA=105.2°C") |
| `timestamp` | Integer | Unix time em nanosegundos |
| `payload.pa` | Float | Temperatura do amplificador de potência (°C) |
| `payload.module` | Float | Temperatura do módulo (°C) |

---

### AntennaStatusEvent

Publicado quando uma antena é detectada como com defeito ou se recupera.

```json
{
  "eventType": "antenna-status-change",
  "readerId": "F412FA828A40",
  "port": 2,
  "change_type": "faulty",
  "timestamp": 1742300000000000000,
  "details": {
    "vswr": 3.8,
    "return_loss": -4.2
  }
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `eventType` | String | Sempre `"antenna-status-change"` |
| `readerId` | String | ID do leitor |
| `port` | Integer | Número da porta de antena (1, 2, 3, 4) |
| `change_type` | String | `"faulty"` (defeito detectado) ou `"recovered"` (recuperada) |
| `timestamp` | Integer | Unix time em nanosegundos |
| `details.vswr` | Float | Razão de ondas estacionárias (VSWR) no momento da detecção |
| `details.return_loss` | Float | Perda de retorno (dB) |

---

### NetworkStatusEvent

Publicado quando WiFi ou MQTT broker conectam/desconectam.

```json
{
  "eventType": "network-status-change",
  "readerId": "F412FA828A40",
  "change_type": "wifi_disconnected",
  "timestamp": 1742300000000000000,
  "details": {
    "rssi": -72
  }
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `eventType` | String | Sempre `"network-status-change"` |
| `readerId` | String | ID do leitor |
| `change_type` | String | `"wifi_connected"`, `"wifi_disconnected"`, `"mqtt_connected"` ou `"mqtt_disconnected"` |
| `timestamp` | Integer | Unix time em nanosegundos |
| `details.rssi` | Integer | Força do sinal WiFi (dBm) — **presente apenas em eventos WiFi** |
| `details.broker` | String | Enderço do broker MQTT — **presente apenas em eventos MQTT** |

---

### InventoryStatusEvent

Publicado quando o inventário é iniciado ou parado no leitor.

```json
{
  "eventType": "inventory-status-change",
  "readerId": "F412FA828A40",
  "change_type": "inventory_started",
  "running": true,
  "timestamp": 1742300000000000000
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `eventType` | String | Sempre `"inventory-status-change"` |
| `readerId` | String | ID do leitor |
| `change_type` | String | `"inventory_started"` ou `"inventory_stopped"` |
| `running` | Boolean | `true` se inventário ativo, `false` se parado |
| `timestamp` | Integer | Unix time em nanosegundos |

---

## 10.4 Limiares e escalada

Os limiares de saúde controlam **quando os alertas são disparados**. Eles são configuráveis e específicos para cada ambiente.

### Escalada de temperatura

A temperatura tem **três níveis de severidade**, formando uma máquina de estados:

```
    Normal
      ↓ (cruza TempWarning)
   Warning  ← Evento publicado: "warning"
      ↓ (cruza TempCritical)
  Critical  ← Evento publicado: "critical"
      ↑ (cai abaixo de TempCritical)
   Warning  ← Evento publicado: "warning"
      ↑ (cai abaixo de TempWarning)
   Normal   ← Evento publicado: "normal"
```

### Limiares padrão

| Limiar | Padrão | Descrição |
|--------|--------|-----------|
| `temp_warning` | `100°C` | Acima deste valor, alerta de nível "warning" é disparado |
| `temp_critical` | `120°C` | Acima deste valor, alerta de nível "critical" é disparado |
| `vswr_warning` | `2.0` | VSWR acima deste valor indica problema de antena |

Configure-os na página **Config** → **Monitoring**:

![Limiares de monitoring](screenshots/config-monitoring-thresholds.png)

---

## 10.5 Tópicos de saúde

Os eventos de saúde são publicados em tópicos MQTT separados, todos no formato `smartreader/{id}/...`:

| Evento | Campo de Config | Tópico Padrão |
|--------|-----------------|----------------|
| Health Alert | `health_alerts` | `smartreader/{id}/healthAlerts` |
| Network Status | `network_status` | `smartreader/{id}/networkStatus` |
| Antenna Status | `antenna_status` | `smartreader/{id}/antennaStatus` |
| Inventory Status | `inventory_status` | `smartreader/{id}/inventoryStatus` |

Esses padrões podem ser **customizados** diretamente no `config.yaml`. Por exemplo, para publicar alertas em `farm/reader1/health`:

```yaml
mqtt:
  topics:
    health_alerts: "farm/%s/health"
```

Mais detalhes em [6. Configuração de Tópicos MQTT](06-topicos.md#63-tópicos-de-eventos-de-saúde).

---

## 10.6 Considerações operacionais

### Primeira execução — silêncio intencional

Quando o gateway inicia, o monitor de saúde executa seu primeiro ciclo de polling mas **não publica eventos iniciais**, mesmo que detecte problemas. Isso evita um flood de eventos ao reiniciar o gateway.

- **Primeira leitura:** Estado armazenado internamente (sem evento)
- **Segunda leitura em diante:** Eventos publicados apenas em transições

### Impacto no desempenho

O monitor de saúde executa em uma goroutine separada por leitor e não impacta o processamento de comandos MQTT. Os intervalos de polling são configuráveis e respeitam o cache TTL existente.

### Tratamento de erros

Se um leitor está offline ou inacessível durante o polling, o monitor:
- Pula o ciclo de verificação sem resetar o estado interno
- Continua tentando na próxima iteração
- Publica um evento de desconexão quando detecta mudança

---

Anterior: [Solução de Problemas](09-solucao-de-problemas.md) | [Voltar ao Índice](index.md)
