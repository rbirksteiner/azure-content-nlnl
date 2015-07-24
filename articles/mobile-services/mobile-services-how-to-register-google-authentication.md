<properties 
	pageTitle="註冊 Google 驗證 - 行動服務" 
	description="了解如何在 Azure 行動服務中註冊您的應用程式以使用 Google 進行驗證。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="glenga"/>

# 在行動服務中註冊您的應用程式以進行 Google 登入

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Google 驗證。

>[AZURE.NOTE]本教學課程有關 [Azure 行動服務](http://azure.microsoft.com/services/mobile-services/)，此方案可協助您建置適用於任何平台的可擴充行動應用程式。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面是[開始使用驗證](mobile-services-ios-get-started-users.md)教學課程的輔助，說明如何將使用者登入您的應用程式。<br/>如果這是您第一次使用行動服務，請完成[開始使用行動服務](mobile-services-ios-get-started.md)教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> (英文) 網站，以您的 Google 帳戶認證登入，按一下 [建立專案]，提供「專案名稱」，然後按一下 [建立]。

   	![Google API 新專案](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png)

2. 按一下 [同意] 畫面，選取您的電子郵件地址，輸入產品名稱，然後按一下 [儲存]。

3. 按一 [API 與驗證] > [認證] > [建立新的用戶端識別碼]。

   	![建立新的用戶端識別碼](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png)

4. 選取 [**Web 應用程式**]，在 [**已授權的 JavaScript 起源**] 中輸入您的行動服務 URL，將 [**已授權的重新導向 URI**] 中所產生的 URL，取代為行動服務的 URL，後面附加路徑 `/login/google`，然後按一下 [**建立用戶端識別碼**]。

	>[AZURE.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 _signin-google_，而您的行動服務為 .NET 服務，例如 `https://todolist.azure-mobile.net/signin-google`。&nbsp;

   	![](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png)

5. 在 [Client ID for web applications] 下，記下 [用戶端識別碼] 和 [用戶端密碼] 的值。

   	![用戶端認證](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png)

    >[AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。

您現在已經準備好設定您的行動服務來使用 Google 登入，以在您的應用程式中進行驗證。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->