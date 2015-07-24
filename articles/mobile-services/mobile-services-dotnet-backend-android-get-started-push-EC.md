<properties 
	pageTitle="開始使用推播 (Android) | 行動開發人員中心" 
	description="了解如何使用 Azure 行動服務傳送推播通知至 Android .Net 應用程式。" 
	services="mobile-services, notification-hubs" 
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
	ms.date="05/12/2015" 
	ms.author="ricksal"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push-EC.md)]

##概觀

本主題說明如何使用 Azure 行動服務傳送推播通知至 Android 應用程式。在本教學課程中，您會使用 Google 雲端通訊 (GCM) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[將行動服務新增至現有的應用程式]，將您的專案連接到行動服務。同樣地，本教學課程也需要 Visual Studio 2013。

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [啟用 GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>設定行動服務以傳送推播要求

1. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-selection.png)

2. 按一下 [推播] 索引標籤，輸入前一個程序中從 GCM 取得的 [API Key] 值，然後按一下 [儲存]。

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-push-tab-android.png)

> [AZURE.IMPORTANT]如果您在入口網站的 [推播] 索引標籤中設定進階推播通知的 GCM 認證，這些認證將會與通知中心共用，以設定您的應用程式適用的通知中心。


您的行動服務現在已設定成使用 GCM 和通知中心。


##<a name="download-the-service"></a>將服務下載至您的本機電腦

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>更新伺服器以傳送推播通知

1. 在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案頂端新增下列 `using` 陳述式：


		using System;
		using System.Collections.Generic;

2. 以下列程式碼更新 `PostTodoItem` 方法定義：

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    此程式碼會在插入 Todo 項目之後傳送推播通知 (含所插入項目的文字)。如果發生錯誤，程式碼將會新增可透過管理入口網站從行動服務的 [記錄檔] 索引標籤來檢視的錯誤記錄項目。


##<a name="publish-the-service"></a>將行動服務發佈至 Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>將推播通知新增至應用程式

###驗證 Android SDK 版本

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考 [設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [新增 Play 服務](../../includes/mobile-services-add-Google-play-services-EC.md)]

###新增程式碼

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push-EC.md)]

##<a name="test-app"></a>對已發佈的行動服務進行應用程式測試

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

###如果您使用模擬器進行測試...

請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1. 從 [視窗] 中選取 [Android Virtual Device Manager]，選取您的裝置，按一下 [編輯] (如果您沒有任何裝置，則按 [新增])。

	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager.png)

2. 在 [目標] 中選取 [Google API] (或 [Google APIs x86])，然後按一下 [確定]。

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager-edit.png)

	如此會使 AVD 變成使用 Google API。如果您安裝了數個版本的 Android SDK，請確定 API 層級符合您先前在專案屬性中設定的層級。

###<a id="local-testing"></a>啟用推播通知以進行本機測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###執行測試

1. 在 Eclipse 的 [執行] 功能表中按一下 [執行]，以啟動應用程式。

2. 在應用程式中，輸入有意義的文字，如 _A new Mobile Services task_，然後按一下 [加入] 按鈕。

  	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-quickstart-push1-android.png)

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

* [開始使用資料] <br/>深入了解如何使用行動服務來儲存與查詢資料。

* [開始使用驗證] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中心應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>取得通知中心解決方案的疑難排解和偵錯指引。

* [如何使用適用於行動服務的 Android 用戶端程式庫] <br/>深入了解如何搭配 Android 使用行動服務。
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[開始使用行動服務]: mobile-services-dotnet-backend-android-get-started.md
[將行動服務新增至現有的應用程式]: mobile-services-dotnet-backend-android-get-started-data.md
[開始使用驗證]: mobile-services-dotnet-backend-android-get-started-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660

[如何使用適用於行動服務的 Android 用戶端程式庫]: mobile-services-android-how-to-use-client-library.md

[Send push notifications to authenticated users]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[什麼是通知中心？]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->