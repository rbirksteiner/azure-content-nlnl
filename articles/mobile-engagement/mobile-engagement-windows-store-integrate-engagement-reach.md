<properties 
	pageTitle="Windows 通用 app Reach SDK 整合" 
	description="如何將 Azure Mobile Engagement Reach 與 Windows 通用 app 整合"
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
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Windows 通用 app Reach SDK 整合

依照本指南進行之前，您必須先遵循 [Windows 通用 Engagement SDK 整合](mobile-engagement-windows-store-integrate-engagement.md)文件中所述的整合程序。

##將 Engagement Reach SDK 內嵌至您的 Windows 通用專案

您不需要新增任何項目。`EngagementReach` 的參考和資源已在您的專案中。

> [AZURE.TIP]您可以自定專案的 `Resources` 資料夾中的影像，尤其是品牌圖示 (預設為 Engagement 的圖示)。在跨平台 app 上，您也可以移動共用專案上的 `Resources` 資料夾，以便在應用程式間共用其內容；但因為 `Resources\EngagementConfiguration.xml` 檔案和平台相依，所以您必須將它保留在預設位置。

##啟用 Windows 通知服務

若要在 `Application UI` 上的 `Package.appxmanifest` 檔案中使用 **Windows 通知服務** (簡稱 WNS)，請在左邊 bot 方塊中的 `All Image Assets` 上按一下。請在 `Notifications` 中方塊的右邊，將 `toast capable` 從 `(not set)` 變更為 `(Yes)`。

此外，您必須將您的應用程式與您的 Microsoft 帳戶以及 Engagement 平台同步。因此您需要建立一個帳戶或登入 [windows 開發人員中心](https://dev.windows.com)。然後，建立新的應用程式，並且尋找 SID 和秘密金鑰。在 Engagement 前端中，繼續應用程式的 `native push` 設定，並貼上您的認證。接下來，在您的專案上按一下滑鼠右鍵，依序選取 `store` 和 `Associate App with the Store...`。您只需要在同步處理之前選取您建立的應用程式。

##初始化 Engagement Reach SDK

修改 `App.xaml.cs`：

-   新增至您的 `using` 陳述式：

		using Microsoft.Azure.Engagement;

-   在 `OnLaunched` 中，將 `EngagementReach.Instance.Init` 插入 `EngagementAgent.Instance.Init` 後方：

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

-   如果您想要在您的應用程式由命令、其他應用程式或自訂配置啟動時啟用 Engagement Reach，請覆寫 `OnActivated` 方法：

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

	`EngagementReach.Instance.Init` 會在專用的執行緒中執行。您不必自行進行此作業。

> [AZURE.TIP]您可以在 `<channelName></channelName>` 上專案的 `Resources\EngagementConfiguration.xml` 檔案中，指定您應用程式的 WNS 推送通道之名稱。根據預設，Engagement 會依 appId 建立名稱。您不需要自行指定名稱，除非您打算於 Engagement 之外使用該推播通道。

##整合

Engagment 提供兩種方式實作 Reach 通知和宣告：「重疊整合」和「Web 檢視」整合。

重疊整合不需要大量程式碼寫入您的應用程式。您只需要用 EngagementPageOverlay 標記您的頁面、xaml 和 cs 檔案。此外，如果您自訂 Engagement 預設檢視，您的自訂項目會在所有已標記的頁面之間共用，而且只定義一次。但如果您的頁面需要從 EngagementPageOverlay 以外的物件繼承，那麼您就只能使用「Web 檢視」整合。

Web 檢視整合的實作較為複雜。但如果您的應用程式需要從 "Page" 以外的物件繼承，那麼您必須整合 「Web 檢視」和其行為。

> [AZURE.TIP]您應該考慮新增根層級的 `<Grid></Grid>` 元素，來包圍所有頁面內容。針對 Web 檢視整合，請新增 Webview 為此格線的子系。如果您需要在其他地方設定 Engagement 的元件，請記住您必須自行管理顯示的大小。

### 重疊整合

Engagement 提供通知和宣告顯示的重疊。

如果您想要使用它，請不要使用 Web 檢視整合。

將您 .xaml 檔案中的 EngagementPage 參考變更為 EngagementPageOverlay

-   新增至命名空間宣告：

			xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   以 `engagement:EngagementPageOverlay` 取代 `engagement:EngagementPage`：

**有 EngagementPage：**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**有 EngagementPageOverlay：**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **有適用於 8.1 的 EngagementPageOverlay：**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

然後在您的 .cs 檔案中，使用 "EngagementPageOverlay" 標記您的頁面，而不是 "EngagementPage"，並匯入 "Microsoft.Azure.Engagement.Overlay"。

			using Microsoft.Azure.Engagement.Overlay;

-   以 `EngagementPageOverlay` 取代 `EngagementPage`：

**有 EngagementPage：**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**有 EngagementPageOverlay：**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

現在此頁面將使用 Engagement 重疊機制，您不需要插入Web 檢視。

Engagement 重疊會使用它在 xaml 檔案中找到的第一個 “Grid”元素，在您的頁面上新增兩個 Web 檢視。如果您想要找出會設定 Web 檢視的位置，您可以定義名為 “EngagementGrid” 的格線，像這樣：

			<Grid x:Name="EngagementGrid"></Grid>

您可以直接在重疊通知和宣告的 xaml 和 cs 檔案上自訂它們：

-   `EngagementAnnouncement.html`：`Announcement` Web 檢視的 html 設計。
-   `EngagementOverlayAnnouncement.xaml`：`Announcement` xaml 設計。
-   `EngagementOverlayAnnouncement.xaml.cs`：`EngagementOverlayAnnouncement.xaml` 已連結的程式碼。
-   `EngagementNotification.html`：`Notification` Web 檢視的 html 設計。
-   `EngagementOverlayNotification.xaml`：`Notification` xaml 設計。
-   `EngagementOverlayNotification.xaml.cs`：`EngagementOverlayNotification.xaml` 已連結的程式碼。
-   `EngagementPageOverlay.cs`：`Overlay`宣告和通知顯示的程式碼。

### Web 檢視整合

如果您想要使用它，請不要使用重疊整合。

若要顯示 Engagement 內容，您需要在要顯示通知和宣告的每個頁面整合這兩個 xaml WebView。因此請將此程式碼新增至您的 xaml 檔案：

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **針對 8.1 整合：**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

您的關聯 .cs 檔案看起來會像這樣：

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> 這項實作的內嵌 WebView 會在裝置的螢幕旋轉時重新調整大小。

##處理資料推送 (選擇性)

如果您希望您的應用程式接收 Reach 資料推送，您必須實作 EngagementReach 類別的兩個事件：

在 App.xaml.cs 中的 "Public App(){}" 新增：

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

然後，使用 `App()` 方法中 `App.xaml.cs` 類別的自訂物件，設定 `EngagementReach.Instance.Handler` 欄位的內容。

**範例程式碼：**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE]根據預設，Engagement 會使用自己的 `EngagementReachHandler` 實作。您不需要自己建立，但如果有需要，您不需要覆寫每個方法。預設行為是選取 Engagement 基底物件。

