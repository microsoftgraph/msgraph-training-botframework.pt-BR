---
ms.openlocfilehash: ab38560fe196ea76bdb1928f218b83aa9c769496
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446782"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="726c6-101">Nesta seção, você usará o Microsoft Graph SDK para obter o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="726c6-101">In this section you'll use the Microsoft Graph SDK to get the logged-in user.</span></span>

## <a name="create-a-graph-service"></a><span data-ttu-id="726c6-102">Criar um serviço de Graph</span><span class="sxs-lookup"><span data-stu-id="726c6-102">Create a Graph service</span></span>

<span data-ttu-id="726c6-103">Comece implementando um serviço que o bot pode usar para obter um **GraphServiceClient** do SDK do Microsoft Graph e, em seguida, disponibilizar esse serviço para o bot por meio da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="726c6-103">Start by implementing a service that the bot can use to get a **GraphServiceClient** from the Microsoft Graph SDK, then making that service available to the bot via dependency injection.</span></span>

1. <span data-ttu-id="726c6-104">Crie um novo diretório na raiz do projeto chamado **Graph**.</span><span class="sxs-lookup"><span data-stu-id="726c6-104">Create a new directory in the root of the project named **Graph**.</span></span> <span data-ttu-id="726c6-105">Crie um novo arquivo no **diretório ./Graph** chamado **IGraphClientService.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="726c6-105">Create a new file in the **./Graph** directory named **IGraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. <span data-ttu-id="726c6-106">Crie um novo arquivo no **diretório ./Graph** chamado **GraphClientService.cs** e adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="726c6-106">Create a new file in the **./Graph** directory named **GraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. <span data-ttu-id="726c6-107">Abra **./Startup.cs** e adicione a seguinte `using` instrução na parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="726c6-107">Open **./Startup.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using CalendarBot.Graph;
    ```

1. <span data-ttu-id="726c6-108">Adicione o seguinte código ao final da função `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="726c6-108">Add the following code to the end of the `ConfigureServices` function.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. <span data-ttu-id="726c6-109">Abra **./Dialogs/MainDialog.cs**.</span><span class="sxs-lookup"><span data-stu-id="726c6-109">Open **./Dialogs/MainDialog.cs**.</span></span> <span data-ttu-id="726c6-110">Adicione as instruções `using` a seguir à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="726c6-110">Add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System;
    using System.IO;
    using AdaptiveCards;
    using CalendarBot.Graph;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="726c6-111">Adicione a seguinte propriedade à **classe MainDialog.**</span><span class="sxs-lookup"><span data-stu-id="726c6-111">Add the following property to the **MainDialog** class.</span></span>

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. <span data-ttu-id="726c6-112">Localize o construtor da classe **MainDialog** e atualize sua assinatura para ter um **parâmetro IGraphServiceClient.**</span><span class="sxs-lookup"><span data-stu-id="726c6-112">Locate the constructor for the **MainDialog** class and update its signature to take an **IGraphServiceClient** parameter.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. <span data-ttu-id="726c6-113">Adicione o código a seguir ao construtor.</span><span class="sxs-lookup"><span data-stu-id="726c6-113">Add the following code to the constructor.</span></span>

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a><span data-ttu-id="726c6-114">Obter o usuário conectado</span><span class="sxs-lookup"><span data-stu-id="726c6-114">Get the logged on user</span></span>

<span data-ttu-id="726c6-115">Nesta seção, você usará o microsoft Graph para obter o nome, o endereço de email e a foto do usuário.</span><span class="sxs-lookup"><span data-stu-id="726c6-115">In this section you'll use the Microsoft Graph to get the user's name, email address, and photo.</span></span> <span data-ttu-id="726c6-116">Em seguida, você criará um Cartão Adaptável para mostrar as informações.</span><span class="sxs-lookup"><span data-stu-id="726c6-116">Then you'll create an Adaptive Card to show the information.</span></span>

