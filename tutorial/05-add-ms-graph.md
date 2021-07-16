<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="75936-101">この演習では、アプリケーションに Microsoft Graphを組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="75936-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="75936-102">このアプリケーションでは、Microsoft Graph [.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet)クライアント ライブラリを使用して Microsoft クライアント ライブラリを呼び出Graph。</span><span class="sxs-lookup"><span data-stu-id="75936-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="75936-103">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="75936-103">Get user details</span></span>

1. <span data-ttu-id="75936-104">**./Graph/GraphHelper.cs** を開き **、GraphHelper** クラスに次の関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="75936-104">Open **./Graph/GraphHelper.cs** and add the following function to the **GraphHelper** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetMeSnippet":::

1. <span data-ttu-id="75936-105">呼び出しの `Main` 直後 **に ./Program.cs** に次のコードを追加して、ユーザーを取得し、ユーザーの表示名 `GetAccessToken` を出力します。</span><span class="sxs-lookup"><span data-stu-id="75936-105">Add the following code in `Main` in **./Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="75936-106">アプリを今すぐ実行する場合は、アプリにログインした後に名前で開きます。</span><span class="sxs-lookup"><span data-stu-id="75936-106">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-a-calendar-view"></a><span data-ttu-id="75936-107">予定表ビューを取得する</span><span class="sxs-lookup"><span data-stu-id="75936-107">Get a calendar view</span></span>

1. <span data-ttu-id="75936-108">次の関数をクラスに `GraphHelper` 追加して、ユーザーの予定表からイベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="75936-108">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="75936-109">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="75936-109">Consider what this code is doing.</span></span>

- <span data-ttu-id="75936-110">呼び出される URL は `/me/calendarview` です。</span><span class="sxs-lookup"><span data-stu-id="75936-110">The URL that will be called is `/me/calendarview`.</span></span>
- <span data-ttu-id="75936-111">パラメーター `startDateTime` と `endDateTime` パラメーターは、予定表ビューの開始と終了を定義します。</span><span class="sxs-lookup"><span data-stu-id="75936-111">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
- <span data-ttu-id="75936-112">ヘッダーを使用すると、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。</span><span class="sxs-lookup"><span data-stu-id="75936-112">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
- <span data-ttu-id="75936-113">この `Top` 関数は、最大で 50 のイベントを要求します。</span><span class="sxs-lookup"><span data-stu-id="75936-113">The `Top` function requests at most 50 events.</span></span>
- <span data-ttu-id="75936-114">この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。</span><span class="sxs-lookup"><span data-stu-id="75936-114">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="75936-115">この `OrderBy` 関数は、結果を開始日時で並べ替える。</span><span class="sxs-lookup"><span data-stu-id="75936-115">The `OrderBy` function sorts the results by the start date and time.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="75936-116">結果の表示</span><span class="sxs-lookup"><span data-stu-id="75936-116">Display the results</span></span>

1. <span data-ttu-id="75936-117">次の関数をクラスに追加 `Program` して、Microsoft の[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)プロパティをユーザー Graph形式に書式設定します。</span><span class="sxs-lookup"><span data-stu-id="75936-117">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="75936-118">次の関数をクラスに追加して、ユーザーのイベントを取得し、 `Program` コンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="75936-118">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="75936-119">関数のコメントの直後に `// List the calendar` 次の項目を追加 `Main` します。</span><span class="sxs-lookup"><span data-stu-id="75936-119">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. <span data-ttu-id="75936-120">すべての変更を保存し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="75936-120">Save all of your changes and run the app.</span></span> <span data-ttu-id="75936-121">[今週 **の予定表を表示する] オプションを選択** して、ユーザーのイベントの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="75936-121">Choose the **View this week's calendar** option to see a list of the user's events.</span></span>

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
