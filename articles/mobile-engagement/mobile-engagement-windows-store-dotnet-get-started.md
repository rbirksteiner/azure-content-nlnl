<properties
	pageTitle="開始使用適用於 Windows 通用 app 的 Azure Mobile Engagement"
	description="了解如何使用適用於 Windows 通用 app 的 Azure Mobile Engagement 執行分析和傳送推播通知。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# 開始使用適用於 Windows 通用 app 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給 Windows 通用 app 的分佈使用者。本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。您將建立一個空白的 Windows 通用 app，以使用 Windows 通知服務 (WNS) 來收集基本的應用程式使用資料及接收推播通知。完成之後，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 處理特定使用者與裝置群組。

本教學課程需要下列各項：

+ Visual Studio 2013
+ [Mobile Engagement Windows 通用 SDK]

> [AZURE.IMPORTANT]完成本教學課程是所有其他 Windows 通用 app 的 Mobile Engagement 教學課程的先決條件。若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>設定 Windows 通用 app 的 Mobile Engagement

1. 登入 Azure 管理入口網站，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後 [建立]。

   	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![][8]

	- **應用程式名稱**：輸入您應用程式的名稱。可自由使用任何字元。
	- **平台**：選取該應用程式 (如果您的應用程式是以多個平台為目標，請針對每個平台重複參與本教學課程) 的目標平台 (**Windows 通用**)。
	- **應用程式資源名稱**：這是此應用程式可透過 API 與 URL 存取時的名稱。您只可以使用 傳統 URL 自字元。自動產生的名稱應該能提供您強健的基礎。建議您附加平台名稱，以避免發生名稱衝突，因為名稱必須是唯一的。
	- **位置**：選取將裝載此應用程式 (更重要的是其「集合」) 的資料中心 。
	- **集合**：如果您已經有建立應用程式，請選取之前建立的集合，若沒有則選取 [新集合]。
	- **集合名稱**：這代表您的應用程式群組。它也會確保您所有的應用程式都在一個群組中，以允許計量的彙總計算。如果適用於此欄位，建議使用您的公司名稱或部門。

	> [AZURE.TIP]如果您的跨平台 app 將以 Windows 和 Windows Phone 兩個平台為目標，那麼您仍應該建立兩個 Mobile Engagment 應用程式，讓每個支援的平台各使用一個。這是為了確定您可以建立適當的對象區隔，以及正確地針對各平台傳送目標式通知。

4. 選取剛才在 [應用程式] 索引標籤建立的應用程式。

5. 按一下 [連線資訊] 來顯示要置入您行動應用程式中 SDK 整合的連線設定。

   	![][10]

6. 複製 [連接字串]，您將需要它在應用程式程式碼中來識別這個應用程式，以及從您的跨平台 app 與 Mobile Engagement 連線。

   	![][11]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知所需的最低設定。您可以在 [Mobile Engagement Windows 通用 SDK 文件]中找到完整的整合文件。

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

###建立一個新的 Windows 通用 app 專案

如果您已經有應用程式且已經熟悉 Windows 通用 app 開發過程，可以略過此步驟。

1. 啟動 Visual Studio，並在主畫面上選取 [新增專案...]。

2. 在快顯視窗中，依序選取 `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`。填入應用程式 `Name` 和 `Solution name`，然後按一下 [確定]。

   	![][13]

現在已建立新的 Windows 通用 app 專案，接著我們要將 Azure Mobile Engagement SDK 整合至其中。

###將您的應用程式連線至 Mobile Engagement 後端

1. 將 [Mobile Engagement Windows 通用 SDK] Nuget 封裝安裝到專案中。如果您是 Windows 和 Windows Phone 兩個平台為目標，那麼您需要對這些專案執行此操作。相同的 Nuget 封裝會在每個專案中放置正確的平台專屬二進位檔。

2. 開啟 `Package.appxmanifest` 並新增下列項目 (如果沒有自動新增)：

		Internet (Client)

	![][20]

3. 現在貼上您稍早為 Mobile Engagement 應用程式複製的連接字串，請將該字串貼在 `Resources\EngagementConfiguration.xml` 檔案中的 `<connectionString>` 和 `</connectionString>` 標記之間：

	![][22]

	>[AZURE.TIP]如果您的應用程式將以 Windows 和 Windows Phone 兩個平台為目標，那麼您仍應該建立兩個 Mobile Engagment 應用程式，讓每個支援的平台各使用一個。這是為了確定您可以建立適當的對象區隔，以及正確地針對各平台傳送目標式通知。

