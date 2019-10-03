<!-- markdownlint-disable MD002 MD041 -->

まず、 [.Net コア CLI](/dotnet/core/tools/?tabs=netcore2x)を使用して、新しい .net コアコンソールプロジェクトを作成します。

1. プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. プロジェクトが作成されたら、現在のディレクトリを**Graphtutorial**ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。

    ```Shell
    dotnet run
    ```

    動作している場合は、アプリ`Hello World!`が出力されます。

## <a name="install-dependencies"></a>依存関係のインストール

に進む前に、後で使用する追加の依存関係を追加します。

- JSON ファイルからアプリケーション構成を読み取るための[Microsoft Extensions。](https://github.com/aspnet/Extensions)
- [.Net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)は、ユーザーを認証し、アクセストークンを取得します。
- Microsoft graph [.Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して、microsoft graph を呼び出すことができます。
- [Microsoft graph .NET SDK の認証プロバイダ](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth)。これにより、Graph クライアントライブラリは、API 呼び出しを行うときにトークンを自動的に要求できるようになります。

CLI で次のコマンドを実行して、依存関係をインストールします。

```Shell
dotnet add package Microsoft.Extensions.Configuration --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.FileExtensions --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.Json --version 2.2.0
dotnet add package Microsoft.Extensions.Configuration.Binder --version 2.2.0
dotnet add package Microsoft.Identity.Client --version 4.3.1
dotnet add package Microsoft.Graph --version 1.17.0
```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、コンソールベースの簡単なメニューを作成します。

テキストエディター ( [Visual Studio Code](https://code.visualstudio.com/)など) で**Program.cs**を開き、その内容全体を次のコードで置き換えます。

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

これは基本的なメニューを実装し、コマンドラインからユーザーの選択を読み取ります。
