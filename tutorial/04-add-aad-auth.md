---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446789"
---
<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você usará o **OAuthPrompt** do Bot Framework para implementar a autenticação no bot e adquirir tokens de acesso para chamar a API do Microsoft Graph.

1. Abra **./appsettings.jse** faça as seguintes alterações.

    - Altere o valor para `MicrosoftAppId` a ID do aplicativo do seu **Graph de bot** de calendário.
    - Altere o valor de `MicrosoftAppPassword` para seu segredo de cliente bot de calendário **Graph** calendário.
    - Adicione um valor nomeado `ConnectionName` com um valor de `GraphBotAuth` .

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > Se você usou um valor diferente do nome da sua entrada no `GraphBotAuth` **OAuth Connection Configurações** no Portal do Azure, use esse valor para a `ConnectionName` entrada.

## <a name="implement-dialogs"></a>Implementar caixas de diálogo

1. Crie um novo diretório na raiz do projeto denominado **Dialogs**. Crie um novo arquivo no **diretório ./Dialogs** chamado **LogoutDialog.cs** e adicione o código a seguir.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    Esta caixa de diálogo fornece uma classe base para todas as outras caixas de diálogo no bot a serem derivadas. Isso permite que o usuário faça logoff independentemente de onde ele está nas caixas de diálogo do bot.

1. Crie um novo arquivo no **diretório ./Dialogs** chamado **MainDialog.cs** e adicione o código a seguir.

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

    Tome um momento para revisar esse código.

    - No construtor, ele configura um [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) com um conjunto de etapas que ocorrem em ordem.
        - Ele `LoginPromptStepAsync` envia um **OAuthPrompt**. Se o usuário não estiver conectado, isso enviará um prompt de interface do usuário para o usuário.
        - Nele, `ProcessLoginStepAsync` verifica se o logon foi bem-sucedido e envia uma confirmação.
        - Ele `PromptUserStepAsync` envia um **ChoicePrompt** com os comandos disponíveis.
        - Nele, salva a escolha do usuário e, em `CommandStepAsync` seguida, reende um **OAuthPrompt**.
        - In `ProcessStepAsync` it takes action based on the command received.
        - In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.

## <a name="update-calendarbot"></a>Atualizar CalendarBot

A próxima etapa é atualizar **CalendarBot** para usar essas novas caixas de diálogo.

1. Abra **./Bots/CalendarBot.cs** e substitua todo o conteúdo pelo código a seguir.

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    Aqui está um breve resumo das alterações.

    - Alterou a **classe CalendarBot** para ser uma classe de modelo, recebendo uma **caixa de diálogo**.
    - Alterou a **classe CalendarBot** para estender **o TeamsActivityHandler**, permitindo que ele entre Microsoft Teams.
    - Adicionadas substituições de método adicionais para habilitar a autenticação.

## <a name="update-startupcs"></a>Atualizar Startup.cs

A etapa final é atualizar o método para adicionar os serviços `ConfigureServices` necessários para autenticação e a nova caixa de diálogo.

1. Abra **./Startup.cs** e remova `services.AddTransient<IBot, Bots.CalendarBot>();` a linha do `ConfigureServices` método.

1. Insira o código a seguir no final do `ConfigureServices` método.

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a>Autenticação de teste

1. Salve todas as alterações e inicie o bot com `dotnet run` .

1. Abra o Bot Framework Emulator. Selecione o ícone de engrenagem &#9881; à esquerda inferior.

1. Insira o caminho local para a instalação do ngrok e habilita o **ngrok Bypass** para endereços locais e Execute **o ngrok** quando o Emulator iniciar opções. Clique em **Salvar**.

1. Selecione o menu **Arquivo** e, em **seguida, Nova Configuração de Bot...**.

1. Preencha os campos da seguinte forma.

    - **Nome do bot:**`CalendarBot`
    - **URL do ponto de extremidade:**`https://localhost:3978/api/messages`
    - **ID do aplicativo microsoft:** a ID do aplicativo do seu **Graph de bot** de calendário
    - **Senha do Aplicativo microsoft:** seu **Graph de cliente bot** de calendário
    - **Criptografar chaves armazenadas em sua configuração de bot:** Habilitado

    ![Uma captura de tela da caixa de diálogo Nova configuração de bot](images/new-bot-config.png)

1. Selecione **Salvar e conectar**. Depois que o emulador se conectar, você deverá ver `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`

1. Digite algum texto e envie-o para o bot. O bot responde com um prompt de logon.

1. Selecione o **botão Logon.** O emulador solicita que você confirme a URL que começa com `oauthlink://https://token.botframeworkcom` . Selecione **Confirmar** para continuar.

1. Na janela pop-up, faça logon com sua Microsoft 365 conta. Revise as permissões solicitadas e aceite.

1. Depois que a autenticação e o consentimento são concluídos, a janela pop-up fornece um código de validação. Copie o código e feche a janela.

    ![Uma captura de tela do código Bot Framework Emulator validação](images/validation-code.png)

1. Insira o código de validação na janela de chat para concluir o logon.

    ![Uma captura de tela da conversa de logon com o bot de exemplo](images/bot-login.png)

1. Se você selecionar o **botão Mostrar token** (ou tipo ), o bot `show token` exibirá o token de acesso. O **botão Sair** (ou digitar ) fará `log out` logoff.

> [!TIP]
> Você pode receber a seguinte mensagem de erro no Bot Framework Emulator ao iniciar uma conversa com o bot.
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> Se isso acontecer, certifique-se de habilitar o **ngrok Executar** quando o Emulator iniciar nas configurações do emulador e reiniciar o emulador.
