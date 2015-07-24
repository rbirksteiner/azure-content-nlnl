<properties 
	pageTitle="使用 Azure 應用程式服務將推播通知新增至 Xamarin iOS 應用程式" 
	description="了解如何使用 Azure 應用程式服務將推播通知傳送至 Xamarin iOS 應用程式。" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ysxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# 將推播通知新增至 Xamarin iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

本主題說明如何使用 Azure 應用程式服務將推播通知傳送至 Xamarin iOS 8 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至[開始使用應用程式服務行動應用程式]專案。完成後，行動後端就會在每次插入記錄時傳送推播通知。

本教學課程需要下列各項：

+ iOS 8 裝置
+ iOS Developer Program 成員資格
+ [Xamarin.iOS Studio]
+ [Azure 行動服務元件]

   >[AZURE.NOTE]基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

Apple 推播通知服務 (APNS) 使用憑證來驗證您的行動應用程式。遵循這些指示建立必要的憑證，並將憑證上傳至您的行動應用程式。如需正式的 APNS 功能文件，請參閱 [Apple 推播通知服務] (英文)。

## <a name="certificates"></a>產生憑證簽署要求檔案

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1. 在公用程式中，執行 Keychain Access 工具。

2. 按一下 [Keychain Access]，並展開 [Certificate Assistant]，然後按一下 [Request a Certificate from a Certificate Authority...]。

    ![][5]

3. 輸入您的 [使用者電子郵件地址]，輸入 [一般名稱] 值，並確定已選取 [儲存至磁碟]，然後按一下 [繼續]。

    ![][6]

4. 在 [另存新檔] 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 [位置] 中選取位置，然後按一下 [儲存]。

    ![][7]
  
    請記住您所選擇的位置。

接下來，您要向 Apple 註冊應用程式、啟用推播通知，以及上傳這個匯出的 CSR 以建立推播憑證。

## <a name="register"></a>針對推播通知註冊應用程式

若要從行動應用程式將推播通知傳送至 iOS 裝置，您必須向 Apple 註冊您的應用程式，並註冊推播通知。

1. 如果您尚未註冊應用程式，請瀏覽至 Apple 開發人員中心的 iOS 佈建入口網站</a>，然後使用您的 Apple ID 登入，並依序按一下 [**識別碼**]、[**App ID**] 及 **+** 號以建立應用程式的應用程式識別碼。
    
    ![][102]

2. 在 [描述] 中輸入您的應用程式名稱，輸入並記住唯一的 [套件組合識別碼]，勾選 [應用程式服務] 區段中的 [推播通知] 選項，然後按一下 [繼續]。此範例會使用識別碼 **MobileServices.Quickstart**，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者中必須是唯一的。因此，建議您在應用程式名稱之後附加您的全名或縮寫。

    ![][103]
   
    這將產生您的應用程式識別碼，並要求您 [提交] 這個資訊。按一下 [提交]。
   
    ![][104]
   
    按一下 [提交] 之後，您將看見 [註冊完成] 畫面，如下所示。按一下 [完成]。
   
    ![][105]

3. 找出剛才建立的應用程式識別碼，並且按一下該資料列。

    ![][106]
   
    按一下應用程式識別碼將顯示應用程式和應用程式識別碼的詳細資料。按一下 [設定] 按鈕。
   
    ![][107]
   
4. 捲動到畫面底部，然後按一下 [Development Push SSL Certificate] 區段下方的 [Create Certificate...] 按鈕。

    ![][108]

    這將顯示 [Add iOS Certificate] 助理。
   
    注意：本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。您將憑證上傳至行動應用程式時，請確定設定相同的憑證類型。

5. 按一下 [**選擇檔案**]，瀏覽至您稍早儲存 CSR 檔案的位置，然後按一下 [**產生**]。

    ![][110]
  
6. 在入口網站建立憑證之後，依序按一下 [下載] 按鈕和 [完成]。
 
    ![][111]

    這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

    ![][9]

    注意：根據預設，下載的檔案 (開發憑證) 的名稱會是 <strong>aps_development.cer</strong>。

