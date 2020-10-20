<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e0a31-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e0a31-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="e0a31-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="e0a31-103">この手順では、 [.net 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="e0a31-104">**Graphtutorial .csproj**を含むディレクトリで CLI を開き、次のコマンドを実行して、 [.net 開発シークレットストア](/aspnet/core/security/app-secrets)を初期化します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="e0a31-105">次のコマンドを使用して、アプリケーション ID と必要なスコープのリストをシークレットストアに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="e0a31-106">を `YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    <span data-ttu-id="e0a31-107">ここでは、設定したアクセス許可のスコープを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="e0a31-107">Let's look at the permission scopes you just set.</span></span>

    - <span data-ttu-id="e0a31-108">**ユーザー** がサインインしているユーザーのプロファイルをアプリで読み取れるようにして、表示名や電子メールアドレスなどの情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-108">**User.Read** will allow the app to read the signed-in user's profile to get information such as display name and email address.</span></span>
    - <span data-ttu-id="e0a31-109">**MailboxSettings** を使用すると、アプリでユーザーの優先タイムゾーン、日付形式、および時刻形式を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-109">**MailboxSettings.Read** will allow the app to read the user's preferred time zone, date format, and time format.</span></span>
    - <span data-ttu-id="e0a31-110">この場合、ユーザーの予定表にある既存のイベントをアプリで読み取り、新しいイベントを追加することができ**ます**。</span><span class="sxs-lookup"><span data-stu-id="e0a31-110">**Calendars.ReadWrite** will allow the app to read the existing events on the user's calendar and add new events.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="e0a31-111">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="e0a31-111">Implement sign-in</span></span>

<span data-ttu-id="e0a31-112">このセクションでは、Graph SDK で使用できる認証プロバイダーを作成し、 [デバイスコードフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)を使用してアクセストークンを明示的に要求するために使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-112">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="e0a31-113">認証プロバイダを作成する</span><span class="sxs-lookup"><span data-stu-id="e0a31-113">Create an authentication provider</span></span>

1. <span data-ttu-id="e0a31-114">「 **Authentication**」という名前を持つ「 **graphtutorial** 」ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-114">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="e0a31-115">**DeviceCodeAuthProvider.cs**という名前の**認証**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-115">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="e0a31-116">このコードの内容を検討してください。</span><span class="sxs-lookup"><span data-stu-id="e0a31-116">Consider what this code does.</span></span>

- <span data-ttu-id="e0a31-117">MSAL 実装を使用して `IPublicClientApplication` トークンを要求および管理します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-117">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="e0a31-118">`GetAccessToken`関数は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e0a31-118">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="e0a31-119">デバイスコードフローを使用してまだサインインしていない場合は、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e0a31-119">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="e0a31-120">返されたトークンが常に最新であることを確認します `AcquireTokenSilent` 。この関数は、有効期限が切れていない場合はキャッシュされたトークンを返し、有効期限が切れている場合はトークンを更新します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-120">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="e0a31-121">このインターフェイスを実装することで、Graph SDK は、クラスを使用して `IAuthenticationProvider` グラフ呼び出しを認証できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e0a31-121">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="e0a31-122">サインインしてアクセストークンを表示する</span><span class="sxs-lookup"><span data-stu-id="e0a31-122">Sign in and display the access token</span></span>

<span data-ttu-id="e0a31-123">このセクションでは、アプリケーションを更新して、ユーザーにサインインする関数を呼び出すようにし `GetAccessToken` ます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-123">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="e0a31-124">トークンを表示するコードも追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-124">You will also add code to display the token.</span></span>

1. <span data-ttu-id="e0a31-125">次の関数を `Program` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-125">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="e0a31-126">行の直後の関数に次のコードを追加し `Main` `Console.WriteLine(".NET Core Graph Tutorial\n");` ます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-126">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="e0a31-127">行の直後の関数に次のコードを追加し `Main` `// Display access token` ます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-127">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="e0a31-128">アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-128">Build and run the app.</span></span> <span data-ttu-id="e0a31-129">アプリケーションには、URL とデバイスコードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0a31-129">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="e0a31-130">エラーが発生した場合は、 **Program.cs** を [GitHub の例](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs)と比較してください。</span><span class="sxs-lookup"><span data-stu-id="e0a31-130">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="e0a31-131">ブラウザーを開き、表示されている URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-131">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="e0a31-132">提供されたコードを入力し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="e0a31-132">Enter the provided code and sign in.</span></span> <span data-ttu-id="e0a31-133">完了したら、アプリケーションに戻り、1を選択し **ます。** アクセストークンを表示するためのアクセストークンオプションを表示します。</span><span class="sxs-lookup"><span data-stu-id="e0a31-133">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
