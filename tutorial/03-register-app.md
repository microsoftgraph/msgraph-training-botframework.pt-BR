---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446824"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="02a2f-101">Neste exercício, você criará um novo registro de Canais bot e um registro de aplicativo Web do Azure AD usando o Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="02a2f-101">In this exercise, you will create a new Bot Channels registration and an Azure AD web application registration using the Azure Portal.</span></span>

## <a name="create-a-bot-channels-registration"></a><span data-ttu-id="02a2f-102">Criar um registro de Canais de Bot</span><span class="sxs-lookup"><span data-stu-id="02a2f-102">Create a Bot Channels registration</span></span>

1. <span data-ttu-id="02a2f-103">Abra um navegador e navegue até o [Portal do Azure.](https://portal.azure.com)</span><span class="sxs-lookup"><span data-stu-id="02a2f-103">Open a browser and navigate to the [Azure Portal](https://portal.azure.com).</span></span> <span data-ttu-id="02a2f-104">Faça logon usando a conta associada à sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="02a2f-104">Login using the account associated with your Azure subscription.</span></span>

1. <span data-ttu-id="02a2f-105">Selecione o menu superior esquerdo e selecione **Criar um recurso**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-105">Select the upper-left menu, then select **Create a resource**.</span></span>

    ![Uma captura de tela do menu do Portal do Azure](images/create-resource.png)

1. <span data-ttu-id="02a2f-107">Na página **Novo,** pesquise `Azure Bot` e selecione Bot do **Azure.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-107">On the **New** page, search for `Azure Bot` and select **Azure Bot**.</span></span>

1. <span data-ttu-id="02a2f-108">Na página **Bot do Azure,** selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-108">On the **Azure Bot** page, select **Create**.</span></span>

1. <span data-ttu-id="02a2f-109">Preencha os campos necessários e deixe o ponto de extremidade **mensagens em** branco.</span><span class="sxs-lookup"><span data-stu-id="02a2f-109">Fill in the required fields, and leave **Messaging endpoint** blank.</span></span> <span data-ttu-id="02a2f-110">O **campo de alça bot** deve ser exclusivo.</span><span class="sxs-lookup"><span data-stu-id="02a2f-110">The **Bot handle** field must be unique.</span></span> <span data-ttu-id="02a2f-111">Revise as diferentes camadas de preços e selecione o que faz sentido para seu cenário.</span><span class="sxs-lookup"><span data-stu-id="02a2f-111">Be sure to review the different pricing tiers and select what makes sense for your scenario.</span></span> <span data-ttu-id="02a2f-112">Se isso for apenas um exercício de aprendizagem, talvez você queira selecionar a opção gratuita.</span><span class="sxs-lookup"><span data-stu-id="02a2f-112">If this is just a learning exercise, you may want to select the free option.</span></span>

1. <span data-ttu-id="02a2f-113">Para **a ID do Aplicativo** Microsoft, selecione Criar nova **ID do Microsoft App.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-113">For **Microsoft App ID**, select **Create new Microsoft App ID**.</span></span>

1. <span data-ttu-id="02a2f-114">Selecione **Examinar + criar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-114">Select **Review + create**.</span></span> <span data-ttu-id="02a2f-115">Depois que a validação é concluída, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-115">Once validation completes, select **Create**.</span></span>

1. <span data-ttu-id="02a2f-116">Depois que a implantação for concluída, selecione **Ir para o recurso**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-116">Once deployment has finished, select **Go to resource**.</span></span>

1. <span data-ttu-id="02a2f-117">Em **Configurações**, selecione **Configuração**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-117">Under **Settings**, select **Configuration**.</span></span> <span data-ttu-id="02a2f-118">Selecione o link **Gerenciar** ao lado da **ID do Microsoft App.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-118">Select the **Manage** link next to **Microsoft App ID**.</span></span>

1. <span data-ttu-id="02a2f-119">Selecione **Novo segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-119">Select **New client secret**.</span></span> <span data-ttu-id="02a2f-120">Adicione uma descrição e escolha uma expiração e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-120">Add a description and choose an expiration, then select **Add**.</span></span>

1. <span data-ttu-id="02a2f-121">Copie o valor de segredo do cliente antes de sair desta página.</span><span class="sxs-lookup"><span data-stu-id="02a2f-121">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="02a2f-122">Você precisará dele nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="02a2f-122">You will need it in the following steps.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="02a2f-123">Esse segredo do cliente nunca é mostrado novamente, portanto, copie-o agora.</span><span class="sxs-lookup"><span data-stu-id="02a2f-123">This client secret is never shown again, so make sure you copy it now.</span></span> <span data-ttu-id="02a2f-124">Você precisará inserir esse valor em vários locais, portanto, mantenha-o seguro.</span><span class="sxs-lookup"><span data-stu-id="02a2f-124">You will need to enter this value in multiple places so keep it safe.</span></span>

1. <span data-ttu-id="02a2f-125">Selecione **Visão** geral no menu à esquerda.</span><span class="sxs-lookup"><span data-stu-id="02a2f-125">Select **Overview** in the left-hand menu.</span></span> <span data-ttu-id="02a2f-126">Copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="02a2f-126">Copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

    ![Captura de tela da ID do aplicativo do novo registro do aplicativo](./images/aad-application-id.png)

1. <span data-ttu-id="02a2f-128">Retorne à janela Registro de Canal bot no navegador e colar a ID do aplicativo no campo **ID do Aplicativo** microsoft.</span><span class="sxs-lookup"><span data-stu-id="02a2f-128">Return to the Bot Channel Registration window in your browser, and paste the application ID into the **Microsoft App ID** field.</span></span> <span data-ttu-id="02a2f-129">Colar o segredo do cliente no campo **Senha.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-129">Paste your client secret into the **Password** field.</span></span> <span data-ttu-id="02a2f-130">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-130">Select **OK**.</span></span>

1. <span data-ttu-id="02a2f-131">Na página **Registro de Canais de Bots,** selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-131">On the **Bots Channels Registration** page, select **Create**.</span></span>

1. <span data-ttu-id="02a2f-132">Aguarde até que o registro de Canais bots seja criado.</span><span class="sxs-lookup"><span data-stu-id="02a2f-132">Wait for the Bot Channels registration to be created.</span></span> <span data-ttu-id="02a2f-133">Depois de criado, retorne à home page no Portal do Azure e selecione **Serviços bot.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-133">Once created, return to the Home page in the Azure Portal, then select **Bot Services**.</span></span> <span data-ttu-id="02a2f-134">Selecione seu novo registro do Canal bots para exibir suas propriedades.</span><span class="sxs-lookup"><span data-stu-id="02a2f-134">Select your new Bots Channel registration to view its properties.</span></span>

## <a name="create-a-web-app-registration"></a><span data-ttu-id="02a2f-135">Criar um registro de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="02a2f-135">Create a web app registration</span></span>

1. <span data-ttu-id="02a2f-136">Volte para a home page do portal do Azure e selecione **Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-136">Return to the home page of the Azure portal, then select **Azure Active Directory**.</span></span>

1. <span data-ttu-id="02a2f-137">Selecione **Registros de aplicativo .**</span><span class="sxs-lookup"><span data-stu-id="02a2f-137">Select **App registrations**.</span></span>

1. <span data-ttu-id="02a2f-138">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-138">Select **New registration**.</span></span> <span data-ttu-id="02a2f-139">Na página **Registrar um aplicativo**, defina os valores da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="02a2f-139">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="02a2f-140">Defina **Nome** para `Graph Calendar Bot Auth`.</span><span class="sxs-lookup"><span data-stu-id="02a2f-140">Set **Name** to `Graph Calendar Bot Auth`.</span></span>
    - <span data-ttu-id="02a2f-141">Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-141">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="02a2f-142">Em **URI de Redirecionamento**, defina o primeiro menu suspenso para `Web` e defina o valor como `https://token.botframework.com/.auth/web/redirect`.</span><span class="sxs-lookup"><span data-stu-id="02a2f-142">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `https://token.botframework.com/.auth/web/redirect`.</span></span>

1. <span data-ttu-id="02a2f-143">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-143">Select **Register**.</span></span> <span data-ttu-id="02a2f-144">Na página **Graph Calendário Bot Auth,** copie o valor da ID do Aplicativo **(cliente)** e salve-a, você precisará dele nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="02a2f-144">On the **Graph Calendar Bot Auth** page, copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

1. <span data-ttu-id="02a2f-145">Selecione **Certificados e segredos** sob **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-145">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="02a2f-146">Selecione o botão **Novo segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-146">Select the **New client secret** button.</span></span> <span data-ttu-id="02a2f-147">Insira um valor em **Descrição** e selecione uma das opções para **Expira** em e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-147">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="02a2f-148">Copie o valor de segredo do cliente antes de sair desta página.</span><span class="sxs-lookup"><span data-stu-id="02a2f-148">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="02a2f-149">Você precisará dele nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="02a2f-149">You will need it in the following steps.</span></span>

1. <span data-ttu-id="02a2f-150">Selecione **permissões de API** e, em seguida, selecione Adicionar uma **permissão**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-150">Select **API permissions**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="02a2f-151">Selecione **Microsoft Graph**, em seguida, selecione Permissões **delegadas**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-151">Select **Microsoft Graph**, then select **Delegated permissions**.</span></span>

1. <span data-ttu-id="02a2f-152">Selecione as seguintes permissões e selecione **Adicionar permissões**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-152">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="02a2f-153">**openid**</span><span class="sxs-lookup"><span data-stu-id="02a2f-153">**openid**</span></span>
    - <span data-ttu-id="02a2f-154">**profile**</span><span class="sxs-lookup"><span data-stu-id="02a2f-154">**profile**</span></span>
    - <span data-ttu-id="02a2f-155">**Calendars.ReadWrite**</span><span class="sxs-lookup"><span data-stu-id="02a2f-155">**Calendars.ReadWrite**</span></span>
    - <span data-ttu-id="02a2f-156">**MailboxSettings.Read**</span><span class="sxs-lookup"><span data-stu-id="02a2f-156">**MailboxSettings.Read**</span></span>

    ![Uma captura de tela de permissões configuradas](images/configured-permissions.png)

### <a name="about-permissions"></a><span data-ttu-id="02a2f-158">Sobre permissões</span><span class="sxs-lookup"><span data-stu-id="02a2f-158">About permissions</span></span>

<span data-ttu-id="02a2f-159">Considere para que cada um desses escopos de permissão permite que o bot faça e para que o bot os usará.</span><span class="sxs-lookup"><span data-stu-id="02a2f-159">Consider what each of those permission scopes allows the bot to do, and what the bot will use them for.</span></span>

- <span data-ttu-id="02a2f-160">**openid** e **perfil**: permite que o bot entre usuários e receba informações básicas do Azure AD no token de identidade.</span><span class="sxs-lookup"><span data-stu-id="02a2f-160">**openid** and **profile**: allows the bot to sign users in and get basic information from Azure AD in the identity token.</span></span>
- <span data-ttu-id="02a2f-161">**Calendars.ReadWrite**: permite que o bot leia o calendário do usuário e adicione novos eventos ao calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="02a2f-161">**Calendars.ReadWrite**: allows the bot to read the user's calendar and to add new events to the user's calendar.</span></span>
- <span data-ttu-id="02a2f-162">**MailboxSettings.Read**: permite que o bot leia as configurações de caixa de correio do usuário.</span><span class="sxs-lookup"><span data-stu-id="02a2f-162">**MailboxSettings.Read**: allows the bot to read the user's mailbox settings.</span></span> <span data-ttu-id="02a2f-163">O bot usará isso para obter o fuso horário selecionado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="02a2f-163">The bot will use this to get the user's selected time zone.</span></span>
- <span data-ttu-id="02a2f-164">**User.Read**: permite que o bot receba o perfil do usuário do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="02a2f-164">**User.Read**: allows the bot to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="02a2f-165">O bot usará isso para obter o nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="02a2f-165">The bot will use this to get the user's name.</span></span>

## <a name="add-oauth-connection-to-the-bot"></a><span data-ttu-id="02a2f-166">Adicionar conexão OAuth ao bot</span><span class="sxs-lookup"><span data-stu-id="02a2f-166">Add OAuth connection to the bot</span></span>

1. <span data-ttu-id="02a2f-167">Navegue até a página bot do seu bot no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="02a2f-167">Navigate to your bot's Azure Bot page in the Azure Portal.</span></span> <span data-ttu-id="02a2f-168">Selecione **Configuração** em **Configurações**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-168">Select **Configuration** under **Settings**.</span></span>

1. <span data-ttu-id="02a2f-169">Selecione **Adicionar conexão OAuth Configurações**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-169">Select **Add OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="02a2f-170">Preencha o formulário da seguinte maneira e selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-170">Fill in the form as follows, then select **Save**.</span></span>

    - <span data-ttu-id="02a2f-171">**Nome**: `GraphBotAuth`</span><span class="sxs-lookup"><span data-stu-id="02a2f-171">**Name**: `GraphBotAuth`</span></span>
    - <span data-ttu-id="02a2f-172">**Provedor**: **Azure Active Directory v2**</span><span class="sxs-lookup"><span data-stu-id="02a2f-172">**Provider**: **Azure Active Directory v2**</span></span>
    - <span data-ttu-id="02a2f-173">**ID do cliente**: A ID do aplicativo do seu **registro Graph Calendar Bot Auth.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-173">**Client id**: The application ID of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="02a2f-174">**Segredo do** cliente : o segredo do cliente do seu **registro Graph Calendar Bot Auth.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-174">**Client secret**: The client secret of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="02a2f-175">**URL do token Exchange**: Deixar em branco</span><span class="sxs-lookup"><span data-stu-id="02a2f-175">**Token Exchange URL**: Leave blank</span></span>
    - <span data-ttu-id="02a2f-176">**ID do locatário**: `common`</span><span class="sxs-lookup"><span data-stu-id="02a2f-176">**Tenant ID**: `common`</span></span>
    - <span data-ttu-id="02a2f-177">**Escopos**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span><span class="sxs-lookup"><span data-stu-id="02a2f-177">**Scopes**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span></span>

1. <span data-ttu-id="02a2f-178">Selecione a entrada **GraphBotAuth** em **OAuth Connection Configurações**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-178">Select the **GraphBotAuth** entry under **OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="02a2f-179">Selecione **Conexão de Teste**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-179">Select **Test Connection**.</span></span> <span data-ttu-id="02a2f-180">Isso abre uma nova janela ou guia do navegador para iniciar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="02a2f-180">This opens a new browser window or tab to start the OAuth flow.</span></span>

1. <span data-ttu-id="02a2f-181">Se necessário, entre.</span><span class="sxs-lookup"><span data-stu-id="02a2f-181">If necessary, sign in.</span></span> <span data-ttu-id="02a2f-182">Revise a lista de permissões solicitadas e selecione **Aceitar**.</span><span class="sxs-lookup"><span data-stu-id="02a2f-182">Review the list of requested permissions, then select **Accept**.</span></span>

1. <span data-ttu-id="02a2f-183">Você deve ver uma **mensagem conexão de teste com 'GraphBotAuth' Bem-sucedida.**</span><span class="sxs-lookup"><span data-stu-id="02a2f-183">You should see a **Test Connection to 'GraphBotAuth' Succeeded** message.</span></span>

> [!TIP]
> <span data-ttu-id="02a2f-184">Você pode selecionar o botão **Copiar Token** nesta página e colar o token para ver as declarações dentro [https://jwt.ms](https://jwt.ms) do token.</span><span class="sxs-lookup"><span data-stu-id="02a2f-184">You can select the **Copy Token** button on this page, and paste the token into [https://jwt.ms](https://jwt.ms) to see the claims inside the token.</span></span> <span data-ttu-id="02a2f-185">Isso é útil ao solucionar problemas de erros de autenticação.</span><span class="sxs-lookup"><span data-stu-id="02a2f-185">This is useful when troubleshooting authentication errors.</span></span>
