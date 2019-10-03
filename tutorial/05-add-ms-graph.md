<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft [graph .Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph への呼び出しを行います。

## <a name="get-user-details"></a>ユーザーの詳細を取得する

1. **Graph**という名前の**graphtutorial**ディレクトリに新しいディレクトリを作成します。
1. **GraphHelper.cs**という名前の**Graph**ディレクトリに新しいファイルを作成し、そのファイルに次のコードを追加します。

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

次のコードを`Main` **Program.cs**の呼び出しの`GetAccessToken`直後に追加して、ユーザーを取得し、ユーザーの表示名を出力します。

```csharp
// Initialize Graph client
GraphHelper.Initialize(authProvider);

// Get signed in user
var user = GraphHelper.GetMeAsync().Result;
Console.WriteLine($"Welcome {user.DisplayName}!\n");
```

アプリをすぐに実行する場合は、アプリにログインした後、名前によって歓迎されます。

## <a name="get-calendar-events-from-outlook"></a>Outlook から予定表のイベントを取得する

次の関数を`GraphHelper`クラスに追加して、ユーザーの予定表からイベントを取得します。

```csharp
public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    try
    {
        // GET /me/events
        var resultPage = await graphClient.Me.Events.Request()
            // Only return the fields used by the application
            .Select("subject,organizer,start,end")
            // Sort results by when they were created, newest first
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        return resultPage.CurrentPage;
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting events: {ex.Message}");
        return null;
    }
}
```

このコードの内容を検討してください。

- 呼び出し先の URL は`/me/events`になります。
- 関数`Select`は、各イベントに対して返されるフィールドを、アプリが実際に使用しているものだけに制限します。
- 関数`OrderBy`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

## <a name="display-the-results"></a>結果を表示する

1. 次の関数を`Program`クラスに追加して、Microsoft Graph の[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)プロパティをユーザーフレンドリな形式に書式設定します。

```csharp
static string FormatDateTimeTimeZone(Microsoft.Graph.DateTimeTimeZone value)
{
    // Get the timezone specified in the Graph value
    var timeZone = TimeZoneInfo.FindSystemTimeZoneById(value.TimeZone);
    // Parse the date/time string from Graph into a DateTime
    var dateTime = DateTime.Parse(value.DateTime);

    // Create a DateTimeOffset in the specific timezone indicated by Graph
    var dateTimeWithTZ = new DateTimeOffset(dateTime, timeZone.BaseUtcOffset)
        .ToLocalTime();

    return dateTimeWithTZ.ToString("g");
}
```

1. 次の関数を`Program`クラスに追加して、ユーザーのイベントを取得し、コンソールに出力します。

```csharp
static void ListCalendarEvents()
{
    var events = GraphHelper.GetEventsAsync().Result;

    Console.WriteLine("Events:");

    foreach (var calendarEvent in events)
    {
        Console.WriteLine($"Subject: {calendarEvent.Subject}");
        Console.WriteLine($"  Organizer: {calendarEvent.Organizer.EmailAddress.Name}");
        Console.WriteLine($"  Start: {FormatDateTimeTimeZone(calendarEvent.Start)}");
        Console.WriteLine($"  End: {FormatDateTimeTimeZone(calendarEvent.End)}");
    }
}
```

最後に、 `Main`関数内のコメントの`// List the calendar`直後に以下を追加します。

```csharp
ListCalendarEvents();
```

すべての変更を保存し、アプリを実行します。 [**カレンダーイベントのリスト**] オプションを選択して、ユーザーのイベントの一覧を表示します。

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
