<!-- markdownlint-disable MD002 MD041 -->

この演習では、管理センターを使用して新AD Azure Azure Active Directory作成します。

1. ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。

1. 左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。

    ![アプリの登録のスクリーンショット ](./images/aad-portal-app-registrations.png)

1. **[新規登録]** を選択します。 **[アプリケーションを登録]** ページで、次のように値を設定します。

    - `.NET Core Graph Tutorial` に **[名前]** を設定します。
    - **[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。
    - [ **リダイレクト URI]** で、ドロップダウンを [パブリック クライアント (モバイル & **デスクトップ)** に変更し、値をに設定します `https://login.microsoftonline.com/common/oauth2/nativeclient` 。

    ![[アプリケーションを登録する] ページのスクリーンショット](./images/aad-register-an-app.png)

1. **[登録]** を選択します。 **[.NET Core Graph チュートリアル**] ページで、アプリケーション **(クライアント) ID** の値をコピーして保存します。次の手順で必要になります。

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. **[管理]** の下の **[認証]** を選択します。 [詳細設定 **] セクションを見つけて** 、[パブリック クライアント **フローの** 許可] トグルを [はい] に変更 **し**、[保存] を **選択します**。

    ![[パブリック クライアント フローの許可] トグルのスクリーンショット](./images/aad-default-client-type.png)
