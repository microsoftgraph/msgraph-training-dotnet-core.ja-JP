<!-- markdownlint-disable MD002 MD041 -->

まず、.NET Core CLI を使用して新しい [.NET Core コンソール プロジェクトを作成します](/dotnet/core/tools/)。

1. プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. プロジェクトが作成された後、現在のディレクトリを **GraphTu clil** ディレクトリに変更し、CLI で次のコマンドを実行して、プロジェクトが動作を確認します。

    ```Shell
    dotnet run
    ```

    動作する場合、アプリは出力する必要があります `Hello World!` 。

## <a name="install-dependencies"></a>依存関係をインストールする

次に進む前に、後で使用する依存関係を追加します。

- [Microsoft.Extensions.Configuriation。.NET 開発シークレット](https://github.com/aspnet/extensions) ストアからアプリケーション構成を読 [み取る](https://docs.microsoft.com/aspnet/core/security/app-secrets)UserSecrets。
- [ユーザーを認証し、アクセス](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) トークンを取得するための .NET 用 Microsoft 認証ライブラリ (MSAL)。
- [Microsoft Graph の呼び出しを](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 行う Microsoft Graph .NET クライアント ライブラリ。
- Windows タイム ゾーン識別子を IANA 識別子に変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)

CLI で次のコマンドを実行して、依存関係をインストールします。

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、コンソール ベースの簡単なメニューを作成します。

テキスト エディター (Visual Studio [Code](https://code.visualstudio.com/)など) で **./Program.cs Visual Studio** を開き、その内容全体を次のコードに置き換えます。

```csharp
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;

namespace GraphTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(".NET Core Graph Tutorial\n");

            int choice = -1;

            while (choice != 0) {
                Console.WriteLine("Please choose one of the following options:");
                Console.WriteLine("0. Exit");
                Console.WriteLine("1. Display access token");
                Console.WriteLine("2. View this week's calendar");
                Console.WriteLine("3. Add an event");

                try
                {
                    choice = int.Parse(Console.ReadLine());
                }
                catch (System.FormatException)
                {
                    // Set to invalid value
                    choice = -1;
                }

                switch(choice)
                {
                    case 0:
                        // Exit the program
                        Console.WriteLine("Goodbye...");
                        break;
                    case 1:
                        // Display access token
                        break;
                    case 2:
                        // List the calendar
                        break;
                    case 3:
                        // Create a new event
                        break;
                    default:
                        Console.WriteLine("Invalid choice! Please try again.");
                        break;
                }
            }
        }
    }
}
```

これにより、基本的なメニューが実装され、コマンド ラインからユーザーの選択が読み取ります。
