---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446734"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="80736-101">Nesta seção, você usará o Microsoft Graph SDK para adicionar um evento ao calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="80736-101">In this section you'll use the Microsoft Graph SDK to add an event to the user's calendar.</span></span>

## <a name="implement-a-dialog"></a><span data-ttu-id="80736-102">Implementar uma caixa de diálogo</span><span class="sxs-lookup"><span data-stu-id="80736-102">Implement a dialog</span></span>

<span data-ttu-id="80736-103">Comece criando uma nova caixa de diálogo personalizada para solicitar ao usuário os valores necessários para adicionar um evento ao calendário.</span><span class="sxs-lookup"><span data-stu-id="80736-103">Start by creating a new custom dialog to prompt the user for the values needed to add an event to their calendar.</span></span> <span data-ttu-id="80736-104">Essa caixa de diálogo usará **um WaterfallDialog** para fazer as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="80736-104">This dialog will use a **WaterfallDialog** to do the following steps.</span></span>

- <span data-ttu-id="80736-105">Prompt for a subject</span><span class="sxs-lookup"><span data-stu-id="80736-105">Prompt for a subject</span></span>
- <span data-ttu-id="80736-106">Pergunte se o usuário deseja convidar pessoas</span><span class="sxs-lookup"><span data-stu-id="80736-106">Ask if the user wants to invite people</span></span>
- <span data-ttu-id="80736-107">Prompt for attendees (if user said yes to previous step)</span><span class="sxs-lookup"><span data-stu-id="80736-107">Prompt for attendees (if user said yes to previous step)</span></span>
- <span data-ttu-id="80736-108">Solicitar uma data e hora de início</span><span class="sxs-lookup"><span data-stu-id="80736-108">Prompt for a start date and time</span></span>
- <span data-ttu-id="80736-109">Solicitar uma data e hora de término</span><span class="sxs-lookup"><span data-stu-id="80736-109">Prompt for an end date and time</span></span>
- <span data-ttu-id="80736-110">Exibir todos os valores coletados e solicitar que o usuário confirme</span><span class="sxs-lookup"><span data-stu-id="80736-110">Display all of the collected values and ask user to confirm</span></span>
- <span data-ttu-id="80736-111">Se o usuário confirmar, obter token de acesso do **OAuthPrompt**</span><span class="sxs-lookup"><span data-stu-id="80736-111">If user confirms, get access token from **OAuthPrompt**</span></span>
- <span data-ttu-id="80736-112">Criar o evento</span><span class="sxs-lookup"><span data-stu-id="80736-112">Create the event</span></span>

1. <span data-ttu-id="80736-113">Crie um novo arquivo no **diretório ./Dialogs** chamado **NewEventDialog.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="80736-113">Create a new file in the **./Dialogs** directory named **NewEventDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="80736-114">Esse é o shell de uma nova caixa de diálogo que solicitará ao usuário os valores necessários para adicionar um evento ao calendário.</span><span class="sxs-lookup"><span data-stu-id="80736-114">This is the shell of a new dialog that will prompt the user for the values needed to add an event to their calendar.</span></span>

