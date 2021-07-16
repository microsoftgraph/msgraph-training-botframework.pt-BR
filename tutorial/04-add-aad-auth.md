---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446789"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="11a56-101">Neste exercício, você usará o **OAuthPrompt** do Bot Framework para implementar a autenticação no bot e adquirir tokens de acesso para chamar a API do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="11a56-101">In this exercise you will use the Bot Framework's **OAuthPrompt** to implement authentication in the bot, and acquire access tokens for calling the Microsoft Graph API.</span></span>

1. <span data-ttu-id="11a56-102">Abra **./appsettings.jse** faça as seguintes alterações.</span><span class="sxs-lookup"><span data-stu-id="11a56-102">Open **./appsettings.json** and make the following changes.</span></span>

    - <span data-ttu-id="11a56-103">Altere o valor para `MicrosoftAppId` a ID do aplicativo do seu **Graph de bot** de calendário.</span><span class="sxs-lookup"><span data-stu-id="11a56-103">Change the value of `MicrosoftAppId` to the application ID of your **Graph Calendar Bot** app registration.</span></span>
    - <span data-ttu-id="11a56-104">Altere o valor de `MicrosoftAppPassword` para seu segredo de cliente bot de calendário **Graph** calendário.</span><span class="sxs-lookup"><span data-stu-id="11a56-104">Change the value of `MicrosoftAppPassword` to your **Graph Calendar Bot** client secret.</span></span>
    - <span data-ttu-id="11a56-105">Adicione um valor nomeado `ConnectionName` com um valor de `GraphBotAuth` .</span><span class="sxs-lookup"><span data-stu-id="11a56-105">Add a value named `ConnectionName` with a value of `GraphBotAuth`.</span></span>

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > <span data-ttu-id="11a56-106">Se você usou um valor diferente do nome da sua entrada no `GraphBotAuth` **OAuth Connection Configurações** no Portal do Azure, use esse valor para a `ConnectionName` entrada.</span><span class="sxs-lookup"><span data-stu-id="11a56-106">If you used a value other than `GraphBotAuth` for the name of your entry in **OAuth Connection Settings** in the Azure Portal, use that value for the `ConnectionName` entry.</span></span>

## <a name="implement-dialogs"></a><span data-ttu-id="11a56-107">Implementar caixas de diálogo</span><span class="sxs-lookup"><span data-stu-id="11a56-107">Implement dialogs</span></span>

1. <span data-ttu-id="11a56-108">Crie um novo diretório na raiz do projeto denominado **Dialogs**.</span><span class="sxs-lookup"><span data-stu-id="11a56-108">Create a new directory in the root of the project named **Dialogs**.</span></span> <span data-ttu-id="11a56-109">Crie um novo arquivo no **diretório ./Dialogs** chamado **LogoutDialog.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="11a56-109">Create a new file in the **./Dialogs** directory named **LogoutDialog.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    <span data-ttu-id="11a56-110">Esta caixa de diálogo fornece uma classe base para todas as outras caixas de diálogo no bot a serem derivadas.</span><span class="sxs-lookup"><span data-stu-id="11a56-110">This dialog provides a base class for all of the other dialogs in the bot to derive from.</span></span> <span data-ttu-id="11a56-111">Isso permite que o usuário faça logoff independentemente de onde ele está nas caixas de diálogo do bot.</span><span class="sxs-lookup"><span data-stu-id="11a56-111">This allows the user to log out no matter where they are in the bot's dialogs.</span></span>

