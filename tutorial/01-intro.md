---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446810"
---
<!-- markdownlint-disable MD002 MD041 -->

Este tutorial ensina como criar um bot da Estrutura de Bots que usa a API do Microsoft Graph para recuperar informações de calendário para um usuário.

> [!TIP]
> Se você preferir apenas baixar o tutorial concluído, você pode baixar ou clonar o [GitHub repositório](https://github.com/microsoftgraph/msgraph-training-botframework). Consulte o arquivo README na pasta **de demonstração** para obter instruções sobre como configurar o aplicativo com uma ID do aplicativo e segredo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter o seguinte instalado em sua máquina de desenvolvimento.

- [SDK do .NET Core](https://dotnet.microsoft.com/download)
- [Bot Framework Emulator](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [ngrok](https://ngrok.com/)

Você também deve ter uma conta pessoal da Microsoft com uma caixa de correio em Outlook.com, ou uma conta de trabalho ou de estudante da Microsoft. Se você não tiver uma conta da Microsoft, há algumas opções para obter uma conta gratuita:

- Você pode [se inscrever em uma nova conta pessoal da Microsoft.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Você pode [se inscrever no programa Office 365 desenvolvedor para](https://developer.microsoft.com/office/dev-program) obter uma assinatura Office 365 gratuita.
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Este tutorial foi escrito com as seguintes versões. As etapas neste guia podem funcionar com outras versões, mas que não foram testadas.
>
> - .NET Core SDK versão 5.0.302
> - Bot Framework Emulator 4.1.3
> - ngrok 2.3.40

## <a name="feedback"></a>Comentários

Forneça qualquer comentário sobre este tutorial no [repositório GitHub .](https://github.com/microsoftgraph/msgraph-training-botframework)
