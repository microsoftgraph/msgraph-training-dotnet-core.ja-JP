# <a name="how-to-run-the-completed-project"></a>完了したプロジェクトを実行する方法

## <a name="prerequisites"></a>前提条件

このフォルダーで完了したプロジェクトを実行するには、次の情報が必要です。

- 開発[マシンにインストールされている .NET Core SDK。](https://dotnet.microsoft.com/download) (**注:** このチュートリアルは、.NET Core SDK バージョン 3.1.402 で記述されています。 このガイドの手順は、他のバージョンで動作する場合がありますが、テストされていない場合があります)。
- Microsoft の仕事または学校のアカウント。

Microsoft アカウントをお持ちでない場合は、Office 365[開発者](https://developer.microsoft.com/office/dev-program)プログラムにサインアップして無料のサブスクリプションOffice 365できます。

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Web アプリケーションを管理センターにAzure Active Directoryする

1. ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。

1. 左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。

    ![アプリの登録のスクリーンショット ](/tutorial/images/aad-portal-app-registrations.png)

1. **[新規登録]** を選択します。 **[アプリケーションを登録]** ページで、次のように値を設定します。

    - `.NET Core Graph Tutorial` に **[名前]** を設定します。
    - [サポート **されているアカウントの種類] を任意****の組織ディレクトリの [アカウント] に設定します**。
    - **[リダイレクト URI]** を空のままにします。

    ![[アプリケーションを登録する] ページのスクリーンショット](/tutorial/images/aad-register-an-app.png)

1. **[登録]** を選択します。 **[.NET Core Graph チュートリアル**] ページで、アプリケーション **(クライアント) ID** の値をコピーして保存します。次の手順で必要になります。

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](/tutorial/images/aad-application-id.png)

1. [リダイレクト **URI の追加] リンクを選択** します。 [リダイレクト **URI] ページで** 、[パブリック クライアント (モバイル、デスクトップ) の推奨リダイレクト **URI] セクションを探** します。 URI を選択 `https://login.microsoftonline.com/common/oauth2/nativeclient` します。

    ![リダイレクト URI ページのスクリーンショット](/tutorial/images/aad-redirect-uris.png)

1. [詳細設定 **] セクションを見つけて** 、[パブリック クライアント **フローの** 許可] トグルを [はい] に変更 **し**、[保存] を **選択します**。

    ![[既定のクライアントの種類] セクションのスクリーンショット](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a>サンプルを構成する

1. .NET [開発シークレット ストアを初期化するには](https://docs.microsoft.com/aspnet/core/security/app-secrets)**、GraphTutorial.csproj** を含むディレクトリで CLI を開き、次のコマンドを実行します。

    ```Shell
    dotnet user-secrets init
    ```

1. 次のコマンドを使用して、アプリケーション ID と必要なスコープの一覧をシークレット ストアに追加します。 `YOUR_APP_ID_HERE`Azure portal で作成したアプリケーション ID に置き換える。

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="build-and-run-the-sample"></a>サンプルの構築と実行

コマンド ライン インターフェイス (CLI) で、プロジェクト ディレクトリに移動し、次のコマンドを実行します。

```Shell
dotnet restore
dotnet build
dotnet run
```
