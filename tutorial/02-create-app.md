<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5892b-101">まず、 [.Net コア CLI](/dotnet/core/tools/)を使用して、新しい .net コアコンソールプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5892b-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="5892b-102">プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="5892b-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="5892b-103">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5892b-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="5892b-104">プロジェクトが作成されたら、現在のディレクトリを**Graphtutorial**ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="5892b-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="5892b-105">動作している場合は、アプリ`Hello World!`が出力されます。</span><span class="sxs-lookup"><span data-stu-id="5892b-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="5892b-106">依存関係のインストール</span><span class="sxs-lookup"><span data-stu-id="5892b-106">Install dependencies</span></span>

<span data-ttu-id="5892b-107">に進む前に、後で使用する追加の依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="5892b-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="5892b-108">[.Net 開発者シークレットストア](https://docs.microsoft.com/aspnet/core/security/app-secrets)からアプリケーション構成を読み取るための[usersecrets](https://github.com/aspnet/extensions) 。</span><span class="sxs-lookup"><span data-stu-id="5892b-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="5892b-109">[.Net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)は、ユーザーを認証し、アクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="5892b-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="5892b-110">Microsoft graph [.Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して、microsoft graph を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="5892b-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>

<span data-ttu-id="5892b-111">CLI で次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="5892b-111">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.2
dotnet add package Microsoft.Identity.Client --version 4.10.0
dotnet add package Microsoft.Graph --version 3.0.1
```

## <a name="design-the-app"></a><span data-ttu-id="5892b-112">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="5892b-112">Design the app</span></span>

<span data-ttu-id="5892b-113">このセクションでは、コンソールベースの簡単なメニューを作成します。</span><span class="sxs-lookup"><span data-stu-id="5892b-113">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="5892b-114">テキストエディター ( [Visual Studio Code](https://code.visualstudio.com/)など) で**Program.cs**を開き、その内容全体を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5892b-114">Open **Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using System;

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
                Console.WriteLine("2. List calendar events");

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
                    default:
                        Console.WriteLine("Invalid choice! Please try again.");
                        break;
                }
            }
        }
    }
}
```

<span data-ttu-id="5892b-115">これは基本的なメニューを実装し、コマンドラインからユーザーの選択を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5892b-115">This implements a basic menu and reads the user's choice from the command line.</span></span>
