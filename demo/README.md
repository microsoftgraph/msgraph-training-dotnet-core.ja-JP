# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="9b478-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="9b478-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b478-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="9b478-102">Prerequisites</span></span>

<span data-ttu-id="9b478-103">このフォルダーで完了したプロジェクトを実行するには、次の情報が必要です。</span><span class="sxs-lookup"><span data-stu-id="9b478-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="9b478-104">開発[マシンにインストールされている .NET Core SDK。](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="9b478-104">The [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="9b478-105">(**注:** このチュートリアルは、.NET Core SDK バージョン 3.1.402 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="9b478-105">(**Note:** This tutorial was written with .NET Core SDK version 3.1.402.</span></span> <span data-ttu-id="9b478-106">このガイドの手順は、他のバージョンで動作する場合がありますが、テストされていない場合があります)。</span><span class="sxs-lookup"><span data-stu-id="9b478-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="9b478-107">Microsoft の仕事または学校のアカウント。</span><span class="sxs-lookup"><span data-stu-id="9b478-107">A Microsoft work or school account.</span></span>

<span data-ttu-id="9b478-108">Microsoft アカウントをお持ちでない場合は、Office 365[開発者](https://developer.microsoft.com/office/dev-program)プログラムにサインアップして無料のサブスクリプションOffice 365できます。</span><span class="sxs-lookup"><span data-stu-id="9b478-108">If you don't have a Microsoft account, you can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="9b478-109">Web アプリケーションを管理センターにAzure Active Directoryする</span><span class="sxs-lookup"><span data-stu-id="9b478-109">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="9b478-110">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="9b478-110">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="9b478-111">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b478-111">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="9b478-112">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="9b478-112">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="9b478-113">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b478-113">Select **New registration**.</span></span> <span data-ttu-id="9b478-114">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b478-114">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="9b478-115">`.NET Core Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="9b478-115">Set **Name** to `.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="9b478-116">[サポート **されているアカウントの種類] を任意\*\*\*\*の組織ディレクトリの [アカウント] に設定します**。</span><span class="sxs-lookup"><span data-stu-id="9b478-116">Set **Supported account types** to **Accounts in any organizational directory**.</span></span>
    - <span data-ttu-id="9b478-117">**[リダイレクト URI]** を空のままにします。</span><span class="sxs-lookup"><span data-stu-id="9b478-117">Leave **Redirect URI** empty.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="9b478-119">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9b478-119">Select **Register**.</span></span> <span data-ttu-id="9b478-120">**[.NET Core Graph チュートリアル**] ページで、アプリケーション **(クライアント) ID** の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="9b478-120">On the **.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="9b478-122">[リダイレクト **URI の追加] リンクを選択** します。</span><span class="sxs-lookup"><span data-stu-id="9b478-122">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="9b478-123">[リダイレクト **URI] ページで** 、[パブリック クライアント (モバイル、デスクトップ) の推奨リダイレクト **URI] セクションを探** します。</span><span class="sxs-lookup"><span data-stu-id="9b478-123">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="9b478-124">URI を選択 `https://login.microsoftonline.com/common/oauth2/nativeclient` します。</span><span class="sxs-lookup"><span data-stu-id="9b478-124">Select the `https://login.microsoftonline.com/common/oauth2/nativeclient` URI.</span></span>

    ![リダイレクト URI ページのスクリーンショット](/tutorial/images/aad-redirect-uris.png)

1. <span data-ttu-id="9b478-126">[詳細設定 **] セクションを見つけて** 、[パブリック クライアント **フローの** 許可] トグルを [はい] に変更 **し**、[保存] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="9b478-126">Locate the **Advanced settings** section and change the **Allow public client flows** toggle to **Yes**, then choose **Save**.</span></span>

    ![[既定のクライアントの種類] セクションのスクリーンショット](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a><span data-ttu-id="9b478-128">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="9b478-128">Configure the sample</span></span>

1. <span data-ttu-id="9b478-129">.NET [開発シークレット ストアを初期化するには](https://docs.microsoft.com/aspnet/core/security/app-secrets)**、GraphTutorial.csproj** を含むディレクトリで CLI を開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b478-129">Initialize the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="9b478-130">次のコマンドを使用して、アプリケーション ID と必要なスコープの一覧をシークレット ストアに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b478-130">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="9b478-131">`YOUR_APP_ID_HERE`Azure portal で作成したアプリケーション ID に置き換える。</span><span class="sxs-lookup"><span data-stu-id="9b478-131">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="build-and-run-the-sample"></a><span data-ttu-id="9b478-132">サンプルの構築と実行</span><span class="sxs-lookup"><span data-stu-id="9b478-132">Build and run the sample</span></span>

<span data-ttu-id="9b478-133">コマンド ライン インターフェイス (CLI) で、プロジェクト ディレクトリに移動し、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b478-133">In your command-line interface (CLI), navigate to the project directory and run the following commands.</span></span>

```Shell
dotnet restore
dotnet build
dotnet run
```
