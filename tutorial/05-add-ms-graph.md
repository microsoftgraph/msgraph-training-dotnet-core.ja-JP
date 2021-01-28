<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。 このアプリケーションでは [、Microsoft Graph .NET クライアント](https://github.com/microsoftgraph/msgraph-sdk-dotnet) ライブラリを使用して Microsoft Graph を呼び出します。

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. Graph という名前の **GraphTu graphl ディレクトリに新** しいディレクトリを作成 **します**。
1. GraphHelper.cs という名前の **Graph** ディレクトリに **新しいファイルを作成し、** そのファイルに次のコードを追加します。

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

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

1. 呼び出しの直後に ./Program.cs に次のコードを追加して、ユーザーを取得し、ユーザーの表示名 `Main`  `GetAccessToken` を出力します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

ここでアプリを実行すると、アプリにログインすると、名前で表示されます。

## <a name="get-a-calendar-view"></a>予定表ビューを取得する

1. 次の関数をクラスに `GraphHelper` 追加して、ユーザーの予定表からイベントを取得します。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

このコードの実行内容を考えましょう。

- 呼び出される URL は `/me/calendarview` です。
- パラメーター `startDateTime` は `endDateTime` 、カレンダー ビューの開始と終了を定義します。
- ヘッダーにより、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。
- この `Top` 関数は、最大で 50 のイベントを要求します。
- この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。
- この `OrderBy` 関数は、結果を開始日時で並べ替える。

## <a name="display-the-results"></a>結果の表示

1. 次の関数をクラスに追加して、Microsoft Graph の dateTimeTimeZone プロパティをユーザー に使い分け可能な形式 `Program` に書式設定します。 [](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. 次の関数をクラスに追加して、ユーザーのイベントを取得し、コンソール `Program` に出力します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. 関数のコメントの直後に `// List the calendar` 次を追加 `Main` します。

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. すべての変更を保存し、アプリを実行します。 [今週 **の予定表を表示] オプションを選択** して、ユーザーのイベントの一覧を表示します。

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
