<properties
   pageTitle="Azure 雲端服務的 Application Insights"
   description="使用 Application Insights 有效地監視您的 Web 和背景工作角色"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="06/17/2015"
   ms.author="sdash"/>

# Azure 雲端服務的 Application Insights


*Application Insights 目前僅供預覽*

[Microsoft Azure 雲端服務應用程式](http://azure.microsoft.com/services/cloud-services/)可以由 [Visual Studio Application Insights][start] 監視可用性、效能、失敗和使用方式。當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![範例](./media/app-insights-cloudservices/sample.png)

您需要 [Microsoft Azure](http://azure.com) 的訂用帳戶。使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。


#### 使用 Application Insights 檢測的範例應用程式

看一看這個[範例應用程式](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)，其中將 Application Insights 新增到雲端服務，搭配兩個背景工作角色託管於 Azure 中。

以下將告訴您如何透過相同的方式來調整您的雲端服務專案。

## 為每個角色建立 Application Insights 資源

Application Insights 資源是您在其中分析和顯示遙測資料的位置。

1.  在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。針對應用程式類型，選擇 ASP.NET 應用程式。 

    ![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-cloudservices/01-new.png)

2.  取得檢測金鑰的副本。您馬上需要這個項目以設定 SDK。

    ![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-cloudservices/02-props.png)


最好能夠從每個 Web 和背景工作角色針對資料建立不同的資源。

或者，您可以從所有角色僅將資料傳送至一個資源，但是設定[預設屬性][apidefaults]，讓您可以篩選或群組每個角色的結果。

## <a name="sdk"></a>在每個專案中安裝 SDK


1. 在 Visual Studio 中，編輯雲端應用程式專案的 NuGet 封裝。

    ![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-cloudservices/03-nuget.png)

2. 新增 [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝。此 SDK 版本包含新增伺服器內容 (如角色資訊) 的模組。

    ![搜尋「Application Insights」](./media/app-insights-cloudservices/04-ai-nuget.png)


3. 設定 SDK 以將資料傳送給 Application Insights 資源。

    開啟 `ApplicationInsights.config` 並插入下面這行：

    `<InstrumentationKey>` *您複製的檢測金鑰* `</InstrumentationKey>`

    使用您從 Application Insights 資源複製的檢測金鑰。

4. 將 ApplicationInsights.config 設定為一律複製到輸出目錄。僅背景工作角色需要這個設定。


您也可以在程式碼中設定檢測金鑰 (iKey) 來代替。這非常有用，例如，若您想使用 Azure 服務組態 (CSCFG) 的設定來管理對應環境的檢測金鑰。[範例應用程式](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)將示範如何設定 iKey：

* [Web 角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
* [背景工作角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
* [針對網頁](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)

## 使用 SDK 報告遙測
### 報告要求
 * 在 Web 角色中，要求模組會自動收集有關 HTTP 要求的資料。如需有關如何覆寫預設收集行為的範例，請參閱[範例 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)。 
 * 您可以藉由與追蹤 HTTP 要求相同的方式追蹤背景工作角色，來擷取背景工作角色呼叫的效能。在 Application Insights 中，要求遙測類型會測量一個單位的具名伺服器端工作，可以進行計時，而且可以獨立成功或失敗。由 SDK 會自動擷取 HTTP 要求時，您可以插入自己的程式碼，來追蹤對背景工作角色的要求。
 * 請參閱檢測兩個範例背景工作角色以報告要求：[WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) 和 [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

### 報告相依性
  * Application Insights SDK 可以報告應用程式對外部相依性的呼叫，例如 REST API 和 SQL Server。這可讓您查看是否有特定的相依性造成回應變慢或失敗。
  * 若要追蹤相依性，您必須搭配 [Application Insights 代理程式](app-insights-monitor-performance-live-website-now.md) (也稱為「狀態監視器」) 設定 Web/背景工作角色。
  * 若要使用 Application Insights 代理程式搭配 Web/背景工作角色：
    * 新增 [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) 資料夾和兩個檔案到 Web/背景工作角色專案中。請務必設定其建置屬性，使它們一律複製到輸出目錄。這些檔案將安裝代理程式。
    * 新增啟動工作到 CSDEF 檔案，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18)所示。
    * 注意：*背景工作角色*需要三個環境變數，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44)所示。Web 角色則不需要這個設定。

以下是您在 Application Insights 入口網站中所看到的範例：

* 自動相互關聯的要求與相依性的豐富診斷：

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Web 角色的效能，包含相依性資訊：

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* 以下是背景工作角色的要求與相依性資訊的螢幕擷取畫面：

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

### 報告例外狀況

* 如需如何從不同的 Web 應用程式類型收集未處理的例外狀況的資訊，請參閱[在 Application Insights 中監視例外狀況](app-insights-asp-net-exceptions.md)。
* 範例 Web 角色具有 MVC5 以及 Web API 2 控制器。來自 2 的未處理例外狀況可如下擷取：
    * 針對 MVC5 控制器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)設定 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)
    * 針對 Web API 2 控制器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25)設定 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)
