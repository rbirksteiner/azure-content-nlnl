<properties 
	pageTitle="使用 Application Insights 監視 Windows 市集和 Phone 應用程式中的使用量" 
	description="使用 Application Insights 分析 Windows 裝置應用程式的使用情况。" 
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
	ms.date="06/19/2015" 
	ms.author="awills"/>

#  使用 Application Insights 監視 Windows 市集和 Windows Phone 應用程式中的使用量

*Application Insights 目前僅供預覽。*

了解您有多少使用者，以及他們他們在查看您應用程式中的哪些頁面。Application Insights 提供現成的這些資料給您。只要在您的應用程式中插入幾行程式碼，就能深入了解使用者在您應用程式中行進的路徑，以及他們因此而達成什麼目的。

[將 Application Insights 加入至應用程式][windows]，並重新發行 (如果尚未這樣做)。


## <a name="usage"></a>追蹤使用量

從 [概觀] 時間表，按一下 [使用者] 和 [工作階段] 圖表，查看更詳細的分析。


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* **使用者**依匿名方式追蹤，所以同一使用者在不同裝置上以兩次計算。
* **工作階段**於應用程式暫停時計算 (排除短暫間隔，以免將意外的擱置計算在內)。

#### 分割

分割圖表，依各種準則取得分解圖。例如，若要查看多少使用者在使用您應用程式的每個版本，請開啟 [使用者] 圖表，再依 [應用程式版本] 分割：

![在使用者圖表上，開啟 [分割]，選擇 [應用程式版本]](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### 頁面檢視

若要探索使用者在您應用程式中的行進路徑，請在程式碼中插入[頁面檢視遙測][api]：

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

查看頁面檢視圖表上的結果，並開啟詳細資料：

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

按一下任何頁面，查看特定發生次數的詳細資料。

#### 自訂事件

只要插入程式碼從您的應用程式傳送自訂事件，就能追蹤使用者的行為，以及特定功能和情節的使用量。

例如：

    telemetry.TrackEvent("GameOver");

資料會出現在 [自訂事件] 方格中。您可以在 [計量瀏覽器] 中查看彙總檢視，也可以按一下任何事件查看特定的發生次數。

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


您可以將字串和數值屬性加入至任何事件。


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


按一下任何發生次數，查看詳細屬性，包括您已定義的屬性。


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

如需自訂事件的詳細資訊，請參閱 [API 參考][api]。

## 工作階段

工作階段是 Application Insights 中的基本概念，Application Insights 會致力於使用特定使用者工作階段關聯每個遙測事件，例如當機或您自行編碼的自訂事件。

它會收集關於每個工作階段的豐富內容資訊，例如裝置特性、地理位置、作業系統等等。

在[診斷問題][diagnostic]時，您可以找到與問題發生所在工作階段相關的所有遙測資料，包括所記錄下來的所有要求、任何事件、例外狀況或追蹤。

工作階段可正確測量出裝置、作業系統或位置等內容的熱門程度。比方說，相較於透過計算頁面檢視計數，透過顯示依裝置所分組的工作階段計數，您可以取得該裝置與您的應用程式搭配使用之頻率的更精確數字。此輸入很適合用來分類任何裝置特定的問題。


#### 什麼是工作階段？

一個工作階段代表使用者和應用程式之間的一次交流。簡單來說，工作階段開始於使用者啟動應用程式，結束於使用者離開應用程式。對於行動應用程式來說，當應用程式暫停 (移至背景) 超過 20 秒，工作階段時就會終止。如果應用程式恢復作業，就會啟動新的工作階段。理所當然地，使用者在一天或甚至一個小時內可能會有多個工作階段。

**工作階段持續時間**是一個度量值，代表工作階段的第一個和最後一個遙測項目間隔的時間範圍 (不包含逾時期間)。


特定間隔期間的**工作階段計數**的定義是，在此間隔期間具有某些活動的唯一工作階段數目。當您查看長時間範圍時，例如過去一週的每日工作階段計數，這通常相當於工作階段的總數。

不過，當您瀏覽較短的時間範圍時，例如每小時的資料粒度，跨越多個小時的長工作階段將會針對工作階段有活動的每個小時來進行計算。

## 使用者和使用者計數

每個使用者工作階段都會與唯一的使用者識別碼相關聯，此識別碼會在應用程式使用時產生並保留在裝置的本機儲存體中。使用多個裝置的使用者將會計算一次以上。

特定間隔期間的**使用者計數**度量的定義是，在此間隔期間具有記錄活動的唯一使用者數目。因此，當您設定的時間範圍資料粒度小於一小時左右，具有長工作階段的使用者可能會計算多次。

**新使用者**會計算應用程式的第一個工作階段發生在此間隔期間的使用者。


## <a name="debug"></a>偵錯與發行模式

#### 偵錯

如果您在偵錯模式下建置，則事件一旦產生，就會立即傳送。如果網際網路連線中斷，而且您在恢復連線之前結束應用程式，則會捨棄離線遙測。

#### 發行

如果您在發行組態中建置，則事件會先儲存在裝置中，等到應用程式恢復時才傳送。第一次使用應用程式時也會傳送資料。如果剛啟動時沒有網際網路連線，先前的遙測及目前生命週期的遙測會先儲存，等到下次恢復時才傳送。

## <a name="next"></a>接續步驟

[了解您的使用者][knowUsers]

[深入了解計量瀏覽器][metrics]


[疑難排解][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=62-->