1. <span data-ttu-id="11a56-112">Crie um novo arquivo no **diretório ./Dialogs** chamado **MainDialog.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="11a56-112">Create a new file in the **./Dialogs** directory named **MainDialog.cs** and add the following code.</span></span>

    ```csharp
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;

    namespace CalendarBot.Dialogs
    {
        public class MainDialog : LogoutDialog
        {
            const string NO_PROMPT = "no-prompt";
            protected readonly ILogger _logger;

            public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger)
                : base(nameof(MainDialog), configuration["ConnectionName"])
            {
                _logger = logger;

                // OAuthPrompt dialog handles the authentication and token
                // acquisition
                AddDialog(new OAuthPrompt(
                    nameof(OAuthPrompt),
                    new OAuthPromptSettings
                    {
                        ConnectionName = ConnectionName,
                        Text = "Please login",
                        Title = "Login",
                        Timeout = 300000, // User has 5 minutes to login
                    }));

                AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));

                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    LoginPromptStepAsync,
                    ProcessLoginStepAsync,
                    PromptUserStepAsync,
                    CommandStepAsync,
                    ProcessStepAsync,
                    ReturnToPromptStepAsync
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> LoginPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessLoginStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                // Get the token from the previous step. If it's there, login was successful
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;
                    if (!string.IsNullOrEmpty(tokenResponse?.Token))
                    {
                        await stepContext.Context.SendActivityAsync(
                            MessageFactory.Text("You are now logged in."), cancellationToken);
                        return await stepContext.NextAsync(null, cancellationToken);
                    }
                }

                await stepContext.Context.SendActivityAsync(
                    MessageFactory.Text("Login was not successful please try again."), cancellationToken);
                return await stepContext.EndDialogAsync();
            }

            private async Task<DialogTurnResult> PromptUserStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                var options = new PromptOptions
                {
                    Prompt = MessageFactory.Text("Please choose an option below"),
                    Choices = new List<Choice> {
                        new Choice { Value = "Show token" },
                        new Choice { Value = "Show me" },
                        new Choice { Value = "Show calendar" },
                        new Choice { Value = "Add event" },
                        new Choice { Value = "Log out" },
                    }
                };

                return await stepContext.PromptAsync(
                    nameof(ChoicePrompt),
                    options,
                    cancellationToken);
            }

            private async Task<DialogTurnResult> CommandStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Save the command the user entered so we can get it back after
                // the OAuthPrompt completes
                var foundChoice = stepContext.Result as FoundChoice;
                // Result could be a FoundChoice (if user selected a choice button)
                // or a string (if user just typed something)
                stepContext.Values["command"] = foundChoice?.Value ?? stepContext.Result;

                // There is no reason to store the token locally in the bot because we can always just call
                // the OAuth prompt to get the token or get a new token if needed. The prompt completes silently
                // if the user is already signed in.
                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;

                    // If we have the token use the user is authenticated so we may use it to make API calls.
                    if (tokenResponse?.Token != null)
                    {
                        var command = ((string)stepContext.Values["command"] ?? string.Empty).ToLowerInvariant();

                        if (command.StartsWith("show token"))
                        {
                            // Show the user's token - for testing and troubleshooting
                            // Generally production apps should not display access tokens
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text($"Your token is: {tokenResponse.Token}"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show me"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show calendar"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("add event"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I'm sorry, I didn't understand. Please try again."),
                                cancellationToken);
                        }
                    }
                }
                else
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("We couldn't log you in. Please try again later."),
                        cancellationToken);
                    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
                }

                // Go to the next step
                return await stepContext.NextAsync(cancellationToken: cancellationToken);
            }

            private async Task<DialogTurnResult> ReturnToPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Restart the dialog, but skip the initial login prompt
                return await stepContext.ReplaceDialogAsync(InitialDialogId, NO_PROMPT, cancellationToken);
            }
        }
    }
    ```

    <span data-ttu-id="11a56-113">Tome um momento para revisar esse código.</span><span class="sxs-lookup"><span data-stu-id="11a56-113">Take a moment to review this code.</span></span>

    - <span data-ttu-id="11a56-114">No construtor, ele configura um [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) com um conjunto de etapas que ocorrem em ordem.</span><span class="sxs-lookup"><span data-stu-id="11a56-114">In the constructor, it sets up a [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) with a set of steps that occur in order.</span></span>
        - <span data-ttu-id="11a56-115">Ele `LoginPromptStepAsync` envia um **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="11a56-115">In `LoginPromptStepAsync` it sends an **OAuthPrompt**.</span></span> <span data-ttu-id="11a56-116">Se o usuário não estiver conectado, isso enviará um prompt de interface do usuário para o usuário.</span><span class="sxs-lookup"><span data-stu-id="11a56-116">If the user isn't logged in, this will send a UI prompt to the user.</span></span>
        - <span data-ttu-id="11a56-117">Nele, `ProcessLoginStepAsync` verifica se o logon foi bem-sucedido e envia uma confirmação.</span><span class="sxs-lookup"><span data-stu-id="11a56-117">In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.</span></span>
        - <span data-ttu-id="11a56-118">Ele `PromptUserStepAsync` envia um **ChoicePrompt** com os comandos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="11a56-118">In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.</span></span>
        - <span data-ttu-id="11a56-119">Nele, salva a escolha do usuário e, em `CommandStepAsync` seguida, reende um **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="11a56-119">In `CommandStepAsync` it saves the user's choice, then resends an **OAuthPrompt**.</span></span>
        - <span data-ttu-id="11a56-120">In `ProcessStepAsync` it takes action based on the command received.</span><span class="sxs-lookup"><span data-stu-id="11a56-120">In `ProcessStepAsync` it takes action based on the command received.</span></span>
        - <span data-ttu-id="11a56-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span><span class="sxs-lookup"><span data-stu-id="11a56-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span></span>

## <a name="update-calendarbot"></a><span data-ttu-id="11a56-122">Atualizar CalendarBot</span><span class="sxs-lookup"><span data-stu-id="11a56-122">Update CalendarBot</span></span>

