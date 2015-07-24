<properties 
	pageTitle="使用 Application Insights 監視應用程式的效能和使用量" 
	description="使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。" 
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
	ms.date="04/26/2015" 
	ms.author="awills"/>

# 使用 Application Insights 監視應用程式的效能和使用量

*Application Insights 目前僅供預覽*


Visual Studio Application Insights 監視您的即時應用程式，協助您[偵測並診斷效能問題和例外狀況][detect]，同時[探索應用程式的使用情況][knowUsers]。它適用於各種應用程式類型：ASP.NET 和 Java Web 應用程式；iOS、Android、Windows 和其他裝置應用程式；HTML+JavaScript 應用程式。

本文的重點放在以 Visual Studio 開發的應用程式類型。也有其他 IDE 的 Application Insights 擴充功能。

[您需要有 Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) 或更新版本，以及 [Microsoft Azure](http://azure.com) 中的帳戶。

## <a name="ide"></a>將 Application Insights 加入至專案

#### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 Application Insights。


![Create an ASP.NET project](./media/app-service-app-insights-get-started/appinsights-01-vsnewp1.png)

Visual Studio 會在 Application Insights 中建立資源，將 SDK 加入至專案，並將金鑰放在 `.config` 檔案中。

如果專案有網頁，則還會將 [JavaScript SDK][client] 加入至主版網頁。

#### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Add Application Insights]。

![Choose Add Application Insights](./media/app-service-app-insights-get-started/appinsights-03-addExisting.png)

Visual Studio 會在 Application Insights 中建立資源，將 SDK 加入至專案，並將金鑰放在 `.config` 檔案中。

在此情況下，不會將 [JavaScript SDK][client] 加入至網頁 - 建議在下一步再這樣做。

#### 設定選項

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure 預覽(該帳戶與 Visual Studio Online 帳戶彼此獨立)。

如果此應用程式是更大應用程式的一部分，您可以使用 [**組態設定**]，將它放在與其他元件相同的資源群組中。

*沒有 Application Insights 選項嗎？ 對於某些專案類型，您可以[手動加入 SDK][windows] 來使用 Application Insights。*

#### 從專案開啟 Application Insights。

![Right-click your project and open the Azure portal](./media/app-service-app-insights-get-started/appinsights-04-openPortal.png)


## <a name="run"></a> 3.執行專案

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-service-app-insights-get-started/appinsights-09eventcount.png)

## <a name="monitor"></a> 4.檢視遙測

返回 [Azure 入口網站][portal]，瀏覽至您的 Application Insights 資源。

在 [概觀] 圖表中尋找資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/app-service-app-insights-get-started/12-first-perf.png)

按一下任何圖表以查看詳細度量。[深入了解度量。][perf]

現在部署應用程式並觀看資料累積情形。


以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。在部署應用程式時，資料累積會較為緩慢。


#### 沒有資料？

* 開啟 [[診斷搜尋][diagnostic]] 磚來查看個別事件。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。


## 後續步驟

您現在正從應用程式的伺服器端傳送資料，以下是獲得更完整觀點的一些步驟：

* [設定 Web 測試][availability]，以確認應用程式處於即時狀態且能夠回應。
* [將 JavaScript SDK 加入至網頁][client]，以取得瀏覽器型遙測，例如 Web 檢視計數、頁面載入時間、指令碼例外狀況，並讓您在頁面指令碼中撰寫自訂遙測。
* 加入相依性追蹤，診斷由資料庫或應用程式使用的其他元作所造成的問題。 
 * [將相依性追蹤加入至 IIS 伺服器][redfield]
 * [將相依性追蹤加入至 Azure Web 應用程式][azure]
* 從您最喜愛的記錄架構[擷取記錄追蹤][netlogs]
* 在用戶端、伺服器或兩者，[追蹤自訂事件和度量][api]，以深入了解應用程式的使用情況。


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->