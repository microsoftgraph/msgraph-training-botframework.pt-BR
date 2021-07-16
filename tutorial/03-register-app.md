---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446824"
---
<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você criará um novo registro de Canais bot e um registro de aplicativo Web do Azure AD usando o Portal do Azure.

## <a name="create-a-bot-channels-registration"></a>Criar um registro de Canais de Bot

1. Abra um navegador e navegue até o [Portal do Azure.](https://portal.azure.com) Faça logon usando a conta associada à sua assinatura do Azure.

1. Selecione o menu superior esquerdo e selecione **Criar um recurso**.

    ![Uma captura de tela do menu do Portal do Azure](images/create-resource.png)

1. Na página **Novo,** pesquise `Azure Bot` e selecione Bot do **Azure.**

1. Na página **Bot do Azure,** selecione **Criar**.

1. Preencha os campos necessários e deixe o ponto de extremidade **mensagens em** branco. O **campo de alça bot** deve ser exclusivo. Revise as diferentes camadas de preços e selecione o que faz sentido para seu cenário. Se isso for apenas um exercício de aprendizagem, talvez você queira selecionar a opção gratuita.

1. Para **a ID do Aplicativo** Microsoft, selecione Criar nova **ID do Microsoft App.**

1. Selecione **Examinar + criar**. Depois que a validação é concluída, selecione **Criar**.

1. Depois que a implantação for concluída, selecione **Ir para o recurso**.

1. Em **Configurações**, selecione **Configuração**. Selecione o link **Gerenciar** ao lado da **ID do Microsoft App.**

1. Selecione **Novo segredo do cliente**. Adicione uma descrição e escolha uma expiração e selecione **Adicionar**.

1. Copie o valor de segredo do cliente antes de sair desta página. Você precisará dele nas etapas a seguir.

    > [!IMPORTANT]
    > Esse segredo do cliente nunca é mostrado novamente, portanto, copie-o agora. Você precisará inserir esse valor em vários locais, portanto, mantenha-o seguro.

1. Selecione **Visão** geral no menu à esquerda. Copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele nas etapas a seguir.

    ![Captura de tela da ID do aplicativo do novo registro do aplicativo](./images/aad-application-id.png)

1. Retorne à janela Registro de Canal bot no navegador e colar a ID do aplicativo no campo **ID do Aplicativo** microsoft. Colar o segredo do cliente no campo **Senha.** Clique em **OK**.

1. Na página **Registro de Canais de Bots,** selecione **Criar**.

1. Aguarde até que o registro de Canais bots seja criado. Depois de criado, retorne à home page no Portal do Azure e selecione **Serviços bot.** Selecione seu novo registro do Canal bots para exibir suas propriedades.

## <a name="create-a-web-app-registration"></a>Criar um registro de aplicativo Web

1. Volte para a home page do portal do Azure e selecione **Azure Active Directory**.

1. Selecione **Registros de aplicativo .**

1. Selecione **Novo registro**. Na página **Registrar um aplicativo**, defina os valores da seguinte forma.

    - Defina **Nome** para `Graph Calendar Bot Auth`.
    - Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI de Redirecionamento**, defina o primeiro menu suspenso para `Web` e defina o valor como `https://token.botframework.com/.auth/web/redirect`.

1. Selecione **Registrar**. Na página **Graph Calendário Bot Auth,** copie o valor da ID do Aplicativo **(cliente)** e salve-a, você precisará dele nas etapas a seguir.

1. Selecione **Certificados e segredos** sob **Gerenciar**. Selecione o botão **Novo segredo do cliente**. Insira um valor em **Descrição** e selecione uma das opções para **Expira** em e selecione **Adicionar**.

1. Copie o valor de segredo do cliente antes de sair desta página. Você precisará dele nas etapas a seguir.

1. Selecione **permissões de API** e, em seguida, selecione Adicionar uma **permissão**.

1. Selecione **Microsoft Graph**, em seguida, selecione Permissões **delegadas**.

1. Selecione as seguintes permissões e selecione **Adicionar permissões**.

    - **openid**
    - **profile**
    - **Calendars.ReadWrite**
    - **MailboxSettings.Read**

    ![Uma captura de tela de permissões configuradas](images/configured-permissions.png)

### <a name="about-permissions"></a>Sobre permissões

Considere para que cada um desses escopos de permissão permite que o bot faça e para que o bot os usará.

- **openid** e **perfil**: permite que o bot entre usuários e receba informações básicas do Azure AD no token de identidade.
- **Calendars.ReadWrite**: permite que o bot leia o calendário do usuário e adicione novos eventos ao calendário do usuário.
- **MailboxSettings.Read**: permite que o bot leia as configurações de caixa de correio do usuário. O bot usará isso para obter o fuso horário selecionado pelo usuário.
- **User.Read**: permite que o bot receba o perfil do usuário do Microsoft Graph. O bot usará isso para obter o nome do usuário.

## <a name="add-oauth-connection-to-the-bot"></a>Adicionar conexão OAuth ao bot

1. Navegue até a página bot do seu bot no Portal do Azure. Selecione **Configuração** em **Configurações**.

1. Selecione **Adicionar conexão OAuth Configurações**.

1. Preencha o formulário da seguinte maneira e selecione **Salvar**.

    - **Nome**: `GraphBotAuth`
    - **Provedor**: **Azure Active Directory v2**
    - **ID do cliente**: A ID do aplicativo do seu **registro Graph Calendar Bot Auth.**
    - **Segredo do** cliente : o segredo do cliente do seu **registro Graph Calendar Bot Auth.**
    - **URL do token Exchange**: Deixar em branco
    - **ID do locatário**: `common`
    - **Escopos**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`

1. Selecione a entrada **GraphBotAuth** em **OAuth Connection Configurações**.

1. Selecione **Conexão de Teste**. Isso abre uma nova janela ou guia do navegador para iniciar o fluxo OAuth.

1. Se necessário, entre. Revise a lista de permissões solicitadas e selecione **Aceitar**.

1. Você deve ver uma **mensagem conexão de teste com 'GraphBotAuth' Bem-sucedida.**

> [!TIP]
> Você pode selecionar o botão **Copiar Token** nesta página e colar o token para ver as declarações dentro [https://jwt.ms](https://jwt.ms) do token. Isso é útil ao solucionar problemas de erros de autenticação.
