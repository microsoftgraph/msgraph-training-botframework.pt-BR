---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446817"
---
# <a name="graphcalendarbot"></a><span data-ttu-id="b7c19-101">GraphCalendarBot</span><span class="sxs-lookup"><span data-stu-id="b7c19-101">GraphCalendarBot</span></span>

<span data-ttu-id="b7c19-102">Exemplo de bot de eco da Estrutura de Bot v4.</span><span class="sxs-lookup"><span data-stu-id="b7c19-102">Bot Framework v4 echo bot sample.</span></span>

<span data-ttu-id="b7c19-103">Esse bot foi criado usando o [Bot Framework](https://dev.botframework.com), ele mostra como criar um bot simples que aceita a entrada do usuário e a ecoa de volta.</span><span class="sxs-lookup"><span data-stu-id="b7c19-103">This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to create a simple bot that accepts input from the user and echoes it back.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7c19-104">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b7c19-104">Prerequisites</span></span>

- <span data-ttu-id="b7c19-105">[.NET Core SDK](https://dotnet.microsoft.com/download) versão 3.1</span><span class="sxs-lookup"><span data-stu-id="b7c19-105">[.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1</span></span>

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a><span data-ttu-id="b7c19-106">Para experimentar este exemplo</span><span class="sxs-lookup"><span data-stu-id="b7c19-106">To try this sample</span></span>

- <span data-ttu-id="b7c19-107">Em um terminal, navegue até `GraphCalendarBot`</span><span class="sxs-lookup"><span data-stu-id="b7c19-107">In a terminal, navigate to `GraphCalendarBot`</span></span>

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- <span data-ttu-id="b7c19-108">Execute o bot de um terminal ou de Visual Studio, escolha a opção A ou B.</span><span class="sxs-lookup"><span data-stu-id="b7c19-108">Run the bot from a terminal or from Visual Studio, choose option A or B.</span></span>

  <span data-ttu-id="b7c19-109">A) De um terminal</span><span class="sxs-lookup"><span data-stu-id="b7c19-109">A) From a terminal</span></span>

  ```bash
  # run the bot
  dotnet run
  ```

  <span data-ttu-id="b7c19-110">B) Ou de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c19-110">B) Or from Visual Studio</span></span>

  - <span data-ttu-id="b7c19-111">Iniciar Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7c19-111">Launch Visual Studio</span></span>
  - <span data-ttu-id="b7c19-112">Arquivo -> Open -> Project/Solution</span><span class="sxs-lookup"><span data-stu-id="b7c19-112">File -> Open -> Project/Solution</span></span>
  - <span data-ttu-id="b7c19-113">Navegue até `GraphCalendarBot` a pasta</span><span class="sxs-lookup"><span data-stu-id="b7c19-113">Navigate to `GraphCalendarBot` folder</span></span>
  - <span data-ttu-id="b7c19-114">Selecionar `GraphCalendarBot.csproj` arquivo</span><span class="sxs-lookup"><span data-stu-id="b7c19-114">Select `GraphCalendarBot.csproj` file</span></span>
  - <span data-ttu-id="b7c19-115">Pressione `F5` para executar o projeto</span><span class="sxs-lookup"><span data-stu-id="b7c19-115">Press `F5` to run the project</span></span>

## <a name="testing-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="b7c19-116">Testar o bot usando Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="b7c19-116">Testing the bot using Bot Framework Emulator</span></span>

<span data-ttu-id="b7c19-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem seus bots no localhost ou em execução remotamente por meio de um túnel.</span><span class="sxs-lookup"><span data-stu-id="b7c19-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.</span></span>

- <span data-ttu-id="b7c19-118">Instale o Bot Framework Emulator versão 4.9.0 ou superior [daqui](https://github.com/Microsoft/BotFramework-Emulator/releases)</span><span class="sxs-lookup"><span data-stu-id="b7c19-118">Install the Bot Framework Emulator version 4.9.0 or greater from [here](https://github.com/Microsoft/BotFramework-Emulator/releases)</span></span>

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="b7c19-119">Conexão ao bot usando Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="b7c19-119">Connect to the bot using Bot Framework Emulator</span></span>

- <span data-ttu-id="b7c19-120">Iniciar Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="b7c19-120">Launch Bot Framework Emulator</span></span>
- <span data-ttu-id="b7c19-121">Arquivo -> Open Bot</span><span class="sxs-lookup"><span data-stu-id="b7c19-121">File -> Open Bot</span></span>
- <span data-ttu-id="b7c19-122">Insira uma URL de Bot de `http://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="b7c19-122">Enter a Bot URL of `http://localhost:3978/api/messages`</span></span>

## <a name="deploy-the-bot-to-azure"></a><span data-ttu-id="b7c19-123">Implantar o bot no Azure</span><span class="sxs-lookup"><span data-stu-id="b7c19-123">Deploy the bot to Azure</span></span>

<span data-ttu-id="b7c19-124">Para saber mais sobre como implantar um bot no Azure, consulte [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.</span><span class="sxs-lookup"><span data-stu-id="b7c19-124">To learn more about deploying a bot to Azure, see [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.</span></span>

## <a name="further-reading"></a><span data-ttu-id="b7c19-125">Leitura posterior</span><span class="sxs-lookup"><span data-stu-id="b7c19-125">Further reading</span></span>

- [<span data-ttu-id="b7c19-126">Documentação da Estrutura de Bots</span><span class="sxs-lookup"><span data-stu-id="b7c19-126">Bot Framework Documentation</span></span>](https://docs.botframework.com)
- [<span data-ttu-id="b7c19-127">Noções básicas do Bot</span><span class="sxs-lookup"><span data-stu-id="b7c19-127">Bot Basics</span></span>](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [<span data-ttu-id="b7c19-128">Processamento de atividades</span><span class="sxs-lookup"><span data-stu-id="b7c19-128">Activity processing</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [<span data-ttu-id="b7c19-129">Introdução ao serviço bot do Azure</span><span class="sxs-lookup"><span data-stu-id="b7c19-129">Azure Bot Service Introduction</span></span>](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [<span data-ttu-id="b7c19-130">Documentação do serviço bot do Azure</span><span class="sxs-lookup"><span data-stu-id="b7c19-130">Azure Bot Service Documentation</span></span>](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [<span data-ttu-id="b7c19-131">Ferramentas cli do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7c19-131">.NET Core CLI tools</span></span>](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [<span data-ttu-id="b7c19-132">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="b7c19-132">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [<span data-ttu-id="b7c19-133">Azure Portal</span><span class="sxs-lookup"><span data-stu-id="b7c19-133">Azure Portal</span></span>](https://portal.azure.com)
- [<span data-ttu-id="b7c19-134">Noções básicas de idioma usando o LUIS</span><span class="sxs-lookup"><span data-stu-id="b7c19-134">Language Understanding using LUIS</span></span>](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [<span data-ttu-id="b7c19-135">Canal e Serviço de Conector de Bot</span><span class="sxs-lookup"><span data-stu-id="b7c19-135">Channels and Bot Connector Service</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

<span data-ttu-id="b7c19-136">Gerado usando `dotnet new echobot` v4.13.2</span><span class="sxs-lookup"><span data-stu-id="b7c19-136">Generated using `dotnet new echobot` v4.13.2</span></span>
