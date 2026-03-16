# 3. Painel Principal (Dashboard)

O Dashboard e a primeira pagina exibida apos o login. Ele fornece uma visao geral do estado do gateway, incluindo conexao MQTT, leitores, tempo de atividade e eventos recentes.

![Dashboard completo](screenshots/03-dashboard-visao-geral.png)

---

## 3.1 Cards de status

A parte superior do dashboard exibe quatro cards com informacoes resumidas:

![Cards de status](screenshots/04-dashboard-cards.png)

### MQTT Status

Indica o estado da conexao com o broker MQTT:
- **Connected** (badge verde) — o gateway esta conectado e operacional
- **Disconnected** (badge vermelho) — a conexao com o broker foi perdida

> **Nota:** Se o status MQTT estiver "Disconnected", os comandos enviados aos leitores nao serao processados. Verifique as configuracoes de conexao na pagina [Config](07-configuracao.md).

### Readers

Exibe dois numeros:
- **Total** — quantidade de leitores configurados
- **Online** — quantidade de leitores com conexao ativa

### Uptime

Tempo decorrido desde que o gateway foi iniciado, no formato `Xh Ym` (ex: `2h 15m`).

### Version

Versao do software do gateway e a versao do runtime Go utilizada (ex: `v0.1.0` / `go1.24.1`).

---

## 3.2 Atividade recente

Abaixo dos cards, a secao `Recent Activity` exibe os ultimos 10 eventos registrados pelo gateway. Cada entrada mostra:

| Elemento | Descricao |
|----------|-----------|
| **Nivel** | Cor indicando a severidade: azul (info), amarelo (warn), vermelho (error), cinza (debug) |
| **Timestamp** | Data e hora do evento no formato local do navegador |
| **Reader ID** | Identificador do leitor relacionado ao evento (quando aplicavel) |
| **Mensagem** | Descricao do evento |

Esta secao e util para verificar rapidamente se ha erros ou eventos importantes sem precisar acessar a pagina de [Logs](05-logs.md) completa.

---

## 3.3 Atualizacao automatica

O dashboard atualiza todas as informacoes automaticamente a cada **2 segundos**. Nao e necessario recarregar a pagina manualmente.

As seguintes informacoes sao atualizadas:
- Status da conexao MQTT
- Contagem de leitores online
- Tempo de atividade (uptime)
- Lista de atividade recente

---

Anterior: [Login e Autenticacao](02-login.md) | Proximo: [Gerenciamento de Leitores](04-leitores.md)
