<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、 [.net 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。

**Graphtutorial .csproj**を含むディレクトリで CLI を開き、次のコマンドを実行して、 [.net 開発シークレットストア](https://docs.microsoft.com/aspnet/core/security/app-secrets)を初期化します。

```Shell
dotnet user-secrets init
```

次に、以下のコマンドを使用して、アプリケーション ID と必要なスコープのリストをシークレットストアに追加します。 を`YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。

```Shell
dotnet user-secrets set appId "YOUR_APP_ID_HERE"
dotnet user-secrets set scopes "User.Read;Calendars.Read"
```

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、Graph SDK で使用できる認証プロバイダーを作成し、[デバイスコードフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)を使用してアクセストークンを明示的に要求するために使用することもできます。

### <a name="create-an-authentication-provider"></a>認証プロバイダを作成する

1. 「 **Authentication**」という名前を持つ「 **graphtutorial** 」ディレクトリに新しいディレクトリを作成します。
1. **DeviceCodeAuthProvider.cs**という名前の**認証**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。

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

このコードの内容を検討してください。

- MSAL `IPublicClientApplication`実装を使用してトークンを要求および管理します。
- 関数`GetAccessToken`は次のとおりです。
  - デバイスコードフローを使用してまだサインインしていない場合は、ユーザーにサインインします。
  - 返されたトークンが常に最新であること`AcquireTokenSilent`を確認します。この関数は、有効期限が切れていない場合はキャッシュされたトークンを返し、有効期限が切れている場合はトークンを更新します。
- この`IAuthenticationProvider`インターフェイスを実装することで、graph SDK は、クラスを使用してグラフ呼び出しを認証できるようになります。

## <a name="sign-in-and-display-the-access-token"></a>サインインしてアクセストークンを表示する

このセクションでは、アプリケーションを更新して、 `GetAccessToken`ユーザーにサインインする関数を呼び出すようにします。 トークンを表示するコードも追加されます。

1. **Program.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. 次の関数を `Program` クラスに追加します。

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .AddUserSecrets<Program>()
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            string.IsNullOrEmpty(appConfig["scopes"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. 行の`Main` `Console.WriteLine(".NET Core Graph Tutorial\n");`直後の関数に次のコードを追加します。

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopesString = appConfig["scopes"];
    var scopes = scopesString.Split(';');

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. 行の`Main` `// Display access token`直後の関数に次のコードを追加します。

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. アプリをビルドして実行します。 アプリケーションには、URL とデバイスコードが表示されます。

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > エラーが発生した場合は、 **Program.cs**を[GitHub の例](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs)と比較してください。

1. ブラウザーを開き、表示されている URL を参照します。 提供されたコードを入力し、サインインします。 完了したら、アプリケーションに戻り、1を選択し**ます。** アクセストークンを表示するためのアクセストークンオプションを表示します。
