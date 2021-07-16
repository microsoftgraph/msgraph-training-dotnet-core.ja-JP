<!-- markdownlint-disable MD002 MD041 -->

まず、.NET Core CLI を使用して新しい .NET Core コンソール [プロジェクトを作成します](/dotnet/core/tools/)。

1. プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。 次のコマンドを実行します。

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. プロジェクトが作成されると、現在のディレクトリを **GraphTutorial** ディレクトリに変更し、CLI で次のコマンドを実行して動作します。

    ```Shell
    dotnet run
    ```

    動作する場合、アプリは出力する必要があります `Hello World!` 。

## <a name="install-dependencies"></a>依存関係のインストール

次に進む前に、後で使用する依存関係を追加します。

- [Microsoft.Extensions.Configを指定します。UserSecrets を](https://github.com/aspnet/extensions) 使用して、.NET 開発シークレット ストアから [アプリケーション構成を読み取る](https://docs.microsoft.com/aspnet/core/security/app-secrets)。
- [Azure SDK クライアント ライブラリ for Azure Identity](https://github.com/Azure/azure-sdk-for-net) を使用して、ユーザーを認証し、アクセス トークンを取得します。
- [Microsoft Graph .NET クライアント ライブラリを使用して](https://github.com/microsoftgraph/msgraph-sdk-dotnet)Microsoft クライアント ライブラリを呼び出Graph。
- タイム ゾーン識別子を IANA 識別子Windows変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)

依存関係をインストールするには、CLI で次のコマンドを実行します。

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Azure.Identity --version 1.4.0
dotnet add package Microsoft.Graph --version 4.0.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、単純なコンソール ベースのメニューを作成します。

テキスト エディター (Visual Studio Code など) で **./Program.cs** を開き [、その内容全体](https://code.visualstudio.com/)を次のコードに置き換えます。

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Extensions.Configuration;

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

これにより、基本的なメニューが実装され、コマンド ラインからユーザーの選択項目が読み取ります。
