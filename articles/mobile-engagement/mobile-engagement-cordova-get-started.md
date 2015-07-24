<properties
	pageTitle="開始使用 Azure Mobile Engagement for Cordova/Phonegap"
	description="了解如何使用 Cordova/Phonegap 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# 開始使用 Azure Mobile Engagement for Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

此主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給以 Cordova 開發之行動應用程式的特定使用者。

在本教學課程中，我們將會使用 Mac 建立空白的 Cordova 應用程式，然後整合 Mobile Engagement SDK。它會收集基本分析資料，並針對 iOS 使用 Apple Push Notification System (APNS)、針對 Android 使用 Google Cloud Messaging (GCM) 接收推播通知。我們會將它部署到 iOS 或 Android 裝置以進行測試。

> [AZURE.IMPORTANT]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

本教學課程需要下列各項：

+ XCode，您可以從您的 Mac App Store 安裝 (適用於部署到 iOS)
+ [Android SDK 和模擬器](http://developer.android.com/sdk/installing/index.html) (適用於部署到 Android)
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center for APNS 取得
+ 您可以從您的 Google Developer Console for GCM 取得的 GCM 專案編號
+ [Mobile Engagement Cordova 外掛程式](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]您可以在 [Github](https://github.com/Azure/azure-mobile-engagement-cordova) 上找到原始程式碼和 ReadMe for the Cordova 外掛程式

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 Azure 管理入口網站，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後 [建立]。

   	![][1]

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![][2]

	- **應用程式名稱**：應用程式的名稱。 
	- **平台**：應用程式的目標平台 (根據您要在其中部署 Cordova 應用程式，選擇 **iOS** 或 **Android**)
	- **應用程式資源名稱**：此應用程式可透過 API 與 URL 存取時的名稱。 
	- **位置**：將裝載此應用程式和應用程式集合的資料中心。
	- **集合**：選取之前建立的集合，或選取「新集合」。
	- **集合名稱**：代表您的應用程式群組。它也會確保您所有的應用程式都在一個群組中，以允許計量的彙總計算。如果適用於此欄位，建議使用您的公司名稱或部門。

4. 選取剛才在 [應用程式] 索引標籤建立的應用程式。

5. 按一下 [連線資訊] 來顯示要置入您行動應用程式中 SDK 整合的連線設定。

   	![][3]

6. 複製 [連接字串]：在您的應用程式程式碼中，以及從您的「電話應用程式」與 Mobile Engagement 連接時 ，將需要它來識別這個應用程式。

   	![][4]

##<a id="connecting-app"></a>將您的應用程式連接至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。

我們將會使用 Cordova 建立基本應用程式來示範整合：

###建立新的 Cordova 專案

1. 啟動 Mac 電腦上的 [*Terminal*]，並且輸入下列項目，從預設範本建立新的 Cordova 專案：

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]請確定您最後用來部署您的 iOS 應用程式的發行設定檔是使用 'com.mycompany.myapp' 做為應用程式識別碼。

2. 執行下列項目來設定 **iOS** 的專案，並在 iOS 模擬器中執行它：

		$ cordova platform add ios 
		$ cordova run ios

3. 執行下列項目來設定 **Android** 的專案，並在 Android 模擬器中執行它：

		$ cordova platform add android
		$ cordova run android

4. 	新增 Cordova 主控台外掛程式。

		$ cordova plugin add cordova-plugin-console 

###將您的應用程式連線至 Mobile Engagement 後端

1. 安裝 Azure Mobile Engagement Cordova 外掛程式，同時提供變數值以設定外掛程式：

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	例如：

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]AppId、SDKKey 和集合可以從連接字串 **Endpoint={YourAppCollection.Domain};SdkKey={YourSDKKey};AppId={YourAppId}** 擷取

##<a id="monitor"></a>啟用即時監視

1. 在 Cordova 專案中 - 編輯 **www/js/index.js** 將呼叫加入至 Mobile Engagement，在收到 *deviceReady* 事件之後宣告新的活動。

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. 執行應用程式：
		
	- **對於 iOS**
	
		藉由執行下列動作，在 `Terminal` 視窗中於新的模擬器執行個體中啟動您的應用程式：

			cordova run ios

	- **對於 Android**
		
		藉由執行下列動作，在 `Terminal` 視窗中於新的模擬器執行個體中啟動您的應用程式：

			cordova run android

