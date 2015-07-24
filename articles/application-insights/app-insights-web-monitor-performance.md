<properties 
	pageTitle="使用 Application Insights 來監視應用程式的健康情況和流量" 
	description="開始使用 Application Insights。分析內部部署或 Microsoft Azure 應用程式的使用情況、可用性和效能。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# 監視 Web 應用程式的效能 (英文)

*Application Insights 目前僅供預覽。*


確認應用程式的運作狀況良好，以及迅速找出任何失敗。[Application Insights][start] 能指出任何效能問題和例外狀況，以及協助您尋找及診斷根本原因。

Application Insights 能監視託管於內部部署設施或虛擬機器上的 ASP.NET Web 應用程式和 WCF 服務，以及 Microsoft Azure 網站。


## <a name="setup"></a>設定效能監視

如果您尚未將 Application Insights 新增至專案中 (亦即專案沒有 ApplicationInsights.config)，請選擇以下任一種方法來開始使用：

* [將 Application Insights 新增至 Visual Studio 中的專案][greenbrown] - 建議方法。除了被動效能監視之外，您還可以插入診斷記錄及追蹤流量。
* [立即監視即時網站的效能][redfield] (英文) - 如果您選用這個方法，便不需要更新應用程式專案或重新部署網站。
* [若為 Microsoft Azure 網站](../insights-how-to-customize-monitoring.md)，您已經可以在網站的 [監視] 透鏡上查看度量。 

使用其中一種方法，即可在 Application Insights 中的 [概觀] 刀鋒視窗中快速查看資料。使用其中一種方法，即可在 Application Insights 中的 [概觀] 刀鋒視窗中快速查看資料。


## <a name="view"></a>探索度量

按一下任一項目可查看詳細資料，也能查看較長期的結果。例如，按一下 [要求] 磚，然後選取時間範圍：


![按一下詳細資料的方式來選取時間範圍](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

按一下圖表以選擇它要顯示的度量，或是加入新圖表並選取其度量：

![按一下圖形以選擇度量](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [AZURE.NOTE]**取消核取所有度量**可查看所有可供選擇的項目。度量分為多個群組；當您選取群組的任何成員時，只有該群組的其他成員會出現。


## <a name="metrics"></a>這具有哪些意義？ 效能磚和報告

您可以取用多種效能度量。我們從預設顯示在應用程式分頁中的度量開始討論。


### 要求

在指定期間內接收到的 HTTP 要求數量。將此度量與其他報告中的結果比較，可了解應用程式在各種負載下的行為。

HTTP 要求包括頁面、資料及影像的所有 GET 或 POST 要求。

按一下磚可取得特定 URL 的計數。

### 平均回應時間

測量從 Web 要求進入應用程式到傳回回應之間的時間。

資料點會指出移動平均值。在有許多要求的情況下，可能會有一些要求偏離平均值，但在圖表中沒有顯著的高低起伏。

找尋異常的高峰。一般來說，當要求增加時，回應時間也會上升。如果出現不相稱的上升跡象，表示應用程式可能遭遇到資源限制 (例如，應用程式使用之服務的 CPU 或容量)。

按一下磚可取得特定 URL 的時間。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)


### 最慢的要求

![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

顯示可能需要進行效能調整的要求。


### 失敗的要求

![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

丟出無法攔截之例外狀況的要求計數。

按一下磚可查看特定失敗的詳細資料；選取個別要求可查看要求的詳細資料。

系統只會針對各項檢查保留一個具代表性的失敗範例。

### 其他度量

若要查看其他可顯示的度量，請按一下圖表，然後取消選取所有度量以查看可供使用的完整集合。按一下 (i) 可查看各項度量的定義。

![取消選取所有度量以查看完整的集合](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)


選取任何度量將會停用其他度量，使其無法顯示在同一個圖表中。

## 收集更多效能計數器

您可以選擇的度量包含[效能計數器](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)。Windows 提供多種計數器，您也可以自行定義。

如果您需要的計數器不在清單中，您可以自行新增至 SDK 所收集的集合中。開啟 ApplicationInsights.config，然後編輯效能收集器指示詞：

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(electronics)# Items Sold" ReportAs="Item sales"/>
      </Counters>
    </Add>

格式為 `\Category(instance)\Counter"`，若是沒有執行個體的類別，則為 `\Category\Counter`。

若計數器名稱含有下列項目以外的字元，則需要 `ReportAs`：字母、圓角括號、斜線、連字號、底線、空格和點。

如果您指定執行個體，系統收集它做為報告度量的 "CounterInstanceName" 屬性。

如果您想要，也可以撰寫程式碼來達到相同效果：

    var perfCollector = new PerformanceCollectorModule();
    perfCollector.Counters = new List<CustomPerformanceCounterCollectionRquest>();
    perfCollector.Counters.Add(new CustomPerformanceCounterCollectionRquest(
      @"\Sales(electronics)# Items Sold", "Items sold"));
    perfCollector.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryModules.Add(perfCollector);



## 設定警示

若要在任何度量有不尋常的值時收到電子郵件通知，請加入警示。您可以選擇將電子郵件傳送給帳戶管理員，或傳送給特定的電子郵件地址。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

設定其他屬性之前的資源。如果您想要設定效能或使用度量的相關警示，請勿選擇 webtest 資源。

請小心注意系統要求您輸入臨界值時所使用的單位。

我沒有看到 [新增警示] 按鈕 - 您只擁有此群組帳戶的唯讀權限嗎？ 請洽詢帳戶管理員。

## <a name="diagnosis"></a>診斷問題

以下是幾個尋找及診斷效能問題的訣竅：

* 設定 [Web 測試][availability] (英文)，以在網站故障、回應異常或過於緩慢時收到警示。 
* 比較要求計數與其他度量，了解失敗或回應過慢的情況是否與負載有關。
* 在程式碼中[插入及搜尋追蹤陳述式][diagnostic] (英文) 有助於找出問題所在。

## <a name="next"></a>接續步驟

[Web 測試][availability] (英文) - 定期從全世界傳送 Web 要求給應用程式。

[擷取及搜尋診斷追蹤][diagnostic] (英文) - 插入追蹤呼叫並詳查結果，以便找出問題所在。

[流量追蹤][usage] (英文) - 了解使用者使用應用程式的情況。

[疑難排解][qna] (英文) - 和問答集

## 影片

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->