4. 在 `App.xaml.cs` 檔案中：

	a.新增 `using` 陳述式：

			using Microsoft.Azure.Engagement;

	b.在 `OnLaunched` 方法中初始化 SDK：

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c.在 `OnActivated` 方法中插入以下項目，並新增此方法 (如果尚未出現)：

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。我們會使用 Mobile Engagement SDK 所提供的 `EngagementPage` 來將我們的 `MainPage` 分成子類別，以達到此目的。

1. 	新增 `using` 陳述式：

		using Microsoft.Azure.Engagement;

2. 使用 `EngagementPage` 替換 `MainPage` 的超級類別 (位於 `Page` 之前)：

	![][23]

3. 在您的 `MainPage.xml` 檔案中：

	a.新增至命名空間宣告：

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b.將 xml 標籤名稱內的 `Page` 以 `engagement:EngagementPage` 取代

###確認您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站。

	在 Azure 入口網站中，確定您已位於我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您將登陸您應用程式的 Engagement 入口網站。從該處按一下 [監視器] 索引標籤，如下所示。![][30]

3. 監視器已經準備好為您即時顯示任何將啟動應用程式的裝置。

4. 回到 Visual Studio，在模擬器或連線的裝置中啟動您的應用程式。

5. 如果應用程式順利運作，您會在監視器中即時看到一個工作階段！![][33]

**恭喜！** 您已成功完成本教學課程的第一個步驟，並有一個已經連線至 Mobile Engagement 後端，且正在傳送資料的應用程式。

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

###啟用您的應用程式接收 WNS 推播通知

1. 在 `Package.appxmanifest` 檔案，`Application` 索引標籤中的 `Notifications` 下，對 `Toast capable:` 選取 `Yes`：

	![][35]

###初始化 REACH SDK

1. 在 `App.xaml.cs` 中，於代理程式初始化後，在 `OnLaunched` 函數中呼叫 `EngagementReach.Instance.Init();`：

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. 在 `App.xaml.cs` 中，於代理程式初始化後，在 `OnActivated` 函數中呼叫 `EngagementReach.Instance.Init(e);`：

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

您已經完成傳送「快顯通知」的設定了，現在我們將驗證您是否正確完成這項基本的整合。

###授與 Mobile Engagement 存取權以傳送 Notifications

1. 若要取得您的`Package security identifier (SID)` 和`Secret Key`，必須將您的 Windows 市集應用程式與您的應用程式建立關聯。您可以從 [Windows 市集開發人員中心]建立一個應用程式，並且確定從 Visual Studio **將應用程式與市集建立關聯**。

2. 接著瀏覽至您的 Mobile Engagement 入口網站的 [設定]，然後按一下左側的 `Native Push` 區段。

3. 按一下 `Edit` 按鈕，輸入您的 `Package security identifier (SID)` 和 `Secret Key`，如下所示：

	![][36]

##<a id="send"></a>傳送通知至應用程式

我們將建立一個簡單的「推播通知」活動，它將會傳送推播通知至我們的應用程式。

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [觸達] 索引標籤

2. 按一下 [新公告] 以建立您的推送活動。![][37]

3. 遵循下列步驟來設定活動的第一個欄位：![][38]

	a.為您的活動命名。

	b.將 [傳遞時間] 選取為 [任何時候]，這樣無論應用程式是否啟動都能接收通知。

	c.在通知文字中，輸入 [標題]，這在推播中會以粗體顯示。

	d.然後輸入您的訊息。

4. 向下捲動，在 [內容] 區段中選取 [僅通知]。![][39]

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動，並按一下 [建立] 按鈕以儲存活動！

6. 最後一個步驟，請按一下 [啟用] 以啟用活動，並傳送推播通知。![][41]

您現在應該會在裝置上看到一則活動的快顯通知，應用程式應為關閉狀態才能看見此快顯通知。如果您曾執行過應用程式，請確定已在幾分鐘前將它關閉，您才能啟用活動以接收快顯通知。如果您想要整合應用程式內通知，讓通知能在應用程式開啟時顯示，請參閱 [Windows 通用 app - 重疊整合]。

<!-- URLs. -->
[Mobile Engagement Windows 通用 SDK 文件]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows 通用 SDK]: http://go.microsoft.com/?linkid=9864592
[Windows 市集開發人員中心]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows 通用 app - 重疊整合]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png
 

<!---HONumber=July15_HO2-->