7. 按兩下下載的推播憑證 **aps_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![][10]

    注意：憑證中的名稱可能會不同，不過字首會加上 <strong>Apple Development iOS Push Notification Services:</strong> 前置詞。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動應用程式以啟用 APNS 驗證。

## <a name="profile"></a>建立應用程式的佈建設定檔
 
1. 返回 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，並依序選取 [**佈建設定檔**] 和 [**全部**]，然後按一下 [**+**] 按鈕建立新的設定檔。如此會啟動 **Add iOS Provisiong Profile** 精靈。

    ![][112]

2. 將 [**開發**] 下方的 [**iOS App 開發]** 選為佈建設定檔類型，然後按一下 **[繼續]**。

3. 接著，從 [**應用程式 ID**] 下拉式清單選取行動應用程式快速入門應用程式的應用程式 ID，然後按一下 [**繼續**]。

    ![][113]

4. 在 [Select certificates] 畫面中選取先前建立的憑證，然後按一下 [Continue]。

    ![][114]

5. 然後，在 [Devices] 選取要用來測試的裝置，然後按一下 [Continue]。
  
    ![][115]

6. 最後，在 [Profile Name] 中為設定檔挑選名稱，然後依序按一下 [Generate] 和 [Done]

    ![][116]

    這將建立新的佈建設定檔。

    ![][117]

## <a name="configure-appServiceMobile"></a>設定應用程式服務行動後端以傳送推播要求

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>更新伺服器以傳送推播通知

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。

2. 搜尋 **Microsoft.Azure.NotificationHubs**，然後為方案中的所有專案按一下 [**安裝**]。

3. 在 Visual Studio 的 [方案總管] 中，展開行動後端專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案頂端新增下列 `using` 陳述式：

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. 將下列程式碼片段新增至 **InsertAsync** 呼叫後面的 `PostTodoItem` 方法：

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    此程式碼會指示與此行動應用程式相關聯的通知中心，在 todo 項目插入後傳送推播通知。


## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>設定您的 Xamarin.iOS 應用程式

1. 在 Xamarin.Studio 中，開啟 **Info.plist**，然後使用您稍早建立的識別碼更新 [套件組合識別碼]。

    ![][121]

2. 向下捲動到 [背景模式]，並勾選 [啟用背景模式] 方塊和 [遠端通知] 方塊。

    ![][122]

3. 按兩下 [方案面板] 中的專案，以開啟 [專案選項]。

4.  選擇 [建置] 下的 [iOS 套件組合簽署]，然後選取對應的 [身分識別] 以及您為此專案設定的 [佈建設定檔]。

    ![][120]

    這將確保 Xamarin 專案使用新的設定檔進行程式碼簽署。如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin 裝置佈建]。

## <a name="add-push"></a>將推播通知新增至應用程式

1. 在 **QSTodoService** 中覆寫現有的用戶端宣告，使 **AppDelegate** 可以取得行動用戶端：
        
        public MobileServiceClient client { get; private set; }

2. 在 **AppDelegate** 中，覆寫 **FinishedLaunching** 事件：

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 在相同的檔案中，覆寫 **RegisteredForRemoteNotifications** 事件：

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. 然後，覆寫 **DidReceivedRemoteNotification** 事件：

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

您的應用程式現在已更新為支援推播通知。


## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>在應用程式中測試推播通知

1. 按 [執行] 按鈕以建置專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。
	
	> [AZURE.NOTE]您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

3. 確認您已接收到通知，然後按一下 [確定] 以關閉通知。

4. 重複執行步驟 2 並立即關閉應用程式，接著確認通知已顯示。

您已成功完成此教學課程。

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[開始使用應用程式服務行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step5.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step6.png
[7]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step7.png

[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step9.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-02.png
[103]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-03.png
[104]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-04.png
[105]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-05.png
[106]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-06.png
[107]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-07.png
[108]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-08.png

[110]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-10.png
[111]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-11.png
[112]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-12.png
[113]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-13.png
[114]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-14.png
[115]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-15.png
[116]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-16.png
[117]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-17.png

[120]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /zh-tw/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /zh-tw/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin 裝置佈建]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303

 

<!---HONumber=62-->