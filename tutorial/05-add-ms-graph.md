<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9b6bc-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="9b6bc-102">このアプリケーションでは、microsoft [graph .Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="9b6bc-103">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="9b6bc-103">Get user details</span></span>

1. <span data-ttu-id="9b6bc-104">**Graph**という名前の**graphtutorial**ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="9b6bc-105">**GraphHelper.cs**という名前の**Graph**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class GraphHelper
        {
            private static GraphServiceClient graphClient;
            public static void Initialize(IAuthenticationProvider authProvider)
            {
                graphClient = new GraphServiceClient(authProvider);
            }

            public static async Task<User> GetMeAsync()
            {
                try
                {
                    // GET /me
                    return await graphClient.Me.Request().GetAsync();
                }
                catch (ServiceException ex)
                {
                    Console.WriteLine($"Error getting signed-in user: {ex.Message}");
                    return null;
                }
            }
        }
    }
    ```

1. <span data-ttu-id="9b6bc-106">次のコードを`Main` **Program.cs**の呼び出しの`GetAccessToken`直後に追加して、ユーザーを取得し、ユーザーの表示名を出力します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-106">Add the following code in `Main` in **Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="9b6bc-107">アプリをすぐに実行する場合は、アプリにログインした後、名前によって歓迎されます。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="9b6bc-108">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="9b6bc-108">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="9b6bc-109">次の関数を`GraphHelper`クラスに追加して、ユーザーの予定表からイベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="9b6bc-110">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="9b6bc-111">呼び出される URL は `/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-111">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="9b6bc-112">関数`Select`は、各イベントに対して返されるフィールドを、アプリが実際に使用しているものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="9b6bc-113">`OrderBy` 関数は、作成された日時で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-113">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="9b6bc-114">結果の表示</span><span class="sxs-lookup"><span data-stu-id="9b6bc-114">Display the results</span></span>

1. <span data-ttu-id="9b6bc-115">次の関数を`Program`クラスに追加して、Microsoft Graph の[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)プロパティをユーザーフレンドリな形式に書式設定します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-115">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="9b6bc-116">次の関数を`Program`クラスに追加して、ユーザーのイベントを取得し、コンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-116">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="9b6bc-117">`Main`関数のコメントの`// List the calendar`直後に以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-117">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents();
    ```

1. <span data-ttu-id="9b6bc-118">すべての変更を保存し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-118">Save all of your changes and run the app.</span></span> <span data-ttu-id="9b6bc-119">[**カレンダーイベントのリスト**] オプションを選択して、ユーザーのイベントの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="9b6bc-119">Choose the **List calendar events** option to see a list of the user's events.</span></span>

    ```Shell
    Welcome Adele Vance

    Please choose one of the following options:
    0. Exit
    1. Display access token
    2. List calendar events
    2
    Events:
    Subject: Team meeting
      Organizer: Adele Vance
      Start: 5/22/19, 3:00 PM
      End: 5/22/19, 4:00 PM
    Subject: Team Lunch
      Organizer: Adele Vance
      Start: 5/24/19, 6:30 PM
      End: 5/24/19, 8:00 PM
    Subject: Flight to Redmond
      Organizer: Adele Vance
      Start: 5/26/19, 4:30 PM
      End: 5/26/19, 7:00 PM
    Subject: Let's meet to discuss strategy
      Organizer: Patti Fernandez
      Start: 5/27/19, 10:00 PM
      End: 5/27/19, 10:30 PM
    Subject: All-hands meeting
      Organizer: Adele Vance
      Start: 5/28/19, 3:30 PM
      End: 5/28/19, 5:00 PM
    ```
