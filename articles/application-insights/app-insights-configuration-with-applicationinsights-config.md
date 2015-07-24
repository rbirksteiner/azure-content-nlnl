<properties 
	pageTitle="使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK" 
	description="啟用或停用資料收集模組，以及加入效能計數器和其他參數" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# 使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK

Application Insights SDK 是由數個模組所組成。核心模組提供基本的 API，可將遙測資料傳送至 Application Insights 入口網站。其他模組則會收集應用程式和其內容的資料。您可以藉由調整組態檔，來啟用或停用模組，並設定一些模組的參數。

組態檔的名稱為 `ApplicationInsights.config` 或 `ApplicationInsights.xml`，端視您的應用程式類型而定。當您[安裝 SDK][start] 時，會自動將組態檔加入至您的專案。[IIS 伺服器上的狀態監視器][redfield]，或是當您[選取 Azure 網站或 VM 的 Appplication Insights 延伸模組][azure]時，也會將組態檔加入至 Web 應用程式。

沒有同等的檔案可以控制[網頁中的 SDK][client]。


## 遙測模組

組態檔中的每個模組都有一個節點。若要停用模組，請刪除節點或將其註解化。

#### Implementation.Tracing.DiagnosticsTelemetryModule

報告 SDK 中的錯誤。例如，SDK 無法存取效能計數器，或自訂 TelemetryInitializer 擲回例外狀況時。

資料會顯示在[診斷搜尋][diagnostic]中。

#### RuntimeTelemetry.RemoteDependencyModule

收集應用程式所使用之外部元件的回應性資料。若要允許此模組用於 IIS 伺服器，您必須[安裝狀態監視器][redfield]。若要在 Azure Web 應用程式或 VM 中使用此模組，[請選取 Application Insights 延伸模組][azure]。

#### Web.WebApplicationLifecycleModule

嘗試排清遙測資料的所有記憶體內緩衝區，才不會在處理序關閉時遺失這些資料。

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

計算抵達 Web 應用程式的要求數，並測量回應時間。

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

計算 Web 應用程式中未處理的例外狀況數。請參閱[失敗和例外狀況][exceptions]。



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## 效能收集器模組

#### PerfCollector.PerformanceCollectorModule

根據預設，此模組會收集各種 Windows 效能計數器。當您在 [計量瀏覽器] 中開啟 [篩選] 刀鋒視窗時，就會看到這些計數器。

您可以監視其他效能計數器，包括標準 Windows 計數器，以及您加入的任何其他計數器。
      
使用以下語法來收集其他效能計數器：
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` 必須是 `\CategoryName(InstanceName)\CounterName` 或 `\CategoryName\CounterName`
      
如果您提供 `ReportAs` 屬性，則會將它當作 Application Insights 中顯示的名稱。

若要向 Application Insights 報告，計數器名稱只能包含：字母、圓角括號、斜線、連字號、底線、空格和點。

如果您想要監視的計數器含有任何無效的字元，例如 '#' 或數字，則您必須使用 ReportAs。
      
支援以下預留位置做為 `InstanceName`：

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## 通道參數 (Java)

這些參數會影響 Java SDK 應該儲存和排清它所收集之遙測資料的方式。

#### MaxTelemetryBufferCapacity

可以儲存在 SDK 記憶體內儲存空間中的遙測項目數。達到這個數目時，會排清遙測緩衝區，也就是將遙測項目傳送至 Application Insights 伺服器。

-	最小值：1
-	最大值：1000
-	預設值：500

    <ApplicationInsights> ...<Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

決定應該以何種頻率排清儲存在記憶體內儲存空間的資料 (並傳送至 Application Insights)。

-	最小值：1
-	最大值：300
-	預設值：5

    <ApplicationInsights> ...<Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

決定分配給本機磁碟上永續性存放裝置的大小上限 (MB)。此存放裝置會用於保存無法傳送至 Application Insights 端點的遙測項目。存放裝置大小達到上限時，會捨棄新的遙測項目。

-	最小值：1
-	最大值：100
-	預設值：10

    <ApplicationInsights> ...<Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## 內容初始設定式

這些元件會收集平台的資料。此資料會存放在 [TelemetryContext 物件][api]中。

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## 遙測初始設定式

這些元件會將資料加入至每個傳送至 Application Insights 的遙測事件。


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

此元件會識別似乎是來自機器 (例如搜尋引擎和 Web 測試) 的 HTTP 要求。它會設定 TelemetryClient.Context.Operation.SyntheticSource。

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

為每個遙測項目加上作業名稱。對 Web 應用程式而言，「作業」代表 HTTP 要求。會設定 TelemetryClient.Context.Operation.Name

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

它可在[診斷搜尋][diagnostic]中啟用「在相同的要求中尋找事件」的功能。會設定 TelemetryClient.Context.Operation.Id

為每個傳送至 Application Insights 的資料項目加上作業識別碼。對 Web 應用程式而言，「作業」是 HTTP 要求。因此，舉例來說，包含在要求處理中的要求、任何自訂事件和追蹤，均會具有相同的作業識別碼。

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

為每個遙測項目加上匿名使用者識別碼。這可讓您在診斷搜尋中，只篩選出與某位使用者活動相關的事件。例如，回報例外狀況時，您就可以追蹤該使用者執行的動作。

會設定 telemetryClient.Context.User

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

為每個事件加上工作階段 ID。Sets telemetryClient.Context.Session

## 自訂初始設定式


如果標準初始設定式不適合您的應用程式，可以自行建立初始設定式。

使用內容初始設定式，以設定要用來初始化每個遙測用戶端的值。例如，如果已經發行多個版本的應用程式，您可以透過篩選自訂屬性來確實區隔資料：

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

您可以使用遙測初始設定式，將處理加入至每個事件。例如，任一要求回應碼 > = 400 時，Web SDK 會標記為失敗。您可以覆寫該行為：

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
若要安裝您的初始設定式，請在 ApplicationInsights.config 中加入以下幾行：

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


或者，您可以撰寫程式碼，以在應用程式執行初期安裝初始設定式。例如：


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

這會決定顯示您資料的 Application Insights 資源。通常您會針對每個應用程式，用個別的金鑰建立個別資源。

如果想要以動態方式設定金鑰 (例如，想要將應用程式的結果傳送到不同的資源)，您可以在組態檔中省略金鑰，並將金鑰設定在程式碼中。

若要針對 TelemetryClient 的所有執行個體 (包括標準遙測模組) 設定金鑰，請在 TelemetryConfiguration.Active 中設定金鑰。請在初始化方法中這麼做，例如 ASP.NET 服務中的 global.aspx.cs：

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

如果您只想將特定的一組事件傳送至不同的資源，可以針對特定的 TelemetryClient 設定金鑰：

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[深入了解 API][api]。

若要取得新的金鑰，請[在 Application Insights 入口網站中建立新的資源][new]。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->