1. <span data-ttu-id="726c6-117">Crie um novo arquivo na raiz do projeto chamado **CardHelper.cs**.</span><span class="sxs-lookup"><span data-stu-id="726c6-117">Create a new file in the root of the project named **CardHelper.cs**.</span></span> <span data-ttu-id="726c6-118">Adicione o código a seguir a esse arquivo.</span><span class="sxs-lookup"><span data-stu-id="726c6-118">Add the following code to the file.</span></span>

    ```csharp
    using AdaptiveCards;
    using Microsoft.Graph;
    using System;
    using System.IO;

    namespace CalendarBot
    {
        public class CardHelper
        {
            public static AdaptiveCard GetUserCard(User user, Stream photo)
            {
              // Create an Adaptive Card to display the user
                // See https://adaptivecards.io/designer/ for possibilities
                var userCard = new AdaptiveCard("1.2");

                var columns = new AdaptiveColumnSet();
                userCard.Body.Add(columns);

                var userPhotoColumn = new AdaptiveColumn { Width = AdaptiveColumnWidth.Auto };
                columns.Columns.Add(userPhotoColumn);

                userPhotoColumn.Items.Add(new AdaptiveImage {
                    Style = AdaptiveImageStyle.Person,
                    Size = AdaptiveImageSize.Small,
                    Url = GetDataUriFromPhoto(photo)
                });

                var userInfoColumn = new AdaptiveColumn {Width = AdaptiveColumnWidth.Stretch };
                columns.Columns.Add(userInfoColumn);

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Weight = AdaptiveTextWeight.Bolder,
                    Wrap = true,
                    Text = user.DisplayName
                });

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Spacing = AdaptiveSpacing.None,
                    IsSubtle = true,
                    Wrap = true,
                    Text = user.Mail ?? user.UserPrincipalName
                });

                return userCard;
            }

            private static Uri GetDataUriFromPhoto(Stream photo)
            {
                // Copy to a MemoryStream to get access to bytes
                var photoStream = new MemoryStream();
                photo.CopyTo(photoStream);

                var photoBytes = photoStream.ToArray();

                return new Uri($"data:image/png;base64,{Convert.ToBase64String(photoBytes)}");
            }
        }
    }
    ```

    <span data-ttu-id="726c6-119">Este código usa o **pacote NuGet AdaptiveCard** para criar um Cartão Adaptável para exibir o usuário.</span><span class="sxs-lookup"><span data-stu-id="726c6-119">This code uses the **AdaptiveCard** NuGet package to build an Adaptive Card to display the user.</span></span>

1. <span data-ttu-id="726c6-120">Adicione a seguinte função à **classe MainDialog.**</span><span class="sxs-lookup"><span data-stu-id="726c6-120">Add the following function to the **MainDialog** class.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    <span data-ttu-id="726c6-121">Considere o que esse código faz.</span><span class="sxs-lookup"><span data-stu-id="726c6-121">Consider what this code does.</span></span>

    - <span data-ttu-id="726c6-122">Ele usa **o graphClient** para [obter o usuário](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0)conectado .</span><span class="sxs-lookup"><span data-stu-id="726c6-122">It uses the **graphClient** to [get the logged-in user](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0).</span></span>
        - <span data-ttu-id="726c6-123">Ele usa o `Select` método para limitar quais campos são retornados.</span><span class="sxs-lookup"><span data-stu-id="726c6-123">It uses the `Select` method to limit which fields are returned.</span></span>
    - <span data-ttu-id="726c6-124">Ele usa **o graphClient** para obter a foto do usuário [,](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)solicitando o menor tamanho suportado de 48 x 48 pixels.</span><span class="sxs-lookup"><span data-stu-id="726c6-124">It uses the **graphClient** to [get the user's photo](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0), requesting the smallest supported size of 48x48 pixels.</span></span>
    - <span data-ttu-id="726c6-125">Ele usa a **classe CardHelper** para construir um Cartão Adaptável e envia o cartão como um anexo.</span><span class="sxs-lookup"><span data-stu-id="726c6-125">It uses the **CardHelper** class to construct an Adaptive Card and sends the card as an attachment.</span></span>

1. <span data-ttu-id="726c6-126">Substitua o código dentro `else if (command.StartsWith("show me"))` do bloco `ProcessStepAsync` pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="726c6-126">Replace the code inside the `else if (command.StartsWith("show me"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. <span data-ttu-id="726c6-127">Salve todas as alterações e reinicie o bot.</span><span class="sxs-lookup"><span data-stu-id="726c6-127">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="726c6-128">Use o Bot Framework Emulator para se conectar ao bot e fazer logoff.</span><span class="sxs-lookup"><span data-stu-id="726c6-128">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="726c6-129">Selecione o **botão Mostrar-me** para exibir o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="726c6-129">Select the **Show me** button to display the logged-on user.</span></span>

    ![Uma captura de tela do Cartão Adaptável mostrando o usuário](images/user-card.png)