### Web 檢視

根據預設，Reach 會使用 DLL 的內嵌資源來顯示通知和頁面。

若要提供完全自訂的可能性，我們只會使用網頁檢視。如果您想要自訂版面配置，請直接覆寫資源檔案 `EngagementAnnouncement.html` 和 `EngagementNotification.html`。Engagement 需要 `<body></body>` 內的所有程式碼正確執行。但您可以在 `engagement_webview_area` 之外新增標記。

不過，您可以決定使用自己的資源。

您可以在您的子類別中覆寫 `EngagementReachHandler` 方法，以告訴 Engagement 要使用您的版面配置，但是請小心內嵌的 Engagement 機制：

**範例程式碼：**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


根據預設，AnnouncementHTML 是 `ms-appx-web:///Resources/EngagementAnnouncement.html`。它代表設計推播訊息之內容的 html 檔案 (文字宣告、Web 宣告和投票宣告)。AnnouncementName 是 `engagement_announcement_content`。它是您 xaml 頁面中 Web 檢視設計的名稱。

NotfificationHTML 是 `ms-appx-web:///Resources/EngagementNotification.html`。它代表設計推播訊息通知的 html 檔案。NotfificationName 是 `engagement_notification_content`。它是您 xaml 頁面中 Web 檢視設計的名稱。

### 自訂

如果您保留 Engagement 物件，便可以自訂通知和宣告的 Web 檢視。請注意，Web 檢視會說明三次 (第一次在 xaml 中、第二次在 "setwebview()" 內的 .cs 檔案中，而第三次則在 html 檔案中)。

-   在 xaml 中是描述目前的圖形版面配置 Web 檢視元件。
-   您可以在 .cs 檔案中定義設定兩種 Web 檢視 (通知、宣告) 的維度之 "setwebview()"。這在應用程式調整大小時非常有效。
-   我們在 Engagement html 檔案中描述 Web 檢視的內容、設計，以及各元素之間的位置。

### 啟動訊息

當使用者按一下系統通知 (快顯通知) 時，Engagement 會啟動該應用程式、載入推播訊息的內容，並顯示對應之活動的頁面。

啟動應用程式與頁面顯示之間會有延遲 (取決於您的網路速度)。

若要向使用者指示正在載入項目，您應該提供視覺化的資訊，例如進度列或進度列指示器。Engagement 無法自行處理這些，但有提供您幾個處理常式。

若要實作回呼，在 App.xaml.cs 中的 "Public app {}" 新增：

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

您可以在 `App.xaml.cs` 檔案的 "Public App(){}" 方法中設定回呼，最好設定在 `EngagementReach.Instance.Init()` 呼叫之前。

> [AZURE.TIP]每個處理常式都是由 UI 執行緒呼叫。在使用 MessageBox 或 UI 相關的項目時您不必擔心。

##自訂配置秘訣

我們提供使用自訂配置。您可以從 Engagement 前端傳送您 Engagement 應用程式使用的不同類型之 URI。如果裝置上沒有安裝預設的應用程式，預設配置 (例如，由 Windows 管理 `http, ftp, ...`) 便會出現視窗提示。可以使用像應用程式配置的其他配置。此外，您可以在您的應用程式中使用自訂配置。

若要在您的應用程式中設定自訂配置，最簡單的方式就是開啟 `Package.appxmanifest`，然後進入 `Declarations` 面板。在 [可用宣告] 捲動方塊中選取 `Protocol` 並將它新增。以您想要的新通訊協定名稱來編輯 `Name` 欄位。

現在若要使用此通訊協定，請使用 `OnActivated` 方法編輯您的 `App.xaml.cs`，並請不要忘記也在此初始化 Engagement：

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion
 

<!---HONumber=July15_HO2-->