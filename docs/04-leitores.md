# 4. Gerenciamento de Leitores

A pagina `Readers` e o centro de operacoes para gerenciar os leitores RFID Identix EZ700 conectados ao gateway. Aqui voce pode adicionar novos leitores, editar configuracoes, testar conexoes, monitorar status e remover leitores.

![Pagina de leitores](screenshots/07-readers-tabela.png)

---

## 4.1 Visao geral da pagina

A pagina e dividida em tres secoes:

1. **Botao `+ Add Reader`** — abre o formulario para adicionar um novo leitor
2. **Discover Reader by MAC Address** — ferramenta para encontrar o IP de um leitor pela rede
3. **Tabela de leitores** — lista todos os leitores configurados com status e acoes

### Tabela de leitores

| Coluna | Descricao |
|--------|-----------|
| **ID** | Identificador unico do leitor (usado nos topicos MQTT) |
| **IP Address** | Endereco IP do leitor na rede |
| **Region** | Regiao de operacao RF (ex: `BRAZIL`) |
| **Status** | Badge verde `Online` ou vermelho `Offline` |
| **Antennas** | Lista de portas de antena ativas (ex: `1, 2, 3, 4`) |
| **Actions** | Botoes de acao: `Test`, `Edit`, `Delete` |

---

## 4.2 Descoberta por endereco MAC

Se voce conhece o endereco MAC do leitor (impresso na etiqueta do equipamento) mas nao sabe o IP atribuido pela rede, use a ferramenta de descoberta.

![Descoberta por MAC com resultado](screenshots/08-readers-descoberta-mac.png)

### Passo a passo

1. Localize o endereco MAC na etiqueta fisica do leitor Identix EZ700
2. No campo `MAC Address`, digite o endereco em qualquer um destes formatos:
   - Com dois-pontos: `F4:12:FA:82:8A:40`
   - Com hifens: `F4-12-FA-82-8A-40`
   - Sem separadores: `F412FA828A40`
3. Clique em `Discover`
4. Aguarde o resultado:
   - **Encontrado** — badge verde mostrando o IP e um botao `Use this IP`
   - **Nao encontrado** — badge amarelo "Device not found on this network"

> **Nota:** O leitor deve estar na mesma rede (ou sub-rede acessivel) que o gateway. A descoberta funciona consultando a tabela ARP do sistema operacional e, se necessario, fazendo um ping sweep nas sub-redes locais.

### Usar o IP descoberto

Se o leitor foi encontrado, clique em `Use this IP`. Isso abre automaticamente o formulario de adicao com o campo IP ja preenchido.

---

## 4.3 Adicionar um novo leitor

Para cadastrar um novo leitor no gateway:

1. Clique no botao `+ Add Reader` no topo da pagina

   ![Modal de adicao de leitor](screenshots/09-readers-modal-adicionar.png)

2. Preencha os campos do formulario:

   | Campo | Descricao | Obrigatorio | Padrao |
   |-------|-----------|:-----------:|--------|
   | **Reader ID** | Identificador unico do leitor. Sera usado nos topicos MQTT (ex: `identix-ez700-01` ou o MAC `F412FA828A40`) | Sim | — |
   | **IP Address** | Endereco IP do leitor na rede (ex: `192.168.67.103`) | Sim | — |
   | **Region** | Regiao de operacao RF | Nao | `BRAZIL` |
   | **Username** | Usuario para autenticacao HTTP no leitor | Sim | — |
   | **Password** | Senha para autenticacao HTTP no leitor | Sim | — |
   | **HTTP Timeout (s)** | Tempo limite em segundos para requisicoes HTTP ao leitor | Nao | `5` |
   | **Session TTL (s)** | Tempo de vida da sessao HTTP com o leitor (em segundos). Apos esse tempo, o gateway faz re-autenticacao automatica | Nao | `300` |
   | **Antennas** | Portas de antena ativas, separadas por virgula (ex: `1,2,3,4`) | Nao | `1,2,3,4` |
   | **Enable tag event passthrough** | Quando marcado, o gateway encaminha eventos de tag RFID do leitor para o topico MQTT correspondente | Nao | Desmarcado |

