<properties 
	pageTitle="註冊 Twitter 驗證 - 行動服務" 
	description="了解如何對於˙ Azure Mobile Services 應用程式使用 Twitter 驗證。" 
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
	ms.date="04/13/2015" 
	ms.author="glenga"/>

#在行動服務中註冊您的應用程式以進行 Twitter 登入

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Twitter 驗證。

>[AZURE.NOTE]本教學課程有關 [Azure 行動服務](http://azure.microsoft.com/services/mobile-services/)，此方案可協助您建置適用於任何平台的可擴充行動應用程式。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面是<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">開始使用驗證</a>教學課程的輔助，說明如何將使用者登入您的應用程式。如果這是您第一次使用行動服務，請完成<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">開始使用行動服務</a>教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。若要建立新的 Twitter 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter 開發人員</a>網站，使用您的 Twitter 帳戶認證登入，然後按一下 [建立新應用程式]。

   	![][1]

2. 輸入您應用程式的 [名稱]、[描述] 和 [網站] 值，然後在 [回呼 URL] 中輸入行動服務的 URL，後面附加路徑 **/login/twitter**。

	>[AZURE.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 並附加 _signin-twitter_ 路徑，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-twitter</code>。

   	![][2]

3.  在頁面底部，請閱讀並接受條款，輸入正確的 CAPTCHA 文字，然後按一下 [Create your Twitter application]。

   	![][3]

   	這會註冊應用程式並顯示應用程式詳細資料。

6. 記下 [消費者金鑰] 和 [消費者密碼] 的值。

   	![][4]

    > [AZURE.NOTE]消費者密碼是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

7. 按一下 [設定] 索引標籤，向下捲動並勾選 [Allow this application to be used to sign in with Twitter]，然後按一下 [Update this Twitter application's settings]。

	![][5]

現在您已準備好提供消費者金鑰和消費者密碼值給行動服務，以在您的應用程式中採用 Twitter 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->