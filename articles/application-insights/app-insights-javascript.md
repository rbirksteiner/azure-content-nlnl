<properties 
	pageTitle="適用 JavaScript Web 應用程式的 Application Insights" 
	description="取得頁面檢視和工作階段計數、Web 用戶端資料，並追蹤使用量模式。Detect exceptions and performance issues in JavaScript web pages." 
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
	ms.date="06/03/2015" 
	ms.author="awills"/>
 
# 適用 JavaScript Web 應用程式的 Application Insights

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

了解網頁的效能和使用量。將 Visual Studio Application Insights 加入至您的頁面，您會了解您擁有多少使用者、他們回來的頻率，以及他們最常使用哪些頁面。您也可以取得載入時間和任何例外狀況的報告。加入幾個[自訂事件和度量][track]，您就可以詳細分析最受歡迎的功能、最常見的錯誤，並調整頁面以滿足使用者的需求。

![選擇 [新增]、[開發人員服務]、[Application Insights]。](./media/app-insights-javascript/16-page-views.png)

如果您已經針對 [ASP.NET][greenbrown] 或 [Java][java] Web 應用程式設定伺服器遙測，就會取得用戶端和伺服器角度的圖片。兩個資料流會在 Application Insights 入口網站中整合。

## 建立 Application Insights 資源

Application Insights 資源是您的頁面的效能和使用量相關資料顯示的位置。(如果您已經建立資源，或許會從您的 Web 伺服器收集資料，略過此步驟)。

