<properties
	pageTitle="開始使用適用於 iOS (Swift) 的 Azure Mobile Engagement"
	description="了解如何使用 iOS 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# 開始使用適用於 iOS 應用程式 (Swift) 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給 iOS 應用程式的區隔使用者。在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。完成之後，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知。

本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 處理特定使用者與裝置群組。

本教學課程需要下列各項：

+ Xcode，可以從您的 MAC App Store 安裝
+ [Mobile Engagement iOS SDK]
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center 取得

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。

> [AZURE.IMPORTANT]完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件，若要完成此課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 Azure 管理入口網站，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後 [建立]。

   	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![][8]

	- **應用程式名稱**：輸入您應用程式的名稱。可自由使用任何字元。
	- **平台**：為應用程式選取目標平台 (**iOS**。如果您的應用程式是針對多種平台，請針對每種平台重複參與此教學課程)。
	- **應用程式資源名稱**：這是此應用程式可透過 API 與 URL 存取時的名稱。您只可以使用 傳統 URL 自字元。自動產生的名稱應該能提供您強健的基礎。建議您附加平台名稱，以避免發生名稱衝突，因為名稱必須是唯一的。
	- **位置**：選取將裝載此應用程式 (更重要的是其「集合」) 的資料中心 。
	- **集合**：如果您已經有建立應用程式，請選取之前建立的集合，若沒有則選取 [新集合]。
	- **集合名稱**：這代表您的應用程式群組。它也會確保您所有的應用程式都在一個群組中，以允許計量的彙總計算。如果適用於此欄位，建議使用您的公司名稱或部門。

4. 選取剛才在 [應用程式] 索引標籤建立的應用程式。

5. 按一下 [連線資訊] 來顯示要置入您行動應用程式中 SDK 整合的連線設定。

   	![][10]

6. 複製 [連接字串]：在您的應用程式程式碼中，以及從您的「電話應用程式」與 Mobile Engagement 連接時 ，將需要它來識別這個應用程式。

   	![][11]

##<a id="connecting-app"></a>將您的應用程式連接至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。完整的整合文件位於 [Mobile Engagement iOS SDK 文件]

我們將會使用 XCode 建立基本應用程式來示範整合：

###建立新的 iOS 專案

如果您已經有應用程式，並熟悉 iOS 開發過程，可以略過此步驟

1. 啟動 Xcode，並在快顯視窗中，選取 [Create a new Xcode project]

   	![][12]

2. 現在選取 [Single View Application]，然後按 [Next]

   	![][14]

3. 輸入 [Product Name]、[Organization Name] 和 [Organization Identifier]。請確定您在語言選取 [Swift]。

   	![][40]

Xcode 會建立示範應用程式，我們將和它整合 Mobile Engagement

###將您的應用程式連線至 Mobile Engagement 後端

1. 下載 [Mobile Engagement iOS SDK]
2. 將 .tar.gz 檔案解壓縮到電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [Add files to ...]

	![][17]

4. 瀏覽至您解壓縮 SDK 的資料夾，選取 `EngagementSDK` 資料夾，然後按 [OK]。

	![][18]

5. 開啟 `Build Phases` 索引標籤，並在 `Link Binary With Libraries` 功能表中新增框架，如下所示：

	![][19]

6. 選擇 [File] > [New] > [File] > [iOS] > [Source] > [Header File] 來建立「橋接」標頭，以使用 SDK 的 Objective C API。

	![][41]

7. 編輯橋接標頭檔案來將 AzME Objective-C 程式碼公開至 Swift 程式碼，請新增以下匯入：

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. 在 [Build Settings]，請確定在 [Swift Compiler - Code Generation] 下的 [Objective-C Bridging Header] 組件設定有指向此標頭的路徑。路徑的範例：**$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (依路徑而定)**

9. 回到 Azure 入口網站中您應用程式的 [連線資訊] 頁面，並複製 [連線字串]。

	![][11]

10. 現在 `didFinishLaunchingWithOptions` 代理人中貼上連接字串。

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕 (活動) 到 Mobile Engagement 後端。

- 請開啟 `ViewController.h` 檔案，匯入 `EngagementViewController.h`，然後將 `ViewController` 介面的超級類別取代為 `EngagementViewController`。

###確認您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站

	從 Azure 入口網站，確定您已在我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您將登陸在應用程式 Engagement 入口網站的 [設定] 頁面中。從該處按一下 [監視] 索引標籤：

	![][30]

3. 監視器可以即時顯示將會啟動您應用程式的任何裝置：

	![][31]

4. 回到 Xcode，在模擬器或連接的裝置啟動您的應用程式

5. 如果它能運作，您將在監視器中看到一個工作階段！

**恭喜！** 您成功完成本教學課程的第一個步驟，有了連接到 Mobile Engagement 後端的應用程式，並且它已經在傳送資料

6. 按一下模擬器的 Home 按鈕會讓監視器中的工作階段數目回到 0，如圖上所示

	![][33]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

### 將觸達程式庫加入至專案

1. 以滑鼠右鍵按一下您的專案
2. 選取 `Add file to ...`
3. 瀏覽至您解壓縮 SDK 所在的資料夾
4. 選取 `EngagementReach` 資料夾
5. 按一下 [新增]
6. 編輯橋接標頭檔案來將 AzME Objective-C 程式碼公開至觸達標頭，請新增以下匯入：

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### 修改您的應用程式代理人

1. 在 `didFinishLaunchingWithOptions` 內建立觸達模組並將它傳遞到您現有的 Engagement 初始化行：

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###讓您的應用程式能接收 APNS 推播通知
1. 將下行新增至 `didFinishLaunchingWithOptions` 方法：

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. 新增 `didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示：

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. 新增 `didReceiveRemoteNotification` 方法，如下所示：

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###將推播憑證的存取權授與給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它對您憑證的存取權。這是藉由設定和輸入您的憑證到 Mobile Engagement 入口網站中而完成。請確定您取得 .p12 憑證，如 Apple 的文件中所述。

1. 瀏覽至您的 Mobile Engagement 入口網站。確認您正位在用於此專案的應用程式內，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您將登陸在 Engagement 入口網站的 [設定] 頁面中。從該處按一下 [原生推送] 區段以上傳 p12 憑證：

	![][27]

3. 選取您的 p12，將它上傳並輸入您的密碼：

	![][28]

4. 現在，新增佈建設定檔，並建置目標裝置的應用程式

您全都準備好了，現在我們要驗證您已正確完成這項基本整合

##<a id="send"></a>傳送通知至應用程式

現在，我們將建立簡單的推播通知活動，它會傳送推播至我們的應用程式：

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [觸達] 索引標籤

2. 按一下 [新增宣告] 來建立您的推播活動。

	![][35]

3. 設定活動的第一個欄位：

	![][36]

	- 	以任何您想要的名稱來命名活動
	- 	將 [傳遞時間] 選取為 [僅從應用程式外]：這是主要為一些文字的簡單 Apple 推播通知類型。
	- 	在通知文字中，先輸入將成為推播第一行的標題
	- 	然後輸入將成為第二行的訊息


4. 向下捲動，在 [內容] 區段中選取 [僅限通知]

	![][37]

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動並建立您的活動以儲存它！![][38]

6. 最後一步，「啟動」您的活動。![][39]

7. 您應該在裝置中看到推播通知！

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-swift-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-swift-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-swift-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-swift-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png
 

<!---HONumber=July15_HO2-->