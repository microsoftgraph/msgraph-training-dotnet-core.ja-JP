<!-- markdownlint-disable MD002 MD041 -->

この演習では、アプリケーションに Microsoft Graphを組み込む必要があります。 このアプリケーションでは、Microsoft Graph [.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet)クライアント ライブラリを使用して Microsoft クライアント ライブラリを呼び出Graph。

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. **./Graph/GraphHelper.cs** を開き **、GraphHelper** クラスに次の関数を追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetMeSnippet":::

1. 呼び出しの `Main` 直後 **に ./Program.cs** に次のコードを追加して、ユーザーを取得し、ユーザーの表示名 `GetAccessToken` を出力します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

アプリを今すぐ実行する場合は、アプリにログインした後に名前で開きます。

## <a name="get-a-calendar-view"></a>予定表ビューを取得する

1. 次の関数をクラスに `GraphHelper` 追加して、ユーザーの予定表からイベントを取得します。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

このコードの実行内容を考えましょう。

- 呼び出される URL は `/me/calendarview` です。
- パラメーター `startDateTime` と `endDateTime` パラメーターは、予定表ビューの開始と終了を定義します。
- ヘッダーを使用すると、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。
- この `Top` 関数は、最大で 50 のイベントを要求します。
- この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。
- この `OrderBy` 関数は、結果を開始日時で並べ替える。

## <a name="display-the-results"></a>結果の表示

1. 次の関数をクラスに追加 `Program` して、Microsoft の[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)プロパティをユーザー Graph形式に書式設定します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. 次の関数をクラスに追加して、ユーザーのイベントを取得し、 `Program` コンソールに出力します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. 関数のコメントの直後に `// List the calendar` 次の項目を追加 `Main` します。

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. すべての変更を保存し、アプリを実行します。 [今週 **の予定表を表示する] オプションを選択** して、ユーザーのイベントの一覧を表示します。

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
