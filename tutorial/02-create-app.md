<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8ff0d-101">まず、.NET Core CLI を使用して新しい [.NET Core コンソール プロジェクトを作成します](/dotnet/core/tools/)。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="8ff0d-102">プロジェクトを作成するディレクトリでコマンド ライン インターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="8ff0d-103">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="8ff0d-104">プロジェクトが作成された後、現在のディレクトリを **GraphTu clil** ディレクトリに変更し、CLI で次のコマンドを実行して、プロジェクトが動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="8ff0d-105">動作する場合、アプリは出力する必要があります `Hello World!` 。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="8ff0d-106">依存関係をインストールする</span><span class="sxs-lookup"><span data-stu-id="8ff0d-106">Install dependencies</span></span>

<span data-ttu-id="8ff0d-107">次に進む前に、後で使用する依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="8ff0d-108">[Microsoft.Extensions.Configuriation。.NET 開発シークレット](https://github.com/aspnet/extensions) ストアからアプリケーション構成を読 [み取る](https://docs.microsoft.com/aspnet/core/security/app-secrets)UserSecrets。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="8ff0d-109">[ユーザーを認証し、アクセス](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) トークンを取得するための .NET 用 Microsoft 認証ライブラリ (MSAL)。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="8ff0d-110">[Microsoft Graph の呼び出しを](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 行う Microsoft Graph .NET クライアント ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="8ff0d-111">Windows タイム ゾーン識別子を IANA 識別子に変換する[TimeZoneConverter。](https://github.com/mj1856/TimeZoneConverter)</span><span class="sxs-lookup"><span data-stu-id="8ff0d-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

<span data-ttu-id="8ff0d-112">CLI で次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a><span data-ttu-id="8ff0d-113">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="8ff0d-113">Design the app</span></span>

<span data-ttu-id="8ff0d-114">このセクションでは、コンソール ベースの簡単なメニューを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="8ff0d-115">テキスト エディター (Visual Studio [Code](https://code.visualstudio.com/)など) で **./Program.cs Visual Studio** を開き、その内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-115">Open **./Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="8ff0d-116">これにより、基本的なメニューが実装され、コマンド ラインからユーザーの選択が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8ff0d-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
