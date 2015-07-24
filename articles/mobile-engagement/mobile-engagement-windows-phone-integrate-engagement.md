<properties 
	pageTitle="Windows Phone Silverlight Engagement SDK 整合" 
	description="如何將 Azure Mobile Engagement 與 Windows Phone Silverlight 應用程式整合" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Windows Phone Silverlight Engagement SDK 整合

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

本程序說明如何以最簡單的方式啟用 Windows Phone Silverlight 應用程式內 Azure Mobile Engagement 的分析與監視功能。

下列步驟便足以啟用計算使用者、工作階段、活動、當機和技術等所有統計資料時需要的記錄檔報告。用來計算其他統計資料 (例如事件、錯誤和工作) 所需的記錄檔報告必須使用 Engagement API 來手動完成 (請參閱下方的[如何在 Windows Phone Silverlight 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-phone-use-engagement-api.md))，因為這些是與應用程式相依的統計資料。

##支援的版本

適用於 Windows Silverlight 的 Mobile Engagement SDK 只能整合至目標為以下作業系統的應用程式：

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE]如果您的目標是 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows 通用整合程序](mobile-engagement-windows-store-integrate-engagement.md)。

##安裝 Mobile Engagement Silverlight SDK

提供適用於 Windows Silverlight 的 Mobile Engagement SDK 時會使用稱為 *MicrosoftAzure.MobileEngagement* 的 Nuget 封裝。您可以從 Visual Studio Nuget 封裝管理員安裝該封裝。

##新增功能

Engagement SDK 需要一些 Windows Phone Silverlight SDK 的功能才能正常運作。

開啟 `WMAppManifest.xml` 檔案，並確定 `Capabilities` 面板中已宣告下列功能：

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##初始化 Engagement SDK

### Engagement 組態

Engagement 組態會集中在您專案的 `Resources\EngagementConfiguration.xml` 檔案中。

編輯此檔案來指定：

-   應用程式在 `<connectionString>` 和 `<\connectionString>` 標記之間的連接字串。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(engagementConfiguration);

應用程式的連接字串會顯示在 Azure 管理入口網站中。

### Engagement 初始化

當您建立新專案時會產生一份 `App.xaml.cs` 檔案。這個類別繼承自 `Application`，且包含的許多重要的方法。它將會用來初始化 Engagement SDK。

修改 `App.xaml.cs`：

-   新增至您的 `using` 陳述式：

		using Microsoft.Azure.Engagement;

-   在 `Application_Launching` 方法中插入 `EngagementAgent.Instance.Init`：

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		  EngagementAgent.Instance.Init();
		}

-   在 `Application_Activated` 方法中插入 `EngagementAgent.Instance.OnActivated`：

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		}

> [AZURE.WARNING]我們強烈地建議您不要在應用程式的其他地方加入 Engagement 初始化。不過請注意，`EngagementAgent.Instance.Init` 方法執行於專用的執行緒，而非 UI 執行緒。

##基本報告

### 建議使用的方法：多載您的 `PhoneApplicationPage` 類別

為了啟用 Engagement 計算使用者、工作階段、活動、當機和技術的統計資料所需的所有記錄檔報告，您可讓所有的 `PhoneApplicationPage` 子類別繼承自 `EngagementPage` 類別。

以下是如何在您應用程式其中一個頁面使用此方法的範例。您可以將相同的方法用於您應用程式的所有頁面。

#### C# 來源檔案

修改您的頁面 `.xaml.cs` 檔案：

-   新增至您的 `using` 陳述式：

		using Microsoft.Azure.Engagement;

-   以 `EngagementPage` 取代 `PhoneApplicationPage`：

**沒有 Engagement：**

		namespace Example
		{
		  public partial class ExamplePage : PhoneApplicationPage
		  {
		    [...]
		  }
		}

**有 Engagement：**

		using Microsoft.Azure.Engagement;
		
		namespace Example
		{
		  public partial class ExamplePage : EngagementPage 
		  {
		    [...]
		  }
		}

> [AZURE.WARNING]如果您的頁面繼承自 `OnNavigatedTo` 方法，請注意要讓 `base.OnNavigatedTo(e)` 執行呼叫。否則不會報告活動。事實上，`EngagementPage` 會呼叫 `OnNavigatedTo` 方法內的 `StartActivity`。

#### XAML 檔案

修改您的頁面 `.xaml` 檔案：

