---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446817"
---
# <a name="graphcalendarbot"></a>GraphCalendarBot

Exemplo de bot de eco da Estrutura de Bot v4.

Esse bot foi criado usando o [Bot Framework](https://dev.botframework.com), ele mostra como criar um bot simples que aceita a entrada do usuário e a ecoa de volta.

## <a name="prerequisites"></a>Pré-requisitos

- [.NET Core SDK](https://dotnet.microsoft.com/download) versão 3.1

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a>Para experimentar este exemplo

- Em um terminal, navegue até `GraphCalendarBot`

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- Execute o bot de um terminal ou de Visual Studio, escolha a opção A ou B.

  A) De um terminal

  ```bash
  # run the bot
  dotnet run
  ```

  B) Ou de Visual Studio

  - Iniciar Visual Studio
  - Arquivo -> Open -> Project/Solution
  - Navegue até `GraphCalendarBot` a pasta
  - Selecionar `GraphCalendarBot.csproj` arquivo
  - Pressione `F5` para executar o projeto

## <a name="testing-the-bot-using-bot-framework-emulator"></a>Testar o bot usando Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) é um aplicativo de área de trabalho que permite que os desenvolvedores de bot testem e depurem seus bots no localhost ou em execução remotamente por meio de um túnel.

- Instale o Bot Framework Emulator versão 4.9.0 ou superior [daqui](https://github.com/Microsoft/BotFramework-Emulator/releases)

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Conexão ao bot usando Bot Framework Emulator

- Iniciar Bot Framework Emulator
- Arquivo -> Open Bot
- Insira uma URL de Bot de `http://localhost:3978/api/messages`

## <a name="deploy-the-bot-to-azure"></a>Implantar o bot no Azure

Para saber mais sobre como implantar um bot no Azure, consulte [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.

## <a name="further-reading"></a>Leitura posterior

- [Documentação da Estrutura de Bots](https://docs.botframework.com)
- [Noções básicas do Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [Processamento de atividades](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Introdução ao serviço bot do Azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Documentação do serviço bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [Ferramentas cli do .NET Core](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure Portal](https://portal.azure.com)
- [Noções básicas de idioma usando o LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [Canal e Serviço de Conector de Bot](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

Gerado usando `dotnet new echobot` v4.13.2
