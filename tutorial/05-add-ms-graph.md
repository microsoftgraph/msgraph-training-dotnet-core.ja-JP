<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft [graph .Net クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet) を使用して microsoft graph への呼び出しを行います。

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

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

1. ユーザーを取得して `Main` ユーザーの表示名を出力する呼び出しの直後に、次のコードを追加**します。/Program.cs。** `GetAccessToken`

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

アプリをすぐに実行する場合は、アプリにログインした後、名前によって歓迎されます。

## <a name="get-a-calendar-view"></a>予定表ビューを取得する

1. 次の関数をクラスに追加して、 `GraphHelper` ユーザーの予定表からイベントを取得します。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

このコードの実行内容を考えましょう。

- 呼び出される URL は `/me/calendarview` です。
- `startDateTime`パラメーターとは、 `endDateTime` カレンダービューの開始と終了を定義します。
- `Prefer: outlook.timezone`ヘッダーにより、 `start` イベントが `end` ユーザーのタイムゾーンで返されます。
- 関数は、 `Top` 最大50イベントを要求します。
- 関数は、 `Select` 各イベントに対して返されるフィールドを、アプリが実際に使用しているものだけに制限します。
- 関数は、 `OrderBy` 開始日時で結果を並べ替えます。

## <a name="display-the-results"></a>結果の表示

1. 次の関数をクラスに追加して、 `Program` Microsoft Graph の [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) プロパティをユーザーフレンドリな形式に書式設定します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. 次の関数をクラスに追加して、 `Program` ユーザーのイベントを取得し、コンソールに出力します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. 関数のコメントの直後に以下を追加し `// List the calendar` `Main` ます。

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. すべての変更を保存し、アプリを実行します。 ユーザーのイベントの一覧を表示するには、[ **今週の予定表を表示** する] オプションを選択します。

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
