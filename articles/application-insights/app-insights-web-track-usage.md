<properties 
	pageTitle="使用 Application Insights 進行 Web 應用程式的使用量分析" 
	description="使用 Application Insights 進行使用量分析的概觀" 
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# 使用 Application Insights 進行 Web 應用程式的使用量分析

知道人員使用您的應用程式的方式可讓您將開發工作著重在對他們來說最重要的案例，並且對他們認為易於或難以達成的目標獲得見解。

Visual Studio Application Insights 提供兩種使用情況追蹤層級：

* **使用者與工作階段資料** - 預設提供。  
* **自訂遙測** - 您[撰寫程式碼][api]，透過應用程式的使用者體驗來追蹤您的使用者。 

## 設定

Web 應用程式的使用情況資料來自用戶端瀏覽器。

#### 設定 Application Insights 資源 

Application Insights 資源是 Microsoft Azure 中用來分析和顯示應用程式遙測資料的位置。您可能已在 [ASP.NET][greenbrown] 或 [J2EE][java] 設定一個資源來顯示應用程式伺服器端的資料。如果還沒，您可以立即設定。

通常來說，用來顯示 Web 用戶端使用情況資料的資源最好與用來顯示伺服器資料的資源相同。如此一來，您就可以輕易地相互關聯這兩端的診斷和度量資料。因此，如果您已經有資源，請跳至下一個步驟。

但如果您想要對使用情況資料使用不同資源，只要登入 [Azure 入口網站][portal]並予以建立即可：

![](./media/app-insights-web-track-usage/01-create.png)

#### 在網頁中插入程式碼

在 [Azure 入口網站][portal]的資源中，開啟 [快速入門] 來取得用來監視網頁的程式碼片段。

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

將程式碼放入主版頁面，例如 (在 .NET 中) _Layout.cshtml，或放入 include 檔案中，以確定它包含在您的所有頁面中。

程式碼片段包含可識別資源的檢測金鑰 (iKey)。若要將資料傳送至不同資源，例如在測試期間，您只需要取代 iKey 即可。

在偵錯模式發行或使用網頁，來產生一些遙測資料。


## 我的 Web 應用程式的熱門程度為何？

登入 [Azure 入口網站][portal]，瀏覽至應用程式資源，然後按一下 [使用情況]：

![](./media/app-insights-web-track-usage/14-usage.png)

* **使用者：**圖表的一段時間範圍內不同使用者的計數。(Cookie 是用來識別傳回的使用者。)
* **工作階段：**當使用者未進行任何要求達 30 分鐘，就會計算為工作階段。
* **頁面檢視** 計算對 trackPageView() 呼叫的數目，通常在每個網頁中呼叫一次。

逐一點選任何圖表以查看詳細資料。請注意，您可以變更圖表的時間範圍。


### 最常讀取哪些頁面？

按一下 [頁面檢視] 圖表以查看詳細資料。

![](./media/app-insights-web-track-usage/appinsights-49usage.png)


按一下圖表以查看您可以顯示的其他度量，或加入新圖表並選取它要顯示的度量。

![](./media/app-insights-web-track-usage/appinsights-63usermetrics.png)

> [AZURE.NOTE]度量只能以某種組合來顯示。當您選取度量時，不相容的度量會停用。



### 我的使用者住在何處？

從使用方式刀鋒視窗中，按一下 [使用者] 圖表以查看詳細資料：

![在 [概觀] 刀鋒視窗中，按一下 [工作階段] 圖表](./media/app-insights-web-track-usage/02-sessions.png)
 
(此範例來自網站，但圖表看起來類似於在裝置上執行的應用程式。)

### 這是否與上週相同？

與前一週比較，看看是否有任何項目變更：

![選取顯示單一度量的圖表，將前一週切換成開啟](./media/app-insights-web-track-usage/021-prior.png)


### 使用者的哪個部分是新的？

比較兩個度量，例如使用者和新使用者：

![選取圖表，搜尋並勾選或取消勾選度量。](./media/app-insights-web-track-usage/031-dual.png)


### 他們使用什麼瀏覽器或作業系統？

依屬性 (例如瀏覽器、作業系統或城市) 群組 (分段) 資料：

![選取顯示單一度量的圖表，將分組切換成開啟，並選擇屬性](./media/app-insights-web-track-usage/03-browsers.png)


## 工作階段

工作階段是 Application Insights 中的基本概念，Application Insights 會致力於使用特定使用者工作階段關聯每個遙測事件，例如要求、頁面檢視、例外狀況或您自行編碼的自訂事件。

它會收集關於每個工作階段的豐富內容資訊，例如裝置特性、地理位置、作業系統等等。

如果您同時檢測用戶端和伺服器 ([ASP.NET][greenbrown] 或 [J2EE][java])，則 SDK 會在用戶端與伺服器之間傳播工作階段識別碼，以便能夠相互關聯這兩端的事件。

