# 6. Configuracao de Topicos MQTT

> **Nota:** No topo da pagina e exibido um banner informativo:
> *"Configure MQTT topic patterns. Use %s as placeholder for reader ID."*

---

## 6.1 O que sao os topicos MQTT

Topicos MQTT funcionam como **enderecos** dentro do broker de mensagens. Cada mensagem
e publicada em um topico especifico, e apenas os clientes inscritos naquele topico a
recebem.

O gateway utiliza topicos para duas finalidades principais:

- **Receber comandos** (subscribe) enviados pelo sistema WMS/WES para cada leitor.
- **Enviar respostas e eventos** (publish) de volta ao sistema de controle.

Todos os padroes de topico utilizam o placeholder `%s`, que e substituido
automaticamente pelo **ID do leitor** configurado na aba `Readers`. Por exemplo, se o
leitor possui ID `identix-ez700-01` e o padrao e `smartreader/%s/control`, o topico
real sera:

```
smartreader/identix-ez700-01/control
```

Isso permite que um unico gateway gerencie varios leitores, cada um com seu conjunto
exclusivo de topicos.

---

## 6.2 Campos de topicos

![Formulario de topicos MQTT](screenshots/19-topics-formulario.png)

A tabela abaixo descreve todos os campos disponiveis na pagina de configuracao de
topicos:

| Campo | Direcao | Padrao | Descricao |
|---|---|---|---|
| `control_command` | Subscribe | `smartreader/%s/control` | Topico para receber comandos de controle de inventario (`start`, `stop`, `mode`, `get-gpo`, `set-gpo`). |
| `management_command` | Subscribe | `smartreader/%s/manage` | Topico para receber comandos de gerenciamento (`status`, `retrieve-settings`, `apply-settings`, `reboot`, etc.). |
| `control_response` | Publish | `smartreader/%s/controlResult` | Topico onde o gateway publica as respostas aos comandos de controle. |
| `management_response` | Publish | `smartreader/%s/manageResult` | Topico onde o gateway publica as respostas aos comandos de gerenciamento. |
| `tag_events` | Publish | `smartreader/%s/tagEvents` | Topico para publicacao de eventos de leitura de tags RFID (quando `tagEventsEnabled` esta ativo no leitor). |
| `management_events` | Publish | `smartreader/%s/event` | Topico para publicacao de eventos gerais do leitor (conexao, erros, etc.). |
| `metric_events` | Publish | `smartreader/%s/metrics` | Topico para publicacao de metricas de desempenho do leitor. |
| `connection_status` | LWT | `smartreader/%s/connection` | Topico utilizado para o Last Will and Testament (LWT) do MQTT, indicando se o gateway esta conectado ou desconectado. |
| `task_commands` | Subscribe | `smartreader/%s/api/v1/tasks/%s` | Topico para receber comandos de tarefas. Possui **dois placeholders**: o primeiro e o ID do leitor e o segundo e o ID da tarefa. |
| `task_events` | Publish | `smartreader/%s/api/v1/tasks/%s/response` | Topico para publicar respostas de tarefas. Tambem possui **dois placeholders**: ID do leitor e ID da tarefa. |

> **Nota:** Os topicos `task_commands` e `task_events` utilizam dois placeholders `%s`.
> O primeiro e substituido pelo ID do leitor e o segundo pelo identificador da tarefa.

---

## 6.3 Pre-visualizacao ao vivo

![Pre-visualizacao de topicos resolvidos](screenshots/20-topics-preview.png)

Abaixo do formulario de topicos, a secao **Resolved Topics Preview** exibe os topicos
ja resolvidos para cada leitor configurado no gateway. Essa pre-visualizacao:

1. Substitui automaticamente o placeholder `%s` pelo ID real de cada leitor.
2. Atualiza em **tempo real** conforme voce digita nos campos de topico (com um
   debounce de 500 ms para evitar atualizacoes excessivas).
3. Agrupa os topicos por leitor, facilitando a verificacao visual.

Essa funcionalidade e especialmente util para:

- Confirmar que os padroes de topico estao corretos antes de salvar.
- Verificar rapidamente quais topicos serao usados por cada leitor.
- Identificar possiveis conflitos de topicos entre leitores.

> **Nota:** A pre-visualizacao reflete apenas o que esta digitado nos campos. As
> alteracoes so terao efeito apos clicar em `Save & Apply`.

---

## 6.4 Salvar e Aplicar

Apos ajustar os padroes de topico desejados:

1. Revise a secao de pre-visualizacao para confirmar que os topicos estao corretos.
2. Clique no botao `Save & Apply`.
3. O gateway salvara as alteracoes no arquivo `config.yaml`.
4. As inscricoes MQTT serao **recarregadas automaticamente** (hot-reload), sem
   necessidade de reiniciar o gateway.

> **Nota:** O hot-reload re-inscreve o gateway nos novos topicos e cancela a inscricao
> nos topicos anteriores. Comandos em andamento no momento da troca podem ser perdidos.

---

## 6.5 Recarregar do Disco

Caso voce tenha feito alteracoes nos campos mas deseje **descartar** tudo e voltar ao
estado salvo:

1. Clique no botao `Reload from Disk`.
2. O gateway relera o arquivo `config.yaml` do disco.
3. Todos os campos do formulario serao atualizados com os valores salvos, descartando
   qualquer alteracao nao salva.

> **Atencao:** Essa acao e irreversivel. Todas as modificacoes feitas nos campos desde
> o ultimo `Save & Apply` serao perdidas.

---

## Navegacao

Anterior: [Visualizador de Logs](05-logs.md) | Proximo: [Configuracoes Gerais](07-configuracao.md)
