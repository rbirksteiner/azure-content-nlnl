<properties 
	pageTitle="開始使用驗證 (Android) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Android 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="ricksal"/>

# 將驗證加入至行動服務 Android 應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## 摘要

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">



<p>本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">10:42</span></div>
</div>

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：


##必要條件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## 註冊您的應用程式以驗證與設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## 限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. 在 Android Studio 中，開啟您完成[開始使用行動服務]教學課程時所建立的專案。 

2. 在 [執行] 功能表中，按一下 [執行應用程式]。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

	 這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## 將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端快取驗證權杖

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>重新整理權杖快取

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)]



## <a name="next-steps"></a>接續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[開始使用行動服務]: /develop/mobile/tutorials/get-started-android
[Add Mobile Services to an existing app]: /develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-android
[使用指令碼授權使用者]: /develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->