1. <span data-ttu-id="80736-115">Adicione a seguinte função à **classe NewEventDialog** para solicitar ao usuário um assunto.</span><span class="sxs-lookup"><span data-stu-id="80736-115">Add the following function to the **NewEventDialog** class to prompt the user for a subject.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. <span data-ttu-id="80736-116">Adicione a seguinte função à **classe NewEventDialog** para armazenar o assunto que o usuário deu na etapa anterior e para perguntar se deseja adicionar participantes.</span><span class="sxs-lookup"><span data-stu-id="80736-116">Add the following function to the **NewEventDialog** class to store the subject the user gave in the previous step, and to ask if they want to add attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. <span data-ttu-id="80736-117">Adicione a seguinte função à **classe NewEventDialog** para verificar a resposta do usuário na etapa anterior e solicitar ao usuário uma lista de participantes, se necessário.</span><span class="sxs-lookup"><span data-stu-id="80736-117">Add the following function to the **NewEventDialog** class to check the user's response from the previous step and prompt the user for a list of attendees if needed.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. <span data-ttu-id="80736-118">Adicione a seguinte função à **classe NewEventDialog** para armazenar a lista de participantes da etapa anterior (se presente) e solicitar ao usuário uma data e hora de início.</span><span class="sxs-lookup"><span data-stu-id="80736-118">Add the following function to the **NewEventDialog** class to store the list of attendees from the previous step (if present) and prompt the user for a start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. <span data-ttu-id="80736-119">Adicione a seguinte função à **classe NewEventDialog** para armazenar o valor inicial da etapa anterior e solicitar ao usuário uma data e hora de término.</span><span class="sxs-lookup"><span data-stu-id="80736-119">Add the following function to the **NewEventDialog** class to store the start value from the previous step and prompt the user for an end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. <span data-ttu-id="80736-120">Adicione a seguinte função à **classe NewEventDialog** para armazenar o valor final da etapa anterior e peça ao usuário para confirmar todas as entradas.</span><span class="sxs-lookup"><span data-stu-id="80736-120">Add the following function to the **NewEventDialog** class to store the end value from the previous step and ask the user to confirm all of the inputs.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. <span data-ttu-id="80736-121">Adicione a seguinte função à **classe NewEventDialog** para verificar a resposta do usuário da etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="80736-121">Add the following function to the **NewEventDialog** class to check the user's response from the previous step.</span></span> <span data-ttu-id="80736-122">Se o usuário confirmar as entradas, use a **classe OAuthPrompt** para obter um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="80736-122">If the user confirms the inputs, use the **OAuthPrompt** class to get an access token.</span></span> <span data-ttu-id="80736-123">Caso contrário, termine a caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="80736-123">Otherwise, end the dialog.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. <span data-ttu-id="80736-124">Adicione a seguinte função à **classe NewEventDialog** para usar o Microsoft Graph SDK para criar o novo evento.</span><span class="sxs-lookup"><span data-stu-id="80736-124">Add the following function to the **NewEventDialog** class to use the Microsoft Graph SDK to create the new event.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    <span data-ttu-id="80736-125">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="80736-125">Consider what this code does.</span></span>

    - <span data-ttu-id="80736-126">Ele obtém o **MailboxSettings** do usuário para determinar o fuso horário preferencial do usuário.</span><span class="sxs-lookup"><span data-stu-id="80736-126">It gets the user's **MailboxSettings** to determine the user's preferred time zone.</span></span>
    - <span data-ttu-id="80736-127">Ele cria um **objeto Event** usando os valores fornecidos pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="80736-127">It creates an **Event** object using the values provided by the user.</span></span> <span data-ttu-id="80736-128">Observe que as **propriedades Start** **e End** são definidas com o fuso horário do usuário.</span><span class="sxs-lookup"><span data-stu-id="80736-128">Note that the **Start** and **End** properties are set with the user's time zone.</span></span>
    - <span data-ttu-id="80736-129">Ele cria o evento no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="80736-129">It creates the event on the user's calendar.</span></span>

## <a name="add-validation"></a><span data-ttu-id="80736-130">Adicionar validação</span><span class="sxs-lookup"><span data-stu-id="80736-130">Add validation</span></span>

<span data-ttu-id="80736-131">Agora adicione validação à entrada do usuário para evitar erros ao criar o evento com o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="80736-131">Now add validation to the user's input to avoid errors when creating the event with Microsoft Graph.</span></span> <span data-ttu-id="80736-132">Queremos ter certeza de que:</span><span class="sxs-lookup"><span data-stu-id="80736-132">We want to make sure that:</span></span>

- <span data-ttu-id="80736-133">Se o usuário der uma lista de participantes, deverá ser uma lista delimitada por ponto-e-vírgula de endereços de email válidos.</span><span class="sxs-lookup"><span data-stu-id="80736-133">If the user gives an attendee list, it should be a semicolon-delimited list of valid email addresses.</span></span>
- <span data-ttu-id="80736-134">A data/hora de início deve ser uma data e hora válidas.</span><span class="sxs-lookup"><span data-stu-id="80736-134">The start date/time should be a valid date and time.</span></span>
- <span data-ttu-id="80736-135">A data/hora de término deve ser uma data e hora válidas e deve ser posterior ao início.</span><span class="sxs-lookup"><span data-stu-id="80736-135">The end date/time should be a valid date and time, and should be later than the start.</span></span>

