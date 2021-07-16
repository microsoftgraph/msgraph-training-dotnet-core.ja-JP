<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5917b-101">まず、.NET Core CLI を使用して新しい .NET Core コンソール [プロジェクトを作成します](/dotnet/core/tools/)。</span><span class="sxs-lookup"><span data-stu-id="5917b-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="5917b-102">プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="5917b-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="5917b-103">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5917b-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="5917b-104">プロジェクトが作成されると、現在のディレクトリを **GraphTutorial** ディレクトリに変更し、CLI で次のコマンドを実行して動作します。</span><span class="sxs-lookup"><span data-stu-id="5917b-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="5917b-105">動作する場合、アプリは出力する必要があります `Hello World!` 。</span><span class="sxs-lookup"><span data-stu-id="5917b-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="5917b-106">依存関係のインストール</span><span class="sxs-lookup"><span data-stu-id="5917b-106">Install dependencies</span></span>

<span data-ttu-id="5917b-107">次に進む前に、後で使用する依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="5917b-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="5917b-108">[Microsoft.Extensions.Configを指定します。UserSecrets を](https://github.com/aspnet/extensions) 使用して、.NET 開発シークレット ストアから [アプリケーション構成を読み取る](https://docs.microsoft.com/aspnet/core/security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5917b-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="5917b-109">[Azure SDK クライアント ライブラリ for Azure Identity](https://github.com/Azure/azure-sdk-for-net) を使用して、ユーザーを認証し、アクセス トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="5917b-109">[Azure SDK Client Library for Azure Identity](https://github.com/Azure/azure-sdk-for-net) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="5917b-110">[Microsoft Graph .NET クライアント ライブラリを使用して](https://github.com/microsoftgraph/msgraph-sdk-dotnet)Microsoft クライアント ライブラリを呼び出Graph。</span><span class="sxs-lookup"><span data-stu-id="5917b-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="5917b-111">タイム ゾーン識別子を IANA 識別子Windows変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)</span><span class="sxs-lookup"><span data-stu-id="5917b-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

<span data-ttu-id="5917b-112">依存関係をインストールするには、CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5917b-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Azure.Identity --version 1.4.0
dotnet add package Microsoft.Graph --version 4.0.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a><span data-ttu-id="5917b-113">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="5917b-113">Design the app</span></span>

<span data-ttu-id="5917b-114">このセクションでは、単純なコンソール ベースのメニューを作成します。</span><span class="sxs-lookup"><span data-stu-id="5917b-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="5917b-115">テキスト エディター (Visual Studio Code など) で **./Program.cs** を開き [、その内容全体](https://code.visualstudio.com/)を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5917b-115">Open **./Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="5917b-116">これにより、基本的なメニューが実装され、コマンド ラインからユーザーの選択項目が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5917b-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
