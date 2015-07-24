<properties
	pageTitle="開始使用適用於 Xamarin iOS 應用程式的通知中心"
	description="了解如何使用 Azure 通知中心，將推播通知傳送到 Xamarin iOS 應用程式。"
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# 開始使用通知中心

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本主題將示範如何使用 Azure 通知中心將推播通知傳送至 iOS 應用程式。在本教學課程中，您將使用 Apple 推播通知服務 (APN)，建立可接收推播通知的空白 Xamarin.iOS 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。[NotificationHubs 應用程式][GitHub]範例中提供完成的程式碼。

本教學課程將示範使用通知中心的簡單廣播案例。

##必要條件

本教學課程需要下列先決條件：

+ [XCode 6.0][Install Xcode]
+ 支援 iOS 7.0 (或更新版本) 的裝置
+ iOS Developer Program 成員資格
+ [Xamarin.iOS]
+ [Azure 行動服務元件]

   >[AZURE.NOTE]基於推播通知組態需求，您必須在具備 iOS 功能的裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

完成本教學課程是 Xamarin.iOS 應用程式所有其他通知中樞教學課程的先決條件。

> [AZURE.IMPORTANT]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。

Apple 推播通知服務 (APNS) 使用憑證來驗證您的行動服務。遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。如需正式的 APNS 功能文件，請參閱 [Apple 推播通知服務] (英文)。


##<a name="certificates"></a>產生憑證簽署要求檔案

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1. 從 Utilities 資料夾中，執行 Keychain Access 工具。

2. 按一下 [Keychain Access]，並展開 [Certificate Assistant]，然後按一下 [Request a Certificate from a Certificate Authority...]。

  	![][5]

3. 選取您的 [使用者電子郵件地址]，接著輸入 [一般名稱] 和 [CA Email Address] 值，並確定已選取 [Saved to disk]，然後按一下 [繼續]。

  	![][6]

4. 在 [另存新檔] 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 [位置] 中選取位置，然後按一下 [儲存]。

  	![][7]

  	這會在選取的位置儲存 CSR 檔；預設位置為桌面。請記住對於此檔案選擇的位置。

接著，向 Apple 註冊您的應用程式，並啟用推播通知，然後上傳這個匯出的 CSR 來建立推播憑證。

##<a name="register"></a>針對推播通知註冊應用程式

若要從行動服務將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊進行推播通知。

1. 如果您尚未註冊應用程式，請瀏覽至 Apple 開發人員中心的 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，然後使用您的 Apple ID 登入，並按一下 [**識別碼**]，接著按一下 [**應用程式識別碼**]，最後按一下 [**+**] 符號註冊新的應用程式。

   	![][105]

2. 在 [說明] 中輸入您應用程式的名稱，並且在 [Bundle Identifier] 中輸入值，接著勾選 [應用程式服務] 區段中的 [推播通知]，然後按一下 [繼續]。

   	![][106]

   	![][107]

   	![][108]


	這將產生您的應用程式識別碼，並要求您提交這個資訊。按一下 [提交]。

   	![][109]

	按一下 [提交] 之後，您將看見 [註冊完成] 畫面，如下所示。按一下 [完成]。

   	![][110]

	> [AZURE.NOTE]如果您選擇提供 **Bundle Identifier** 值而非 **MobileServices.Quickstart**，則您必須也更新 Xcode 專案中的套件組合識別碼值。

3. 找出剛才建立的應用程式識別碼，並且按一下該資料列。

   	![][111]

	按一下應用程式識別碼將顯示應用程式和應用程式識別碼的詳細資料。

   	![][112]

   	![][113]

4. 按一下 [編輯]，然後捲動至畫面底端，並按一下 [Development Push SSL Certificate] 區段下的 [建立憑證...]。

   	![][114]

	這將顯示 [Add iOS Certificate] 助理。

   	![][115]

	> [AZURE.NOTE]本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。您將憑證上傳至行動服務時，請確定設定相同的憑證類型。

5. 按一下 [選擇檔案]，瀏覽至您儲存第一個工作中建立的 CSR 檔所在的位置，然後按一下 [產生]。

  	![][116]

6. 入口網站建立憑證後，按一下 [下載]，然後按一下 [完成]。

  	![][118]

  	![][119]

   	這將下載簽署憑證並儲存至您電腦中的 **Downloads** 資料夾。

  	![][9]

    > [AZURE.NOTE]依預設，下載的檔案 (開發憑證) 的名稱會是 **aps_development.cer**。

7. 按兩下下載的推播憑證 **aps_development.cer**。

	這樣會將新的憑證安裝在金鑰鏈中，如下所示：

   	![][10]

	> [AZURE.NOTE]憑證中的名稱可能會不同，不過字首會加上 <strong>Apple Development iOS Push Notification Services:</strong> 前置詞。

	然後，您將使用此憑證產生 .p12 檔，並將該檔案上傳至通知中樞，以便能夠透過 APNS 進行推播通知。

##<a name="profile"></a>建立應用程式的佈建設定檔

1. 返回 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，並依序選取 [**佈建設定檔**] 和 [**全部**]，然後按一下 [**+**] 按鈕建立新的設定檔。這將顯示 [Add iOS Provisioning Profile] 精靈。

   	![][120]

2. 選取 [Development] 下的 [iOS App Development] 作為佈建設定檔類型，並按一下 [Continue]

   	![][121]

3. 接著，從 [App ID] 下拉式清單中選取行動服務快速入門應用程式的應用程式識別碼，並按一下 [Continue]。

   	![][122]