1. <span data-ttu-id="80736-136">Adicione a seguinte função à **classe NewEventDialog** para validar a entrada do usuário para participantes.</span><span class="sxs-lookup"><span data-stu-id="80736-136">Add the following function to the **NewEventDialog** class to validate the user's entry for attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. <span data-ttu-id="80736-137">Adicione as seguintes funções à **classe NewEventDialog** para validar a entrada do usuário para a data e a hora de início.</span><span class="sxs-lookup"><span data-stu-id="80736-137">Add the following functions to the **NewEventDialog** class to validate the user's entry for start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. <span data-ttu-id="80736-138">Adicione a seguinte função à **classe NewEventDialog** para validar a entrada do usuário para a data e a hora de término.</span><span class="sxs-lookup"><span data-stu-id="80736-138">Add the following function to the **NewEventDialog** class to validate the user's entry for end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a><span data-ttu-id="80736-139">Adicionar etapas ao WaterfallDialog</span><span class="sxs-lookup"><span data-stu-id="80736-139">Add steps to WaterfallDialog</span></span>

<span data-ttu-id="80736-140">Agora que você tem todas as "etapas" da caixa de diálogo, a última etapa é adicioná-las a um **WaterfallDialog** no construtor e adicionar o **NewEventDialog** ao **MainDialog**.</span><span class="sxs-lookup"><span data-stu-id="80736-140">Now that you have all of the "steps" for the dialog, the last step is to add them to a **WaterfallDialog** in the constructor, then add the **NewEventDialog** to the **MainDialog**.</span></span>

1. <span data-ttu-id="80736-141">Localize **o construtor NewEventDialog** e adicione o seguinte código a ele.</span><span class="sxs-lookup"><span data-stu-id="80736-141">Locate the **NewEventDialog** constructor and add the following code to it.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    <span data-ttu-id="80736-142">Isso adiciona todas as caixas de diálogo usadas e adiciona todas as funções implementadas como etapas no **WaterfallDialog**.</span><span class="sxs-lookup"><span data-stu-id="80736-142">This adds all of the dialogs that are used, and adds all of the functions you implemented as steps in the **WaterfallDialog**.</span></span>

1. <span data-ttu-id="80736-143">Abra **./Dialogs/MainDialog.cs** e adicione a seguinte linha ao construtor.</span><span class="sxs-lookup"><span data-stu-id="80736-143">Open **./Dialogs/MainDialog.cs** and add the following line to the constructor.</span></span>

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. <span data-ttu-id="80736-144">Substitua o código dentro `else if (command.StartsWith("add event"))` do bloco `ProcessStepAsync` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="80736-144">Replace the code inside the `else if (command.StartsWith("add event"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. <span data-ttu-id="80736-145">Salve todas as alterações e reinicie o bot.</span><span class="sxs-lookup"><span data-stu-id="80736-145">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="80736-146">Use o Bot Framework Emulator para se conectar ao bot e fazer logoff.</span><span class="sxs-lookup"><span data-stu-id="80736-146">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="80736-147">Selecione o **botão Adicionar evento.**</span><span class="sxs-lookup"><span data-stu-id="80736-147">Select the **Add event** button.</span></span>

1. <span data-ttu-id="80736-148">Responda aos prompts para criar um novo evento.</span><span class="sxs-lookup"><span data-stu-id="80736-148">Respond to the prompts to create a new event.</span></span> <span data-ttu-id="80736-149">Observe que, quando solicitado a valores de início e fim, você pode usar frases como "hoje às 15h" ou "agora".</span><span class="sxs-lookup"><span data-stu-id="80736-149">Note that when prompted for start and end values, you can use phrases like "today at 3PM", or "now".</span></span>

    ![Uma captura de tela da caixa de diálogo adicionar evento no Bot Framework Emulator](images/add-event.png)
