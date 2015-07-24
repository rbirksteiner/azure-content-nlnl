<properties 
	pageTitle="Windows Phone Silverlight Reach SDK 整合" 
	description="如何將 Azure Mobile Engagement Reach 與 Windows Phone Silverlight 應用程式整合" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="piyushjo" />

#Windows Phone Silverlight Reach SDK 整合

依照本指南進行之前，您必須先遵循 [Windows Phone Silverlight Engagement SDK 整合](mobile-engagement-windows-phone-integrate-engagement.md)中所述的整合程序。

##將 Engagement Reach SDK 內嵌到您的 Windows Phone Silverlight 專案

您不需要新增任何項目。`EngagementReach` 的參考和資源已在您的專案中。

> [AZURE.TIP]您可以自定專案的 `Resources` 資料夾中的影像，尤其是品牌圖示 (預設為 Engagement 的圖示)。

##新增功能

Engagement Reach SDK 需要一些額外的功能。

開啟 `WMAppManifest.xml` 檔案，並確定已宣告下列功能：

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

第一項是 MPNS 服務用於允許顯示快顯通知的功能。第二項是將瀏覽器工作內嵌到 SDK 的功能。

編輯 `WMAppManifest.xml` 檔案，並在 `<Capabilities />` 標記內加入：

	<Capability Name="ID_CAP_PUSH_NOTIFICATION" />
	<Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##啟用 Microsoft 推播通知服務

若要使用「Microsoft 推播通知服務」(簡稱 MPNS)，您的 `WMAppManifest.xml` 檔案必須包含 `<App />` 標記，且 `Publisher` 屬性設為您專案的名稱。

##初始化 Engagement Reach SDK

### Engagement 組態

Engagement 組態會集中在您專案的 `Resources\EngagementConfiguration.xml` 檔案中。

編輯此檔案來指定 Reach 的組態：

-   (選擇性) 在 `<enableNativePush>` 和 `</enableNativePush>` 標記之間指定是否啟用原生推送 (MPNS) 的設定 (預設為 `true`)。
-   (選擇性) 在 `<channelName>` 和 `</channelName>` 標記之間指定推送通道的名稱，提供應用程式目前使用的推送通道名稱或保留空白。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	engagementConfiguration.Reach.EnableNativePush = true;                  
	/* [Optional] whether the native push (MPNS) is activated or not. */
	
	engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
	/* [Optional] Provide the same channel name that your application may currently use. */
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP]您可以指定應用程式的 MPNS 推播通道之名稱。根據預設，Engagement 會依 appId 建立名稱。您不需要自行指定名稱，除非您打算於 Engagement 之外使用該推播通道。

### Engagement 初始化

修改 `App.xaml.cs`：

-   新增至您的 `using` 陳述式：

		using Microsoft.Azure.Engagement;

-   在 `Application_Launching` 中，將 `EngagementReach.Instance.Init` 插入 `EngagementAgent.Instance.Init` 後方：

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

-   在 `Application_Activated` 方法中插入 `EngagementReach.Instance.OnActivated`：

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

> [AZURE.IMPORTANT]`EngagementReach.Instance.Init` 會在專用的執行緒中執行。您不必自行進行此作業。

##應用程式商店提交考量事項

Microsoft 對於推播通知的使用有一些規定：

摘錄自 Microsoft [應用程式原則]文件，第 2.9 節：

1) 您必須詢問使用者是否要接收推播通知。然後，在您的設定中加入停用推播通知的方法。

EngagementReach 物件提供兩種方法來管理加入/退出、`EnableNativePush()` 和 `DisableNativePush()`。例如，您可以在設定中建立有開關的選項，以停用或啟用 MPNS。

您也可以選擇透過 Engagement 組態 <windows-phone-sdk-reach-configuration> 來停用 MPNS。

> 2.9.1) 應用程式必須先描述所提供之通知的內容，並取得使用者明確的許可 (選擇加入)，且必須提供使用者可以選擇退出接收推播通知的機制。使用 Microsoft 推播通知服務提供的所有通知必須與提供給使用者的描述一致，且必須遵守所有適用的[應用程式原則][Content Policies]和[適用於特定應用程式類型的額外需求]。

2) 您不應該使用太多推播通知。Engagement 將為您處理通知。

> 2.9.2) 應用程式及其對 Microsoft 推播通知服務的使用不得過度使用 Microsoft 推播通知服務的網路容量或頻寬，或以過度的推播通知對 Windows Phone 或其他 Microsoft 裝置或服務造成大量的負荷 (由 Microsoft 之合理的處理權判斷)，且不得傷害或干擾任何 Microsoft 網路或伺服器，或任何協力廠商伺服器或連線至 Microsoft 推播通知服務的網路。

3) 請勿依賴 MPNS 傳送重要資訊。Engagement 使用 MPNS，所以此規定也適用於在 Engagement 前端所建立的活動。

> 2.9.3) Microsoft 推播通知服務不得用來傳送緊急或攸關生命安全的通知，包括但不限於與醫療裝置或狀況相關的緊急通知。Microsoft 清楚聲明對於 Microsoft 推播通知服務或 Microsoft 推播通知服務之通知的傳送，不提供不會有任何中斷、錯誤，或為即時運作的任何擔保。

