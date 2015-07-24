
本節說明如何從 .NET 主控台應用程式和任何其他應用程式傳送通知。如果您使用行動服務，請參閱[開始使用推播](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md)教學課程。如果您想要使用 Java 或 PHP，請參閱[如何從 Java/PHP 使用通知中樞](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)。您可以使用[通知中樞 REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)，從任何後端傳送通知。

下列程式碼會傳送通知給 Windows 市集、Windows Phone、iOS 和 Android 裝置。

如果您在完成[開始使用通知中樞][get-started]時建立了主控台應用程式，則請略過步驟 1-3。

1. 在 Visual Studio 中建立新的 Visual C# 主控台應用程式： 

   	![][13]

2. 在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]** 和 **[Package Manager Console]**，然後在主控台視窗中輸入下列資訊並按 **Enter** 鍵：

        Install-Package WindowsAzure.ServiceBus
 	
	透過使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>，將在 Azure 服務匯流排 SDK 中新增參照。

3. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

4. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

        private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template="ToastText01">" 
                        + "<text id="1">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version="1.0" encoding="utf-8"?>" +
                        "<wp:Notification xmlns:wp="WPNotification">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{"aps":{"alert":"Breaking " + category + " News!"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{"data":{"msg":"Breaking " + category + " News!"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	此程式碼會將字串陣列中每個標籤 (共六個) 的通知傳送給 Windows 市集、Windows Phone 和 iOS 裝置。使用標籤可確保裝置只會收到已登錄類別的通知。
	
	> [AZURE.NOTE]此後端程式碼支援 Windows 市集、Windows Phone、iOS 和 Android 用戶端。當尚未針對特定用戶端平台設定通知中樞時，傳送方法會傳回錯誤回應。

6. 在上述程式碼中，請使用您的通知中心名稱及先前取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with full access>` 預留位置。

7. 在 **[主要]** 方法中新增下列命令列。

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=July15_HO2-->