<properties
	pageTitle="開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement"
	description="了解如何使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement 執行分析和傳送推播通知。"
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# 開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給 Windows Phone Silverlight 應用程式的分佈使用者。本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。在課程中，您將建立一個空白的 Windows Phone Silverlight 應用程式，以使用 Microsoft 推播通知服務 (MPNS) 來收集基本資料及接收推播通知。完成之後，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知 (使用 MPNS)。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 處理特定使用者與裝置群組。

> [AZURE.NOTE]如果您的目標是 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows 通用教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

本教學課程需要下列各項：

+ Visual Studio 2013
+ [Mobile Engagement Windows Phone SDK]

> [AZURE.IMPORTANT]完成本教學課程是與 Windows Phone Silverlight 應用程式有關之所有其他 Mobile Engagement 教學課程的先決條件，而且若要完成本教學課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>設定適用於 Windows Phone Silverlight 應用程式的 Mobile Engagement

1. 登入 Azure 管理入口網站，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後 [建立]。

   	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：

   	![][8]

	- **應用程式名稱**：輸入您應用程式的名稱。可自由使用任何字元。
	- **平台**：選取該應用程式 (如果您的應用程式是以多個平台為目標，請針對每個平台重複參與本教學課程) 的目標平台 (**Windows Phone Silverlight**)。
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

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知所需的最低設定。您可以在 [Mobile Engagement Windows Phone SDK 文件]中找到完整的整合文件。

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

###建立新的 Windows Phone Silverlight 專案

1. 啟動 Visual Studio，並在主畫面上選取 [新增專案...]。

2. 在快顯視窗中，依序選取 `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`。填入應用程式 `Name` 和 `Solution name`，然後按一下 [確定]。

   	![][13]

3. 您可以選擇要將目標設為 **Windows Phone 8.0** 或 **Windows Phone 8.1** 版本。

現在已建立新的 Windows Phone Silverlight 應用程式，接著我們要將 Azure Mobile Engagement SDK 整合至其中。

###將您的應用程式連接至 Mobile Engagement 後端

1. 將 [Mobile Engagement Windows Phone SDK] Nuget 封裝安裝到專案中。

2. 在 Properties 資料夾中開啟 `WMAppManifest.xml`，然後確認 `<Capabilities />` 標記中已宣告下列項目 (如果沒有宣告，請自行新增)：

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. 現在貼上您稍早為 Mobile Engagement 應用程式複製的連接字串，請將該字串貼在 `Resources\EngagementConfiguration.xml` 檔案中的 `<connectionString>` 和 `</connectionString>` 標記之間：

	![][21]

4. 在 `App.xaml.cs` 檔案中：

	a.新增 `using` 陳述式

			using Microsoft.Azure.Engagement;

	b.在 `Application_Launching` 方法中初始化 SDK：

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c.在 `Application_Activated` 中插入以下內容

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕 (活動) 到 Mobile Engagement 後端。我們會使用 Mobile Engagement SDK 所提供的 `EngagementPage` 來將我們的 `MainPage` 分成子類別，以達到此目的。

1. 新增 `using` 陳述式：

		using Microsoft.Azure.Engagement;

2. 使用 `EngagementPage` 替換位於 `PhoneApplicationPage` 之前，`MainPage` 的超級類別，如下所示：

	![][22]

3. 在您的 `MainPage.xml` 檔案中：a. 新增命名空間宣告：

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b.將 xml 標籤名稱內的 `phone:PhoneApplicationPage` 以 `engagement:EngagementPage` 取代

###確認您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站。

	在 Azure 入口網站中，確定您已位於我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕。

	![][26]

2. 您將登陸您應用程式的 Engagement 入口網站。從該處按一下 [監視器] 索引標籤，如下所示。![][30]

3. 監視器已經準備好為您即時顯示任何將啟動應用程式的裝置。

4. 回到 Visual Studio，在模擬器或連線的裝置中啟動您的應用程式。

5. 如果應用程式順利運作，您會在監視器中看到一個工作階段！![][33]

**恭喜！** 您已成功完成本教學課程的第一個步驟，並有一個已經連線至 Mobile Engagement 後端，且正在傳送資料的應用程式。

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的應用程式來接收它們。

###啟用您的應用程式接收 MPNS 推播通知

新增「功能」到您的 `WMAppManifest.xml` 檔案：

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###初始化 REACH SDK

1. 在 `App.xaml.cs` 中，於代理程式初始化後，在 `Application_Launching` 函數中呼叫 `EngagementReach.Instance.Init();`：

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. 在 `App.xaml.cs` 中，於代理程式初始化後，在 `Application_Activated` 函數中呼叫 `EngagementReach.Instance.OnActivated(e);`：

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

您都完成了，現在我們將驗證您是否正確完成這項基本的整合。

##<a id="send"></a>傳送通知至應用程式

我們將建立一個簡單的「推播通知」活動，它將會傳送推播通知至我們的應用程式。

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [觸達] 索引標籤

2. 按一下 [新公告] 以建立您的推送活動。![][35]

3. 遵循下列步驟來設定活動的第一個欄位：![][36]

	1. 以任何您想要的名稱來命名活動。
	2. 將 [傳遞時間] 選取為 [任何時候]，這樣無論應用程式是否啟動都能接收通知。
	3. 在通知文字中，輸入 [標題]，這在推送中會以粗體顯示。
	4. 然後輸入您的訊息。

4. 向下捲動，在 [內容] 區段中選取 [僅通知]。![][37]

5. 您已經完成設定一個盡可能最基本的活動，現在向下捲動，並按一下 [建立] 按鈕以儲存活動！

6. 最後一個步驟，請按一下 [啟用] 以啟用活動，並傳送推播通知。![][39]

7. 您應該會在您的裝置上看到通知，**恭喜您！**：![][40]

<!-- URLs. -->
[Mobile Engagement Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK 文件]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=July15_HO2-->