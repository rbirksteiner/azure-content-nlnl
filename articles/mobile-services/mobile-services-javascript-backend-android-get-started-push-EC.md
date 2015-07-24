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
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push-EC.md)]

本主題說明如何搭配使用 Azure 行動服務和 Google Cloud Messaging (GCM)，將推播通知傳送至 Android 應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [啟用 Google Cloud Messaging](#register)
2. [設定行動服務](#configure)
3. [將推播通知新增至應用程式](#add-push)
4. [更新指令碼以傳送推播通知](#update-scripts)
5. [插入資料以接收通知](#test)


>[AZURE.NOTE]如果您想要查看已完成的應用程式的原始程式碼，請到<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">這裡</a>。

##必要條件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites-EC.md)]

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [啟用 GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>將推播通知新增至應用程式

###驗證 Android SDK 版本

[AZURE.INCLUDE [確認 SDK](../../includes/mobile-services-verify-android-sdk-version-EC.md)]

下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考[設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [新增 Play 服務](../../includes/mobile-services-add-Google-play-services-EC.md)]

###新增程式碼

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push-EC.md)]


##<a id="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

1. 在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。 

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-portal-data-tables.png)

2. 在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。
   
  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-insert-script-push2.png)

   	這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    "data": {
		        "message": item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	如此即會註冊新的 insert 指令碼，該指令碼會在插入成功之後，使用 [gcm 物件]將推播通知傳送給所有已註冊的裝置。

##<a id="test"></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

###設定用於測試的模擬器

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1. 重新啟動 Eclipse，然後在 Package Explorer 中以滑鼠右鍵按一下專案，按一下 [Properties]，按一下 [Android]，核取 [Google API]，然後按一下 [OK]。

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-import-android-properties.png)

  	如此會使專案的目標變成 Google API。

2. 從 [Window] 中選取 [Android Virtual Device Manager]，選取您的裝置，按一下 [Edit]。

	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager.png)

3. 在 [Target] 中選取 [Google API]，然後按一下 [OK]。

   	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager-edit.png)

	如此會使 AVD 變成使用 Google API。

###執行測試

1. 在 Eclipse 的 [執行] 功能表中按一下 [執行]，以啟動應用程式。

2. 在應用程式中，輸入有意義的文字，如 _A new Mobile Services task_，然後按一下 [加入] 按鈕。

  	![](./media/mobile-services-javascript-backend-android-get-started-push-EC/mobile-quickstart-push1-android.png)

3. 從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。


您已成功完成此教學課程。


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

* [開始使用資料] <br/>深入了解使用行動服務來儲存與查詢資料。

* [將驗證新增至應用程式][Get started with authentication] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中樞？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

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
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-android-get-started.md
[開始使用資料]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[設定 Google Play 服務 SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[如何使用適用於行動服務的 Android 用戶端程式庫]: mobile-services-android-how-to-use-client-library.md

[gcm 物件]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[什麼是通知中樞？]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=July15_HO1-->