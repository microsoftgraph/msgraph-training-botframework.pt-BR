---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446810"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cab3c-101">Este tutorial ensina como criar um bot da Estrutura de Bots que usa a API do Microsoft Graph para recuperar informações de calendário para um usuário.</span><span class="sxs-lookup"><span data-stu-id="cab3c-101">This tutorial teaches you how to build a Bot Framework bot that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="cab3c-102">Se você preferir apenas baixar o tutorial concluído, você pode baixar ou clonar o [GitHub repositório](https://github.com/microsoftgraph/msgraph-training-botframework).</span><span class="sxs-lookup"><span data-stu-id="cab3c-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span> <span data-ttu-id="cab3c-103">Consulte o arquivo README na pasta **de demonstração** para obter instruções sobre como configurar o aplicativo com uma ID do aplicativo e segredo.</span><span class="sxs-lookup"><span data-stu-id="cab3c-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cab3c-104">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cab3c-104">Prerequisites</span></span>

<span data-ttu-id="cab3c-105">Antes de iniciar este tutorial, você deve ter o seguinte instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cab3c-105">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- [<span data-ttu-id="cab3c-106">SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cab3c-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="cab3c-107">Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="cab3c-107">Bot Framework Emulator</span></span>](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [<span data-ttu-id="cab3c-108">ngrok</span><span class="sxs-lookup"><span data-stu-id="cab3c-108">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="cab3c-109">Você também deve ter uma conta pessoal da Microsoft com uma caixa de correio em Outlook.com, ou uma conta de trabalho ou de estudante da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cab3c-109">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="cab3c-110">Se você não tiver uma conta da Microsoft, há algumas opções para obter uma conta gratuita:</span><span class="sxs-lookup"><span data-stu-id="cab3c-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="cab3c-111">Você pode [se inscrever em uma nova conta pessoal da Microsoft.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="cab3c-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="cab3c-112">Você pode [se inscrever no programa Office 365 desenvolvedor para](https://developer.microsoft.com/office/dev-program) obter uma assinatura Office 365 gratuita.</span><span class="sxs-lookup"><span data-stu-id="cab3c-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>
- <span data-ttu-id="cab3c-113">Uma assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="cab3c-113">An Azure subscription.</span></span> <span data-ttu-id="cab3c-114">Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.</span><span class="sxs-lookup"><span data-stu-id="cab3c-114">If you do not have one, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

> [!NOTE]
> <span data-ttu-id="cab3c-115">Este tutorial foi escrito com as seguintes versões.</span><span class="sxs-lookup"><span data-stu-id="cab3c-115">This tutorial was written with the following versions.</span></span> <span data-ttu-id="cab3c-116">As etapas neste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="cab3c-116">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="cab3c-117">.NET Core SDK versão 5.0.302</span><span class="sxs-lookup"><span data-stu-id="cab3c-117">.NET Core SDK version 5.0.302</span></span>
> - <span data-ttu-id="cab3c-118">Bot Framework Emulator 4.1.3</span><span class="sxs-lookup"><span data-stu-id="cab3c-118">Bot Framework Emulator 4.1.3</span></span>
> - <span data-ttu-id="cab3c-119">ngrok 2.3.40</span><span class="sxs-lookup"><span data-stu-id="cab3c-119">ngrok 2.3.40</span></span>

## <a name="feedback"></a><span data-ttu-id="cab3c-120">Comentários</span><span class="sxs-lookup"><span data-stu-id="cab3c-120">Feedback</span></span>

<span data-ttu-id="cab3c-121">Forneça qualquer comentário sobre este tutorial no [repositório GitHub .](https://github.com/microsoftgraph/msgraph-training-botframework)</span><span class="sxs-lookup"><span data-stu-id="cab3c-121">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span>
