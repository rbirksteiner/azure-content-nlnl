<properties 
	pageTitle="監視應用程式的效能" 
	description="圖表載入和回應時間、相依性資訊以及設定效能警示。" 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# 監視應用程式的效能

在 [Azure 入口網站](http://portal.azure.com) 中，您可以設定監視功能，使其收集您 Web 應用程式或虛擬機器中應用程式相依性的統計資料和詳細資料。

Azure 運用*擴充功能*支援「應用程式」效能監視功能 (或 *APM*)。這些擴充功能已安裝在應用程式中，可收集資料並回報給監視服務。

## 啟用擴充功能

1. 按一下 [**瀏覽**]，然後選取您要檢測的 Web 應用程式或虛擬機器。

2. 按一下 [**監視**] 底下的 [**應用程式監視**] 磚。

3. 選擇您要使用的擴充功能提供者，例如 **Application Insights** 或 **New Relic**。

![Web 應用程式 APM](./media/insights-perf-analytics/05-extend.png)

如果您使用的是虛擬機器：

![虛擬機器](./media/insights-perf-analytics/10-vm1.png)

### 如果是 Application insights：請使用 SDK 重建

New Relic 可以自動安裝，完全不需任何其他的檢測，但 Application Insights 有一個額外的需求。

在 Visual Studio 中，將 Application Insights SDK 加入專案。

![以滑鼠右鍵按一下 Web 專案，然後選擇 [加入 Application Insights]。](./media/insights-perf-analytics/03-add.png)

系統要求您登入時，請使用 Azure 帳戶的認證。

您可以在開發電腦中執行應用程式來測試遙測，或是直接繼續執行並將重新發佈遙測。

SDK 會提供 API 供您[撰寫自訂遙測](../app-insights-api-custom-events-metrics.md)以追蹤使用情況。

## 探索資料

使用您的應用程式一段時間以產生一些遙測。

1. 接著，從您的 Web 應用程式或虛擬機器刀鋒視窗，您將會看到已安裝的擴充功能
2. 在代表您應用程式的資料列上按一下以瀏覽至該提供者：![按一下 [重新整理]](./media/insights-perf-analytics/06-overview.png)

您也可以按一下 [**瀏覽**] 直接移至 Application Insights 元件，或您使用的 New Relic 帳戶。

以 Application Insights 為例，您進入刀鋒視窗後，您可以：- 開啟效能：

![在 Application Insights 的概觀刀鋒視窗中，按一下 [效能] 磚](./media/insights-perf-analytics/07-dependency.png)

- 逐步深入以查看個別要求：

![在方格中，按一下相依性，查看相關的要求。](./media/insights-perf-analytics/08-requests.png)

- 以下範例說明在 SQL 相依性中花費的時間，該相依性中包括 SQL 的呼叫數和相關的統計資料 (例如平均持續時間和標準差)。 

![](./media/insights-perf-analytics/01-example.png)



## 後續步驟

* [監視服務健康狀態計量](insights-how-to-customize-monitoring.md)可確保您的服務可用且回應正常。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md)來在您服務中收集詳細的高頻率計量。
* 每當發生操作事件或計量超過臨界值時，[接收警示通知](insights-receive-alert-notifications.md)。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../app-insights-web-track-usage.md)，以取得有關造訪網頁瀏覽器的用戶端分析。
* 使用 Application Insights [監視任何網頁的可用性和回應性](../app-insights-monitor-web-app-availability.md)，讓您可以找出您的頁面是否關閉。
 

<!---HONumber=62-->