3. 您可以在主控台記錄檔中看到下列項目：

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###確認您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站

	在 Azure 入口網站中，確定您已位於我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][6]

2. 您將登陸在應用程式 **Engagement 入口網站**的 [設定] 頁面中。從該處按一下 [**監視**] 索引標籤：

	![][7]

3. 如果您在模擬器中執行的應用程式已正確設定，則您會在應用程式執行時，即時看到工作階段記錄：

	![][8]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您使用「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動。此模組在 Mobile Engagement 入口網站中稱為 REACH。以下各節將設定您的用程式來接收它們。

###設定 Mobile Engagement 的推播認證

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它對您的 Apple iOS 憑證或 GCM Server API 金鑰的存取權。
	
1. 瀏覽至您的 Mobile Engagement 入口網站。確認您正位在用於此專案的應用程式內，然後按一下底部的 [**Engage**] 按鈕：
	
	![][10]
	
2. 您將登陸在 Engagement 入口網站的 [設定] 頁面中。在這裡按一下 [**原生推播**] 區段：
	
	![][11]

3. 設定 iOS 憑證/GCM Server API 金鑰

	**[iOS]**

	a.選取您的 .p12、將它上傳並輸入您的密碼：
	
	![][12]

	**[Android]**

	a.按一下 [GCM 設定] 區段中 [**API 金鑰**] 前面的編輯圖示，如下所示：
		
	![][20]
	
	b.在快顯視窗中，貼上 GCM Server 金鑰，然後按一下 [**確定**]。
	
	![][21]

###在 Cordova 應用程式中啟用推播通知

編輯 **www/js/index.js** 將呼叫加入至 Mobile Engagement 以要求推播通知，並宣告處理常式：

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###執行應用程式

**[iOS]**

1. 我們將使用 XCode 在裝置上建置及部署應用程式，以測試推播通知，因為 iOS 只允許推播通知到實際裝置。請移至您建立 Cordova 專案的位置，並瀏覽至 **...\\platforms\\ios** 位置。在 XCode 中開啟原生 .xcodeproj 檔案。 
	
2. 建置 Cordova 應用程式並部署至 iOS 裝置，方法是使用具有佈建設定檔的帳戶，該設定檔包含憑證 (您剛剛上傳至 Mobile Engagement 入口網站) 和應用程式識別碼 (符合您在建立 Cordova 應用程式時提供的識別碼)。您可以在 XCode 中的 **Resources*-info.plist** 檔案檢查 [*套件組合識別碼*] 以進行配對。

3. 您會在您的裝置上看到標準 iOS 快顯視窗，上面顯示應用程式要求傳送通知的權限。授與權限。

**[Android]**

您只能使用模擬器執行 Android 應用程式，因為 GCM 通知只在 Android 模擬器上受到支援。

	cordova run android

##<a id="send"></a>傳送通知至應用程式

現在，我們將建立簡單的推播通知行銷活動，它會傳送推播到裝置上執行中的應用程式：

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [Reach] 索引標籤

2. 按一下 [**新增宣告**] 來建立您的推播行銷活動

	![][13]

3. 提供輸入來建立您的行銷活動：

	![][14]

	- 	提供您的行銷活動的名稱。 
	- 	**[Android]** 選取 [**傳遞類型**] 為 [*系統通知*] - [*簡單*]
	- 	在 [傳遞時間] 選取 
		- 	對於 **iOS**：[*僅限應用程式外*]
		- 	對於 **Android**：[*任何時間*]
		
		這是主要為一些文字的簡單推播通知類型。
	- 	在通知文字中，先輸入將成為推送第一行的標題
	- 	然後輸入將成為第二行的訊息

4. 向下捲動，並在 [內容] 區段選取 [**只有通知**]

	![][15]

5. [選用] 您也可以提供動作 URL。請確定它會使用在設定外掛程式的 **AZME 重新導向 URL** 變數 (例如 *myapp://test*) 時提供的 URL 配置。

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動並**建立**您的活動以儲存它。
	
	![][16]

6. 最後一步，「**啟動**」您的行銷活動
	
	![][17]

7. 您現在應該會在裝置或模擬器上看到推播通知，做為此行銷活動的一部分。

##<a id="next-steps"></a>後續步驟
[可用於 Cordova Mobile Engagement SDK 之所有方法的概觀](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO2-->