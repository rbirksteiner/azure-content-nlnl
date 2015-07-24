<properties
	pageTitle="開始使用 Azure Mobile Engagement"
	description="了解如何使用 Android 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="get-started-article" 
	ms.date="05/01/2015"
	ms.author="piyushjo" />

# 開始使用適用於 Android 應用程式的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 來了解您應用程式的使用情形，以及傳送推播通知給 Android 應用程式的分段使用者。本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。在此案例中，您先建立空白的 Android 應用程式，使用 Google 雲端通訊 (GCM) 來收集基本資料並接收推播通知。完成之後，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 處理特定使用者與裝置群組。


本教學課程需要下列各項：

+ Android SDK (假設您將使用 Android Studio)，您可以從[這裡](http://go.microsoft.com/fwlink/?LinkId=389797)下載
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]完成本教學課程是 Android 應用程式適用之所有其他 Mobile Engagement 教學課程的先決條件，若要完成此作業，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，以及 [建立]。

   	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![][8]

	1. **應用程式名稱**：您可以輸入應用程式的名稱。可自由使用任何字元。
	2. **平台**：選取該應用程式的目標平台 (如果您的應用程式以多個平台為目標，請針對每個平台重複參閱本教學課程)。
	3. **應用程式資源名稱**：這是此應用程式可透過 API 與 URL 存取時的名稱。我們建議您僅使用傳統的 URL 字元：自動產生的名稱應可提供您有力的依據。我們也建議您附加平台名稱，以避免發生名稱衝突，因為名稱必須是唯一的。
	4. **位置**：選取將裝載此應用程式的資料中心 (更重要的是其「集合」- 請參閱下方資訊)。
	5. **集合**：如果您已經有建立應用程式，請選取之前建立的集合，若沒有則選取 [新集合]。
	6. **集合名稱**：這代表您的應用程式群組。它也可以確保您所有的應用程式都在將允許彙總計算的群組中。我們強烈建議您使用公司名稱或部門名稱。


	當您完成時，請按一下檢查按鈕以完成您應用程式的建立作業。

4. 現在請在 [應用程式] 索引標籤中按一下/選取您剛剛建立的應用程式。

   	![][9]

5. 然後按一下 [連線資訊] 以顯示要置入 SDK 整合中的連線設定。

   	![][10]

6. 最後，請記下 [連線字串]，您將需要此字串來從您的應用程式程式碼中識別此應用程式。

   	![][11]

	>[AZURE.TIP]您可以使用連線字串右邊的複製圖示來將它複製到剪貼簿以方便使用。

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知所需的最低設定。完整的整合文件位於 [Mobile Engagement Android SDK 文件]中。

我們將使用 Android Studio 建立一個基本應用程式來示範整合。

###建立新的 Android 專案

如果您已經有應用程式並且熟悉 Android 開發過程，可以略過此步驟。

1. 啟動 Android Studio，然後在快顯視窗中選取 [開始新的 Android Studio 專案]。

   	![][12]

2. 填入應用程式名稱與公司網域。請記下這些資訊，以便稍後使用，然後按 [下一步]。

   	![][13]

3. 現在選取目標尺寸和 API 層級，然後按 [下一步]。
	>[AZURE.NOTE]Mobile Engagement 至少需要 API 層級 10 (Android 2.3.3)。

   	![][14]

4. 我們現在要新增活動到這個簡單的應用程式中，做為它的主要、唯一畫面。請務必選取 [空白活動]，然後按 [下一步]。

   	![][15]

5. 在精靈的最後一個畫面中，您可以因應本教學課程的用途而保留所有項目，然後按一下 [完成]。

   	![][16]

Android Studio 現在將建立要用於整合 Mobile Engagement 的示範應用程式。

###在您的專案中包含 SDK 程式庫

下載及整合 SDK 程式庫

1. 下載 [Mobile Engagement Android SDK]。
2. 將封存檔案解壓縮至電腦中的資料夾。
3. 找出此 SDK 目前版本的 .jar 程式庫，並將它複製到剪貼簿。

	![][17]

4. 導覽至專案區段 (1) 並將 .jar 貼到 libs 資料夾 (2)。

	![][18]

5. 同步專案以載入程式庫。

	![][19]


###使用「連線字串」將您的應用程式連線到 Mobile Engagement 後端

1. 將下列程式碼行複製到活動建立中 (必須只在應用程式中的一個位置完成，通常是主要活動)。

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. 回到 Azure 入口網站中您應用程式的 [連線資訊] 頁面，，並複製 [連線字串]。

	![][11]

3. 將此字串貼到 `setConnectionString` 參數中以取代所提供的範例，如下所示 (下方的 AppId 和 Sdkkey 已隱藏)。

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration 和 EngagementAgent 可能會顯示為無法解析 (在程式碼中顯示為紅色)。按一下每個無法解析的類別，然後按 Alt-Enter 鍵進行自動解析。

	![][20]

###新增權限和服務宣告

1. 將這些權限加入至您專案的 Manifest.xml 中，緊接在 `<application>` 標記之前或之後：

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	結果應如下所示：

	![][21]

2. 在 `<application>` 和 `</application>` 標記之間加入下列內容，宣告代理程式服務：

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. 在您剛才貼上的程式碼中，更換標籤中的 `"<Your application name>"`。這是設定功能表中顯示的項目，使用者可以在該功能表中看到裝置上正在執行的服務。例如，您可以在該標籤中加入「服務」這個字。

