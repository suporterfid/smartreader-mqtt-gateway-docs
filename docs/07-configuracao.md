# 7. Configuracoes Gerais

A pagina **Config** centraliza as configuracoes de conexao MQTT, API local e logging do
gateway. Todas as alteracoes feitas aqui sao salvas no arquivo `config.yaml`.

![Configuracao de conexao MQTT](screenshots/21-config-mqtt.png)

---

## 7.1 Conexao MQTT

A secao de conexao MQTT define como o gateway se conecta ao broker de mensagens. Os
campos disponiveis sao:

| Campo | Tipo | Padrao | Descricao |
|---|---|---|---|
| Broker URL | Texto | `mqtt://192.168.1.10` | Endereco do broker MQTT. Use `mqtt://` para conexoes sem criptografia ou `mqtts://` para conexoes com TLS. |
| Port | Numero | `1883` | Porta do broker. Valores comuns: `1883` (sem TLS), `8883` ou `8884` (com TLS). |
| Client ID | Texto | `smartreader-identix-gw` | Identificador unico do gateway no broker. Deve ser unico entre todos os clientes conectados ao mesmo broker. |
| Username | Texto | *(vazio)* | Nome de usuario para autenticacao no broker. Opcional se o broker nao exigir autenticacao. |
| Password | Senha | *(vazio)* | Senha para autenticacao no broker. O campo possui um botao de mostrar/ocultar para facilitar a digitacao. |
| Connect Timeout | Numero | `10` | Tempo maximo (em segundos) para estabelecer a conexao com o broker. |
| Keep Alive | Numero | `30` | Intervalo (em segundos) entre pacotes de keep-alive enviados ao broker para manter a conexao ativa. |

> **Nota:** O campo de senha exibe `***` quando ja existe uma senha configurada. Para
> manter a senha atual, deixe o campo como esta. Altere-o apenas se desejar definir uma
> nova senha.

### Testar conexao MQTT

Antes de salvar, voce pode verificar se os parametros de conexao estao corretos:

1. Preencha os campos de conexao MQTT (broker, porta, credenciais).
2. Clique no botao `Test Connection`.
3. O gateway tentara estabelecer uma conexao de teste com o broker usando os parametros
   informados.
4. Se a conexao for bem-sucedida, uma notificacao de sucesso sera exibida.

![Teste de conexao MQTT bem-sucedido](screenshots/22-config-teste-mqtt-ok.png)

5. Se a conexao falhar, uma notificacao de erro sera exibida com detalhes sobre a falha
   (broker inacessivel, credenciais invalidas, timeout, etc.).

> **Nota:** O teste de conexao utiliza os valores **atualmente digitados** nos campos,
> nao os valores salvos. Isso permite testar antes de aplicar.

---

## 7.2 API Local

![Configuracao de API local e logging](screenshots/23-config-api-logging.png)

A secao de API local controla o servidor HTTP embutido no gateway, que fornece a
interface web e os endpoints REST.

| Campo | Tipo | Padrao | Descricao |
|---|---|---|---|
| Enabled | Checkbox | Ativado | Habilita ou desabilita o servidor de API local. |
| Listen Address | Texto | `:8080` | Endereco e porta onde o servidor HTTP escuta. |

Formatos aceitos para o campo `Listen Address`:

- `:8080` — escuta em **todas as interfaces** de rede na porta 8080.
- `127.0.0.1:8080` — escuta apenas em **localhost** (acesso somente da propria maquina).
- `0.0.0.0:8080` — equivalente a `:8080`, escuta em todas as interfaces.
- `192.168.1.100:8080` — escuta apenas no IP especifico.

> **Atencao:** Alterar o endereco de escuta da API ou desabilitar a API tornara a
> interface web **inacessivel** pelo endereco atual. Certifique-se de que voce tera
> acesso pelo novo endereco antes de salvar. Caso perca o acesso, edite o arquivo
> `config.yaml` manualmente e reinicie o gateway.

---

## 7.3 Logging

A secao de logging controla o nivel de detalhe e o formato das mensagens de log geradas
pelo gateway.

| Campo | Tipo | Opcoes | Padrao | Descricao |
|---|---|---|---|---|
| Log Level | Dropdown | `Debug`, `Info`, `Warn`, `Error` | `Info` | Nivel minimo de severidade das mensagens registradas. |
| Log Format | Dropdown | `JSON`, `Text` | `JSON` | Formato de saida dos logs. |

Descricao dos niveis de log:

- **Debug** — Maximo detalhe. Inclui informacoes de depuracao, payloads completos de
  requisicoes/respostas HTTP e mensagens MQTT. Util para diagnostico de problemas.
- **Info** — Nivel padrao. Registra operacoes normais como conexoes, comandos recebidos
  e respostas enviadas.
- **Warn** — Registra situacoes atipicas que nao impedem o funcionamento, como comandos
  nao suportados ou campos ignorados.
- **Error** — Registra apenas erros que afetam o funcionamento, como falhas de conexao
  MQTT ou erros HTTP ao comunicar com leitores.

Descricao dos formatos de log:

- **JSON** — Saida estruturada em JSON. Ideal para integracao com ferramentas de
  monitoramento (Elasticsearch, Grafana Loki, etc.).
- **Text** — Saida em texto plano, mais legivel para leitura direta no terminal.

---

## 7.4 Salvar configuracao

Apos ajustar os parametros desejados:

1. Revise todos os campos alterados.
2. Clique no botao `Save Configuration`.
3. O gateway validara os valores informados.
4. Se a validacao for bem-sucedida, as configuracoes serao salvas no arquivo
   `config.yaml` e uma notificacao de sucesso sera exibida.
5. Se algum campo estiver invalido, uma mensagem de erro indicara o problema.

> **Nota:** Algumas alteracoes (como parametros de conexao MQTT) exigem reinicio do
> gateway para entrar em vigor. Nesse caso, um banner de reinicio sera exibido apos
> salvar.

---

## 7.5 Banner de reinicio

![Banner solicitando reinicio do gateway](screenshots/24-config-banner-restart.png)

Apos salvar alteracoes que exigem reinicio do gateway, um banner e exibido no topo da
pagina com uma mensagem explicativa e o botao `Restart Now`.

As alteracoes que exigem reinicio incluem:

- Endereco do broker MQTT (URL e porta).
- Client ID do MQTT.
- Credenciais do MQTT (usuario e senha).
- Timeouts de conexao e keep-alive.
- Endereco de escuta da API local.
- Habilitacao/desabilitacao da API local.

Para aplicar essas alteracoes:

1. Clique no botao `Restart Now` no banner.
2. O gateway sera reiniciado automaticamente.
3. A pagina sera recarregada apos o reinicio.
4. Verifique no [Dashboard](03-dashboard.md) se o gateway reconectou corretamente ao
   broker MQTT.

> **Atencao:** Durante o reinicio, o gateway ficara temporariamente indisponivel. Todos
> os leitores serao desconectados e reconectados automaticamente apos o reinicio. O
> broker MQTT recebera a mensagem LWT de desconexao e, apos o reinicio, a mensagem de
> conexao sera publicada novamente.

---

## Navegacao

Anterior: [Configuracao de Topicos MQTT](06-topicos.md) | Proximo: [Informacoes do Sistema](08-sistema.md)
