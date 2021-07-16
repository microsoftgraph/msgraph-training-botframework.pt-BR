---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446775"
---
<!-- markdownlint-disable MD002 MD041 -->

Nesta seção, você criará um projeto da Estrutura de Bot.

1. Abra sua interface de linha de comando (CLI) em um diretório onde você deseja criar o projeto. Execute o seguinte comando para criar um novo projeto usando o **modelo Microsoft.Bot.Framework.CSharp.EchoBot.**

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > Se você receber um `No templates matched the input template name: echobot.` erro, instale o modelo com o seguinte comando e execute o comando anterior.
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. Renomeie a classe **padrão EchoBot** para **CalendarBot.** Abra **./Bots/EchoBot.cs** e substitua todas as instâncias `EchoBot` de por `CalendarBot` . Renomeie o arquivo para **CalendarBot.cs**.

1. Substitua todas as instâncias `EchoBot` dos `CalendarBot` arquivos **.cs restantes.**

1. Em sua CLI, altere o diretório atual para o diretório **GraphCalendarBot** e execute o seguinte comando para confirmar as builds do projeto.

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a>Adicionar pacotes NuGet

Antes de continuar, instale alguns pacotes NuGet que você usará posteriormente.

- [AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) para permitir que o bot envie Cartões Adaptáveis em respostas.
- [Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) para adicionar suporte à caixa de diálogo ao bot.
- [Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) para converter as expressões TIMEX retornadas de prompts de bot em **objetos DateTime.**
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) para fazer chamadas para o Microsoft Graph.

1. Execute os seguintes comandos em sua CLI para instalar as dependências.

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a>Testar o bot

Antes de adicionar qualquer código, teste o bot para garantir que ele funcione corretamente e se o Bot Framework Emulator está configurado para testá-lo.

1. Inicie o bot executando o seguinte comando.

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > Embora você possa usar qualquer editor de texto para editar os arquivos de origem no projeto, recomendamos [usar](https://code.visualstudio.com/)Visual Studio Code . Visual Studio Code oferece suporte à depuração, Intellisense e muito mais. Se estiver Visual Studio Code, você poderá iniciar o bot usando o menu **Executar**  ->  **Iniciar Depuração.**

1. Confirme se o bot está sendo executado abrindo seu navegador e indo para `http://localhost:3978` . Você deve ver que **seu bot está pronto!** Mensagem.

1. Abra o Bot Framework Emulator. Escolha o menu **Arquivo** **e,** em seguida, Abra Bot .

1. Insira `http://localhost:3978/api/messages` a URL do **Bot** e selecione **Conexão**.

1. O bot responde na `Hello and welcome!` janela de chat. Envie uma mensagem para o bot e confirme se ela a ecoa de volta.

    ![Uma captura de tela do Bot Framework Emulator conectado ao bot](images/test-emulator.png)