<span data-ttu-id="11a56-123">A próxima etapa é atualizar **CalendarBot** para usar essas novas caixas de diálogo.</span><span class="sxs-lookup"><span data-stu-id="11a56-123">The next step is to update **CalendarBot** to use these new dialogs.</span></span>

1. <span data-ttu-id="11a56-124">Abra **./Bots/CalendarBot.cs** e substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="11a56-124">Open **./Bots/CalendarBot.cs** and replace its entire contents with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    <span data-ttu-id="11a56-125">Aqui está um breve resumo das alterações.</span><span class="sxs-lookup"><span data-stu-id="11a56-125">Here's a brief summary of the changes.</span></span>

    - <span data-ttu-id="11a56-126">Alterou a **classe CalendarBot** para ser uma classe de modelo, recebendo uma **caixa de diálogo**.</span><span class="sxs-lookup"><span data-stu-id="11a56-126">Changed the **CalendarBot** class to be a template class, receiving a **Dialog**.</span></span>
    - <span data-ttu-id="11a56-127">Alterou a **classe CalendarBot** para estender **o TeamsActivityHandler**, permitindo que ele entre Microsoft Teams.</span><span class="sxs-lookup"><span data-stu-id="11a56-127">Changed the **CalendarBot** class to extend **TeamsActivityHandler**, allowing it to sign in in Microsoft Teams.</span></span>
    - <span data-ttu-id="11a56-128">Adicionadas substituições de método adicionais para habilitar a autenticação.</span><span class="sxs-lookup"><span data-stu-id="11a56-128">Added additional method overrides to enable authentication.</span></span>

## <a name="update-startupcs"></a><span data-ttu-id="11a56-129">Atualizar Startup.cs</span><span class="sxs-lookup"><span data-stu-id="11a56-129">Update Startup.cs</span></span>

<span data-ttu-id="11a56-130">A etapa final é atualizar o método para adicionar os serviços `ConfigureServices` necessários para autenticação e a nova caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="11a56-130">The final step is to update the `ConfigureServices` method to add the services needed for authentication and the new dialog.</span></span>

1. <span data-ttu-id="11a56-131">Abra **./Startup.cs** e remova `services.AddTransient<IBot, Bots.CalendarBot>();` a linha do `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="11a56-131">Open **./Startup.cs** and remove the `services.AddTransient<IBot, Bots.CalendarBot>();` line from the `ConfigureServices` method.</span></span>

1. <span data-ttu-id="11a56-132">Insira o código a seguir no final do `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="11a56-132">Insert the following code at the end of the `ConfigureServices` method.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a><span data-ttu-id="11a56-133">Autenticação de teste</span><span class="sxs-lookup"><span data-stu-id="11a56-133">Test authentication</span></span>

1. <span data-ttu-id="11a56-134">Salve todas as alterações e inicie o bot com `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="11a56-134">Save all of your changes and start the bot with `dotnet run`.</span></span>

1. <span data-ttu-id="11a56-135">Abra o Bot Framework Emulator.</span><span class="sxs-lookup"><span data-stu-id="11a56-135">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="11a56-136">Selecione o ícone de engrenagem &#9881; à esquerda inferior.</span><span class="sxs-lookup"><span data-stu-id="11a56-136">Select the gear icon &#9881; on the bottom left.</span></span>

1. <span data-ttu-id="11a56-137">Insira o caminho local para a instalação do ngrok e habilita o **ngrok Bypass** para endereços locais e Execute **o ngrok** quando o Emulator iniciar opções.</span><span class="sxs-lookup"><span data-stu-id="11a56-137">Enter the local path to your installation of ngrok, and enable the **Bypass ngrok for local addresses** and **Run ngrok when the Emulator starts up** options.</span></span> <span data-ttu-id="11a56-138">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="11a56-138">Select **Save**.</span></span>

1. <span data-ttu-id="11a56-139">Selecione o menu **Arquivo** e, em **seguida, Nova Configuração de Bot...**.</span><span class="sxs-lookup"><span data-stu-id="11a56-139">Select the **File** menu, then **New Bot Configuration...**.</span></span>

1. <span data-ttu-id="11a56-140">Preencha os campos da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="11a56-140">Fill in the fields as follows.</span></span>

    - <span data-ttu-id="11a56-141">**Nome do bot:**`CalendarBot`</span><span class="sxs-lookup"><span data-stu-id="11a56-141">**Bot name:** `CalendarBot`</span></span>
    - <span data-ttu-id="11a56-142">**URL do ponto de extremidade:**`https://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="11a56-142">**Endpoint URL:** `https://localhost:3978/api/messages`</span></span>
    - <span data-ttu-id="11a56-143">**ID do aplicativo microsoft:** a ID do aplicativo do seu **Graph de bot** de calendário</span><span class="sxs-lookup"><span data-stu-id="11a56-143">**Microsoft App ID:** the application ID of your **Graph Calendar Bot** app registration</span></span>
    - <span data-ttu-id="11a56-144">**Senha do Aplicativo microsoft:** seu **Graph de cliente bot** de calendário</span><span class="sxs-lookup"><span data-stu-id="11a56-144">**Microsoft App password:** your **Graph Calendar Bot** client secret</span></span>
    - <span data-ttu-id="11a56-145">**Criptografar chaves armazenadas em sua configuração de bot:** Habilitado</span><span class="sxs-lookup"><span data-stu-id="11a56-145">**Encrypt keys stored in your bot configuration:** Enabled</span></span>

    ![Uma captura de tela da caixa de diálogo Nova configuração de bot](images/new-bot-config.png)

