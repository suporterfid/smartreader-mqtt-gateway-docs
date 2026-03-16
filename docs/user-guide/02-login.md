# 2. Login e Autenticacao

## Visao Geral

Quando a autenticacao esta habilitada na configuracao do gateway (`localApi.auth.enabled: true`), o acesso a interface web e protegido por um sistema de login baseado em sessao. Todos os usuarios devem se autenticar antes de acessar qualquer funcionalidade do painel.

## Tela de Login

Ao acessar a interface web pela primeira vez, ou quando a sessao expirar, voce sera redirecionado automaticamente para a tela de login.

![Tela de login do gateway](screenshots/01-login-tela.png)

A tela de login apresenta dois campos de entrada e um botao de acao:

- **Username** -- campo para inserir o nome de usuario configurado
- **Password** -- campo para inserir a senha correspondente
- **`Sign In`** -- botao para enviar as credenciais e iniciar a sessao

## Procedimento de Login

1. Abra o navegador e acesse o endereco do gateway (por exemplo, `http://192.168.1.10:8080`).
2. Na tela de login, insira seu nome de usuario no campo **Username**.
3. Insira sua senha no campo **Password**.
4. Clique no botao `Sign In` ou pressione **Enter** para autenticar.
5. Apos a autenticacao bem-sucedida, voce sera redirecionado para o **Painel Principal (Dashboard)**.

> **Nota:** As credenciais de acesso sao definidas no arquivo de configuracao do gateway (`config.yaml`), nas propriedades `localApi.auth.username` e `localApi.auth.password`.

## Tratamento de Erros

Caso as credenciais informadas estejam incorretas, uma mensagem de erro sera exibida na tela de login indicando que o nome de usuario ou a senha sao invalidos.

![Mensagem de erro na tela de login](screenshots/02-login-erro.png)

- Verifique se o nome de usuario e a senha estao corretos.
- Confirme se as credenciais correspondem ao que esta configurado no arquivo `config.yaml`.
- Caso tenha esquecido a senha, consulte o administrador do sistema ou verifique diretamente o arquivo de configuracao no servidor.

> **Atencao:** Apos multiplas tentativas de login sem sucesso, nao ha bloqueio automatico de conta. No entanto, recomenda-se nao compartilhar credenciais e manter senhas seguras.

## Expiracao de Sessao

A sessao do usuario possui um tempo de vida configuravel. Por padrao, a sessao expira apos **8 horas** de inatividade. Esse valor pode ser ajustado por meio da propriedade `sessionTtlMinutes` na configuracao do gateway.

Quando a sessao expirar:

1. A proxima requisicao a interface web detectara que a sessao nao e mais valida.
2. O usuario sera redirecionado automaticamente para a tela de login.
3. Apos autenticar novamente, o usuario retornara ao painel principal.

> **Nota:** Alterar o valor de `sessionTtlMinutes` requer reiniciar o gateway para que a nova configuracao seja aplicada.

## Logout

O botao de logout esta localizado no **canto superior direito** do cabecalho da interface web. Para encerrar sua sessao:

1. Clique no botao `Logout` no canto superior direito da pagina.
2. A sessao sera encerrada imediatamente.
3. Voce sera redirecionado para a tela de login.

> **Nota:** Fechar o navegador sem clicar em `Logout` nao encerra a sessao. Ela permanecera ativa no servidor ate atingir o tempo de expiracao configurado.

## Acesso sem Autenticacao

Quando a autenticacao esta desabilitada (`localApi.auth.enabled: false`), a interface web pode ser acessada diretamente sem necessidade de login. Nesse cenario:

- A tela de login nao e exibida.
- Todas as funcionalidades do painel ficam disponiveis imediatamente ao acessar o endereco do gateway.
- O botao de `Logout` nao e exibido no cabecalho.

> **Atencao:** Desabilitar a autenticacao e recomendado apenas em ambientes de desenvolvimento ou redes internas seguras. Em ambientes de producao, mantenha a autenticacao habilitada para proteger o acesso ao gateway.

---

Anterior: [Introducao](01-introducao.md) | Proximo: [Painel Principal (Dashboard)](03-dashboard.md)
