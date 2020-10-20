<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

1. 次の関数を**Graphhelper**クラスに追加して、 **graph/Graph/graphper.cs を開きます。**

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="CreateEventSnippet":::

    このコードでは、 **Event** オブジェクトを初期化し、Graph SDK を使用してユーザーの予定表に追加します。

1. Program.cs を開き、次の関数を**Program**クラスに追加します **。**

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="UserInputSnippet":::

    これらの関数は、ユーザー入力を読み取るためのヘルパー関数です。

1. 次の関数を **Program** クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="CreateEventSnippet":::

    この関数は、件名、出席者、開始日、終了日、および本文の入力を求めるメッセージを表示し、それらの値を使用して呼び出しを `GraphHelper.CreateEvent` 行います。

1. 関数のコメントの直後に以下を追加し `// Create a new event` `Main` ます。

    ```csharp
    CreateEvent(user.MailboxSettings.TimeZone);
    ```

1. すべての変更を保存し、アプリを実行します。 [ **イベントの追加** ] オプションを選択します。 プロンプトに応答して、ユーザーの予定表に新しいイベントを作成します。