1. <span data-ttu-id="11a56-147">Selecione **Salvar e conectar**.</span><span class="sxs-lookup"><span data-stu-id="11a56-147">Select **Save and connect**.</span></span> <span data-ttu-id="11a56-148">Depois que o emulador se conectar, você deverá ver `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span><span class="sxs-lookup"><span data-stu-id="11a56-148">After the emulator connects, you should see `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span></span>

1. <span data-ttu-id="11a56-149">Digite algum texto e envie-o para o bot.</span><span class="sxs-lookup"><span data-stu-id="11a56-149">Type some text and send it to the bot.</span></span> <span data-ttu-id="11a56-150">O bot responde com um prompt de logon.</span><span class="sxs-lookup"><span data-stu-id="11a56-150">The bot responds with a login prompt.</span></span>

1. <span data-ttu-id="11a56-151">Selecione o **botão Logon.**</span><span class="sxs-lookup"><span data-stu-id="11a56-151">Select the **Login** button.</span></span> <span data-ttu-id="11a56-152">O emulador solicita que você confirme a URL que começa com `oauthlink://https://token.botframeworkcom` .</span><span class="sxs-lookup"><span data-stu-id="11a56-152">The emulator prompts you to confirm the URL that starts with `oauthlink://https://token.botframeworkcom`.</span></span> <span data-ttu-id="11a56-153">Selecione **Confirmar** para continuar.</span><span class="sxs-lookup"><span data-stu-id="11a56-153">Select **Confirm** to continue.</span></span>

1. <span data-ttu-id="11a56-154">Na janela pop-up, faça logon com sua Microsoft 365 conta.</span><span class="sxs-lookup"><span data-stu-id="11a56-154">In the pop-up window, login with your Microsoft 365 account.</span></span> <span data-ttu-id="11a56-155">Revise as permissões solicitadas e aceite.</span><span class="sxs-lookup"><span data-stu-id="11a56-155">Review the requested permissions and accept.</span></span>

1. <span data-ttu-id="11a56-156">Depois que a autenticação e o consentimento são concluídos, a janela pop-up fornece um código de validação.</span><span class="sxs-lookup"><span data-stu-id="11a56-156">Once authentication and consent are complete, the pop-up window provides a validation code.</span></span> <span data-ttu-id="11a56-157">Copie o código e feche a janela.</span><span class="sxs-lookup"><span data-stu-id="11a56-157">Copy the code and close the window.</span></span>

    ![Uma captura de tela do código Bot Framework Emulator validação](images/validation-code.png)

1. <span data-ttu-id="11a56-159">Insira o código de validação na janela de chat para concluir o logon.</span><span class="sxs-lookup"><span data-stu-id="11a56-159">Enter the validation code in the chat window to complete the login.</span></span>

    ![Uma captura de tela da conversa de logon com o bot de exemplo](images/bot-login.png)

1. <span data-ttu-id="11a56-161">Se você selecionar o **botão Mostrar token** (ou tipo ), o bot `show token` exibirá o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="11a56-161">If you select the **Show token** button (or type `show token`), the bot displays the access token.</span></span> <span data-ttu-id="11a56-162">O **botão Sair** (ou digitar ) fará `log out` logoff.</span><span class="sxs-lookup"><span data-stu-id="11a56-162">The **Log out** button (or typing `log out`) will log you out.</span></span>

> [!TIP]
> <span data-ttu-id="11a56-163">Você pode receber a seguinte mensagem de erro no Bot Framework Emulator ao iniciar uma conversa com o bot.</span><span class="sxs-lookup"><span data-stu-id="11a56-163">You may receive the following error message in the Bot Framework Emulator when starting a conversation with the bot.</span></span>
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> <span data-ttu-id="11a56-164">Se isso acontecer, certifique-se de habilitar o **ngrok Executar** quando o Emulator iniciar nas configurações do emulador e reiniciar o emulador.</span><span class="sxs-lookup"><span data-stu-id="11a56-164">If this happens, be sure to enable the **Run ngrok when the Emulator starts up** option in the emulator settings and restart the emulator.</span></span>
