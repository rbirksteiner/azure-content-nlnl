<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證"
	description="了解如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證。"
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="mahender"/>

# 如何設定應用程式以使用 Microsoft 帳戶登入

本主題說明如何設定 Azure 應用程式服務，以使用 Microsoft 帳戶做為驗證提供者。

## <a name="register"> </a>使用 Microsoft 帳戶註冊您的應用程式

1. 瀏覽到 Microsoft 帳戶開發人員中心的 [我的應用程式]頁面，並視需要使用您的 Microsoft 帳戶登入。

2. 按一下 [建立應用程式]、然後輸入 [應用程式名稱]，並按一下 [I accept]。

3. 按一下 [**API 設定**]。針對 [**行動或桌面用戶端應用程式**]，選取 [**是**]。在 [**重新導向 URL**] 欄位中，輸入附加路徑 _/signin-microsoft_ 的閘道 URL。例如：`https://contosogateway.azurewebsites.net/signin-microsoft`。請確實使用 HTTPS 配置。輸入重新導向 URL 之後，按一下 [**儲存**]。

	![][0]

	>[AZURE.NOTE]針對現有的 Microsoft 帳戶應用程式註冊，您可能必須先啟用 [**增強型重新導向安全性**]。

4. 按一下 [應用程式設定]，記下 [用戶端 ID] 和 [用戶端密碼] 的值。

    > [AZURE.NOTE]用戶端密碼是重要的安全性認證。請勿與任何人共用此用戶端密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至行動應用程式

5. 登入 [Azure 管理入口網站]，然後導覽至您的 App Services 閘道。

6. 在 [**設定**] 下選擇 [**身分識別**]，然後選取 [**Microsoft 帳戶**]。貼入您先前取得的應用程式識別碼和應用程式密鑰值。然後按一下 [儲存]。

    ![][1]

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-settings.png

<!-- URLs. -->

[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 管理入口網站]: https://portal.azure.com/
 

<!---HONumber=62-->