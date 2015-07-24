
<properties 
	pageTitle="開始使用推播通知 (Android JavaScript) | 行動開發人員中心" 
	description="了解如何使用 Azure 行動服務傳送推播通知至 Android JavaScript 應用程式。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	writer="ricksal" 
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


# 將推播通知新增至行動服務 Android 應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 摘要

本主題說明如何使用 Azure 行動服務，透過 Google 雲端通訊 ("GCM") 將推播通知傳送至 Android 應用程式。您會將推播通知新增至快速入門專案 (本教學課程的先決條件)。使用您的行動服務中內含的 Azure 通知中樞來啟用推播通知。完成後，行動服務就會在每次插入記錄時傳送推播通知。

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->


## 必要條件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>將推播通知新增至應用程式



下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考[設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [新增 Play 服務](../../includes/mobile-services-add-Google-play-services.md)]

###新增程式碼

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

###設定用於測試的 Android 模擬器

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1. 從工具列的右端，選取 [Android 虛擬裝置管理員]，選取您的裝置，按一下右邊的編輯圖示。

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. 選取裝置描述行上的 [**變更**]，選取 [**Google API**]，然後按一下 [確定]。

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	如此會使 AVD 變成使用 Google API。

###執行測試

1. 從 [**執行**] 功能表項目，按一下 [**執行應用程式**] 來啟動應用程式。

2. 在應用程式中，輸入有意義的文字，如 _A new Mobile Services task_，然後按一下 [加入] 按鈕。

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. 從螢幕頂端向下撥動將裝置的通知中樞開啟，以檢視通知。


您已成功完成此教學課程。

## 疑難排解

### 驗證 Android SDK 版本

[AZURE.INCLUDE [確認 SDK](../../includes/mobile-services-verify-android-sdk-version.md)]


## 舊版程式碼

如果您想要查看本教學課程的 Eclipse 版本，請移至：[開始使用推播通知 (Eclipse)]。


<!--
To see a completed version of the source code in an Eclipse project, go <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/Android">here</a>.
-->


## <a name="next-steps"> </a>後續步驟

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]<br/>深入了解如何使用行動服務來儲存與查詢資料。

* [開始使用驗證] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中心應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>取得通知中心解決方案的疑難排解和偵錯指引。

* [如何使用適用於行動服務的 Android 用戶端程式庫] <br/>深入了解如何搭配使用行動服務與 Android。

* [行動服務伺服器指令碼參考] <br/>深入了解如何在您的行動服務中實作商務邏輯。


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[開始使用推播通知 (Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[開始使用資料]: mobile-services-android-get-started-data.md
[開始使用驗證]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[設定 Google Play 服務 SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[如何使用適用於行動服務的 Android 用戶端程式庫]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[什麼是通知中心？]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=July15_HO1-->