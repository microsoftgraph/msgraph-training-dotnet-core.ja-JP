# <a name="how-to-run-the-completed-project"></a>完了したプロジェクトを実行する方法

## <a name="prerequisites"></a>前提条件

このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。

- 開発用コンピューターにインストールされている[.Net コア SDK](https://dotnet.microsoft.com/download) 。 (**注:** このチュートリアルは、.NET Core SDK バージョン2.2.401 を使用して作成されています。 このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)
- Microsoft 職場または学校のアカウント。

Microsoft アカウントを持っていない場合は、 [office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Web アプリケーションを Azure Active Directory 管理センターに登録する

1. ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。

1. 左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。

    ![アプリの登録のスクリーンショット ](/tutorial/images/aad-portal-app-registrations.png)

1. **[新規登録]** を選択します。 **[アプリケーションを登録]** ページで、次のように値を設定します。

    - `.NET Core Graph Tutorial` に **[名前]** を設定します。
    - **サポートされているアカウントの種類**を**任意の組織ディレクトリのアカウント**に設定します。
    - **[リダイレクト URI]** を空のままにします。

    ![[アプリケーションを登録する] ページのスクリーンショット](/tutorial/images/aad-register-an-app.png)

1. **[登録]** を選択します。 **.Net コアグラフのチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存し、次の手順で必要になります。

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](/tutorial/images/aad-application-id.png)

1. [**リダイレクト URI を追加する**] リンクを選択します。 [**リダイレクト uri** ] ページで、[**パブリッククライアント (モバイル、デスクトップ)] セクションの推奨されるリダイレクト uri**を見つけます。 `https://login.microsoftonline.com/common/oauth2/nativeclient` URI を選択します。

    ![リダイレクト Uri ページのスクリーンショット](/tutorial/images/aad-redirect-uris.png)

1. [**既定のクライアントの種類**] セクションを探し、[アプリケーションを**パブリッククライアントとして扱う**] を [**はい**] に変更して、[**保存**] を選択します。

    ![[既定のクライアントの種類] セクションのスクリーンショット](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a>サンプルを構成する

1. **Graphtutorial .csproj**を含むディレクトリで CLI を開き、次のコマンドを実行して、 [.net 開発シークレットストア](https://docs.microsoft.com/aspnet/core/security/app-secrets)を初期化します。

    ```Shell
    dotnet user-secrets init
    ```

1. 次のコマンドを使用して、アプリケーション ID と必要なスコープのリストをシークレットストアに追加します。 を`YOUR_APP_ID_HERE` Azure ポータルで作成したアプリケーション ID に置き換えます。

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="build-and-run-the-sample"></a>サンプルの構築と実行

コマンドラインインターフェイス (CLI) で、プロジェクトディレクトリに移動し、次のコマンドを実行します。

```Shell
dotnet restore
dotnet build
dotnet run
```
