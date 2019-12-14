<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b75c7-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="b75c7-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="b75c7-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="b75c7-103">この手順では、 [.net 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="b75c7-104">新しいファイルを、 **appsettings**という名前の**graphtutorial**のディレクトリに作成します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-104">Create a new file in the **GraphTutorial** directory named **appsettings.json**.</span></span> <span data-ttu-id="b75c7-105">そのファイルに次のテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-105">Add the following text in that file.</span></span>

```json
{
  "appId": "YOUR_APP_ID_HERE",
  "scopes": [
    "User.Read",
    "Calendars.Read"
  ]
}
```

<span data-ttu-id="b75c7-106">を`YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b75c7-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b75c7-107">Git などのソース管理を使用している場合は、この時点で、ソース管理からその**appsettings**ファイルを除外して、アプリ ID が誤ってリークしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b75c7-107">If you're using source control such as git, now would be a good time to exclude the **appsettings.json** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="b75c7-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="b75c7-108">Implement sign-in</span></span>

<span data-ttu-id="b75c7-109">このセクションでは、Graph SDK で使用できる認証プロバイダーを作成し、[デバイスコードフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)を使用してアクセストークンを明示的に要求するために使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="b75c7-109">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="b75c7-110">認証プロバイダを作成する</span><span class="sxs-lookup"><span data-stu-id="b75c7-110">Create an authentication provider</span></span>

1. <span data-ttu-id="b75c7-111">「 **Authentication**」という名前を持つ「 **graphtutorial** 」ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-111">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="b75c7-112">**DeviceCodeAuthProvider.cs**という名前の**認証**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-112">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Microsoft.Identity.Client;
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class DeviceCodeAuthProvider : IAuthenticationProvider
        {
            private IPublicClientApplication _msalClient;
            private string[] _scopes;
            private IAccount _userAccount;

            public DeviceCodeAuthProvider(string appId, string[] scopes)
            {
                _scopes = scopes;

                _msalClient = PublicClientApplicationBuilder
                    .Create(appId)
                    .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount, true)
                    .Build();
            }

            public async Task<string> GetAccessToken()
            {
                // If there is no saved user account, the user must sign-in
                if (_userAccount == null)
                {
                    try
                    {
                        // Invoke device code flow so user can sign-in with a browser
                        var result = await _msalClient.AcquireTokenWithDeviceCode(_scopes, callback => {
                            Console.WriteLine(callback.Message);
                            return Task.FromResult(0);
                        }).ExecuteAsync();

                        _userAccount = result.Account;
                        return result.AccessToken;
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"Error getting access token: {exception.Message}");
                        return null;
                    }
                }
                else
                {
                    // If there is an account, call AcquireTokenSilent
                    // By doing this, MSAL will refresh the token automatically if
                    // it is expired. Otherwise it returns the cached token.

                        var result = await _msalClient
                            .AcquireTokenSilent(_scopes, _userAccount)
                            .ExecuteAsync();

                       return result.AccessToken;
                }
            }

            // This is the required function to implement IAuthenticationProvider
            // The Graph SDK will call this function each time it makes a Graph
            // call.
            public async Task AuthenticateRequestAsync(HttpRequestMessage requestMessage)
            {
                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("bearer", await GetAccessToken());
            }
        }
    }
    ```

<span data-ttu-id="b75c7-113">このコードの内容を検討してください。</span><span class="sxs-lookup"><span data-stu-id="b75c7-113">Consider what this code does.</span></span>

- <span data-ttu-id="b75c7-114">MSAL `IPublicClientApplication`実装を使用してトークンを要求および管理します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-114">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="b75c7-115">関数`GetAccessToken`は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b75c7-115">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="b75c7-116">デバイスコードフローを使用してまだサインインしていない場合は、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="b75c7-116">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="b75c7-117">返されたトークンが常に最新であること`AcquireTokenSilent`を確認します。この関数は、有効期限が切れていない場合はキャッシュされたトークンを返し、有効期限が切れている場合はトークンを更新します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-117">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="b75c7-118">この`IAuthenticationProvider`インターフェイスを実装することで、graph SDK は、クラスを使用してグラフ呼び出しを認証できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b75c7-118">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="b75c7-119">サインインしてアクセストークンを表示する</span><span class="sxs-lookup"><span data-stu-id="b75c7-119">Sign in and display the access token</span></span>

<span data-ttu-id="b75c7-120">このセクションでは、アプリケーションを更新して、 `GetAccessToken`ユーザーにサインインする関数を呼び出すようにします。</span><span class="sxs-lookup"><span data-stu-id="b75c7-120">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="b75c7-121">トークンを表示するコードも追加されます。</span><span class="sxs-lookup"><span data-stu-id="b75c7-121">You will also add code to display the token.</span></span>

1. <span data-ttu-id="b75c7-122">**Program.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-122">Open **Program.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.Extensions.Configuration;
    using System.IO;
    ```

1. <span data-ttu-id="b75c7-123">次の関数を `Program` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-123">Add the following function to the `Program` class.</span></span>

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", false, true)
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            // Make sure there's at least one value in the scopes array
            string.IsNullOrEmpty(appConfig["scopes:0"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. <span data-ttu-id="b75c7-124">行の`Main` `Console.WriteLine(".NET Core Graph Tutorial\n");`直後の関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-124">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopes = appConfig.GetSection("scopes").Get<string[]>();

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. <span data-ttu-id="b75c7-125">行の`Main` `// Display access token`直後の関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-125">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="b75c7-126">アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-126">Build and run the app.</span></span> <span data-ttu-id="b75c7-127">アプリケーションには、URL とデバイスコードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b75c7-127">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="b75c7-128">エラーが発生した場合は、 **Program.cs**を[GitHub の例](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs)と比較してください。</span><span class="sxs-lookup"><span data-stu-id="b75c7-128">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="b75c7-129">ブラウザーを開き、表示されている URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-129">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="b75c7-130">提供されたコードを入力し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="b75c7-130">Enter the provided code and sign in.</span></span> <span data-ttu-id="b75c7-131">完了したら、アプリケーションに戻り、1を選択し**ます。** アクセストークンを表示するためのアクセストークンオプションを表示します。</span><span class="sxs-lookup"><span data-stu-id="b75c7-131">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
