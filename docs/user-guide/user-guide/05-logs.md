# 5. Visualizador de Logs

## Visao Geral

O Visualizador de Logs e uma pagina dedicada da interface web que exibe as entradas de log do gateway em tempo real. Ele permite monitorar a atividade do sistema, diagnosticar problemas e acompanhar o fluxo de comandos entre o broker MQTT e os leitores Identix EZ700.

![Visao geral do visualizador de logs](screenshots/14-logs-visao-geral.png)

As entradas de log sao transmitidas continuamente para o navegador via streaming, refletindo a atividade mais recente do gateway sem necessidade de recarregar a pagina.

---

## 5.1 Streaming em Tempo Real

O visualizador de logs utiliza uma conexao **Server-Sent Events (SSE)** para receber entradas de log do gateway em tempo real. Assim que a pagina e carregada, a conexao SSE e estabelecida automaticamente e as novas entradas de log aparecem na tela conforme sao geradas pelo gateway.

Caso a conexao SSE seja interrompida (por exemplo, devido a instabilidade de rede ou reinicio do gateway), o visualizador tenta **reconectar automaticamente a cada 3 segundos** ate que a conexao seja restabelecida.

> **Nota:** Durante a reconexao, as entradas de log geradas pelo gateway nao sao perdidas no servidor, mas nao serao exibidas na interface ate que a conexao seja restabelecida. Somente as novas entradas a partir da reconexao serao exibidas.

---

## 5.2 Filtros

O visualizador oferece tres mecanismos de filtragem que podem ser usados de forma combinada para localizar entradas especificas. Os filtros sao aplicados em tempo real sobre as entradas ja exibidas e tambem sobre novas entradas que chegam via streaming.

### Filtro por Nivel (Level)

Um menu suspenso permite filtrar as entradas de log por nivel de severidade:

- **All Levels** -- exibe todas as entradas, independentemente do nivel
- **Debug** -- exibe apenas entradas de nivel `DEBUG`
- **Info** -- exibe apenas entradas de nivel `INFO`
- **Warn** -- exibe apenas entradas de nivel `WARN`
- **Error** -- exibe apenas entradas de nivel `ERROR`

![Filtro por nivel de log](screenshots/15-logs-filtros.png)

> **Nota:** O filtro por nivel opera sobre as entradas ja recebidas no navegador. A configuracao de nivel de log do gateway (`log.level` no arquivo `config.yaml`) determina quais entradas sao efetivamente geradas pelo servidor. Por exemplo, se o gateway esta configurado com `log.level: info`, entradas de nivel `DEBUG` nao serao geradas e portanto nao apareceram no visualizador, mesmo selecionando o filtro **Debug**.

### Filtro por Leitor (Reader)

Um menu suspenso lista todos os leitores configurados no gateway, alem da opcao para exibir todos:

- **All Readers** -- exibe entradas de log de todos os leitores e do sistema em geral
- **{reader-id}** -- exibe apenas entradas associadas ao leitor selecionado (por exemplo, `identix-ez700-01`)

![Filtro por leitor](screenshots/16-logs-filtro-reader.png)

> **Nota:** Entradas de log do sistema que nao estao associadas a um leitor especifico (como mensagens de inicializacao ou conexao MQTT) somente aparecem quando a opcao **All Readers** esta selecionada.

### Campo de Pesquisa (Search)

O campo de pesquisa permite buscar texto livre nas entradas de log. A busca e realizada nos seguintes campos de cada entrada:

- **message** -- o texto principal da mensagem de log
- **error** -- detalhes do erro, quando presente
- **readerId** -- o identificador do leitor associado a entrada

![Campo de pesquisa de logs](screenshots/17-logs-pesquisa.png)

A pesquisa e **case-insensitive** (nao diferencia maiusculas de minusculas). Os resultados sao atualizados dinamicamente conforme o usuario digita no campo de pesquisa.

> **Nota:** O campo de pesquisa funciona em conjunto com os demais filtros. Por exemplo, voce pode selecionar o nivel **Error**, um leitor especifico e digitar um termo de busca para localizar uma entrada de erro especifica daquele leitor.

---

## 5.3 Controles

A barra de controles do visualizador oferece tres botoes de acao para gerenciar a exibicao das entradas de log.

### Botao `Pause` / `Resume`

