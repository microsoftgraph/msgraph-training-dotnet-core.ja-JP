<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="99685-101">この演習では、前の演習からアプリケーションを拡張して、Azure サーバーでの認証をサポートAD。</span><span class="sxs-lookup"><span data-stu-id="99685-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="99685-102">これは、Microsoft サーバーを呼び出す必要がある OAuth アクセス トークンを取得するために必要Graph。</span><span class="sxs-lookup"><span data-stu-id="99685-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="99685-103">この手順では [、.NET の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="99685-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="99685-104">.NET [開発シークレット ストアを初期化するには](/aspnet/core/security/app-secrets)**、GraphTutorial.csproj** を含むディレクトリで CLI を開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99685-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="99685-105">次のコマンドを使用して、アプリケーション ID と必要なスコープの一覧をシークレット ストアに追加します。</span><span class="sxs-lookup"><span data-stu-id="99685-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="99685-106">`YOUR_APP_ID_HERE`Azure portal で作成したアプリケーション ID に置き換える。</span><span class="sxs-lookup"><span data-stu-id="99685-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    <span data-ttu-id="99685-107">設定したアクセス許可スコープを見てみます。</span><span class="sxs-lookup"><span data-stu-id="99685-107">Let's look at the permission scopes you just set.</span></span>

    - <span data-ttu-id="99685-108">**User.Read** を使用すると、アプリはサインインしているユーザーのプロファイルを読み取り、表示名や電子メール アドレスなどの情報を取得できます。</span><span class="sxs-lookup"><span data-stu-id="99685-108">**User.Read** will allow the app to read the signed-in user's profile to get information such as display name and email address.</span></span>
    - <span data-ttu-id="99685-109">**MailboxSettings.Read** を使用すると、アプリはユーザーの優先タイム ゾーン、日付形式、および時刻形式を読み取るできます。</span><span class="sxs-lookup"><span data-stu-id="99685-109">**MailboxSettings.Read** will allow the app to read the user's preferred time zone, date format, and time format.</span></span>
    - <span data-ttu-id="99685-110">**Calendars.ReadWrite** を使用すると、アプリはユーザーの予定表の既存のイベントを読み取り、新しいイベントを追加できます。</span><span class="sxs-lookup"><span data-stu-id="99685-110">**Calendars.ReadWrite** will allow the app to read the existing events on the user's calendar and add new events.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="99685-111">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="99685-111">Implement sign-in</span></span>

<span data-ttu-id="99685-112">このセクションでは、クラスを使用して、デバイス コード フローを使用してアクセス `DeviceCodeCredential` トークン [を要求します](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)。</span><span class="sxs-lookup"><span data-stu-id="99685-112">In this section you will use the `DeviceCodeCredential` class to request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

1. <span data-ttu-id="99685-113">GraphTutorial ディレクトリに新 **しいディレクトリを作成** します **。Graph。**</span><span class="sxs-lookup"><span data-stu-id="99685-113">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="99685-114">**GraphHelper.cs** という名前の **Graph** ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="99685-114">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Azure.Core;
    using Azure.Identity;
    using Microsoft.Graph;
    using TimeZoneConverter;

    namespace GraphTutorial
    {
        public class GraphHelper
        {
            private static DeviceCodeCredential tokenCredential;
            private static GraphServiceClient graphClient;

            public static void Initialize(string clientId,
                                          string[] scopes,
                                          Func<DeviceCodeInfo, CancellationToken, Task> callBack)
            {
                tokenCredential = new DeviceCodeCredential(callBack, clientId);
                graphClient = new GraphServiceClient(tokenCredential, scopes);
            }

            public static async Task<string> GetAccessTokenAsync(string[] scopes)
            {
                var context = new TokenRequestContext(scopes);
                var response = await tokenCredential.GetTokenAsync(context);
                return response.Token;
            }
        }
    }
    ```

1. <span data-ttu-id="99685-115">次の関数を `Program` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="99685-115">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="99685-116">行の直後に次の `Main` コードを関数に追加 `Console.WriteLine(".NET Core Graph Tutorial\n");` します。</span><span class="sxs-lookup"><span data-stu-id="99685-116">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="99685-117">行の直後に次の `Main` コードを関数に追加 `// Display access token` します。</span><span class="sxs-lookup"><span data-stu-id="99685-117">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="99685-118">アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="99685-118">Build and run the app.</span></span> <span data-ttu-id="99685-119">アプリケーションは URL とデバイス コードを表示します。</span><span class="sxs-lookup"><span data-stu-id="99685-119">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="99685-120">エラーが発生した場合は **、Program.cs** を次の例と [比較GitHub。](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs)</span><span class="sxs-lookup"><span data-stu-id="99685-120">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="99685-121">ブラウザーを開き、表示される URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="99685-121">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="99685-122">指定されたコードを入力し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="99685-122">Enter the provided code and sign in.</span></span> <span data-ttu-id="99685-123">完了したら、アプリケーションに戻り **、1 を選択します。アクセス トークンを表示** するアクセス トークン オプションを表示します。</span><span class="sxs-lookup"><span data-stu-id="99685-123">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
