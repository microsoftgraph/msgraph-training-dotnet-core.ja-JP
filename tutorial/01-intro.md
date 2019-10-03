<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="11c5a-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する .NET コアコンソールアプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="11c5a-101">This tutorial teaches you how to build a .NET Core console app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="11c5a-102">完成したチュートリアルをダウンロードするだけで済む場合は、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-dotnet-core)をダウンロードするか、クローンを作成できます。</span><span class="sxs-lookup"><span data-stu-id="11c5a-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="11c5a-103">前提条件</span><span class="sxs-lookup"><span data-stu-id="11c5a-103">Prerequisites</span></span>

<span data-ttu-id="11c5a-104">このチュートリアルを開始する前に、開発用のコンピューターに[.Net CORE SDK](https://dotnet.microsoft.com/download)をインストールしておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="11c5a-104">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="11c5a-105">SDK または Maven を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="11c5a-105">If you do not have the SDK or Maven, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="11c5a-106">このチュートリアルは、.NET Core SDK version 2.2.401 を使用して作成されています。</span><span class="sxs-lookup"><span data-stu-id="11c5a-106">This tutorial was written with .NET Core SDK version 2.2.401.</span></span> <span data-ttu-id="11c5a-107">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="11c5a-107">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="11c5a-108">フィードバック</span><span class="sxs-lookup"><span data-stu-id="11c5a-108">Feedback</span></span>

<span data-ttu-id="11c5a-109">このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-dotnet-core)に記入してください。</span><span class="sxs-lookup"><span data-stu-id="11c5a-109">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-dotnet-core).</span></span>
