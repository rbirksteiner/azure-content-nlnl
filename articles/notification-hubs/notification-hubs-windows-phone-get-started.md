<properties
	pageTitle="開始使用 Azure 通知中心"
	description="了解如何使用 Azure 通知中心推播通知。"
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="06/16/2015"
	ms.author="wesmc"/>
# 開始使用通知中心

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本主題將示範如何使用 Azure 通知中樞，將推播通知傳送到 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式。如果您的目標是 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md) 版本。在本教學課程中，您將使用 Microsoft 推播通知服務 (MPNS)，建立可接收推播通知的空白 Windows Phone 8 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

> [AZURE.NOTE]通知中心 Windows Phone SDK 不支援將 WNS 與 Windows Phone 8.1 Silverlight 應用程式搭配使用。若要搭配使用 WNS (而非 MPNS) 與 Windows Phone 8.1 Silverlight 應用程式，請遵循使用 REST API 的這個範例[通知中心 - WP Silverlight 教學課程]。

本教學課程將示範使用通知中心的簡單廣播案例。

##必要條件

本教學課程需要下列各項：

+ [Visual Studio 2012 Express for Windows Phone] 或更新版本。

完成本教學課程是 Windows Phone 8 應用程式所有其他通知中心教學課程的先決條件。

> [AZURE.NOTE]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)。

##建立您的通知中樞

1. 登入 [Azure 管理入口網站]，並按一下畫面底部的 [+NEW]。

2. 依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

   	![][7]

3. 為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

   	![][8]

4. 按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [設定] 索引標籤。

   	![][9]

5. 按一下頂端的 [Notification Hubs] 索引標籤，然後按一下您剛才建立的通知中心。

   	![][10]

6. 按一下底部的 [連接資訊]。記下這兩個連接字串。

   	![][12]

7. 按一下 [設定] 索引標籤，然後按一下 [Windows Phone notifications settings] 區段中的 [Enable unauthenticated push notifications] 核取方塊。

   	![][15]

現在您已擁有註冊 Windows Phone 8 應用程式和傳送通知所需的連接字串。

> [AZURE.NOTE]本教學課程使用處於未通過驗證模式的 MPNS。MPNS 未通過驗證模式內含您可傳送至每個通道的通知限制。通知中心支援 [MPNS 驗證模式](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx) (英文)。<!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##將您的應用程式連接到通知中樞

1. 在 Visual Studio 中建立新的 Windows Phone 8 應用程式。

   	![][13]

	在 Visual Studio 2013 Update 2 或更新版本中，您必須改為建立 Windows Phone Silverlight 應用程式。

	![][11]

2. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。

	此時會顯示 [管理 NuGet 封裝] 對話方塊。

3. 搜尋 `WindowsAzure.Messaging.Managed`，然後按一下 [**安裝**] 並接受使用條款。

	![][20]

	這會使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 封裝</a>來下載、安裝並新增適用於 Windows 的 Azure 訊息程式庫參考。

4. 開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. 在 App.xaml.cs 中 **Application_Launching** 方法頂端上的下列程式碼：

	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    請確定插入您的中心名稱，及您在上一節中取得、名為 **DefaultListenSharedAccessSignature** 的連接字串。此程式碼會從 MPNS 中擷取應用程式的 ChannelURI，然後向您的通知中心註冊該 ChannelURI。它也保證每次啟動應用程式時，ChannelURI 便會在通知中心中註冊。

	>[AZURE.NOTE]本教學課程將傳送快顯通知給裝置。傳送磚通知時，您必須在通道上改為呼叫 **BindToShellTile** 方法。若要同時支援快顯和磚通知，請呼叫 **BindToShellTile** 和 **BindToShellToast**。

6. 在 [方案總管] 中，展開 [**屬性**]、開啟 WMAppManifest.xml 檔案、按一下 [**功能**] 索引標籤，然後確定已核取 [**ID___CAP___PUSH_NOTIFICATION**] 功能。

   	![][14]

   	This ensures that your app can receive push notifications.

7. 按 F5 鍵以執行應用程式。

	此時會顯示註冊訊息。

##從後端傳送通知

您可以使用 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，從任何後端使用通知中心傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式來傳送通知。如需從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱**開始在行動服務中使用推播通知** ([.NET backend](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [JavaScript backend](../mobile-services-javascript-backend-windows-phone-get-started-push.md))。如需使用 REST API 傳送通知的範例，請參閱**如何從 Java/PHP 使用通知中心** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md))。

1. 以滑鼠右鍵按一下方案，選取 [新增] 和 [新增專案...]，然後按一下 [Visual C#] 下方的 [Windows] 和 [主控台應用程式]，再按一下 [確定]。

   	![][6]

	即會將新的 Visual C# 主控台應用程式新增到方案中。您也可以在個別方案中進行此項作業。

4. 按一下滑鼠右鍵，再依序按一下 [工具]、[程式庫套件管理員] 和 [套件管理器主控台]。

	即會顯示套件管理器主控台。

6. 在主控台視窗中，將 [預設專案] 設為您的新主控台應用程式專案，然後在主控台視窗中執行下列命令：

        Install-Package WindowsAzure.ServiceBus

	這會使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>新增 Azure 服務匯流排 SDK 的參考。

5. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

6. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	請務必使用出現在入口網站 [通知中心] 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置。此外，請將連接字串預留位置取代為您在「設定您的通知中心」一節中取得，且名為 **DefaultFullSharedAccessSignature** 的連接字串。

	>[AZURE.NOTE]請確定您使用的連接字串具有 [**完整**] 存取權，而非 [**接聽**] 存取權。接聽存取權的字串沒有傳送通知的權限。

4. 接著在您的 Main 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

5. 當您的 Windows Phone 模擬器正在執行且您的應用程式已關閉時，將主控台應用程式專案設為預設啟動專案，然後按 F5 鍵來執行應用程式。

	您將會收到快顯通知。點選快顯通知即可載入應用程式。

您可以在 MSDN 上的[快顯目錄] (英文) 和[磚目錄] (英文) 主題中找到所有可能的裝載。

##後續步驟

在此簡單範例中，您將廣播通知到您的所有 Windows Phone 8 裝置。為了鎖定特定使用者，請參閱教學課程[使用通知中心來推播通知給使用者]。如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞]。在[通知中心指引]中深入了解如何使用通知中心。



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Azure 管理入口網站]: https://manage.windowsazure.com/
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS 驗證模式]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[使用通知中心來推播通知給使用者]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[使用通知中心傳送即時新聞]: notification-hubs-windows-phone-send-breaking-news.md
[快顯目錄]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[磚目錄]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[通知中心 - WP Silverlight 教學課程]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp
 

<!---HONumber=July15_HO2-->