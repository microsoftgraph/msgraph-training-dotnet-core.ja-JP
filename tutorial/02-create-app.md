<!-- markdownlint-disable MD002 MD041 -->

まず、 [.Net コア CLI](/dotnet/core/tools/)を使用して、新しい .net コアコンソールプロジェクトを作成します。

1. プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. プロジェクトが作成されたら、現在のディレクトリを **Graphtutorial** ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。

    ```Shell
    dotnet run
    ```

    動作している場合は、アプリが出力さ `Hello World!` れます。

## <a name="install-dependencies"></a>依存関係のインストール

に進む前に、後で使用する追加の依存関係を追加します。

- [ Uration をMicrosoft.Extensions.Configします。](https://github.com/aspnet/extensions) [.Net 開発者シークレットストア](https://docs.microsoft.com/aspnet/core/security/app-secrets)からアプリケーション構成を読み取るための usersecrets。
- [.Net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) は、ユーザーを認証し、アクセストークンを取得します。
- Microsoft graph [.Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して、microsoft graph を呼び出すことができます。
- Windows タイムゾーン識別子を IANA 識別子に変換するための[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) 。

CLI で次のコマンドを実行して、依存関係をインストールします。

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.8
dotnet add package Microsoft.Identity.Client --version 4.19.0
dotnet add package Microsoft.Graph --version 3.15.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、コンソールベースの簡単なメニューを作成します。

テキストエディター ( [Visual Studio Code](https://code.visualstudio.com/)など) で Program.cs を開き、その内容全体を次のコードで置き換えます **。**

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

これは基本的なメニューを実装し、コマンドラインからユーザーの選択を読み取ります。
