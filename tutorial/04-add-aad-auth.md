<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、 [.net 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。

1. **Graphtutorial .csproj**を含むディレクトリで CLI を開き、次のコマンドを実行して、 [.net 開発シークレットストア](/aspnet/core/security/app-secrets)を初期化します。

    ```Shell
    dotnet user-secrets init
    ```

1. 次のコマンドを使用して、アプリケーション ID と必要なスコープのリストをシークレットストアに追加します。 を`YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、Graph SDK で使用できる認証プロバイダーを作成し、[デバイスコードフロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)を使用してアクセストークンを明示的に要求するために使用することもできます。

### <a name="create-an-authentication-provider"></a>認証プロバイダを作成する

1. 「 **Authentication**」という名前を持つ「 **graphtutorial** 」ディレクトリに新しいディレクトリを作成します。
1. **DeviceCodeAuthProvider.cs**という名前の**認証**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

このコードの内容を検討してください。

- MSAL `IPublicClientApplication`実装を使用してトークンを要求および管理します。
- 関数`GetAccessToken`は次のとおりです。
  - デバイスコードフローを使用してまだサインインしていない場合は、ユーザーにサインインします。
  - 返されたトークンが常に最新であること`AcquireTokenSilent`を確認します。この関数は、有効期限が切れていない場合はキャッシュされたトークンを返し、有効期限が切れている場合はトークンを更新します。
- この`IAuthenticationProvider`インターフェイスを実装することで、graph SDK は、クラスを使用してグラフ呼び出しを認証できるようになります。

## <a name="sign-in-and-display-the-access-token"></a>サインインしてアクセストークンを表示する

このセクションでは、アプリケーションを更新して、 `GetAccessToken`ユーザーにサインインする関数を呼び出すようにします。 トークンを表示するコードも追加されます。

1. 次の関数を `Program` クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. 行の`Main` `Console.WriteLine(".NET Core Graph Tutorial\n");`直後の関数に次のコードを追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

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
    > エラーが発生した場合は、 **Program.cs**を[GitHub の例](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs)と比較してください。

1. ブラウザーを開き、表示されている URL を参照します。 提供されたコードを入力し、サインインします。 完了したら、アプリケーションに戻り、1を選択し**ます。** アクセストークンを表示するためのアクセストークンオプションを表示します。
