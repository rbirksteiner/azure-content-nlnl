<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Google 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Google 驗證。"
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
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

# 如何設定應用程式以使用 Google 登入

本主題說明如何設定 Azure 應用程式服務，以使用 Google 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

## <a name="register"> </a>向 Google 註冊您的應用程式

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> 網站，以您的 Google 帳戶認證登入，按一下 [**建立專案**]，提供 [**名稱**]，然後按一下 [**建立**]。

2. 在左側導覽列中，按一下 [**API 和驗證**]。然後按一下 [**同意畫面**]。選取您的 [**電子郵件地址**]，並輸入 [**產品名稱**]。然後按一下 [儲存]。

3. 同時在 [**API 和驗證**] 下選取 [**API**]，並啟用 [**Google+ API**]。

4. 再次於 [**API 和驗證**] 下選取 [**憑證**]，然後選取 [**建立新的用戶端 ID**]。

5. 選取 [**網路應用程式**]。在 [**已授權的 JavaScript 來源**] 中輸入您的應用程式服務閘道 URL，然後將 [**已授權的重新導向 URI**] 中產生的 URL，取代為附加路徑 _/signin-google_ 的閘道 URL。例如：`https://contoso.azurewebsites.net/signin-google`。請確實使用 HTTPS 配置。然後按一下 [**建立用戶端 ID**]。

     ![][0]

6. 在下一個畫面上，在 [**Web 應用程式的用戶端識別碼**] 下，記下 [**用戶端識別碼**] 和 [**用戶端密鑰**] 的值。

    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Google 資訊新增至行動應用程式

7. 登入 [Azure 管理入口網站]，然後導覽至您的 App Services 閘道。

8. 在 [**設定**] 下選擇 [**身分識別**]，然後選取 [**Google**]。貼入您先前取得的用戶端識別碼和用戶端密鑰值。然後按一下 [儲存]。

     ![][1]


現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 管理入口網站]: https://portal.azure.com/
 

<!---HONumber=62-->