###傳送螢幕到 Mobile Engagement

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕 (活動) 到 Mobile Engagement 後端。我們使用 SDK 提供的 EngagementActivity 將活動子類別化，來達成此目的。為了完成此作業，請將 ActionBarActivity 之前的 MainActivity 超級類別更換為 EngagementActivity，如下所示：

![][22]

>[AZURE.NOTE]如果類別顯示為紅色，請不要忘記按一下該類別並按 Alt-Enter 鍵以解析類別。

##<a id="monitor"></a>如何檢查您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站。

	在 Azure 入口網站中，確定您已位於我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕。

	![][26]

2. 您將登陸您應用程式的 Engagement 入口網站。從該處按一下 [監視器] 索引標籤，如下所示。![][30]

3. 監視器可以即時顯示將會啟動您應用程式的任何裝置。![][31]

4. 回到 Android Studio，在監視器或連線的裝置中 (按一下綠色三角形，然後選取您的裝置) 啟動您的應用程式。![][32]

5. 如果應用程式順利運作，您會在監視器中看到一個工作階段！![][33]

**恭喜！** 您已成功完成本教學課程的第一個步驟，並有一個已經連線至 Mobile Engagement 後端，且正在傳送資料的應用程式。


##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

### 啟用應用程式內傳訊

1. 將下列應用程式內傳訊資源複製到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. 透過下列步驟，將資源複製到您的專案：
	1. 瀏覽回到您的 SDK 下載內容，並且複製 'res' 資料夾。

		![][23]

	2. 返回 Android Studio，選取專案檔案的「主要」部分，並且貼上以將資源加入至您的專案。

		![][24]

###指定通知的圖示

下列程式碼會定義用來在應用程式中顯示以及顯示系統通知的圖示。

雖然這是應用程式內通知的選用功能，但是對於系統通知是必要的，Android 會拒絕具有無效圖示的系統通知。

此 xml 程式碼片段要貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。

請確定您使用存在於其中一個**可繪製**資料夾 (例如 ``engagement_close.png``) 中的圖示。我們不支援 **mipmap** 資料夾。
	
		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

這只是示範語法的範例，當然，您應該依據 [Android 設計指導方針](http://developer.android.com/design/patterns/notifications.html)，使用適用於通知的圖示。

您不應該使用啟動器圖示，它的解析度不同，而且通常是在 mipmap 資料夾中，我們不支援該資料夾。

>[AZURE.TIP]若要確保使用正確的圖示解析度，您可以查看[這些範例](https://www.google.com/design/icons)。向下捲動至 [*通知*] 區段、按一下圖示，然後按一下 `PNGS` 即可下載圖示可繪製集合。您會看到對於每個版本的圖示要使用哪種解析度的可繪製資料夾。

###啟用應用程式接收 GCM 推播通知

1. 將下列內容複製並貼入 Manifest.xml 的 `<application>` 和 `</application>` 標記之間，以輸入 gcm:sender 中繼資料。下面的隱藏值 (星號) 是取自 Google Play 主控台的`project number`。請務必在專案編號後面加上 \\n。

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 將下列程式碼貼到 Manifest.xml 的 `<application>` 和 `</application>` 標記之間。取代封裝名稱 <Your package name>。

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. 將下列反白顯示的最後一組權限集新增至 `<application>` 標記之前。將 `<Your package name>` 取代為您的應用程式的實際封裝名稱。

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###授與 GCM API 金鑰的存取權給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推播通知，您必須授與 API 金鑰的存取權。完成此項作業的方法為在 Mobile Engagement 入口網站中設定並輸入您的金鑰。

1. 瀏覽至您的 Mobile Engagement 入口網站

	在 Azure 入口網站中，確定您已位於我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您現在會在 Engagement 入口網站的設定頁面中。在該處按一下 [原生推送] 區段以輸入 GCM 金鑰：![][27]

3. 按一下 [GCM 設定] 區段中 [API 金鑰] 前面的編輯圖示，如下所示：![][28]

4. 在快顯視窗中，貼上您從 [啟用 Google Cloud Messaging] 區段中取得的 GCM 伺服器金鑰，然後按一下 [確定]。[](#register)

	![][29]

您都完成了，現在我們將驗證您是否正確完成這項基本的整合。

> [AZURE.IMPORTANT]請務必建置、使用此新程式碼啟動、結束應用程式、等待大約 1 分鐘，然後再執行下列動作

##<a id="send"></a>如何傳送通知至應用程式

我們將建立一個簡單的「推播通知」活動，它將會傳送推播通知給我們的應用程式。

1. 瀏覽至 Mobile Engagement 入口網站中的 [REACH] 索引標籤。![][34]

2. 按一下 [新公告] 以建立您的推送活動。![][35]

3. 遵循下列步驟來設定活動的第一個欄位：![][36]

	1. 以任何您想要的名稱來命名活動。
	2. 將 [傳遞類型] 選取為 [系統通知 / 簡易]：這是 Android 簡易推播通知類型，具備一個標題和一小行文字。
	3. 將 [傳遞時間] 選取為 [任何時候]，這樣無論應用程式是否啟動都能接收通知。
	4. 在通知文字中，輸入 [標題]，這在推送中會以粗體顯示。
	5. 然後輸入您的訊息。

4. 向下捲動，在 [內容] 區段中選取 [僅通知]。![][37]

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動並建立您的活動以儲存它！![][38]

6. 最後一步，「啟動」您的活動。![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK 文件]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=July15_HO2-->