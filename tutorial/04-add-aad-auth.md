<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="71db4-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="71db4-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="71db4-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="71db4-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="71db4-103">この手順では、 [.net 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="71db4-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="71db4-104">**Graphtutorial .csproj**を含むディレクトリで CLI を開き、次のコマンドを実行して、 [.net 開発シークレットストア](/aspnet/core/security/app-secrets)を初期化します。</span><span class="sxs-lookup"><span data-stu-id="71db4-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="71db4-105">次のコマンドを使用して、アプリケーション ID と必要なスコープのリストをシークレットストアに追加します。</span><span class="sxs-lookup"><span data-stu-id="71db4-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="71db4-106">を`YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71db4-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="71db4-107">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="71db4-107">Implement sign-in</span></span>

<span data-ttu-id="71db4-108">このセクションでは、Graph SDK で使用できる認証プロバイダーを作成し、[デバイスコードフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)を使用してアクセストークンを明示的に要求するために使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="71db4-108">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="71db4-109">認証プロバイダを作成する</span><span class="sxs-lookup"><span data-stu-id="71db4-109">Create an authentication provider</span></span>

1. <span data-ttu-id="71db4-110">「 **Authentication**」という名前を持つ「 **graphtutorial** 」ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="71db4-110">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="71db4-111">**DeviceCodeAuthProvider.cs**という名前の**認証**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="71db4-111">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="71db4-112">このコードの内容を検討してください。</span><span class="sxs-lookup"><span data-stu-id="71db4-112">Consider what this code does.</span></span>

- <span data-ttu-id="71db4-113">MSAL `IPublicClientApplication`実装を使用してトークンを要求および管理します。</span><span class="sxs-lookup"><span data-stu-id="71db4-113">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="71db4-114">関数`GetAccessToken`は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="71db4-114">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="71db4-115">デバイスコードフローを使用してまだサインインしていない場合は、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="71db4-115">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="71db4-116">返されたトークンが常に最新であること`AcquireTokenSilent`を確認します。この関数は、有効期限が切れていない場合はキャッシュされたトークンを返し、有効期限が切れている場合はトークンを更新します。</span><span class="sxs-lookup"><span data-stu-id="71db4-116">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="71db4-117">この`IAuthenticationProvider`インターフェイスを実装することで、graph SDK は、クラスを使用してグラフ呼び出しを認証できるようになります。</span><span class="sxs-lookup"><span data-stu-id="71db4-117">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="71db4-118">サインインしてアクセストークンを表示する</span><span class="sxs-lookup"><span data-stu-id="71db4-118">Sign in and display the access token</span></span>

<span data-ttu-id="71db4-119">このセクションでは、アプリケーションを更新して、 `GetAccessToken`ユーザーにサインインする関数を呼び出すようにします。</span><span class="sxs-lookup"><span data-stu-id="71db4-119">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="71db4-120">トークンを表示するコードも追加されます。</span><span class="sxs-lookup"><span data-stu-id="71db4-120">You will also add code to display the token.</span></span>

1. <span data-ttu-id="71db4-121">次の関数を `Program` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="71db4-121">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="71db4-122">行の`Main` `Console.WriteLine(".NET Core Graph Tutorial\n");`直後の関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="71db4-122">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="71db4-123">行の`Main` `// Display access token`直後の関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="71db4-123">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="71db4-124">アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="71db4-124">Build and run the app.</span></span> <span data-ttu-id="71db4-125">アプリケーションには、URL とデバイスコードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="71db4-125">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="71db4-126">エラーが発生した場合は、 **Program.cs**を[GitHub の例](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs)と比較してください。</span><span class="sxs-lookup"><span data-stu-id="71db4-126">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="71db4-127">ブラウザーを開き、表示されている URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="71db4-127">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="71db4-128">提供されたコードを入力し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="71db4-128">Enter the provided code and sign in.</span></span> <span data-ttu-id="71db4-129">完了したら、アプリケーションに戻り、1を選択し**ます。** アクセストークンを表示するためのアクセストークンオプションを表示します。</span><span class="sxs-lookup"><span data-stu-id="71db4-129">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
