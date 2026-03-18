# 9. Solucao de Problemas

Esta pagina reune os problemas mais comuns encontrados ao utilizar o SmartReader Gateway e suas respectivas solucoes. Para cada problema, sao listados os sintomas observados, as possiveis causas e os passos para resolucao.

> **Nota:** Antes de investigar qualquer problema, verifique a pagina de logs (`Logs`) da Web UI. Na maioria dos casos, as mensagens de erro fornecem indicacoes claras sobre a causa raiz.

---

### 9.1 Nao consigo acessar a Web UI

**Sintomas:**
- O navegador exibe "Conexao recusada" ou "Pagina indisponivel".
- A URL `http://<ip>:8080` nao carrega.

**Possiveis causas:**
- O gateway nao esta em execucao.
- A porta configurada em `localApi.listen` e diferente de `8080`.
- Um firewall esta bloqueando a porta.
- O endereco de escuta esta vinculado a uma interface de rede diferente (por exemplo, `127.0.0.1` em vez de `0.0.0.0`).

**Solucao:**
1. Verifique se o processo do gateway esta em execucao (`ps aux | grep gateway` no Linux ou Gerenciador de Tarefas no Windows).
2. Confirme o valor de `localApi.listen` no arquivo de configuracao. O formato e `":8080"` para escutar em todas as interfaces.
3. Verifique as regras de firewall e libere a porta correspondente.
4. Tente acessar `http://localhost:8080` diretamente na maquina onde o gateway esta instalado.

---

### 9.2 MQTT mostra "Disconnected"

**Sintomas:**
- O indicador no cabecalho da Web UI exibe um ponto vermelho.
- O dashboard mostra o status MQTT como `Disconnected`.

**Possiveis causas:**
- O broker MQTT esta inacessivel (fora do ar ou em outro endereco).
- A URL ou porta do broker esta incorreta na configuracao.
- As credenciais (usuario/senha) estao invalidas.
- Problema de rede entre o gateway e o broker.
- O broker exige TLS, mas a configuracao usa `mqtt://` em vez de `mqtts://`.

**Solucao:**
1. Acesse a pagina `Config` e verifique a URL e a porta do broker MQTT.
2. Utilize o botao `Test Connection` para diagnosticar a conectividade.
3. Confirme que o usuario e a senha estao corretos.
4. Se o broker exigir TLS, altere o protocolo para `mqtts://` e a porta para `8883`.
5. Verifique se ha conectividade de rede entre o gateway e o broker (por exemplo, `ping <broker-ip>`).

---

### 9.3 Leitor mostra status "Offline"

**Sintomas:**
- Na lista de leitores, a linha do leitor exibe um badge vermelho `Offline`.

**Possiveis causas:**
- O leitor esta desligado ou reiniciando.
- O endereco IP configurado para o leitor esta incorreto.
- As credenciais HTTP do leitor (usuario/senha) estao invalidas.
- O valor de `httpTimeoutSeconds` esta muito baixo para a rede atual.
- Problema de conectividade de rede entre o gateway e o leitor.

**Solucao:**
1. Na pagina `Readers`, clique no botao `Test` ao lado do leitor para executar um diagnostico de conectividade.
2. Verifique o endereco IP do leitor. Utilize a funcao de descoberta por MAC (`MAC Discovery`) para confirmar o IP atual.
3. Confirme que as credenciais do leitor estao corretas (padrao de fabrica: `admin` / `admin`).
4. Aumente o valor de `httpTimeoutSeconds` na configuracao do leitor (por exemplo, de `5` para `10`).
5. Verifique se o gateway consegue alcalcar o leitor na rede (`ping <reader-ip>`).

---

### 9.4 Logs nao aparecem

**Sintomas:**
- A pagina `Logs` esta em branco ou parou de atualizar.

**Possiveis causas:**
- A conexao SSE (Server-Sent Events) foi perdida.
- O filtro de nivel de log esta muito restritivo (por exemplo, apenas `Error` selecionado).
- O filtro de leitor esta ativo, mostrando apenas logs de um leitor especifico.
- O botao `Pause` esta ativo, interrompendo a exibicao de novos logs.
- A aba do navegador ficou inativa por muito tempo.

**Solucao:**
1. Verifique se o botao `Pause` esta ativo. Se estiver, clique em `Resume` para retomar a exibicao.
2. No dropdown de nivel, selecione `All Levels` para exibir todos os niveis de log.
3. No dropdown de leitor, selecione `All Readers` para remover o filtro por leitor.
4. Atualize a pagina no navegador (F5) para restabelecer a conexao SSE.
5. Se o problema persistir, verifique se o gateway esta em execucao e acessivel.

---

### 9.5 Descoberta por MAC nao encontra o leitor