-   加入至命名空間宣告：

		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   以 `engagement:EngagementPage` 取代 `phone:PhoneApplicationPage`：

**沒有 Engagement：**

		<phone:PhoneApplicationPage>
		    <!-- layout -->
		</phone:PhoneApplicationPage>

**有 Engagement：**

		<engagement:EngagementPage 
		    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
		
		    <!-- layout -->
		</engagement:EngagementPage >

#### 覆寫預設行為

根據預設，頁面的類別名稱會在報告時做為活動名稱 (沒有額外的名稱)。如果類別使用 "Page" 尾碼，Engagement 也會移除它。

如果想要覆寫名稱的預設行為，您只需將下列加入您的程式碼：

		// in the .xaml.cs file
		protected override string GetEngagementPageName()
		{
		   /* your code */
		   return "new name";
		}

如果想要報告活動的一些額外資訊，您可以將以下內容加入至您的程式碼：

		// in the .xaml.cs file
		protected override Dictionary<object,object> GetEngagementPageExtra()
		{
		   /* your code */
		   return extra;
		}

系統會從您頁面的 `OnNavigatedTo` 方法中呼叫這些方法。

### 替代方法：手動呼叫 `StartActivity()`

如果您無法或不想要多載您的 `PhoneApplicationPage` 類別，您可以改為透過直接呼叫 `EngagementAgent` 方法來啟動活動。

我們建議您於 PhoneApplicationPage 的 `OnNavigatedTo` 方法內呼叫 `StartActivity`。

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		   base.OnNavigatedTo(e);
		   EngagementAgent.Instance.StartActivity("MyPage");
		}

> [AZURE.IMPORTANT]請確定您正確地結束工作階段。
>
> SDK 會在應用程式關閉時自動呼叫 `EndActivity` 方法。因此，「**強烈**」建議每當使用者的活動變更時便叫呼叫 `StartActivity` 方法，並且「**絕對不要**」呼叫 `EndActivity` 方法。這個方法會將訊息傳送給 Engagement 伺服器，目前的使用者已離開應用程式，這會影響所有應用程式記錄檔。

##進階報告

(選擇性) 您可以報告應用程式的特定事件、錯誤和工作；若要這樣做，請使用 `EngagementAgent` 類別中找到的其他方法。Engagement API 允許使用所有 Engagement 的進階功能。

如需詳細資訊，請參閱[如何在 Windows Phone Silverlight 應用程式中使用進階的 Mobile Engagement 標記 API](../mobile-engagement-windows-phone-use-engagement-api/)。

##進階組態

### 停用自動當機報告

您可以停用 Engagement 的自動當機報告功能。然後，發生未處理的例外狀況時，Engagement 將不會執行任何動作。

> [AZURE.WARNING]如果您打算停用此功能，請注意當您的應用程式中將發生未處理的當機時，Engagement 將不會傳送該當機，「且」亦不會關閉工作階段和工作。

若要停用自動當機報告，只要依照您宣告的方式自訂您的組態即可：

#### 從 `EngagementConfiguration.xml` 檔案

在 `<reportCrash>` 和 `</reportCrash>` 標記之間，將報告當機設為 `false`。

#### 於執行階段時從 `EngagementConfiguration` 物件

使用 EngagementConfiguration 物件將報告當機設為 false。

		/* Engagement configuration. */

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		/* Disable Engagement crash reporting. */ engagementConfiguration.Agent.ReportCrash = false;

### 高載模式

根據預設，Engagement 服務會即時報告記錄檔。如果應用程式報告記錄檔的頻率很高，最好先緩衝處理記錄檔，再定期一次報告它們 (此稱為「高載模式」)。

若要這樣做，請呼叫方法：

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

該引數是以「毫秒」為單位的值。如果您想要重新啟動及時記錄，您隨時可以呼叫該方法，而不需要使用任何參數 (或使用 0 為值)。

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響： 所有工作階段和工作持續時間將調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。建議使用低於 30000 (30 秒) 的閾值。您必須留意儲存記錄檔僅限於 300 個項目。如果傳送太長，您可能會遺失某些記錄檔。

> [AZURE.WARNING]高載閾值無法設定為小於一秒的時間間隔。如果您嘗試這樣做，SDK 會顯示含錯誤訊息的追蹤，並且會自動重設為預設值 (0 秒)。這樣會觸發 SDK 以即時的方式報告記錄檔。
 

<!---HONumber=July15_HO2-->