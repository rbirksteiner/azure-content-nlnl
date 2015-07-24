<properties 
	pageTitle="使用 Application Insights 偵測並診斷 Windows 市集和 Phone 應用程式中的使用量" 
	description="使用 Application Insights 分析 Windows 裝置應用程式的效能問題。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

# 使用 Application Insights 偵測並診斷 Windows 市集和 Phone 應用程式中的使用量

*Application Insights 目前僅供預覽。*

如果您的使用者在應用程式中遇到了損毀，您會想要快速得知，並且想知道發生的情況的詳細資料。利用 Application Insights，您可以監視損毀發生的頻率、在發生時收到警示，並且調查個別事件的報告。

「損毀」表示應用程式因為未捕捉的例外狀況而終止。如果您的應用程式捕捉到例外狀況，您可以使用 [TrackException API][apiexceptions] 來報告，並繼續執行應用程式。在該情況下，將不會記錄為損毀。


## 監視損毀頻率

[將 Application Insights 加入至應用程式][platforms]，並重新發行 (如果尚未這樣做)。

損毀會顯示在 [Application Insights 入口網站][portal]上應用程式的 [概觀] 分頁上。

![](./media/app-insights-crashes/appinsights-d018-oview.png)

您可以編輯圖表所顯示的時間範圍。


## 設定警示以偵測損毀

![從損毀圖表中按一下 [警示規則]，然後按一下 [加入警示]](./media/app-insights-crashes/appinsights-d023-alert.png)

## 診斷損毀

若要找出您的某些應用程式版本是否較其他的更易於損毀，請逐一點選損毀圖表，然後依「應用程式版本」分段：

![](./media/app-insights-crashes/appinsights-d26crashSegment.png)


若要探索造成損毀的例外狀況，請開啟 [診斷搜尋]。您可能想要移除其他類型的遙測以著重在例外狀況：

![](./media/app-insights-crashes/appinsights-d26crashExceptions.png)

[深入了解診斷搜尋中的篩選][diagnostic]。
 

按一下任何例外狀況以查看詳細資料，包括關聯的屬性和堆疊追蹤。

![](./media/app-insights-crashes/appinsights-d26crash.png)

查看發生時接近該例外狀況的其他例外狀況和事件：


![](./media/app-insights-crashes/appinsights-d26crashRelated.png)

## 插入追蹤記錄和事件

為協助您診斷問題，您可以[插入追蹤呼叫並且在 Application Insights 中搜尋記錄][diagnostic]。

## <a name="debug"></a>偵錯與發行模式

#### 偵錯

如果您在偵錯模式下建置，則事件一旦產生，就會立即傳送。如果網際網路連線中斷，而且您在恢復連線之前結束應用程式，則會捨棄離線遙測。

#### 發行

如果您在發行組態中建置，則事件會先儲存在裝置中，等到應用程式恢復時才傳送。第一次使用應用程式時也會傳送資料。如果剛啟動時沒有網際網路連線，先前的遙測及目前生命週期的遙測會先儲存，等到下次恢復時才傳送。

## <a name="next"></a>接續步驟

[使用 Application Insights 刪除、分級和診斷][detect]

[Application Insights API][api]





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->