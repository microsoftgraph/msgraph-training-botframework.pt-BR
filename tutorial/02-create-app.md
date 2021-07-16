---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446775"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e1122-101">Nesta seção, você criará um projeto da Estrutura de Bot.</span><span class="sxs-lookup"><span data-stu-id="e1122-101">In this section you'll create a Bot Framework project.</span></span>

1. <span data-ttu-id="e1122-102">Abra sua interface de linha de comando (CLI) em um diretório onde você deseja criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="e1122-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="e1122-103">Execute o seguinte comando para criar um novo projeto usando o **modelo Microsoft.Bot.Framework.CSharp.EchoBot.**</span><span class="sxs-lookup"><span data-stu-id="e1122-103">Run the following command to create new project using the **Microsoft.Bot.Framework.CSharp.EchoBot** template.</span></span>

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > <span data-ttu-id="e1122-104">Se você receber um `No templates matched the input template name: echobot.` erro, instale o modelo com o seguinte comando e execute o comando anterior.</span><span class="sxs-lookup"><span data-stu-id="e1122-104">If you receive an `No templates matched the input template name: echobot.` error, install the template with the following command and re-run the previous command.</span></span>
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. <span data-ttu-id="e1122-105">Renomeie a classe **padrão EchoBot** para **CalendarBot.**</span><span class="sxs-lookup"><span data-stu-id="e1122-105">Rename the default **EchoBot** class to **CalendarBot**.</span></span> <span data-ttu-id="e1122-106">Abra **./Bots/EchoBot.cs** e substitua todas as instâncias `EchoBot` de por `CalendarBot` .</span><span class="sxs-lookup"><span data-stu-id="e1122-106">Open **./Bots/EchoBot.cs** and replace all instances of `EchoBot` with `CalendarBot`.</span></span> <span data-ttu-id="e1122-107">Renomeie o arquivo para **CalendarBot.cs**.</span><span class="sxs-lookup"><span data-stu-id="e1122-107">Rename the file to **CalendarBot.cs**.</span></span>

1. <span data-ttu-id="e1122-108">Substitua todas as instâncias `EchoBot` dos `CalendarBot` arquivos **.cs restantes.**</span><span class="sxs-lookup"><span data-stu-id="e1122-108">Replace all instances of `EchoBot` with `CalendarBot` in the remaining **.cs** files.</span></span>

1. <span data-ttu-id="e1122-109">Em sua CLI, altere o diretório atual para o diretório **GraphCalendarBot** e execute o seguinte comando para confirmar as builds do projeto.</span><span class="sxs-lookup"><span data-stu-id="e1122-109">In your CLI, change the current directory to the **GraphCalendarBot** directory and run the following command to confirm the project builds.</span></span>

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a><span data-ttu-id="e1122-110">Adicionar pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="e1122-110">Add NuGet packages</span></span>

<span data-ttu-id="e1122-111">Antes de continuar, instale alguns pacotes NuGet que você usará posteriormente.</span><span class="sxs-lookup"><span data-stu-id="e1122-111">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="e1122-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) para permitir que o bot envie Cartões Adaptáveis em respostas.</span><span class="sxs-lookup"><span data-stu-id="e1122-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) to allow the bot to send Adaptive Cards in responses.</span></span>
- <span data-ttu-id="e1122-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) para adicionar suporte à caixa de diálogo ao bot.</span><span class="sxs-lookup"><span data-stu-id="e1122-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) to add dialog support to the bot.</span></span>
- <span data-ttu-id="e1122-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) para converter as expressões TIMEX retornadas de prompts de bot em **objetos DateTime.**</span><span class="sxs-lookup"><span data-stu-id="e1122-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) to convert the TIMEX expressions returned from bot prompts into **DateTime** objects.</span></span>
- <span data-ttu-id="e1122-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e1122-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="e1122-116">Execute os seguintes comandos em sua CLI para instalar as dependências.</span><span class="sxs-lookup"><span data-stu-id="e1122-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a><span data-ttu-id="e1122-117">Testar o bot</span><span class="sxs-lookup"><span data-stu-id="e1122-117">Test the bot</span></span>

<span data-ttu-id="e1122-118">Antes de adicionar qualquer código, teste o bot para garantir que ele funcione corretamente e se o Bot Framework Emulator está configurado para testá-lo.</span><span class="sxs-lookup"><span data-stu-id="e1122-118">Before adding any code, test the bot to make sure that it works correctly, and that the Bot Framework Emulator is configured to test it.</span></span>

1. <span data-ttu-id="e1122-119">Inicie o bot executando o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="e1122-119">Start the bot by running the following command.</span></span>

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > <span data-ttu-id="e1122-120">Embora você possa usar qualquer editor de texto para editar os arquivos de origem no projeto, recomendamos [usar](https://code.visualstudio.com/)Visual Studio Code .</span><span class="sxs-lookup"><span data-stu-id="e1122-120">While you can use any text editor to edit the source files in the project, we recommend using [Visual Studio Code](https://code.visualstudio.com/).</span></span> <span data-ttu-id="e1122-121">Visual Studio Code oferece suporte à depuração, Intellisense e muito mais.</span><span class="sxs-lookup"><span data-stu-id="e1122-121">Visual Studio Code offers debugging support, Intellisense, and more.</span></span> <span data-ttu-id="e1122-122">Se estiver Visual Studio Code, você poderá iniciar o bot usando o menu **Executar**  ->  **Iniciar Depuração.**</span><span class="sxs-lookup"><span data-stu-id="e1122-122">If using Visual Studio Code, you can start the bot using the **Run** -> **Start Debugging** menu.</span></span>

1. <span data-ttu-id="e1122-123">Confirme se o bot está sendo executado abrindo seu navegador e indo para `http://localhost:3978` .</span><span class="sxs-lookup"><span data-stu-id="e1122-123">Confirm the bot is running by opening your browser and going to `http://localhost:3978`.</span></span> <span data-ttu-id="e1122-124">Você deve ver que **seu bot está pronto!**</span><span class="sxs-lookup"><span data-stu-id="e1122-124">You should see a **Your bot is ready!**</span></span> <span data-ttu-id="e1122-125">Mensagem.</span><span class="sxs-lookup"><span data-stu-id="e1122-125">message.</span></span>

1. <span data-ttu-id="e1122-126">Abra o Bot Framework Emulator.</span><span class="sxs-lookup"><span data-stu-id="e1122-126">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="e1122-127">Escolha o menu **Arquivo** **e,** em seguida, Abra Bot .</span><span class="sxs-lookup"><span data-stu-id="e1122-127">Choose the **File** menu, then **Open Bot**.</span></span>

1. <span data-ttu-id="e1122-128">Insira `http://localhost:3978/api/messages` a URL do **Bot** e selecione **Conexão**.</span><span class="sxs-lookup"><span data-stu-id="e1122-128">Enter `http://localhost:3978/api/messages` in the **Bot URL**, then select **Connect**.</span></span>

1. <span data-ttu-id="e1122-129">O bot responde na `Hello and welcome!` janela de chat.</span><span class="sxs-lookup"><span data-stu-id="e1122-129">The bot responds with `Hello and welcome!` in the chat window.</span></span> <span data-ttu-id="e1122-130">Envie uma mensagem para o bot e confirme se ela a ecoa de volta.</span><span class="sxs-lookup"><span data-stu-id="e1122-130">Send a message to the bot and confirm it echoes it back.</span></span>

    ![Uma captura de tela do Bot Framework Emulator conectado ao bot](images/test-emulator.png)