4. 在 [Select certificates] 畫面中，選取稍早建立的憑證，並按一下 [Continue]。

   	![][123]

5. 然後，在 [Devices] 選取要用來測試的裝置，然後按一下 [Continue]。

   	![][124]

6. 最後，在 [Profile Name] 中選擇設定檔的名稱，並按一下 [Generate]，然後按一下 [Done]。

   	![][125]

   	![][126]

  	這將建立新的佈建設定檔。

7. 在 Xcode 中，開啟 Organizer，選取 Devices 檢視，並選取左窗格的 [Provisioning Profiles] 區段中顯示的 [Provisioning Profiles]，然後匯入您剛才建立的佈建設定檔。

8. 在左邊選取您的裝置，並再次匯入佈建設定檔。

9. 在 Keychain Access 中，以滑鼠右鍵按一下新憑證，按一下 [Export]，並且輸入憑證的名稱，接著選取 **.p12** 格式，然後按一下 [Save]。

   	![][18]

  	記下匯出憑證的檔案名稱和位置。

這將確保 Xcode 專案使用新的設定檔進行程式碼簽署。接著，您必須將憑證上傳至通知中心。

##<a name="configure-hub"></a>設定通知中心

1. 登入 [Azure 管理入口網站]，並按一下畫面底部的 [+新增]。

2. 依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

   	![][27]

3. 為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

   	![][28]

4. 按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [設定] 索引標籤。

   	![][29]

5. 按一下頂端的 [Notification Hubs] 索引標籤，然後按一下您剛才建立的通知中心。

   	![][210]

6. 選取頂端的 [設定] 索引標籤，然後按一下 Apple 通知設定的 [上傳]。然後選取稍早匯出的 **.p12** 憑證，以及憑證的密碼。務必選擇要使用「生產」(如果要傳送推播通知給在市集購買您應用程式的使用者) 或「沙箱」(開發期間) 推播服務。

   	![][211]

7. 按一下頂端的 [儀表板] 索引標籤，然後按一下 [連接資訊]。記下這兩個連接字串。

   	![][212]

現在已將您的通知中心設定成使用 APN，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

##<a name="connecting-app"></a>將您的應用程式連接到通知中心

### 建立新專案

1. 在 Xamarin Studio 中，建立新的 iOS 專案，並選取 [**統一 API > 單一檢視應用程式**] 範本。

   	![][31]

2. 首先，新增 Azure 訊息元件的參考。在 [方案] 檢視中，以滑鼠右鍵按一下專案的 [元件] 資料夾，並選擇 [Get More Components]。搜尋 [**Azure 訊息**] 元件，並將該元件新增至您的專案。

3. 在 **AppDelegate.cs** 中，新增下列 using 陳述式：

    using WindowsAzure.Messaging;

4. 宣告 **SBNotificationHub** 的執行個體：

		private SBNotificationHub Hub { get; set; }

5. 建立含有下列變數的 **Constants.cs** 類別：

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. 在 **AppDelegate.cs** 中，更新 **FinishedLaunching()** 以符合下列內容：

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. 覆寫 **AppDelegate.cs** 中的 **RegisteredForRemoteNotifications()** 方法：

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. 覆寫 **AppDelegate.cs** 中的 **ReceivedRemoteNotification()** 方法：

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. 在 **AppDelegate.cs** 中建立下列 **ProcessNotification()** 方法：

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the json gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE]您可以選擇覆寫 **FailedToRegisterForRemoteNotifications()** 以處理不含無網路連線等的情況。


10. 在您的裝置上執行應用程式。

##<a name="send"></a>從後端傳送通知

您可以使用 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，從任何後端使用通知中心來傳送通知。在本教學課程中，我們將透過 .NET 主控台應用程式，以及使用節點指令碼透過行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1. 建立新的 Visual C# 主控台應用程式：

   	![][213]

2. 使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>新增 Azure 服務匯流排 SDK 的參考。在 Visual Studio 主功能表中，依序按一下 [工具]、[Library Package Manager]、[Package Manager Console]。接著，在主控台視窗中輸入：

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. 開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

3. 在 `Program` 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. 接著在 `Main` 方法中新增下一行程式碼：

         SendNotificationAsync();
		 Console.ReadLine();

5. 按 F5 鍵以執行應用程式。您應該會在裝置上收到警示。如果您使用 Wi-fi，請確定連線正在運作。

您可以在 Apple [本機和推播通知程式設計指南] (英文) 中找到所有可能的裝載。

若要使用行動服務傳送通知，請依照[開始使用行動服務]中的步驟進行，然後執行下列動作：

1. 登入 [Azure 管理入口網站]，然後選取您的行動服務。

2. 選取頂端的 [排程器] 索引標籤。

   	![][215]

3. 建立新的排定工作、插入名稱，然後選取 [隨選]。

   	![][216]

4. 在工作建立之後，按一下此工作名稱。然後按一下頂端列中的 [指令碼]。

5. 將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 [儲存]。

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. 按一下底列上的 [執行一次]。您應該會在裝置上收到警示。

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 iOS 裝置。若要以特定使用者為目標，請參閱教學課程[使用通知中心來推播通知給使用者]，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞]。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心概觀]和 [iOS 的通知中心作法] (英文)。

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[開始使用行動服務]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Azure 通知中心概觀]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 的通知中心作法]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[使用通知中心來推播通知給使用者]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心傳送即時新聞]: /manage/services/notification-hubs/breaking-news-dotnet

[本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
 

<!---HONumber=62-->