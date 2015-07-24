<properties 
	pageTitle="註冊 Facebook 驗證 - 行動服務" 
	description="了解如何在 Azure 行動服務應用程式中使用 Facebook 驗證。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/27/2015" 
	ms.author="glenga"/>

# 在行動服務中註冊應用程式以採用 Facebook 驗證

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Facebook 驗證。

>[AZURE.NOTE]本教學課程有關 [Azure 行動服務]，此方案可協助您建置適用於任何平台的可擴充行動應用程式。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面是<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">開始使用驗證</a>教學課程的輔助，說明如何將使用者登入您的應用程式。如果這是您第一次使用行動服務，請完成<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">開始使用行動服務</a>教學課程。
	
若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>。

1. 導覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook 開發人員</a>網站，並以您的 Facebook 帳戶認證登入。

2. (選用) 按一下 [**我的應用程式**]，然後按一下 [**註冊為開發人員**]，接受政策並遵循註冊步驟 (若您尚未註冊)。

3. 按一下 [**我的應用程式**] > [**加入新的應用程式**] > [**進階設定**]。

4. 輸入應用程式的唯一 [**顯示名稱**]，選擇 [**類別**] 下的 [**Apps for Pages**]，然後按一下 [**建立應用程式識別碼**] 並完成安全性練習。

	這會建立新的 Facebook 應用程式識別碼。

5. 按一下 [**設定**]，在 [**應用程式網域**] 中輸入您行動服務的網域，輸入選用 [**連絡人電子郵件**]，然後按一下 [**新增平台**] 並選取 [**網站**]。

   	![][3]

6. 在 [網站 URL] 中輸入您行動服務的 URL，然後按一下 [儲存變更]。

7. 按一下 [顯示]，提供您的密碼 (如有要求)，然後記下 [應用程式 ID] 和 [應用程式密鑰] 的值。

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]應用程式密鑰是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。&nbsp;

8. 按一下 [**進階**] 索引標籤，在 [**有效的 OAuth 重新導向 URI**] 中，輸入行動服務的 URL，後面附加路徑 _/login/facebook_，然後按一下 [**儲存變更**]。

     >[AZURE.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 _signin-facebook_，而您的行動服務為 .NET 服務，例如 `https://todolist.azure-mobile.net/signin-facebook`。
       

9. 按一下 [**狀態與檢閱**] > [**是**]，以啟用應用程式的一般公用存取權。

	您用來註冊新應用程式的 Facebook 帳戶是應用程式的管理員，具有該應用程式的管理員存取權。此步驟會授與一般公用存取權，讓應用程式能夠使用其他 Facebook 帳戶進行驗證。


現在您已準備好提供應用程式 ID 和應用程式密碼值給行動服務，以在您的應用程式中採用 Facebook 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Azure 行動服務]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=July15_HO2-->