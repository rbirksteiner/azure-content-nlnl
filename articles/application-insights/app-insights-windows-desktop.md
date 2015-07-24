<properties 
	pageTitle="Windows 桌面應用程式與服務的 Application Insights" 
	description="使用 Application Insights 分析 Windows 桌面應用程式的使用情况和效能。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="awills"/>

# Windows 桌面應用程式與服務的 Application Insights

*Application Insights 目前僅供預覽。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights 可讓您監視所部署應用程式的使用量和效能。

Windows 桌面應用程式與服務的支援是由 Application Insights 核心 SDK 所提供。此 SDK 可為所有遙測資料提供完整的 API 支援，但不提供任何遙測自動集合。


## <a name="add"></a> 建立 Application Insights 資源


1.  在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。針對應用程式類型，選擇 Windows 市集應用程式。 

    ![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-windows-desktop/01-new.png)

    (您選擇的應用程式類型將設定 [概觀] 分頁的內容，以及[計量瀏覽器][metrics]中可用的屬性。)

2.  取得檢測金鑰的副本。

    ![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>在應用程式中安裝 SDK


1. 在 Visual Studio 中，編輯桌面應用程式專案的 NuGet 封裝。![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-windows-desktop/03-nuget.png)

2. 安裝 Application Insights API 套件。

    ![搜尋「Application Insights」](./media/app-insights-windows-desktop/04-core-nuget.png)

3. 編輯 ApplicationInsights.config (已由 NuGet 安裝加入)。在結尾標記前面插入此內容：

    `<InstrumentationKey>*the key you copied*</InstrumentationKey>`

    或者，您也可以使用此程式碼來達成相同的效果：
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`


## <a name="telemetry"></a>插入遙測呼叫

建立 `TelemetryClient` 執行個體，然後[使用它來傳送遙測][api]。

使用 `TelemetryClient.Flush()` 在關閉應用程式之前傳送訊息。核心 SDK 會使用記憶體中緩衝區。排清方法可確保這個緩衝區會清空，協助確保資料在程序關閉時不會遺失 (對其他類型應用程式不建議使用。平台 SDK 會自動實作這個行為)。

例如，在 Windows Forms 應用程式中，您可以編寫：

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

使用任一個 [Application Insights API][api] 來傳送遙測。在 Windows 桌面應用程式中，不會自動傳送遙測。一般您會使用：

* TrackPageView(pageName) 用於切換表單、頁面或索引標籤
* TrackEvent(eventName) 用於其他使用者動作
* TrackMetric(name, value) 用在背景工作，以傳送未附加到特定事件之度量的一般報告。
* TrackTrace(logEvent) 用於[診斷記錄][diagnostic]
* TrackException(exception) 用在 catch 子句中

#### 內容初始設定式

若要查看使用者和工作階段的計數，您可以對每個 `TelemetryClient` 執行個體設定值。或者，您可以使用內容初始設定式來對所有用戶端執行這個加入動作：

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>執行專案

[使用 F5 執行應用程式](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)並使用它，以產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>查看監視資料

返回 Azure 入口網站中的應用程式分頁。

前幾個事件將出現在診斷搜尋中。

如果您預期有更多資料，請在幾秒之後按一下 [重新整理]。

如果您使用 TrackMetric 或 TrackEvent 的測量參數，請開啟 [[計量瀏覽器][metrics]]，並開啟 [篩選器] 分頁，您可在其中看到所有的度量。



## <a name="usage"></a>後續步驟

[追蹤應用程式的使用量][knowUsers]

[擷取及搜尋診斷記錄][diagnostic]

[疑難排解][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=62-->