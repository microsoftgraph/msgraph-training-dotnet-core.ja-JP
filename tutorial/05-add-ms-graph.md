<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="66189-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="66189-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="66189-102">このアプリケーションでは、microsoft [graph .Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet) を使用して microsoft graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="66189-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="66189-103">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="66189-103">Get user details</span></span>

1. <span data-ttu-id="66189-104">**Graph**という名前の**graphtutorial**ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="66189-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="66189-105">**GraphHelper.cs**という名前の**Graph**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="66189-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

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
                    return await graphClient.Me
                        .Request()
                        .Select(u => new{
                            u.DisplayName,
                            u.MailboxSettings
                        })
                        .GetAsync();
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

1. <span data-ttu-id="66189-106">ユーザーを取得して `Main` ユーザーの表示名を出力する呼び出しの直後に、次のコードを追加**します。/Program.cs。** `GetAccessToken`</span><span class="sxs-lookup"><span data-stu-id="66189-106">Add the following code in `Main` in **./Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="66189-107">アプリをすぐに実行する場合は、アプリにログインした後、名前によって歓迎されます。</span><span class="sxs-lookup"><span data-stu-id="66189-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-a-calendar-view"></a><span data-ttu-id="66189-108">予定表ビューを取得する</span><span class="sxs-lookup"><span data-stu-id="66189-108">Get a calendar view</span></span>

1. <span data-ttu-id="66189-109">次の関数をクラスに追加して、 `GraphHelper` ユーザーの予定表からイベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="66189-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="66189-110">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="66189-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="66189-111">呼び出される URL は `/me/calendarview` です。</span><span class="sxs-lookup"><span data-stu-id="66189-111">The URL that will be called is `/me/calendarview`.</span></span>
- <span data-ttu-id="66189-112">`startDateTime`パラメーターとは、 `endDateTime` カレンダービューの開始と終了を定義します。</span><span class="sxs-lookup"><span data-stu-id="66189-112">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
- <span data-ttu-id="66189-113">`Prefer: outlook.timezone`ヘッダーにより、 `start` イベントが `end` ユーザーのタイムゾーンで返されます。</span><span class="sxs-lookup"><span data-stu-id="66189-113">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
- <span data-ttu-id="66189-114">関数は、 `Top` 最大50イベントを要求します。</span><span class="sxs-lookup"><span data-stu-id="66189-114">The `Top` function requests at most 50 events.</span></span>
- <span data-ttu-id="66189-115">関数は、 `Select` 各イベントに対して返されるフィールドを、アプリが実際に使用しているものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="66189-115">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="66189-116">関数は、 `OrderBy` 開始日時で結果を並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="66189-116">The `OrderBy` function sorts the results by the start date and time.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="66189-117">結果の表示</span><span class="sxs-lookup"><span data-stu-id="66189-117">Display the results</span></span>

1. <span data-ttu-id="66189-118">次の関数をクラスに追加して、 `Program` Microsoft Graph の [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) プロパティをユーザーフレンドリな形式に書式設定します。</span><span class="sxs-lookup"><span data-stu-id="66189-118">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="66189-119">次の関数をクラスに追加して、 `Program` ユーザーのイベントを取得し、コンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="66189-119">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="66189-120">関数のコメントの直後に以下を追加し `// List the calendar` `Main` ます。</span><span class="sxs-lookup"><span data-stu-id="66189-120">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. <span data-ttu-id="66189-121">すべての変更を保存し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="66189-121">Save all of your changes and run the app.</span></span> <span data-ttu-id="66189-122">ユーザーのイベントの一覧を表示するには、[ **今週の予定表を表示** する] オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="66189-122">Choose the **View this week's calendar** option to see a list of the user's events.</span></span>

    ```Shell
    Welcome Lynne Robbins!

    Please choose one of the following options:
    0. Exit
    1. Display access token
    2. View this week's calendar
    3. Add an event
    2
    Events:
    Subject: Meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 10:00 AM
      End: 9/28/2020 11:30 AM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 2:00 PM
      End: 9/28/2020 3:00 PM
    Subject: Carpool
      Organizer: Lynne Robbins
      Start: 9/28/2020 4:00 PM
      End: 9/28/2020 5:30 PM
    Subject: Tailspin Toys Proposal Review + Lunch
      Organizer: Lidia Holloway
      Start: 9/29/2020 12:00 PM
      End: 9/29/2020 1:00 PM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/29/2020 2:00 PM
      End: 9/29/2020 3:00 PM
    Subject: Project Tailspin
    ```
