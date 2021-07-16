<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習からアプリケーションを拡張して、Azure サーバーでの認証をサポートAD。 これは、Microsoft サーバーを呼び出す必要がある OAuth アクセス トークンを取得するために必要Graph。 この手順では [、.NET の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。

1. .NET [開発シークレット ストアを初期化するには](/aspnet/core/security/app-secrets)**、GraphTutorial.csproj** を含むディレクトリで CLI を開き、次のコマンドを実行します。

    ```Shell
    dotnet user-secrets init
    ```

1. 次のコマンドを使用して、アプリケーション ID と必要なスコープの一覧をシークレット ストアに追加します。 `YOUR_APP_ID_HERE`Azure portal で作成したアプリケーション ID に置き換える。

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    設定したアクセス許可スコープを見てみます。

    - **User.Read** を使用すると、アプリはサインインしているユーザーのプロファイルを読み取り、表示名や電子メール アドレスなどの情報を取得できます。
    - **MailboxSettings.Read** を使用すると、アプリはユーザーの優先タイム ゾーン、日付形式、および時刻形式を読み取るできます。
    - **Calendars.ReadWrite** を使用すると、アプリはユーザーの予定表の既存のイベントを読み取り、新しいイベントを追加できます。

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、クラスを使用して、デバイス コード フローを使用してアクセス `DeviceCodeCredential` トークン [を要求します](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)。

1. GraphTutorial ディレクトリに新 **しいディレクトリを作成** します **。Graph。**
1. **GraphHelper.cs** という名前の **Graph** ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。

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

1. 次の関数を `Program` クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. 行の直後に次の `Main` コードを関数に追加 `Console.WriteLine(".NET Core Graph Tutorial\n");` します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. 行の直後に次の `Main` コードを関数に追加 `// Display access token` します。

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. アプリをビルドして実行します。 アプリケーションは URL とデバイス コードを表示します。

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > エラーが発生した場合は **、Program.cs** を次の例と [比較GitHub。](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs)

1. ブラウザーを開き、表示される URL を参照します。 指定されたコードを入力し、サインインします。 完了したら、アプリケーションに戻り **、1 を選択します。アクセス トークンを表示** するアクセス トークン オプションを表示します。