* 針對背景工作角色：有兩種方式來追蹤例外狀況。
    * TrackException(ex)
    * 如果您已新增 Application Insights 追蹤接聽項 NuGet 套件，您可以使用 System.Diagnostics.Trace 來記錄例外狀況。[程式碼範例。](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

### 效能計數器

根據預設會收集下列計數器：

    * \Process(??APP_WIN32_PROC??)% Processor Time
	* \Memory\Available Bytes
	* .NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)% Processor Time

此外，也會為 Web 角色收集下列計數器：

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

您可以指定額外的自訂計數器，或其他 Windows 效能計數器，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)所示

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

### 背景工作角色的相互關聯遙測

當您可以看見導致失敗或高延遲要求的原因時，診斷體驗會更加豐富。使用 Web 角色，SDK 會在關聯的遙測間自動設定相互關聯。針對背景工作角色，您可以使用自訂遙測初始設定式，來設定一個通用 Operation.Id 內容屬性，讓所有的遙測可以達到此目的。這可讓您查看是否因為相依性或程式碼導致延遲/失敗問題，一目了然！

方式如下：

* 設定相互關聯識別碼到 CallContext 中，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)所示。在此案例中，我們使用「要求 ID」做為相互關聯識別碼
* 新增自訂的 TelemetryInitializer 實作，其會將 Operation.Id 設定到前面所設定的 correlationId。如這裡所示：[ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* 新增自訂的遙測初始設定式。您可以在 ApplicationInsights.config 檔案，或在程式碼中執行，如[這裡](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)所示

就這麼簡單！ 入口網站體驗已經準備好協助您一覽所有相關聯的遙測：

![](./media/app-insights-cloudservices/bHxuUhd.png)

#### 沒有資料？

* 開啟 [[搜尋][diagnostic]] 磚來查看個別事件。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。


## 完成安裝

若要取得您的應用程式的完整 360 度檢視，您需要執行一些動作：


* [將 JavaScript SDK 加入至網頁][client]，以取得瀏覽器型遙測，例如 Web 檢視計數、頁面載入時間、指令碼例外狀況，並讓您在頁面指令碼中撰寫自訂遙測。
* 加入相依性追蹤，診斷由資料庫或應用程式使用的其他元作所造成的問題：
 * [在您的 Azure Web 應用程式或 VM 中][azure]
 * [在您的內部部署 IIS 伺服器中][redfield]
* 從您最喜愛的記錄架構[擷取記錄追蹤][netlogs]
* 在用戶端、伺服器或兩者，[追蹤自訂事件和度量][api]，以深入了解應用程式的使用情況。
* [設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。



## 範例

[此範例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)監視具有 Web 角色和兩個背景工作角色的服務。



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=62-->