<properties 
	pageTitle="使用診斷搜尋" 
	description="搜尋和篩選個別事件、要求和記錄追蹤。" 
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
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中使用診斷搜尋

「診斷搜尋」是 [Application Insights][start] 中的分頁，您會用來尋找及探索個別遙測項目，例如頁面檢視、例外狀況或 Web 要求。而您可以檢視所編寫的記錄追蹤和事件。

## 何時會看到「診斷搜尋」？

您可以明確開啟診斷搜尋：

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


在您逐一點選某些圖表和格線項目時，它也會開啟。在此情況中，其篩選器會預設為著重在您選取的項目類型。

例如，如果您的應用程式是 Web 服務，[概觀] 分頁會顯示要求數量的圖表。按一下它便可取得更詳細的圖表，包含一分清單，顯示對每個 URL 進行的要求數量。按一下任何列，便可取得該 URL 的個別要求的清單：

![Open diagnostic search](./media/app-insights-diagnostic-search/07-open-from-filters.png)


「診斷搜尋」的主體是遙測項目的清單 - 伺服器要求、頁面檢視、您編寫的自訂事件等等。在清單上方是摘要圖表，顯示一般時間的事件計數。


## 檢查個別項目

選取任何遙測項目來查看重要欄位和相關項目。如果您想要查看完整的欄位集合，請按一下 "..."。

![Open diagnostic search](./media/app-insights-diagnostic-search/10-detail.png)

若要尋找欄位的完整集合，請使用純文字字串 (不含萬用字元)。可用的欄位因遙測類型而定。

## 篩選事件類型

開啟 [篩選器] 分頁並選擇要查看的事件類型。(如果之後想要還原您所開啟分頁的篩選器，請按一下 [重設]。)


![選擇篩選器並選取遙測類型](./media/app-insights-diagnostic-search/02-filter-req.png)


事件類型包括：

* **追蹤** - 診斷記錄包括 TrackTrace、log4Net、NLog 和 System.Diagnostic.Trace 呼叫。
* **要求** - 伺服器應用程式收到的 HTTP 要求，包括頁面、指令碼、影像、樣式檔案和資料。這些事件是用來建立要求和回應概觀圖表。
* **頁面檢視** - 遙測是由 Web 用戶端傳送，用來建立頁面檢視報告。 
* **自訂事件** - 如果您插入呼叫到 TrackEvent() 來[監視使用量][track]，您可以在這裡搜尋它們。
* **例外狀況** - 未在伺服器上捕捉到的例外狀況，以及您使用 TrackException() 記錄的那些。

## 依據屬性值篩選

您可以依屬性的值篩選事件。可用的屬性因您所選取的事件類型而定。

例如，找出具有特定回應代碼的要求。

![展開屬性並選擇值](./media/app-insights-diagnostic-search/03-response500.png)

未對特定屬性選擇值與選擇所有值具有相同效果；它會將對該屬性的篩選切換成關閉。


### 縮小搜尋

篩選器值右側的計數顯示目前篩選的集合中有多少個出現次數。

在此範例中，很明顯 `Reports/Employees` 要求造成多數的 500 錯誤：

![展開屬性並選擇值](./media/app-insights-diagnostic-search/04-failingReq.png)

此外，如果您也想要查看在這段時間其他事件發生的情況，可以勾選 [**包含未定義屬性的事件**]。

## 檢查個別出現次數

將該要求名稱加入到篩選器集，然後您可以檢查該事件的個別出現次數。

![選取值](./media/app-insights-diagnostic-search/05-reqDetails.png)

針對 [要求] 事件，詳細資料顯示處理要求時發生的例外狀況。

逐一點選例外狀況來查看其詳細資料。

![按一下例外狀況](./media/app-insights-diagnostic-search/06-callStack.png)

## 尋找具有相同屬性的事件

尋找具有相同屬性值的所有項目：

![以滑鼠右鍵按一下屬性](./media/app-insights-diagnostic-search/12-samevalue.png)

## 依據度量值搜尋

取得回應時間大於 5 秒的所有要求。時間是依刻度代表：10000 刻度 = 1 毫秒。

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## 搜尋資料

您可以搜尋任何屬性值中的字詞。如果您已編寫具有屬性值的[自訂事件][track]，這特別實用。

您可能想要設定時間範圍，因為對一小段時間範圍進行的搜尋較快速。

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

搜尋詞彙，而不是子字串。詞彙是英數字串，包含 '.' 和 '_' 之類的標點符號。例如：

詞彙|*不*符合|但這些符合
---|---|---
HomeController.About|about<br/>home|h*about<br/>home*
IsLocal|local<br/>is<br/>*local|isl*<br/>islocal<br/>i*l*
New Delay|w d|new<br/>delay<br/>n* AND d*


以下是您可以使用搜尋運算式：

範例查詢 | 效果 
---|---
slow|在日期範圍中尋找欄位含有詞彙 "slow" 的所有事件
database??|比對 database01、databaseAB、...<br/>? 不允許出現在搜尋詞彙的開頭。
database*|比對 database、database01、databaseNNNN<br/>* 不允許出現在搜尋詞彙的開頭
apple AND banana|尋找同時含有這些詞彙的事件。請使用大寫 "AND"，而不是 "and"。
apple OR banana<br/>apple banana|尋找含有任一詞彙的事件。請使用 "OR"，而不是 "or"。</br/>簡短格式。
apple NOT banana<br/>apple -banana|尋找含有一個詞彙但不含另一個詞彙的事件。<br/>簡短格式。
app* AND banana -(grape pear)|邏輯運算子和括號。
"Metric": 0 TO 500<br/>"Metric" : 500 TO * | 尋找在值範圍內包含指定度量的事件。


## 儲存搜尋

設定需要的所有篩選器之後，您可以將搜尋儲存為我的最愛。如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![按一下 [我的最愛]，設定名稱，然後按一下 [儲存]](./media/app-insights-diagnostic-search/08-favorite-save.png)


若要再次查看搜尋，請**前往 [概觀] 分頁**，並開啟 [我的最愛]：

![我的最愛磚](./media/app-insights-diagnostic-search/09-favorite-get.png)

如果儲存時使用「相對」時間範圍，重新開啟的分頁會具有最新資料。如果儲存時使用「絕對」時間範圍，則會每次都看到相同資料。


## 傳送更多遙測到 Application Insights

除了 Application Insights SDK 傳送的內建遙測，您可以：

* 在 [.NET][netlogs] 或 [Java][javalogs] 中，從您最喜愛的記錄架構擷取記錄追蹤。這表示您可以搜尋您的記錄追蹤，並將它們與頁面檢視、例外狀況和其他事件相互關聯。 
* [編寫程式碼][track]以傳送自訂事件、頁面檢視和例外狀況。 

[了解如何傳送記錄和自訂的遙測到 Application Insights][trace]。


## <a name="questions"></a>問與答

### <a name="limits"></a>保留多少資料？

每個應用程式每秒最多 500 個事件。事件會保留七天。

### 我如何查看我的伺服器要求中的 POST 資料？

我們不會自動記錄 POST 資料，但您可以使用 [TrackTrace 或記錄呼叫][trace]。將 POST 資料放在訊息參數中。您無法以對屬性的相同方式來篩選訊息，但大小限制較長。

## <a name="add"></a>接續步驟

* [傳送記錄和自訂的遙測到 Application Insights][trace]
* [設定可用性和回應性測試][availability]
* [疑難排解][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->