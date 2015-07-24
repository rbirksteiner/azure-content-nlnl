<properties 
	pageTitle="在 Application Insights 中探索度量" 
	description="使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中探索度量

[Application Insights][start] 中的度量為從您的應用程式傳送的遙測中的測量值和事件計數。它們幫助您偵測效能問題，並觀察使用應用程式方式的趨勢。標準度量的範圍很廣泛，而您也可以建立自己的自訂度量和事件。

度量和事件計數會顯示在彙總值 (例如總和、平均或計數) 的圖表中。

例如，如果您將 Application Insights 加入至 Web 應用程式，以下是您會在概觀接近最上方看到的內容：

![開啟 Azure 入口網站中應用程式的 [概觀] 分頁](./media/app-insights-metrics-explorer/01-overview.png)

有些圖表經過分段：任何點的圖表高度總計為顯示的度量的總合。圖例預設會顯示最大數量。

虛線顯示前一週度量的值。

## 點值

將滑鼠移至圖表上可顯示該點度量的值。


![垂將滑鼠移至圖表上](./media/app-insights-metrics-explorer/02-focus.png)

特定點的度量值會繼著前一個取樣間隔而彙總。這可能因整個圖表的時間範圍而變動。

取樣間隔或「粒度」會顯示在分頁的頂端。

![分頁的標題。](./media/app-insights-metrics-explorer/11-grain.png)

## 時間範圍

您可以在任何分頁上變更多數圖表或格線涵蓋的時間範圍。

![開啟 Azure 入口網站中應用程式的 [概觀] 分頁](./media/app-insights-metrics-explorer/03-range.png)


如果您預期的部分資料尚未出現，請按一下 [重新整理]。圖表不會自動更新。在發行模式中，資料從分析管線往圖表上顯示可能需要一些時間。

在 [概觀] 分頁中，在圖表上的一部分拖曳可在新圖表上放大到該處。


![拖曳過圖表的一部分。](./media/app-insights-metrics-explorer/12-drag.png)


## 計量瀏覽器

按一下 [概觀] 分頁上的任何圖表，可查看更詳細的一組相關圖表和格線。您可以編輯這些圖表和格線，以著重在感興趣的詳細資料。

例如，逐一點選 Web 應用程式的「失敗的要求」圖表：

![在 [概觀] 分頁上，按一下圖表](./media/app-insights-metrics-explorer/14-trix.png)


## 數據代表意義為何？

旁邊的圖例預設會顯示圖表在這段期間的彙總值。

圖表上的每個資料點也是在前一個取樣間隔或「粒度」中所收到的資料值彙總。粒度會顯示在分頁頂端，並隨著圖表的時幅而有所不同。

不同的度量會以不同方式彙總：

 * 針對度量 (例如：回應時間)，值會隨著圖表的期間加以**平均**。
 * 針對事件計數 (例如：失敗的要求)，彙總是在這段期間的計數**總和**。
 * 針對使用者計數，彙總是這段期間的**唯一**使用者數量。(如果在此期間追蹤了某個使用者超過一次，也只會將他們計入一次。)

若要了解值是總和、平均或唯一值，請按一下圖表並向下捲動到選取的值。您也可以取得度量的簡短描述。

![滑鼠暫留 (i)](./media/app-insights-metrics-explorer/06-total.png)
 


## 編輯圖表和格線

若要對分頁加入新圖表：

![在 [計量瀏覽器] 中，選擇 [加入圖表]](./media/app-insights-metrics-explorer/04-add.png)

選取現有或新圖表來編輯其顯示的內容：

![選取一或多個度量](./media/app-insights-metrics-explorer/08-select.png)

您可以在圖表上顯示一個以上的度量，不過，可以一起顯示的組合有一些限制。只要您選取一個度量，便會停用某些其他度量。

如果您編寫[自訂度量][track]到您的應用程式 (對 TrackMetric 和 TrackEvent 呼叫)，會在這裡予以列出。

## 分段資料

選取圖表或格線，將分組切換成開啟，並選取要分組依據的屬性：

![選取 [分組開啟]，然後在 [群組依據] 中選取屬性](./media/app-insights-metrics-explorer/15-segment.png)

如果您將[自訂度量][track]編寫至您的應用程式，並且它們包含屬性值，將可以在清單中選取屬性。

圖表是否對分段的資料來說太小？ 調整其高度：


![調整滑桿](./media/app-insights-metrics-explorer/18-height.png)


## 篩選資料

若只要查看選取的一組屬性值的度量：

![按一下 [篩選器]，然後檢查一些值](./media/app-insights-metrics-explorer/19-filter.png)

如果您不為特定屬性選取任何值，這與將它們全選相同：該屬性上沒有篩選器。

請注意每個屬性值旁的事件計數。選取一個屬性的值時，會調整計數與其他屬性值。

## 移除 Bot 和 Web 測試流量

使用篩選器**真實或綜合流量**並勾選**真實**。

您也可以依**綜合流量的來源**篩選。

## 編輯圖表類型

特別是，請注意您可以在格線與圖形之間切換：

![選取格線或圖表，然後選擇圖表類型](./media/app-insights-metrics-explorer/16-chart-grid.png)

## 儲存您的度量分頁

建立一些圖表後，請將它們儲存為我的最愛。如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![選擇 [我的最愛]](./media/app-insights-metrics-explorer/21-favorite-save.png)

若要再次查看分頁，請**前往 [概觀] 分頁**，並開啟 [我的最愛]：

![在 [概觀] 分頁中，選擇 [我的最愛]](./media/app-insights-metrics-explorer/22-favorite-get.png)

如果儲存時選擇「相對」時間範圍，會以最新度量更新分頁。如果選擇「絕對」時間範圍，它會每次都顯示相同資料。

## 重設分頁

如果您編輯分頁，但之後想要回到原始儲存的集合，只需要按一下 [重設]。

![在 [計量瀏覽器] 上方的按鈕中](./media/app-insights-metrics-explorer/17-reset.png)

## 設定警示

若要在任何度量有不尋常的值時收到電子郵件通知，請加入警示。您可以選擇將電子郵件傳送給帳戶管理員，或傳送給特定的電子郵件地址。

![在 [計量瀏覽器] 中，選擇 [警示規則]，然後選擇 [加入警示]](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[深入了解警示][alerts]。

## 匯出至 Excel

您可以將 [計量瀏覽器] 中顯示的度量資料匯出到 Excel 檔案。匯出的資料包括入口網站中所示所有圖表和資料表的資料。


![在 [計量瀏覽器] 中，選擇 [警示規則]，然後選擇 [加入警示]](./media/app-insights-metrics-explorer/31-export.png)

每個圖表或資料表的資料都會匯出到 Excel 檔案中的個別工作表。

您看到的內容即為匯出的內容。如果您想要變更所匯出資料的範圍，請變更時間範圍或篩選器。對於資料表，如果有顯示 [**載入更多**] 命令，您可以先按它再按一下 [匯出]，以匯出更多資料。

*匯出動作目前僅適用於 Internet Explorer 和 Chrome。我們正努力新增其他瀏覽器的支援。*

如果您想要連續匯出資料以在外部處理，請考慮使用[連續匯出](app-insights-export-telemetry.md)。


## 後續步驟

* [使用 Application Insights 監視使用情況](app-insights-overview-usage.md)
* [使用診斷搜尋](app-insights-diagnostic-search.md)


<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->