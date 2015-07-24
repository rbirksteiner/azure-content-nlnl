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
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="glenga"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 行動服務將推播通知傳送至 Windows Phone Silverlight 應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

本教學課程以 TodoList 範例應用程式為基礎。在開始本教學課程之前，您必須先完成[將行動服務新增至現有的應用程式]主題，將專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈可為您建立此連線。

>[AZURE.NOTE]若要傳送推播通知到 Windows Phone 8.1 市集應用程式，請遵循此教學課程的 [Windows 市集應用程式](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)版本進行。

##<a id="update-app"></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;

3. 新增下列程式碼至 App.xaml.cs：
	
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
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    此程式碼會從 Windows Phone 8.x "Silverlight" 使用的 Microsoft 推播通知服務 (MPNS)，擷取適用於應用程式的 ChannelURI，然後註冊該 ChannelURI，以進行推播通知。

	>[AZURE.NOTE]在本教學課程中，行動服務會傳送快顯通知給裝置。傳送磚通知時，您必須在通道上改為呼叫 **BindToShellTile** 方法。

4. 在 App.xaml.cs 中的 **Application_Launching** 事件處理常式最前面，在新的 **AcquirePushChannel** 方法中新增下列呼叫：

        AcquirePushChannel();

	這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

5. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。
  
6.	在 [方案總管] 中展開 [屬性]，開啟 WMAppManifest.xml 檔案，按一下 [功能] 索引標籤，確定已核取 **ID___CAP___PUSH_NOTIFICATION** 功能。

   	![在 VS 中啟用通知](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

   	如此可確定您的應用程式可以提出快顯通知。

##<a id="update-scripts"></a>更新伺服器指令碼以傳送推播通知

最後，您必須在 TodoItem 資料表上更新對插入作業註冊的指令碼，以傳送通知。

1. 按一下 [TodoItem]，按一下 [指令碼]，然後選取 [插入]。 

2. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	這個 insert 指令碼會在插入成功後，傳送推播通知 (含所插入項目的文字) 給 Windows Phone 應用程式註冊。

3. 按一下 [推播] 索引標籤，核取 [Enable unauthenticated push notifications]，然後按一下 [儲存]。

	這會使行動服務以未經驗證的模式連接到 MPNS，以傳送推播通知。

	>[AZURE.NOTE]本教學課程使用處於未通過驗證模式的 MPNS。在此模式中，MPNS 會限制可傳送至裝置通道的通知數。若要移除此限制，您必須按一下 [上傳] 並選取憑證，以產生憑證並加以上傳。如需產生憑證的詳細資訊，請參閱[設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知] (英文)。

##<a id="test"></a>在應用程式中測試推播通知

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

    >[AZURE.NOTE]在 Windows Phone 模擬器上測試時，可能會發生 401 未授權的 RegistrationAuthorizationException。因為 Windows Phone 模擬器將其時鐘與主機電腦同步的方式，可能會在 `RegisterNativeAsync()` 呼叫期間發生這種情形。這樣可能會產生將被拒絕的安全性權杖。若要解決這個問題，只要在測試之前，手動設定模擬器中的時鐘即可。

5. 在應用程式的文字方塊中，輸入文字 "hello push"，按一下 [儲存]，然後立即按一下啟動按鈕或返回按鈕，以離開應用程式。

   	![輸入文字到應用程式](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

  	如此會傳送插入要求給行動服務來儲存新增的項目。請注意，裝置會接收到顯示 **hello push** 的快顯通知。

	![接收到快顯通知](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]如果您仍在應用程式中，就不會收到該通知。若要在應用程式作用中的情況下，接收快顯通知，您必須處理 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) 事件。

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接著，請考慮完成下列其中一個教學課程：

+ [將推播通知傳送給驗證的使用者](mobile-services-javascript-backend-windows-phone-push-notifications-app-users.md) <br/>了解如何利用標記，從行動服務將推播通知只傳送給已驗證的使用者。

+ [將廣播通知傳送給訂閱者](../notification-hubs-windows-phone-send-breaking-news.md) <br/>了解使用者如何註冊及接收其所需之類別的推播通知。

+ [將跨平台通知傳送給訂閱者](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>了解如何使用範本從您的行動服務傳送推播通知，但不必在後端製作平台特定的裝載。


在下列主題中深入了解行動服務和通知中心：

* [Azure 通知中樞 - 診斷指導方針](../notification-hubs-diagnosing.md) <br/>了解如何疑難排解推播通知問題。

* [開始使用驗證] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [行動服務 .NET 作法概念參考資料] <br/>深入了解如何使用搭配 .NET 的行動服務。

* [行動服務伺服器指令碼參考] <br/>深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[將行動服務新增至現有的應用程式]: mobile-services-windows-phone-get-started-data.md
[開始使用驗證]: mobile-services-windows-phone-get-started-users.md

[設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md

[什麼是通知中心？]: ../notification-hubs-overview.md

 

<!---HONumber=July15_HO2-->