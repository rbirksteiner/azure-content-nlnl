<properties 
	pageTitle="ASP.NET 5 的 Application Insights" 
	description="監視 Web 應用程式的可用性、效能和使用方式。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# ASP.NET 5 的 Application Insights

Visual Studio Application Insights 可讓您監視 Web 應用程式的可用性、效能和使用情形。當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![範例](./media/app-insights-asp-net-five/sample.png)

您需要 [Microsoft Azure](http://azure.com) 的訂用帳戶。使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。

## 建立 ASP.NET 5 專案

... 如果您尚未完成。

在 Visual Studio 2015 中使用標準 ASP.NET 5 專案範本。


## 建立 Application Insights 資源

在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。挑選 [ASP.NET] 選項。

![按一下 [新增]、[開發人員服務]、[Application Insights]](./media/app-insights-asp-net-five/01-new-asp.png)

開啟的[資源][roles]刀鋒視窗是您查看您的應用程式效能和使用量資料的位置。若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。或者按一下 [瀏覽] 以尋找它。

應用程式類型的選擇會設定[計量瀏覽器][metrics]中可見的資源刀鋒視窗和屬性的預設內容。

##  使用檢測金鑰設定您的專案。

從您的 Application Insights 資源複製金鑰：

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-asp-net-five/02-props-asp.png)

在 ASP.NET 5 專案中，將它貼入 `config.json`：

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

或者，如果您偏好動態的組態，您可以將此程式碼加入至應用程式的啟動類別：

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## 將 Application Insights 加入至專案


#### 參考 NuGet 封裝

尋找 NuGet 封裝的[最新版本號碼](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases)。

開啟 `project.json` 並編輯 `dependencies` 區段：

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### 剖析組態檔

在 `startup.cs` 中：

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

在 `Startup` 方法中：

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

在 `ConfigurationServices` 方法中：

    services.AddApplicationInsightsTelemetry(Configuration);

在 `Configure` 方法中：

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## 新增 JavaScript 用戶端檢測

如果您有 _Layout.cshtml 檔案，在其中插入下列程式碼。否則，將程式碼放在您想要追蹤的任何頁面中。

在檔案的最上層定義插入：

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

在 `</head>` 標記之前和任何其他指令碼之前插入 Html 協助程式。您要從頁面報告的任何自訂 JavaScript 遙測應該插入到這個程式碼片段之後：

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## 執行您的應用程式

在 Visual Studio 中偵錯您的應用程式，或是將它發佈到 Web 伺服器。

## 檢視應用程式相關的資料

返回 [Azure 入口網站][portal]，並且瀏覽至您的 Application Insights 資源。如果 [概觀] 刀鋒視窗中沒有任何資料，請等待一兩分鐘，然後按一下 [重新整理]。

* [追蹤應用程式的使用量][usage]
* [診斷效能問題][detect]
* [設定 Web 測試以監視可用性][availability]



## 開放原始碼

[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=62-->