**Sintomas:**
- Apos executar a descoberta por MAC, a mensagem "Device not found on this network" e exibida.

**Possiveis causas:**
- O leitor esta em uma sub-rede diferente da do gateway.
- O leitor ainda nao apareceu na tabela ARP do sistema operacional.
- O endereco MAC informado esta incorreto.

**Solucao:**
1. Confirme o endereco MAC do leitor (geralmente impresso em uma etiqueta no equipamento).
2. Certifique-se de que o leitor e o gateway estao no mesmo segmento de rede (mesma sub-rede).
3. Tente executar um `ping` para o endereco IP conhecido do leitor antes de realizar a descoberta por MAC. Isso forca a entrada na tabela ARP.
4. Aguarde alguns segundos e tente novamente.

> **Nota:** A descoberta por MAC depende da tabela ARP do sistema operacional. Se o leitor estiver em outra sub-rede ou VLAN, ele nao sera encontrado por esse metodo.

---

### 9.6 Configuracao nao salva

**Sintomas:**
- Uma mensagem de erro (toast) aparece apos clicar em `Save`.
- As alteracoes sao perdidas apos reiniciar o gateway.

**Possiveis causas:**
- Erros de validacao: campos obrigatorios nao preenchidos ou valores invalidos.
- O processo do gateway nao tem permissao de escrita no arquivo de configuracao.
- A alteracao de nivel de log foi feita pela pagina `System` (valida apenas em tempo de execucao) e nao foi persistida na pagina `Config`.

**Solucao:**
1. Verifique a mensagem de erro exibida no toast. Ela indicara qual campo esta com problema.
2. Certifique-se de que todos os campos obrigatorios estao preenchidos (URL do broker, ID do cliente, etc.).
3. Verifique as permissoes do arquivo de configuracao no sistema operacional.
4. Para persistir alteracoes no nivel de log, utilize a pagina `Config` e clique em `Save`, em vez de alterar apenas pela pagina `System`.

> **Atencao:** Sempre faca um backup da configuracao antes de realizar alteracoes significativas. Utilize o botao `Download Config Backup` na pagina `System`.

---

### 9.7 Sessao expira frequentemente

**Sintomas:**
- O usuario e redirecionado para a pagina de login com frequencia, mesmo durante o uso ativo.

**Possiveis causas:**
- O valor de `sessionTtlMinutes` esta configurado com um tempo muito baixo.

**Solucao:**
1. Acesse o arquivo de configuracao e localize a secao `localApi.auth`.
2. Aumente o valor de `sessionTtlMinutes`. O padrao e `480` (equivalente a 8 horas).
3. Salve a configuracao e reinicie o gateway para aplicar a alteracao.

```yaml
localApi:
  auth:
    enabled: true
    username: admin
    password: ${API_PASSWORD}
    sessionTtlMinutes: 480  # 8 horas
```

> **Nota:** Se voce compartilha o acesso ao gateway com outros usuarios, considere um valor de sessao adequado ao contexto de seguranca do ambiente.

---

### 9.8 Eventos de saúde não são publicados

**Sintomas:**
- Nenhuma mensagem nos tópicos `healthAlerts`, `networkStatus`, `antennaStatus` ou `inventoryStatus` mesmo com leitores online.
- O gateway está rodando, mas os eventos de saúde não aparecem no broker MQTT.

**Possíveis causas:**
- `events.enabled` não está configurado como `true`
- A categoria específica de evento está desabilitada (ex: `publish_health_alerts: false`)
- O leitor está offline e o monitor não consegue fazer polling
- Os intervalos de polling estão configurados muito altos

**Solução:**
1. Verifique se `events.enabled: true` está configurado no `config.yaml` ou na página Config (seção [7.7](07-configuracao.md#77-eventos-de-saúde-proativos))
2. Confirme que o checkbox da categoria desejada está marcado (ex: `publish_health_alerts`)
3. Use o botão `Test` na página [Readers](04-leitores.md#45-testar-conexao-do-leitor) para verificar se o leitor está acessível
4. Aguarde o primeiro ciclo de polling:
   - Monitor: até 1 segundo para temperatura/VSWR
   - Network: até 30 segundos
5. Verifique os logs na página [Logs](05-logs.md) para mensagens de erro relacionadas ao monitor de saúde

> **Nota:** O monitor de saúde não publica eventos durante o primeiro ciclo após inicialização do gateway para evitar flood de eventos. Aguarde o segundo ciclo de polling.

Consulte [10. Monitoramento de Saúde](10-monitoramento-saude.md) para mais detalhes sobre os eventos e seus formatos.

---

**Navegacao:**
Anterior: [Informacoes do Sistema](08-sistema.md) | Proximo: [Monitoramento de Saúde](10-monitoramento-saude.md) | [Voltar ao Indice](index.md)
