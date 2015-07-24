<properties 
	pageTitle="使用 Azure 行動服務及通知中心傳送通知至 Windows Phone 應用程式。" 
	description="了解如何搭配 Azure 行動服務使用通知中心傳送推播通知至 Windows Phone 應用程式。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="wesmc"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

##概觀

本主題說明如何使用 Azure 行動服務搭配 .NET 後端，利用 Microsoft 推播通知服務 (MPNS)，將推播通知傳送至 Windows Phone Silverlight 8 應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

本教學課程會以行動服務 TodoList 專案為基礎。在開始本教學課程之前，您必須先完成[將行動服務新增至現有的應用程式]，將專案連接到行動服務。

>[AZURE.NOTE]此教學課程的目標是 Windows Phone 8.1 Silverlight 應用程式。如果您是要建立 Windows Phone 8.1 市集應用程式，則請參閱此教學課程的 [Windows 市集應用程式](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push)版本。如需 Windows Phone Silverlight 應用程式及其與 Windows Phone 市集應用程式比較的相關資訊，請參閱 [Windows Phone Silverlight 8.1 應用程式]。

##更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;

2. 將下列 `AcquirePushChannel` 方法新增至 `App` 類別：

        public static HttpNotificationChannel CurrentChannel { get; private set; }	
        
        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }
            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {
                    // Register for notifications using the new channel
                    System.Exception exception = null;
                    try
                    {
                        await MobileService.GetPush()
                            .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                    }
                    catch (System.Exception ex)
                    {
                        CurrentChannel.Close();
                        exception = ex;
                    }
                    if (exception != null)
                    {
                        Deployment.Current.Dispatcher.BeginInvoke(() =>
                        {
                            MessageBox.Show(exception.Message, 
                                            "Registering for Push Notifications",
                                            MessageBoxButton.OK);
                        });
                    }
            });
            CurrentChannel.ShellToastNotificationReceived += 
                new EventHandler<NotificationEventArgs>((o, args) =>
                {
                    string message = "";
                    foreach (string key in args.Collection.Keys)
                    {
                        message += key + " : " + args.Collection[key] + ", ";
                    }
                    Deployment.Current.Dispatcher.BeginInvoke(() =>
                    {
                        MessageBox.Show(message);
                    });
            });
        }

    此程式碼會擷取應用程式的通道 URI (如果存在)。如果不存在，則會加以建立。接著，URI 將會開啟並繫結，以供快顯通知使用。在通道 URI 完全開啟後，將會呼叫 `ChannelUriUpdated` 方法的處理常式，並註冊通道，以接收推播通知。如果註冊失敗，通道將會關閉，使後續的應用程式執行可重新嘗試註冊。此時會設定 `ShellToastNotificationReceived` 處理常式，讓應用程式可在執行時接收及處理推播通知。
    
4. 在 App.xaml.cs 的 `Application_Launching` 事件處理常式中，將下列呼叫新增至新的 `AcquirePushChannel` 方法：

        AcquirePushChannel();

	這會確使在每次載入應用程式時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

5. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。
  
6. 在 Visual Studio 中開啟 Package.appxmanifest 檔案，並在 [應用程式 UI] 索引標籤上確認 [支援快顯通知] 設為 [是]。

   	![][1]

   	This makes sure that your app can raise toast notifications.

##更新伺服器以傳送推播通知

1. 在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs，並以下列程式碼更新 `PostTodoItem` 方法定義：  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

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

2. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

3. 按一下 [推播] 索引標籤，核取 [Enable unauthenticated push notifications]，然後按一下 [儲存]。

   	![行動服務推播索引標籤](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

	>[AZURE.NOTE]本教學課程使用處於未通過驗證模式的 MPNS。在此模式中，MPNS 會限制可傳送至裝置通道的通知數。若要移除此限制，您必須產生憑證，然後按一下 [上傳]<strong></strong> 並選取憑證，以上傳憑證。如需產生憑證的詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知</a>。

這會使行動服務以未經驗證的模式連接到 MPNS，以傳送推播通知。

##啟用推播通知以進行本機測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##在應用程式中測試推播通知

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

    >[AZURE.NOTE]在 Windows Phone 模擬器上測試時，可能會發生 401 未授權的 RegistrationAuthorizationException。因為 Windows Phone 模擬器將其時鐘與主機電腦同步的方式，可能會在 `RegisterNativeAsync()` 呼叫期間發生這種情形。這樣可能會產生將被拒絕的安全性權杖。若要解決這個問題，只要在測試之前，手動設定模擬器中的時鐘即可。

5. 在應用程式的文字方塊中，輸入文字 "hello push"，按一下 [儲存]，然後立即按一下啟動按鈕或返回按鈕，以離開應用程式。

   	![插入文字][2]

  	如此會傳送插入要求給行動服務來儲存新增的項目。請注意，裝置會接收到顯示 **hello push** 的快顯通知。

	![收到的推播通知](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]如果您仍在應用程式中，就不會收到該通知。若要在應用程式作用中的情況下，接收快顯通知，您必須處理 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) 事件。

##後續步驟

此教學課程示範如何讓 Windows Phone 應用程式能夠使用行動服務和通知中心來傳送推播通知的基本步驟。接著，請考慮完成下一個教學課程：[將推播通知傳送給驗證的使用者]，其顯示如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

考慮更深入了解下列行動服務和通知中心主題：

* [將驗證新增至應用程式] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中樞？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中心應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>取得通知中心解決方案的疑難排解和偵錯指引。

* [行動服務 .NET 作法概念參考資料] <br/>深入了解如何使用搭配 .NET 的行動服務。



<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png

<!-- URLs. -->
[將行動服務新增至現有的應用程式]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[將驗證新增至應用程式]: mobile-services-dotnet-backend-windows-phone-get-started-users.md

[將推播通知傳送給驗證的使用者]: mobile-services-dotnet-backend-windows-phone-push-notifications-app-users.md

[什麼是通知中樞？]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-phone-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-phone-send-localized-breaking-news.md


[行動服務 .NET 作法概念參考資料]: mobile-services-html-how-to-use-client-library.md
[Windows Phone Silverlight 8.1 應用程式]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Azure 管理入口網站]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->