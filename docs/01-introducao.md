# 1. Introducao

O **SmartReader Gateway** e um middleware que conecta leitores RFID Identix EZ700 a sistemas WMS/WES atraves do protocolo MQTT. A interface web (Web UI) permite configurar, monitorar e operar os leitores sem necessidade de linha de comando.

## O que a Web UI oferece

- **Gerenciamento de leitores** — adicionar, editar, testar e remover leitores RFID
- **Monitoramento em tempo real** — status de conexao, logs ao vivo, atividade recente
- **Configuracao** — parametros MQTT, topicos, nivel de log e API local
- **Diagnostico** — descoberta de leitores por endereco MAC, teste de conexao, exportacao de logs

## Como acessar

Abra o navegador e acesse:

```
http://<endereco-do-gateway>:8080
```

Onde `<endereco-do-gateway>` e o IP ou hostname da maquina onde o gateway esta instalado. A porta padrao e `8080`, mas pode ser alterada na configuracao (`localApi.listen`).

> **Exemplo:** Se o gateway esta rodando na maquina local, acesse `http://localhost:8080`.

Se a autenticacao estiver habilitada, voce sera direcionado para a [tela de login](02-login.md). Caso contrario, o painel principal sera exibido diretamente.

## Navegacao

A interface e organizada em tres areas principais:

![Visao geral da interface](screenshots/03-dashboard-visao-geral.png)

### Cabecalho (Header)

A barra superior esta presente em todas as paginas e exibe:

- **Logo e nome** — "SmartReader Gateway"
- **Indicador MQTT** — ponto verde (conectado) ou vermelho (desconectado)
- **Contagem de leitores** — numero total de leitores configurados
- **Alternancia de tema** — botao para alternar entre tema escuro e claro
- **Botao Logout** — encerra a sessao (visivel quando autenticacao esta habilitada)

![Cabecalho com indicadores de status](screenshots/05-header-barra-status.png)

### Barra lateral (Sidebar)

A barra lateral a esquerda contem os links de navegacao:

| Icone | Pagina | Descricao |
|-------|--------|-----------|
| Velocimetro | **Dashboard** | Painel principal com status geral |
| Torre | **Readers** | Gerenciamento de leitores RFID |
| Pergaminho | **Logs** | Visualizador de logs em tempo real |
| Diagrama | **Topics** | Configuracao de topicos MQTT |
| Controles | **Config** | Configuracoes gerais (MQTT, API, logging) |
| Servidor | **System** | Informacoes do sistema e acoes administrativas |

A pagina ativa e destacada com uma borda azul a esquerda.

![Barra lateral de navegacao](screenshots/06-sidebar-navegacao.png)

## Temas

A Web UI suporta dois temas visuais:

- **Tema escuro** (padrao) — fundo escuro, ideal para ambientes com pouca luz
- **Tema claro** — fundo branco, ideal para ambientes bem iluminados

Para alternar, clique no icone de lua/sol no cabecalho. A preferencia e salva no navegador e mantida entre sessoes.

| Tema escuro | Tema claro |
|-------------|------------|
| ![Tema escuro](screenshots/28-tema-escuro.png) | ![Tema claro](screenshots/29-tema-claro.png) |

## Convencoes deste manual

- Elementos da interface (botoes, campos, menus) sao referenciados em ingles entre crases, como aparecem na tela: `+ Add Reader`, `Save Configuration`
- Procedimentos sao apresentados em listas numeradas (passo a passo)
- Notas importantes sao destacadas com:
  > **Nota:** Informacao complementar.
  >
  > **Atencao:** Alerta sobre acao que pode causar impacto.

---

Proximo: [Login e Autenticacao](02-login.md)