3. Clique em `Save`

Apos salvar, o leitor aparece na tabela e o gateway:
- Cria uma sessao HTTP com o leitor
- Inscreve-se nos topicos MQTT de controle e gerenciamento
- Salva a configuracao no arquivo `config.yaml`

> **Atencao:** O `Reader ID` nao pode ser alterado apos a criacao. Escolha um identificador significativo.

---

## 4.4 Editar um leitor

Para alterar as configuracoes de um leitor existente:

1. Na tabela de leitores, clique no botao `Edit` (icone de lapis) na linha do leitor desejado

   ![Modal de edicao de leitor](screenshots/10-readers-modal-editar.png)

2. O formulario sera aberto com os dados atuais preenchidos
   - O campo `Reader ID` estara **desabilitado** (nao pode ser alterado)
   - O campo `Password` estara vazio com o placeholder "Leave empty to keep current"
3. Altere os campos desejados
4. Clique em `Save`

> **Nota:** Se voce nao preencher o campo de senha, a senha atual sera mantida. Preencha apenas se desejar alterar.

---

## 4.5 Testar conexao do leitor

Antes de operar um leitor, e recomendavel testar a conexao para verificar se o gateway consegue se comunicar com ele.

1. Na tabela de leitores, clique no botao `Test` (icone de plug) na linha do leitor
2. Aguarde o resultado:

   - **Sucesso** — notificacao verde: "Connection successful"

     ![Teste de conexao bem-sucedido](screenshots/12-readers-teste-ok.png)

   - **Falha** — notificacao vermelha com a descricao do erro (ex: "connection refused", "timeout")

     ![Teste de conexao com falha](screenshots/13-readers-teste-erro.png)

### O que o teste verifica

O teste faz uma requisicao HTTP `GET /rfid/properties` ao leitor usando o IP, usuario e senha configurados. Se o leitor responde com sucesso, a conexao esta OK.

### Causas comuns de falha

| Erro | Possivel causa |
|------|----------------|
| Connection refused | Leitor desligado ou IP incorreto |
| Timeout | Leitor inacessivel na rede ou firewall bloqueando |
| 401 Unauthorized | Usuario ou senha incorretos |
| Connection reset | Leitor reiniciando ou com problema de rede |

---

## 4.6 Excluir um leitor

Para remover um leitor do gateway:

1. Na tabela de leitores, clique no botao `Delete` (icone de lixeira) na linha do leitor
2. Um dialogo de confirmacao sera exibido:

   ![Confirmacao de exclusao](screenshots/11-readers-modal-excluir.png)

3. Clique em `Delete` para confirmar ou `Cancel` para cancelar

Ao confirmar a exclusao:
- O gateway cancela a inscricao nos topicos MQTT do leitor
- O leitor e removido da configuracao
- O arquivo `config.yaml` e atualizado automaticamente

> **Atencao:** Esta acao e irreversivel. Apos excluir, voce precisara adicionar o leitor novamente caso deseje restaura-lo.

---

## 4.7 Indicadores de status

A coluna `Status` na tabela mostra o estado atual da conexao com cada leitor:

| Badge | Significado |
|-------|-------------|
| `Online` (verde) | O gateway conseguiu se comunicar com o leitor recentemente |
| `Offline` (vermelho) | O leitor nao esta respondendo ou nao foi possivel estabelecer conexao |

O status e atualizado automaticamente a cada 2 segundos junto com a atualizacao do cabecalho.

> **Nota:** Um leitor pode aparecer como `Offline` temporariamente durante uma reinicializacao ou se a rede estiver instavel. Use o botao `Test` para diagnosticar.

---

Anterior: [Painel Principal (Dashboard)](03-dashboard.md) | Proximo: [Visualizador de Logs](05-logs.md)