O botao `Pause` interrompe a exibicao de novas entradas de log na tela. As entradas continuam sendo recebidas em segundo plano, mas nao sao adicionadas a area de visualizacao.

![Visualizador de logs pausado](screenshots/18-logs-pausado.png)

- Ao clicar em `Pause`, o botao muda para `Resume` e um indicador visual sinaliza que o streaming esta pausado.
- Ao clicar em `Resume`, todas as entradas acumuladas durante a pausa sao adicionadas a area de visualizacao e o streaming em tempo real e retomado.

> **Nota:** Pausar o visualizador nao afeta a operacao do gateway. Os logs continuam sendo gerados e armazenados normalmente. A pausa afeta apenas a exibicao no navegador.

### Botao `Clear`

O botao `Clear` remove todas as entradas de log atualmente exibidas na area de visualizacao.

- As entradas sao removidas apenas da tela do navegador.
- O streaming continua ativo e novas entradas aparecerao normalmente apos a limpeza.
- Esta acao nao pode ser desfeita.

> **Atencao:** O botao `Clear` remove todas as entradas visíveis, incluindo aquelas que correspondem aos filtros ativos. Se voce precisa preservar as entradas antes de limpar, utilize o botao `Export` primeiro.

### Botao `Export`

O botao `Export` faz o download de todas as entradas de log atualmente exibidas no visualizador em formato JSON.

- O arquivo e salvo com o nome `gateway-logs-{date}.json`, onde `{date}` corresponde a data atual no formato ISO (por exemplo, `gateway-logs-2026-03-16.json`).
- O arquivo contem um array JSON com todas as entradas exibidas no momento da exportacao, respeitando os filtros ativos.
- Cada entrada no arquivo inclui todos os campos originais: timestamp, level, readerId, message, error (quando presente), entre outros.

> **Nota:** A exportacao considera apenas as entradas visíveis no momento, levando em conta os filtros aplicados. Para exportar todos os logs sem filtro, certifique-se de que os filtros estejam configurados como **All Levels**, **All Readers** e o campo de pesquisa esteja vazio.

---

## 5.4 Formato das Entradas

Cada entrada de log exibida no visualizador apresenta as seguintes informacoes, dispostas em uma unica linha:

| Elemento | Descricao | Exemplo |
|----------|-----------|---------|
| **Timestamp** | Data e hora no formato locale do navegador | `16/03/2026, 14:32:05` |
| **Level** | Nivel de severidade com texto alinhado e cor de destaque | `INFO` |
| **Reader ID** | Identificador do leitor entre colchetes (quando aplicavel) | `[identix-ez700-01]` |
| **Message** | Texto principal da mensagem de log | `command dispatched` |
| **Error** | Detalhes do erro, exibidos apenas quando presentes | `connection refused` |

### Cores por Nivel

Cada nivel de log e exibido com uma cor distinta para facilitar a identificacao visual:

| Nivel | Cor |
|-------|-----|
| `DEBUG` | Cinza |
| `INFO` | Azul |
| `WARN` | Amarelo |
| `ERROR` | Vermelho |

O texto do nivel e alinhado (padded) para manter o alinhamento visual das entradas, independentemente do comprimento do nome do nivel.

> **Nota:** As cores sao aplicadas apenas ao texto do nivel. O restante da entrada utiliza a cor padrao do tema da interface.

---

## 5.5 Limite de Buffer

O visualizador mantem no maximo **1000 entradas** de log exibidas simultaneamente na tela. Quando o limite e atingido e novas entradas chegam:

1. As entradas **mais antigas** sao descartadas automaticamente.
2. As novas entradas sao adicionadas ao final da lista.
3. A area de visualizacao realiza **auto-scroll** para o final, mantendo as entradas mais recentes visiveis.

> **Nota:** O limite de 1000 entradas se aplica apenas a exibicao no navegador. O gateway continua gerando e registrando logs normalmente no seu sistema de logging interno, independentemente do estado do visualizador.

> **Atencao:** Se voce precisa analisar um volume grande de logs ou entradas historicas, considere utilizar o botao `Export` periodicamente para salvar os logs em arquivo, ou acesse os arquivos de log do gateway diretamente no servidor.

---

Anterior: [Gerenciamento de Leitores](04-leitores.md) | Proximo: [Configuracao de Topicos MQTT](06-topicos.md)