在[診斷問題][diagnostic]時，您可以找到與問題發生所在工作階段相關的所有遙測資料，包括所記錄下來的所有要求、任何事件、例外狀況或追蹤。

工作階段可正確測量出裝置、作業系統或位置等內容的熱門程度。比方說，相較於透過計算頁面檢視計數，透過顯示依裝置所分組的工作階段計數，您可以取得該裝置與您的應用程式搭配使用之頻率的更精確數字。此輸入很適合用來分類任何裝置特定的問題。


#### 什麼是工作階段？

一個工作階段代表使用者和應用程式之間的一次交流。簡單來說，工作階段開始於使用者啟動應用程式，結束於使用者離開應用程式。對 Web 應用程式來說，工作階段預設會在停止活動 30 分鐘後或持續活動 24 小時後終止。

您可以編輯程式碼片段來變更這些預設值：

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs`：因使用者未活動而讓工作階段過期的時間 (毫秒)。預設值：30 分鐘。
* `sessionExpirationMs`：工作階段最大長度，以毫秒為單位。如果使用者持續活動超過這段時間，就會計入另一個工作階段。預設值：24 小時。

**工作階段持續時間**是一個[度量值][metrics]，會記錄工作階段的第一個和最後一個遙測項目間隔的時間範圍 (不包含逾時期間)。

特定間隔期間的**工作階段計數**的定義是，在此間隔期間具有某些活動的唯一工作階段數目。當您查看長時間範圍時，例如過去一週的每日工作階段計數，這通常相當於工作階段的總數。

不過，當您瀏覽較短的時間範圍時，例如每小時的資料粒度，跨越多個小時的長工作階段將會針對工作階段有活動的每個小時來進行計算。

## 使用者和使用者計數

每個使用者工作階段都會與唯一的使用者識別碼相關聯。

根據預設，會放置 cookie 來識別使用者。在此情況下，使用多個瀏覽器或裝置的使用者將會計算一次以上。

特定間隔期間的**使用者計數**度量的定義是，在此間隔期間具有記錄活動的唯一使用者數目。因此，當您設定的時間範圍資料粒度小於一小時左右，具有長工作階段的使用者可能會計算多次。

**新使用者**會計算應用程式的第一個工作階段發生在此間隔期間的使用者。如果使用依使用者和 cookie 進行計算的預設方法，則這也會包括已清除其 cookie 或是使用新裝置或瀏覽器第一次存取您的應用程式的使用者。

## 綜合流量

綜合流量包括來自可用性和負載測試、搜尋引擎編目程式和其他代理程式的要求。

Application Insights 會努力試著自動判斷和分類綜合流量並適當地標記。在大部分情況下，綜合流量不會叫用 JavaScript SDK，因此這個活動會排除在使用者和工作階段的計算之外。

不過，對於 Application Insights [Web 測試][availability]，使用者識別碼會自動根據 POP 位置進行設定，而工作階段識別碼則會根據測試回合識別碼進行設定。在預設報告中，預設會篩選掉綜合流量，排除這些使用者和工作階段。不過，若包含綜合流量，可能會造成整體使用者和工作階段計數少量增加。
 
## 頁面使用量

逐一點選頁面檢視圖表以取得更放大的版本，以及最受歡迎頁面的分解：


![從 [概觀] 刀鋒視窗，按一下 [頁面檢視] 圖表](./media/app-insights-web-track-usage/05-games.png)
 
以上範例來自遊戲網站。透過它，我們可以立即看到：

* 在上一週使用量未改善。也許我們應該考慮搜尋引擎最佳化？
* 看遊戲頁面的人比看首頁的人少很多。我們的首頁何不吸引人來玩遊戲？
* 'Crossword' 是最受歡迎的遊戲。我們應該優先推行新想法和改善。

## 自訂追蹤

我們假設不要在個別網頁實作每個遊戲，您決定將它們全部重新分解成相同的單一頁面應用程式，其中多數功能的網頁都是以 Javascript 編寫。這可讓使用者在遊戲之間快速切換，或甚至是在一個頁面中有數個遊戲。

但您仍想要 Application Insights 記錄開啟每個遊戲的次數，以他們在不同頁面上完全相同的方式記錄。那很簡單：只需要插入呼叫到 JavaScript 中您要記錄已開啟新「頁面」的遙測模組即可：

	telemetryClient.trackPageView(game.Name);

## 自訂事件

使用自訂事件可以進行作業。您可以從裝置應用程式、網頁或 Web 伺服器傳送事件：

(JavaScript)

    telemetryClient.trackEvent("GameEnd");

(C#)

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

(VB)

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


最常見的自訂事件會列在 [概觀] 刀鋒視窗上。

![在 [概觀] 刀鋒視窗上，向下捲動並按一下 [自訂事件]。](./media/app-insights-web-track-usage/04-events.png)

按一下資料表標題來查看事件數的總計。您可以依各種屬性 (例如事件名稱) 來分段圖表：

![選取僅顯示一個度量的圖表。將分組切換成開啟。選擇屬性。並非所有屬性都可供使用。](./media/app-insights-web-track-usage/06-eventsSegment.png)

時間軸特別實用的功能是您可以將變更與其他度量和事件相互關聯。例如，在玩很多遊戲的時候，您會預期看到被放棄的遊戲也有上升。但是，被放棄的遊戲上升不成比例，您會想要知道高負載是否造成問題，使得使用者覺得無法接受。

## 深入特定事件

若要更了解一般工作階段的進行情況，您可能想要著重在包含特定事件類型的特定使用者工作階段。

在此範例中，我們編寫了自訂事件 "NoGame"，當使用者登出而實際上未啟動遊戲時會呼叫它。為什麼使用者要那麼做？ 也許我們深入一些特定的出現次數可獲得線索。

從應用程式收到的自訂事件會依名稱列在 [概觀] 刀鋒視窗上：


![在 [概觀] 刀鋒視窗中，按一下其中一個自訂事件類型。](./media/app-insights-web-track-usage/07-clickEvent.png)
 
逐一點選感興趣的事件，並選取最近的特定出現次數：


![在摘要圖表下的清單中，按一下事件](./media/app-insights-web-track-usage/08-searchEvents.png)
 
讓我們查看工作階段的所有遙測，其中發生了特定 NoGame 事件。


![按一下 [工作階段的所有遙測]](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
沒有例外狀況，因此沒有因為一些錯誤而防止使用者玩遊戲。
 
為此原因，我們可以將頁面檢視以外的所有類型的遙測篩選掉：


![](./media/app-insights-web-track-usage/10-filter.png)
 
而現在我們可以看到，這個使用者登入只是為了要查看最新的分數。也許我們應該考慮開發可讓這個動作更易於進行的使用者劇本。(而我們應該實作自訂事件，在這個特定劇本發生時提出報告。)

## 使用屬性來篩選、搜尋和分割您的資料
您可以附加任意標記和數值到事件。
 

用戶端的 JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );

伺服器上的 C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);

伺服器上的 VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

以相同的方式將屬性附加至頁面檢視：

用戶端的 JavaScript

    appInsights.trackPageView("Win", 
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});

在「診斷搜尋」中，逐一點選個別事件出現次數可檢視屬性。


![在事件的清單中，開啟事件，然後按一下 '...' 來查看更多屬性](./media/app-insights-web-track-usage/11-details.png)
 
使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。


![在 [搜尋] 欄位中輸入值](./media/app-insights-web-track-usage/12-searchEvents.png)


## A | B 測試

如果您不知道哪個功能的變數將更可能成功，請兩者都釋出，讓兩者都可供不同使用者存取。測量每一個的成功，然後移至整合的版本。

針對此技術，您會附加獨特的標記到每個應用程式版本所傳送的所有遙測。您可以在作用中 TelemetryContext 中定義屬性來執行該動作。這些預設屬性會加入到應用程式傳送的每個遙測訊息 - 不只是您的自訂訊息，還有標準遙測。

在 Application Insights 入口網站中，您將可以依標記篩選並分組 (分段) 資料，以便比較不同版本。

伺服器上的 C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");

伺服器上的 VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")

個別的遙測可以覆寫預設值。

您可以設定全域初始設定式，使得所有新 TelemetryClients 會自動使用您的內容。

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

在應用程式初始設定式例如 Global.asax.cs 中：

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }


## 建置 - 測量 - 了解

使用分析時，它會成為開發週期的整合部分 - 而不只是協助解決問題的用途。以下是一些秘訣：

* 判斷應用程式的關鍵度量。您是想要盡可能多的使用者，或您只需要少量開心使用的使用者？ 您要讓造訪或銷售獲得最大效益？
* 計劃測量每個劇本。在草擬新使用者劇本或功能，或計劃更新現有功能時，一律要考慮如何測量變更的成功。編寫開始之前，詢問「如果有作用，這對我們的度量將有什麼影響？ 我們是否應該追蹤任何新事件？」 而當然，當功能上線時，請確定您查看分析並依據結果採取行動。 
* 將其他度量與關鍵度量產生交互關聯。例如，如果您加入「我的最愛」功能，則會想要知道使用者加入最愛的頻率。但也許知道他們回到我的最愛的頻率會更有趣。同時，更重要的是，使用我的最愛的客戶最終是否購買您的更多產品？
* Canary 測試。設定可讓您使新功能僅對部分使用者顯示的功能開關。使用 Application Insights 來查看新功能是否正依您設想的方式提供使用。進行調整，然後發行給更廣大的對象。
* 與您的使用者討論！ 單單分析是不足夠的，但可以補充以保有良好的客戶關係。


## 影片

> [Azure.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->