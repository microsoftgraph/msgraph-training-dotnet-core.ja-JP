<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fae14-101">この手順では、Azure Active Directory 管理センターを使用して、新しい Azure AD アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="fae14-101">In this exercise you will create a new Azure AD application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="fae14-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="fae14-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="fae14-103">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="fae14-104">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="fae14-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="fae14-105">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-105">Select **New registration**.</span></span> <span data-ttu-id="fae14-106">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="fae14-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="fae14-107">`.NET Core Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="fae14-107">Set **Name** to `.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="fae14-108">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="fae14-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="fae14-109">**[リダイレクト URI]** を空のままにします。</span><span class="sxs-lookup"><span data-stu-id="fae14-109">Leave **Redirect URI** empty.</span></span>

    ![[アプリケーションの登録] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="fae14-111">[**登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-111">Select **Register**.</span></span> <span data-ttu-id="fae14-112">**.Net コアグラフのチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存し、次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="fae14-112">On the **.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="fae14-114">[**リダイレクト URI を追加する**] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-114">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="fae14-115">[**リダイレクト uri** ] ページで、[**パブリッククライアント (モバイル、デスクトップ)] セクションの推奨されるリダイレクト uri**を見つけます。</span><span class="sxs-lookup"><span data-stu-id="fae14-115">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="fae14-116">`https://login.microsoftonline.com/common/oauth2/nativeclient` URI を選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-116">Select the `https://login.microsoftonline.com/common/oauth2/nativeclient` URI.</span></span>

    ![リダイレクト Uri ページのスクリーンショット](./images/aad-redirect-uris.png)

1. <span data-ttu-id="fae14-118">[**既定のクライアントの種類**] セクションを探し、[アプリケーションを**パブリッククライアントとして扱う**] を [**はい**] に変更して、[**保存**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="fae14-118">Locate the **Default client type** section and change the **Treat application as a public client** toggle to **Yes**, then choose **Save**.</span></span>

    ![[既定のクライアントの種類] セクションのスクリーンショット](./images/aad-default-client-type.png)