在 [Azure 入口網站](http://portal.azure.com) 中，建立新的 Application Insights 資源：

![選擇 [新增]、[開發人員服務]、[Application Insights]。](./media/app-insights-javascript/01-create.png)

*已經有問題了嗎？* [建立資源的詳細資訊][new]。


## 將 SDK 指令碼加入至您的應用程式或網頁

在快速入門中，取得網頁指令碼：

![在您的應用程式概觀刀鋒視窗中，選擇 [快速入門]，取得程式碼以監視我的網頁。複製指令碼。](./media/app-insights-javascript/02-monitor-web-page.png)

在您想要追蹤之每一個頁面的 &lt;/head&gt; 標記之前插入指令碼。如果您的網站有主版頁面，您可以那裡放入指令碼。例如：

* 在 ASP.NET MVC 專案中，可放在 View\Shared_Layout.cshtml 中
* 在 SharePoint 網站中，在控制台中開啟[站台設定/主要頁面](app-insights-sharepoint.md)。

指令碼包含檢測金鑰，會將資料導向您的 Application Insights 資源。

*(如果您使用的是已知網頁架構，請在 Application Insights 配接器附近尋找。例如，有 [AngularJS 模組](http://ngmodules.org/modules/angular-appinsights)。)*

 
## <a name="run"></a>執行您的應用程式

執行您的 Web 應用程式，稍微使用一下來產生遙測，並等候數秒鐘。您可以在開發電腦上按 F5 執行它，或發佈它讓使用者處理。

如果您想要檢查 Web 應用程式傳送至 Application Insights 的遙測，請使用您的瀏覽器的偵錯工具 (在許多瀏覽器上為 F12)。資料會傳送至 dc.services.visualstudio.com。

## 探索資料

在應用程式概觀刀鋒視窗中，頂端附近有圖表顯示將頁面載入瀏覽器的平均時間：


![](./media/app-insights-javascript/05-browser-page-load.png)


*仍沒有資料？ 按一下頁面頂端的 [**重新整理**]。仍然沒有嗎？ 請參閱[疑難排解][qna]。*

按一下該圖表，您會取得更詳細的版本：

![](./media/app-insights-javascript/07-client-perf.png)

這是堆疊圖表，該圖表將總頁面載入時間細分為 [W3C 所定義的標準時間](http://www.w3.org/TR/navigation-timing/#processing-model)。

![](./media/app-insights-javascript/08-client-split.png)

請注意，*網路連接*時間通常低於您所預期的時間，因為它是從瀏覽器到伺服器之所有要求的平均值。許多個別要求的連接時間為 0，因為已經有與伺服器的作用中連線。


### 依據頁面的效能

進一步探索詳細資料刀鋒視窗，有一個依據網頁 URL 分割的方格：


![](./media/app-insights-javascript/09-page-perf.png)

如果您想要查看一段時間的頁面效能，請按兩下方格，並變更其圖表類型：

![](./media/app-insights-javascript/10-page-perf-area.png)

## 用戶端使用量概觀

返回 [概觀] 刀鋒視窗上，按一下 [使用量]：

![](./media/app-insights-javascript/14-usage.png)

* **使用者：**圖表的一段時間範圍內不同使用者的計數。(Cookie 是用來識別傳回的使用者。)
* **工作階段：**當使用者未進行任何要求達 30 分鐘，就會計算為工作階段。
* **頁面檢視** 計算對 trackPageView() 呼叫的數目，通常在每個網頁中呼叫一次。


### 逐一點選以獲得詳細資料

逐一點選任何圖表以查看詳細資料。請注意，您可以變更圖表的時間範圍。

![](./media/app-insights-javascript/appinsights-49usage.png)


按一下圖表以查看您可以顯示的其他度量，或加入新圖表並選取它要顯示的度量。

![](./media/app-insights-javascript/appinsights-63usermetrics.png)

> [AZURE.NOTE]度量只能以某種組合來顯示。當您選取度量時，不相容的度量會停用。



## 自訂頁面計數

依預設，每當用戶端瀏覽器載入新頁面時，頁面計數便會發生。不過您也許會想要計算其他頁面檢視。例如，由於頁面可能會將內容顯示在索引標籤中，因此您想要在使用者切換索引標籤時計算一次頁面。抑或是頁面中的 JavaScript 程式碼可能會在未變更瀏覽器 URL 的情況下載入新內容。

請將與以下範例相似的 JavaScript 呼叫插入用戶端程式碼中的適當位置：

    appInsights.trackPageView(myPageName);

頁面名稱可能會含有與 URL 相同的字元，不過 "#" 或 "?" 之後的任何字元都將遭到忽略。


## 檢查個別的頁面檢視事件

頁面檢視遙測資料一般是由 Application Insights 進行分析，而您只會看見以所有使用者為單位平均計算的累積報告。然而在偵錯時，您也可以查看個別的頁面檢視事件。

在 [Diagnostic Search] 分頁中，將 [篩選器] 設定為 [網頁檢視]。

![](./media/app-insights-javascript/12-search-pages.png)

選取任一事件以查看詳細資料。

> [AZURE.NOTE]如果您使用[搜尋][diagnostic]，請注意，您必須比對完整字詞："Abou" 和 "bout" 與 "About" 不相符，但是 "Abou* " 相符。此外，您不能將萬用字元當做搜尋詞彙的開頭。例如，搜尋 "*bou" 將無法與 "About" 相符。

> [深入了解診斷搜尋][diagnostic]

## 自訂使用情況追蹤

想要了解使用者如何使用您的應用程式嗎？ 在您的用戶端和伺服器程式碼中插入呼叫，可以將您自己的遙測傳送至 Application Insights。例如，您可以了解雖然建立訂單但未完成的使用者數目、最常發生的驗證錯誤，或遊戲中的平均分數。

[深入了解自訂事件和度量 API][track]。

## 伺服器遙測

如果您尚未這樣做，您可以從您的伺服器取得詳細資料，並且與用戶端的資料一同顯示，讓您評估伺服器的效能和診斷任何問題。

* [將 Application Insights 加入至 ASP.NET 應用程式][greenbrown]
* [將 Application Insights 加入至 Java Web 應用程式][java]


## <a name="video"></a>影片：追蹤使用量

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 後續步驟

[使用事件和度量來追蹤使用量][track]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[new]: app-insights-create-new-resource.md
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->