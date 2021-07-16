---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446734"
---
<!-- markdownlint-disable MD002 MD041 -->

Nesta seção, você usará o Microsoft Graph SDK para adicionar um evento ao calendário do usuário.

## <a name="implement-a-dialog"></a>Implementar uma caixa de diálogo

Comece criando uma nova caixa de diálogo personalizada para solicitar ao usuário os valores necessários para adicionar um evento ao calendário. Essa caixa de diálogo usará **um WaterfallDialog** para fazer as etapas a seguir.

- Prompt for a subject
- Pergunte se o usuário deseja convidar pessoas
- Prompt for attendees (if user said yes to previous step)
- Solicitar uma data e hora de início
- Solicitar uma data e hora de término
- Exibir todos os valores coletados e solicitar que o usuário confirme
- Se o usuário confirmar, obter token de acesso do **OAuthPrompt**
- Criar o evento

1. Crie um novo arquivo no **diretório ./Dialogs** chamado **NewEventDialog.cs** e adicione o código a seguir.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using CalendarBot.Graph;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Graph;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    using TimexTypes = Microsoft.Recognizers.Text.DataTypes.TimexExpression.Constants.TimexTypes;

    namespace CalendarBot.Dialogs
    {
        public class NewEventDialog : LogoutDialog
        {
            protected readonly ILogger _logger;
            private readonly IGraphClientService _graphClientService;

            public NewEventDialog(
                IConfiguration configuration,
                IGraphClientService graphClientService)
                : base(nameof(NewEventDialog), configuration["ConnectionName"])
            {

            }

            // Generate a DateTime from the list of
            // DateTimeResolutions provided by the DateTimePrompt
            private static DateTime GetDateTimeFromResolutions(IList<DateTimeResolution> resolutions)
            {
                var timex = new TimexProperty(resolutions[0].Timex);

                // Handle the "now" case
                if (timex.Now ?? false)
                {
                    return DateTime.Now;
                }

                // Otherwise generate a DateTime
                return TimexHelpers.DateFromTimex(timex);
            }
        }
    }
    ```

    Esse é o shell de uma nova caixa de diálogo que solicitará ao usuário os valores necessários para adicionar um evento ao calendário.

1. Adicione a seguinte função à **classe NewEventDialog** para solicitar ao usuário um assunto.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para armazenar o assunto que o usuário deu na etapa anterior e para perguntar se deseja adicionar participantes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para verificar a resposta do usuário na etapa anterior e solicitar ao usuário uma lista de participantes, se necessário.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para armazenar a lista de participantes da etapa anterior (se presente) e solicitar ao usuário uma data e hora de início.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para armazenar o valor inicial da etapa anterior e solicitar ao usuário uma data e hora de término.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para armazenar o valor final da etapa anterior e peça ao usuário para confirmar todas as entradas.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para verificar a resposta do usuário da etapa anterior. Se o usuário confirmar as entradas, use a **classe OAuthPrompt** para obter um token de acesso. Caso contrário, termine a caixa de diálogo.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para usar o Microsoft Graph SDK para criar o novo evento.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    Considere o que esse código faz.

    - Ele obtém o **MailboxSettings** do usuário para determinar o fuso horário preferencial do usuário.
    - Ele cria um **objeto Event** usando os valores fornecidos pelo usuário. Observe que as **propriedades Start** **e End** são definidas com o fuso horário do usuário.
    - Ele cria o evento no calendário do usuário.

## <a name="add-validation"></a>Adicionar validação

Agora adicione validação à entrada do usuário para evitar erros ao criar o evento com o Microsoft Graph. Queremos ter certeza de que:

- Se o usuário der uma lista de participantes, deverá ser uma lista delimitada por ponto-e-vírgula de endereços de email válidos.
- A data/hora de início deve ser uma data e hora válidas.
- A data/hora de término deve ser uma data e hora válidas e deve ser posterior ao início.

1. Adicione a seguinte função à **classe NewEventDialog** para validar a entrada do usuário para participantes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. Adicione as seguintes funções à **classe NewEventDialog** para validar a entrada do usuário para a data e a hora de início.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. Adicione a seguinte função à **classe NewEventDialog** para validar a entrada do usuário para a data e a hora de término.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a>Adicionar etapas ao WaterfallDialog

Agora que você tem todas as "etapas" da caixa de diálogo, a última etapa é adicioná-las a um **WaterfallDialog** no construtor e adicionar o **NewEventDialog** ao **MainDialog**.

1. Localize **o construtor NewEventDialog** e adicione o seguinte código a ele.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    Isso adiciona todas as caixas de diálogo usadas e adiciona todas as funções implementadas como etapas no **WaterfallDialog**.

1. Abra **./Dialogs/MainDialog.cs** e adicione a seguinte linha ao construtor.

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. Substitua o código dentro `else if (command.StartsWith("add event"))` do bloco `ProcessStepAsync` pelo seguinte.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. Salve todas as alterações e reinicie o bot.

1. Use o Bot Framework Emulator para se conectar ao bot e fazer logoff. Selecione o **botão Adicionar evento.**

1. Responda aos prompts para criar um novo evento. Observe que, quando solicitado a valores de início e fim, você pode usar frases como "hoje às 15h" ou "agora".

    ![Uma captura de tela da caixa de diálogo adicionar evento no Bot Framework Emulator](images/add-event.png)