**如果您不遵守這些建議，我們無法保證您的應用程式會通過驗證程序。**

##處理資料推送 (選擇性)

如果您希望您的應用程式接收 Reach 資料推送，您必須實作 EngagementReach 類別的兩個事件：

	EngagementReach.Instance.DataPushStringReceived += (body) =>
	{
	   Debug.WriteLine("String data push message received: " + body);
	   return true;
	};
	
	EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
	{
	   Debug.WriteLine("Base64 data push message received: " + encodedBody);
	   // Do something useful with decodedBody like updating an image view
	   return true;
	};

您可以看到每個方法的回呼會傳回布林值。Engagement 會在發送資料推送之後傳送回饋到它的後端。如果回呼傳回 false，會傳送 `exit` 回饋。否則將會是 `action`。如果沒有設定事件的回呼，就會傳送 `drop` 回饋到 Engagement。

> [AZURE.WARNING]Engagement 無法接收單一資料推送的多個回饋。如果計畫在單一事件上設定多個處理常式，請留意回饋將與最後一個傳送的對應。在此情況下，我們建議一律傳回相同的值，避免在前端有令人困惑的回饋。

##自訂 UI (選擇性)

### 第一步

我們讓您可以自訂 Reach UI。

若要這樣做，您必須建立 `EngagementReachHandler` 類別的子類別。

**範例程式碼：**

	using Microsoft.Azure.Engagement;
	
	namespace Example
	{
	   internal class ExampleReachHandler : EngagementReachHandler
	   {
	      // Override EngagementReachHandler methods depending on your needs
	   }
	}

然後，使用 `Application_Launching` 方法中 `App.xaml.cs` 類別的自訂物件，設定 `EngagementReach.Instance.Handler` 欄位的內容。

**範例程式碼：**

	private void Application_Launching(object sender, LaunchingEventArgs e)
	{
	   // your app initialization 
	   EngagementReach.Instance.Handler = new ExampleReachHandler();
	   // Engagement Agent and Reach initialization
	}

> [AZURE.NOTE]根據預設，Engagement 會使用自己的 `EngagementReachHandler` 實作。您不需要自己建立，但如果有需要，您不需要覆寫每個方法。預設行為是選取 Engagement 基底物件。

### 版面配置

根據預設，Reach 會使用 DLL 的內嵌資源來顯示通知和頁面。

不過，您可以在這些元件中使用自己的資源以反映您的品牌。

您可以在您的子類別中覆寫 `EngagementReachHandler` 方法，藉此告訴 Engagement 要使用您的版面配置：

**範例程式碼：**

	// In your subclass of EngagementReachHandler
	
	public override string GetTextViewAnnouncementUri()
	{
	   // return the path of your own xaml
	}
	
	public override string GetWebViewAnnouncementUri()
	{
	   // return the path of your own xaml
	}
	
	public override string GetPollUri()
	{
	   // return the path of your own xaml
	}
	
	public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
	{
	   // return a new instance of your own notification
	}

> [AZURE.TIP]`CreateNotification` 方法可以傳回 Null。通知將無法顯示，且觸達活動將會被捨棄。

若要簡化您的版面配置實作，我們也提供可讓您做為程式碼基礎的 xaml。它們在 Engagement SDK 封存內 (/src/reach/)。

> [AZURE.WARNING]我們提供的來源與我們使用的完全相同。因此，如果您想要直接修改它們，別忘了變更命名空間和名稱。

### 通知的位置

根據預設，應用程式內通知會顯示在應用程式底部的左側。您可以透過覆寫 `EngagementReachHandler` 物件的 `GetNotificationPosition` 方法來變更此行為。

	// In your subclass of EngagementReachHandler
	
	public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
	{
	   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
	}

您目前可以選擇 `BOTTOM` (預設) 或 `TOP` 兩種位置。

### 啟動訊息

當使用者按一下系統通知 (快顯通知) 時，Engagement 會啟動該應用程式、載入推播訊息的內容，並顯示對應之活動的頁面。

啟動應用程式與頁面顯示之間會有延遲 (取決於您的網路速度)。

若要向使用者指示正在載入項目，您應該提供視覺化的資訊，例如進度列或進度列指示器。Engagement 無法自行處理這些，但有提供您幾個處理常式。

若要實作回呼，請執行：

	/* The application has launched and the content is loading.
	 * You should display an indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
	
	/* The application has finished loading the content and the page
	 * is about to be displayed.
	 * You should hide the indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
	
	/* The content has been loaded, but an error has occurred.
	 * You can provide an information to the user.
	 * You should hide the indicator here.
	 */
	EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

您可以在 `App.xaml.cs` 檔案的 `Application_Launching` 方法中設定回呼，最好設定在 `EngagementReach.Instance.Init()` 呼叫之前。

> [AZURE.TIP]每個處理常式都是由 UI 執行緒呼叫。在使用 MessageBox 或 UI 相關的項目時您不必擔心。

[應用程式原則]: http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]: http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[適用於特定應用程式類型的額外需求]: http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 

<!---HONumber=July15_HO1-->