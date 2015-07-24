<properties
	pageTitle="使用 Application Insights 進行使用量分析"
	description="使用 Application Insights 進行使用量分析的概觀"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="kamrani"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/03/2015"
	ms.author="awills"/>

# 使用 Application Insights 進行使用量分析

知道人員使用您的應用程式的方式可讓您將開發工作著重在對他們來說最重要的案例，並且對他們認為易於或難以達成的目標獲得見解。

Application Insights 可提供應用程式使用量的清楚檢視，幫助您改善使用者的體驗，並達成您的業務目標。

## 內建的分析功能

將 [Application Insights][start] 加入您的專案，不需多費力氣，便能獲得能顯示您擁有的使用者數量的圖表等等。

![在 Azure 中，[瀏覽] > [Application Insights] > 您的專案，並向下捲動](./media/app-insights-overview-usage/01-overview.png)

將滑鼠移至圖形上的空白部分，以查看特定點的計數。否則，數字會顯示在一段期間的彙總值，例如一段期間的平均、總計或獨特使用者計數。

在 Web 應用程式中，是使用 Cookie 來計算使用者。使用數個瀏覽器、清除 Cookie 或使用私密瀏覽功能的人員將被計入數次。

Web 工作階段在 30 分鐘無活動後會被計入。在電話或其他裝置上的工作階段，在應用程式暫停超過幾秒後便會被計入。

逐一點選任何圖表以查看更詳細的資料。例如：

![在 [概觀] 分頁上，按一下 [工作階段] 圖表](./media/app-insights-overview-usage/02-sessions.png)

(此範例來自網站，但圖表看起來類似於在裝置上執行的應用程式。)

與前一週比較，看看是否有任何項目變更：

![選取顯示單一度量的圖表，將前一週切換成開啟](./media/app-insights-overview-usage/021-prior.png)

比較兩個度量，例如使用者和新使用者：

![選取圖表，搜尋並勾選或取消勾選度量。](./media/app-insights-overview-usage/031-dual.png)

依屬性 (例如瀏覽器、作業系統或城市) 群組 (分段) 資料：

![選取顯示單一度量的圖表，將分組切換成開啟，並選擇屬性](./media/app-insights-overview-usage/03-browsers.png)


## 頁面使用量

逐一點選頁面檢視圖表以取得更放大的版本，以及最受歡迎頁面的分解：


![從 [概觀] 刀鋒視窗，按一下 [頁面檢視] 圖表](./media/app-insights-overview-usage/05-games.png)

以上範例來自遊戲網站。透過它，我們可以立即看到：

* 在上一週使用量未改善。也許我們應該考慮搜尋引擎最佳化？
* 看遊戲頁面的人比看首頁的人少很多。我們的首頁何不吸引人來玩遊戲？
* 'Crossword' 是最受歡迎的遊戲。我們應該優先推行新想法和改善。

## 自訂追蹤

我們假設不要在個別網頁實作每個遊戲，您決定將它們全部重新分解成相同的單一頁面應用程式，其中多數功能的網頁都是以 Javascript 編寫。這可讓使用者在遊戲之間快速切換，或甚至是在一個頁面中有數個遊戲。

但您仍想要 Application Insights 記錄開啟每個遊戲的次數，以他們在不同頁面上完全相同的方式記錄。那很簡單：只需要插入呼叫到 JavaScript 中您要記錄已開啟新「頁面」的遙測模組即可：

	telemetryClient.trackPageView(game.Name);

## 自訂事件

您可以透過許多方式使用遙測來了解使用應用程式的方式。但您不會想要總是將訊息與頁面檢視混雜在一起。改為使用自訂事件。您可以從裝置應用程式、網頁或 Web 伺服器傳送事件：

(JavaScript)

    telemetryClient.trackEvent("GameEnd");

(C#)

    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");

(VB)

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


最常見的自訂事件會列在 [概觀] 刀鋒視窗上。

![在 [概觀] 刀鋒視窗上，向下捲動並按一下 [自訂事件]。](./media/app-insights-overview-usage/04-events.png)

按一下資料表標題來查看事件數的總計。您可以依各種屬性 (例如事件名稱) 來分段圖表：

![選取僅顯示一個度量的圖表。將分組切換成開啟。選擇屬性。並非所有屬性都可供使用。](./media/app-insights-overview-usage/06-eventsSegment.png)

時間軸特別實用的功能是您可以將變更與其他度量和事件相互關聯。例如，在玩很多遊戲的時候，您會預期看到被放棄的遊戲也有上升。但是，被放棄的遊戲上升不成比例，您會想要知道高負載是否造成問題，使得使用者覺得無法接受。

## 深入特定事件

若要更了解一般工作階段的進行情況，您可能想要著重在包含特定事件類型的特定使用者工作階段。

在此範例中，我們編寫了自訂事件 "NoGame"，當使用者登出而實際上未啟動遊戲時會呼叫它。為什麼使用者要那麼做？ 也許我們深入一些特定的出現次數可獲得線索。

從應用程式收到的自訂事件會依名稱列在 [概觀] 刀鋒視窗上：


![在 [概觀] 刀鋒視窗中，按一下其中一個自訂事件類型。](./media/app-insights-overview-usage/07-clickEvent.png)

逐一點選感興趣的事件，並選取最近的特定出現次數：


![在摘要圖表下的清單中，按一下事件](./media/app-insights-overview-usage/08-searchEvents.png)

讓我們查看工作階段的所有遙測，其中發生了特定 NoGame 事件。


![按一下 [工作階段的所有遙測]](./media/app-insights-overview-usage/09-relatedTelemetry.png)

沒有例外狀況，因此沒有因為一些錯誤而防止使用者玩遊戲。

為此原因，我們可以將頁面檢視以外的所有類型的遙測篩選掉：


![](./media/app-insights-overview-usage/10-filter.png)

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


![在事件的清單中，開啟事件，然後按一下 '...' 來查看更多屬性](./media/app-insights-overview-usage/11-details.png)

使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。


![在 [搜尋] 欄位中輸入值](./media/app-insights-overview-usage/12-searchEvents.png)


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
* 將其他度量與關鍵度量產生交互關聯。例如，如果您加入「我的最愛」功能，您會想要知道使用者加入最愛的頻率。但也許知道他們回到我的最愛的頻率會更有趣。同時，更重要的是，使用我的最愛的客戶最終是否購買您的更多產品？
* Canary 測試。設定可讓您使新功能僅對部分使用者顯示的功能開關。使用 Application Insights 來查看新功能是否正依您設想的方式提供使用。進行調整，然後發行給更廣大的對象。
* 與您的使用者討論！ 單單分析是不足夠的，但可以補充以保有良好的客戶關係。


## 影片

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[start]: app-insights-get-started.md
 

<!---HONumber=62-->