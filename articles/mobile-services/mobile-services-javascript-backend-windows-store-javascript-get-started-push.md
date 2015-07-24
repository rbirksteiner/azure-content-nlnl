<properties 
	pageTitle="將推播通知新增至行動服務應用程式 (Windows 市集) | 行動開發人員中心" 
	description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至通用 Windows 市集應用程式。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

本主題說明如何使用 Azure 行動服務傳送推播通知至 Windows 市集應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

>[AZURE.NOTE]本主題說明如何針對 Windows 市集應用程式，使用 Windows Notification Service (WNS) 來手動設定推播通知。您可以利用 Visual Studio 2013 工具，在 Windows 應用程式專案中自動設定相同的推播通知。如需詳細資訊，請參閱此教學課程的[通用 Windows 應用程式版本](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md)。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [向 WNS 註冊您的應用程式，並設定行動服務](#register)
2. [更新應用程式以註冊通知](#update-app)
3. [更新伺服器指令碼以傳送推播通知](#update-scripts)
3. [插入資料以接收推播通知](#test)

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[開始使用資料]，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。

##<a id="register"></a>向 WNS 註冊您的應用程式，並設定行動服務

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

您的行動服務和應用程式現在都已完成設定，而可與 WNS 和通知中心搭配使用。接下來，您將更新 Windows 市集應用程式以註冊通知。

##<a id="update-app"></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 開啟 default.js 檔案，然後在建立 **MobileServiceClient** 執行個體的程式碼後面，插入下列程式碼：

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

	此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後為推播通知註冊該 ChannelURI。

2. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。

6. 開啟 Package.appxmanifest 檔案，並在 [應用程式 UI] 索引標籤中確認 [支援快顯通知] 設為 [是]。

   	![][2]

   	如此可確定您的應用程式可以提出快顯通知。

##<a id="update-scripts"></a>更新伺服器指令碼以傳送推播通知

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接下來，請考慮閱讀下個教學課程，[將推播通知傳送給驗證的使用者]，此課程說明如何使用標籤將行動服務中的推播通知指定傳送給驗證的使用者。

<!---+ [Send push notifications to authenticated users]
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

* [行動服務 .NET 作法概念參考資料] <br/>深入了解如何使用搭配 .NET 的行動服務。

* [行動服務伺服器指令碼參考] <br/>深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: ../mobile-services-windows-store-get-started.md
[開始使用資料]: mobile-services-windows-store-javascript-get-started-data.md
[開始使用驗證]: mobile-services-windows-store-javascript-get-started-users.md

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md


[將推播通知傳送給驗證的使用者]: mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users.md

[什麼是通知中心？]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-localized-breaking-news.md
 

<!---HONumber=July15_HO2-->