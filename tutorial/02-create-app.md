<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a2398-101">まず、 [.Net コア CLI](/dotnet/core/tools/?tabs=netcore2x)を使用して、新しい .net コアコンソールプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a2398-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x).</span></span>

1. <span data-ttu-id="a2398-102">プロジェクトを作成するディレクトリで、コマンドラインインターフェイス (CLI) を開きます。</span><span class="sxs-lookup"><span data-stu-id="a2398-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="a2398-103">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a2398-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="a2398-104">プロジェクトが作成されたら、現在のディレクトリを**Graphtutorial**ディレクトリに変更し、CLI で次のコマンドを実行して、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="a2398-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="a2398-105">動作している場合は、アプリ`Hello World!`が出力されます。</span><span class="sxs-lookup"><span data-stu-id="a2398-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="a2398-106">依存関係のインストール</span><span class="sxs-lookup"><span data-stu-id="a2398-106">Install dependencies</span></span>

<span data-ttu-id="a2398-107">に進む前に、後で使用する追加の依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="a2398-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="a2398-108">JSON ファイルからアプリケーション構成を読み取るための[Microsoft Extensions。](https://github.com/aspnet/Extensions)</span><span class="sxs-lookup"><span data-stu-id="a2398-108">[Microsoft.Extensions.Configuration](https://github.com/aspnet/Extensions) to read application configuration from a JSON file.</span></span>
- <span data-ttu-id="a2398-109">[.Net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)は、ユーザーを認証し、アクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="a2398-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="a2398-110">Microsoft graph [.Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して、microsoft graph を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a2398-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="a2398-111">[Microsoft graph .NET SDK の認証プロバイダ](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth)。これにより、Graph クライアントライブラリは、API 呼び出しを行うときにトークンを自動的に要求できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a2398-111">[Authentication Providers for Microsoft Graph .NET SDK](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth) to enable the Graph client library to request tokens automatically when making API calls.</span></span>

<span data-ttu-id="a2398-112">CLI で次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a2398-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.FileExtensions --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Json --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Binder --version 3.1.0
dotnet add package Microsoft.Identity.Client --version 4.7.1
dotnet add package Microsoft.Graph --version 1.21.0
```

## <a name="design-the-app"></a><span data-ttu-id="a2398-113">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="a2398-113">Design the app</span></span>

<span data-ttu-id="a2398-114">このセクションでは、コンソールベースの簡単なメニューを作成します。</span><span class="sxs-lookup"><span data-stu-id="a2398-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="a2398-115">テキストエディター ( [Visual Studio Code](https://code.visualstudio.com/)など) で**Program.cs**を開き、その内容全体を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a2398-115">Open **Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

<span data-ttu-id="a2398-116">これは基本的なメニューを実装し、コマンドラインからユーザーの選択を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="a2398-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
