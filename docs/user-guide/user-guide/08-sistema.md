# 8. Informacoes do Sistema

A pagina **System** exibe informacoes sobre a versao do gateway, tempo de atividade e oferece acoes administrativas como alterar o nivel de log, fazer backup da configuracao e reiniciar o servico.

Para acessar, clique em `System` no menu lateral da Web UI.

---

## 8.1 Informacoes do sistema

![Informacoes do sistema](screenshots/25-system-info.png)

A secao superior apresenta uma tabela somente leitura com os dados do gateway:

| Campo | Descricao | Exemplo |
|-------|-----------|---------|
| **Version** | Versao do software do gateway | `v0.1.0` |
| **Go Version** | Versao do runtime Go utilizada na compilacao | `go1.24.1` |
| **Uptime** | Tempo decorrido desde que o gateway foi iniciado | `2d 5h 32m` |
| **Readers** | Numero total de leitores configurados | `3` |
| **MQTT** | Status da conexao com o broker MQTT | Badge verde `Connected` ou vermelho `Disconnected` |

> **Nota:** Essas informacoes sao atualizadas automaticamente ao carregar a pagina. O badge de MQTT reflete o estado em tempo real da conexao.

---

## 8.2 Alterar nivel de log

![Acoes do sistema](screenshots/26-system-acoes.png)

O dropdown `Log Level` permite alterar o nivel de log em tempo de execucao. As opcoes disponiveis sao:

1. **Debug** -- registra todas as mensagens, incluindo detalhes internos de depuracao.
2. **Info** -- registra eventos operacionais normais (padrao).
3. **Warn** -- registra apenas avisos e erros.
4. **Error** -- registra apenas erros.

A alteracao entra em vigor imediatamente apos a selecao.

> **Nota:** Essa mudanca e valida apenas durante a execucao atual. Para que o nivel de log persista apos reiniciar o gateway, salve a configuracao na pagina `Config` (veja [Configuracoes Gerais](07-configuracao.md)).

---

## 8.3 Backup da configuracao

O botao `Download Config Backup` faz o download da configuracao atual do gateway como um arquivo JSON.

1. Clique em `Download Config Backup`.
2. O navegador ira baixar um arquivo com o nome `gateway-config-{YYYY-MM-DD}.json`.
3. Guarde o arquivo em local seguro.

> **Nota:** E recomendavel fazer um backup antes de realizar alteracoes significativas na configuracao. Assim, voce podera restaurar a configuracao anterior caso necessario.

---

## 8.4 Reiniciar o gateway

O botao `Restart Gateway` (estilo vermelho/perigo) encerra o processo do gateway de forma controlada (graceful shutdown).

![Confirmacao de restart](screenshots/27-system-confirmar-restart.png)

1. Clique em `Restart Gateway`.
2. O navegador exibira uma caixa de confirmacao perguntando se deseja continuar.
3. Confirme para prosseguir ou cancele para voltar.
4. Apos a confirmacao, o gateway publicara a mensagem LWT de desconexao para cada leitor e encerrara o processo.
5. A pagina perdera a conexao temporariamente ate que o gateway seja reiniciado pelo gerenciador de processos.

> **Atencao:** O gateway depende de um gerenciador de processos (systemd, Windows Service, Docker) para ser reiniciado automaticamente. Se nenhum gerenciador estiver configurado, o gateway sera encerrado e **nao voltara a funcionar** ate ser iniciado manualmente.

---

**Navegacao:**
Anterior: [Configuracoes Gerais](07-configuracao.md) | Proximo: [Solucao de Problemas](09-solucao-de-problemas.md)
