<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cfa1c-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する .NET Core コンソール アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-101">This tutorial teaches you how to build a .NET Core console app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="cfa1c-102">完了したチュートリアルをダウンロードする場合は、リポジトリをダウンロードまたは複製GitHub[できます](https://github.com/microsoftgraph/msgraph-training-dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cfa1c-103">前提条件</span><span class="sxs-lookup"><span data-stu-id="cfa1c-103">Prerequisites</span></span>

<span data-ttu-id="cfa1c-104">このチュートリアルを開始する前に、開発マシンに [.NET Core SDK](https://dotnet.microsoft.com/download) がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-104">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="cfa1c-105">SDK または Maven がない場合は、ダウンロード オプションの前のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-105">If you do not have the SDK or Maven, visit the previous link for download options.</span></span>

<span data-ttu-id="cfa1c-106">また、Outlook.com 上のメールボックスを持つ個人用 Microsoft アカウント、または Microsoft の仕事用または学校用のアカウントを持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-106">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="cfa1c-107">Microsoft アカウントをお持ちでない場合は、無料アカウントを取得するためのオプションが 2 つご利用できます。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-107">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="cfa1c-108">新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-108">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="cfa1c-109">開発者プログラム[にサインアップしてOffice 365無料](https://developer.microsoft.com/office/dev-program)のサブスクリプションをOffice 365できます。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-109">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="cfa1c-110">このチュートリアルは、.NET Core SDK バージョン 5.0.302 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-110">This tutorial was written with .NET Core SDK version 5.0.302.</span></span> <span data-ttu-id="cfa1c-111">このガイドの手順は、他のバージョンでも動作しますが、テストされていない場合があります。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-111">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="cfa1c-112">フィードバック</span><span class="sxs-lookup"><span data-stu-id="cfa1c-112">Feedback</span></span>

<span data-ttu-id="cfa1c-113">このチュートリアルに関するフィードバックは、GitHub[してください](https://github.com/microsoftgraph/msgraph-training-dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="cfa1c-113">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>
