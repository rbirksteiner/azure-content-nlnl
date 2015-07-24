<properties 
 pageTitle="如何使用 Azure 排程器建立複雜的排程和進階週期" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="05/15/2015" 
 ms.author="krisragh"/>

# 如何使用 Azure 排程器建立複雜的排程和進階週期  

## 概觀

Azure 排程器的核心工作是*排程*。排程會決定排程器何時和如何執行工作。

Azure 排程器可讓您指定不同的一次和週期工作排程。*一次*排程會在指定的時間引發一次 – 實際上，它們是僅執行一次的*週期*排程。週期排程會根據預先決定的頻率引發。

由於這種彈性，Azure 排程器可讓您支援各種商務案例：

-	定期清除資料 – 例如，每一天，刪除所有超過 3 個月的推文
-	保存 – 例如，每個月，將發票歷程記錄推送至備份服務
-	要求外部資料 – 例如，每隔 15 分鐘，從 NOAA 提取新的 Ski 氣象報告
-	映像處理 – 例如，每個工作天，在離峰時段，使用雲端運算壓縮當天上傳的映像


在本文中，我們會逐步引導您完成您可以使用 Azure 排程器建立的範例工作。我們提供描述每個排程的 JSON 資料。如果您使用 [排程器 REST API](https://msdn.microsoft.com/library/azure/dn528946.aspx)，則可以使用這個相同的 JSON，[建立 Azure 排程器工作](https://msdn.microsoft.com/library/azure/dn528937.aspx)。

## 支援的案例

本主題中的許多範例將說明 Azure 排程器支援之案例的廣度。廣義來說，這些範例說明如何為許多行為模式建立排程，包括下列排程：

-	在特定日期和時間執行一次
-	執行並重複執行明確的次數
-	立即執行並重複執行 
-	執行並每隔 *n* 分鐘、小時、天、週或個月重複執行一次，從特定時間開始
-	執行並每週或每個月重複執行一次，但是只在特定日、特定星期幾或特定月日
-	執行並在某個期間執行多次 – 例如，每個月的最後一個星期五和星期一，或在每天上午 5:15 和下午 5:15

## 日期和日期時間

Azure 排程器工作中的日期遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601) ，並且只包含日期。

Azure 排程器工作中的日期時間參考遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601) ，並且同時包含日期和時間。未指定 UTC 時差的日期時間假設為 UTC。

## 如何：使用 JSON 和 REST API 建立排程

若要使用本文中的 JSON 範例和 Azure 排程器 REST API 建立簡單的排程，[首先建立雲端服務](https://msdn.microsoft.com/library/azure/dn528943.aspx)，[然後建立工作集合](https://msdn.microsoft.com/library/azure/dn528940.aspx)，[最後建立工作](https://msdn.microsoft.com/library/azure/dn528937.aspx)。當建立工作時，您可以使用如下摘錄的 JSON 指定排程與週期：

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## 概觀：工作結構描述的基本概念

下表提供與工作中週期和排程相關之主要元素的高階概觀：

|**JSON 名稱**|**說明**|
|:--|:--|
|**_startTime_**|_startTime_ 是日期時間。若為簡單排程，_startTime_ 是第一次發生的時間，若為複雜排程，工作一到 _startTime_ 即啟動。|
|**_recurrence_**|_recurrence_ 物件會指定工作的循環規則和工作將搭配執行的週期。recurrence 物件支援 _frequency、interval、endTime、count_ 和 _schedule_ 元素。如果已定義 _recurrence_，則 _frequency_ 為必要元素，而 _recurrence_ 的其他元素則為選用元素。|
|**_frequency_**|表示工作重複發生之頻率的 _frequency_ 字串。支援的值有 _"minute"、"hour"、"day"、"week" _ 或 _"month"_。|
|**_interval_**|_interval_ 是正整數，表示工作多久將執行一次之 _frequency_ 的間隔。比方說，如果 _interval_ 為 3，而 _frequency_ 為 "week"，則工作每隔 3 週重複執行一次。Azure 排程器支援的每個月頻率、每週頻率和每日頻率的最大 _interval_ 分別為 18 個月、78 週和 548 天。對於 hour 和 minute 頻率，支援的範圍為 1 <= _interval_ <= 1000。|
|**_endTime_**|_EndTime_ 字串會指定工作不應執行的過去日期時間。具有過去的 _endTime_ 是無效的。如果沒有 _endTime_ 或已指定計數，則工作會無限期執行。相同工作不能同時包含 _endTime_ 和 _count_。|
|**_count_**|<p> _count_ 是正整數 (大於零)，指定工作在完成之前應該執行的次數。</p><p> _count_ 表示工作在被判斷為已完成之前執行的次數。例如，每天執行的工作，若 _count_ 為 5，開始日期為星期一，則工作會在星期五執行之後完成。如果開始日期為過去日期，則會從建立時間計算第一次執行時間。</p><p>如果未指定 _endTime_ 或 _count_，則工作會無限期執行。相同工作不能同時包含 _endTime_ 和 _count_。</p>|
|**_schedule_**|具有指定頻率的工作會根據週期排程來改變其週期。_schedule_ 包含根據分鐘數、小時數、星期幾數、月日數和週數的修改。|

## 概觀：工作結構描述的預設值、限制和範例

在本概觀之後，讓我們詳細討論其中每一個元素。

|**JSON 名稱**|**值類型**|**必要？**|**預設值**|**有效值**|**範例**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|否|None|ISO 8601 日期時間|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recurrence_**|Object|否|None|Recurrence 物件|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|String|是|None|"minute"、"hour"、"day"、"week"、"month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|否|1|1 到 1000。|<code>"interval":10</code>|
|**_endTime_**|String|否|None|代表未來時間的日期時間值|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|否|None|>= 1|<code>"count": 5</code>|
|**_schedule_**|Object|否|None|Schedule 物件|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## 深入探討：_startTime_

下表擷取 _startTime_ 如何控制工作的執行方式。

|**startTime 值**|**無週期**|**週期。無排程**|**搭配排程的週期**|
|:--|:--|:--|:--|
|**没有開始時間**|立即執行一次|立即執行一次。根據從上次執行時間算出的時間執行後續的執行作業|<p>立即執行一次</p><p>根據週期排程執行後續的執行作業</p>|
|**過去的開始時間**|立即執行一次|<p>計算在開始時間之後的第一個未來執行時間，並在該時間執行</p><p>根據從上次執行時間算出的時間執行後續的執行作業</p><p>如需進一步說明，請參閱這個表格之後的範例</p>|<p>工作_一到_指定的開始時間即啟動。第一個週期根據從開始時間計算的排程</p><p>根據週期排程執行後續的執行作業</p>|
|**未來或目前的開始時間**|在指定的開始時間執行一次|<p>在指定的開始時間執行一次</p><p>根據從上次執行時間算出的時間執行後續的執行作業</p>|<p>工作_一到_指定的開始時間即啟動。第一個週期根據從開始時間計算的排程</p><p>根據週期排程執行後續的執行作業</p>|

我們來看看發生什麼情況的事範例，其中 _startTime_ 是過去時間，搭配 _recurrence_，但不搭配 _schedule_。假設目前的時間是 2015-04-08 13:00，_startTime_ 是 2015-04-07 14:00，而 _recurrence_ 是每 2 天 (以 _frequency_：天和 _interval_：2 定義。) 請注意，_startTime_ 是過去時間，因此在目前時間之前發生

根據這些條件，_第一次執行_將在 2015-04-09 的 14:00 發生。排程器引擎會從開始時間計算執行週期。過去的任何執行個體都會遭到捨棄。引擎會使用下一個在未來發生的執行個體。因此，在此情況下，_startTime_ 為 2015-04-07 下午 2:00，而下一個執行個體是在該時間的 2 天後，即 2015-04-09 下午 2:00。

請注意，第一次執行將相同，即使 startTime 為 2015-04-05 14:00 或 2015-04-01 14:00 也一樣。第一次執行之後，後續執行是使用排程來計算 – 因此，執行時間將為 2015-04-11 下午 2:00，接著 2015-04-13 下午 2:00，然後 2015-04-15 下午 2:00，依此類推。

最後，當工作具有排程時，如果未在排程中設定小時及/或分鐘，則它們會分別預設為第一次執行的小時及/或分鐘。

## 深入探討：_schedule_

一方面，_schedule_ 可以_限制_工作的執行次數。比方說，如果搭配 "month" 頻率的工作具有只在第 31 天執行的 _schedule_，則工作只會在具有第 31<sup></sup> 天的月份執行。

另一方面，_schedule_ 也可以_提高_工作的執行次數。例如，如果搭配 "month" 頻率的工作具有在當月 1 日及 2 日執行的 _schedule_，則工作會在當月的 1<sup></sup> 日和 2<sup></sup> 日執行，而不是一個月只執行一次。

如果指定了多個 schedule 元素，則評估的順序是從最大到最小 – 週數、月日、星期幾、小時和分鐘。

下表詳細說明 _schedule_ 元素。

|**JSON 名稱**|**說明**|**有效值**|
|:---|:---|:---|
|**minutes**|一小時內工作將執行的分鐘數|<ul><li>整數或</li><li>一連串整數</li></ul>|
|**hours**|一天內工作將執行的小時數|<ul><li>整數或</li><li>一連串整數</li></ul>|
|**weekDays**|工作將執行的星期幾。只能搭配 weekly 頻率指定。|<ul><li>"Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday" 或 "Sunday"</li><li>一連串上述值 (最多 7 個)</li></ul>_Not_ case-sensitive|
|**monthlyOccurrences**|決定將在當月哪幾天執行工作。只能搭配 monthly 頻率指定。|<ul><li>一連串 monthlyOccurence 物件：</li></ul> <pre>{ "day": _day_,<br /> "occurrence": _occurence_<br />}</pre><p> _day_ 是工作將執行的星期幾，例如，{Sunday} 是當月的每個星期日。必要。</p><p>週期是月日的 _occurrence_，例如 {Sunday, -1} 是當月的最後一個星期日。選用。</p>|
|**monthDays**|工作將執行的月日。只能搭配 monthly 頻率指定。|<ul><li>任何 < =-1 和 > =-31 的值。</li><li>任何 > = 1 和 < = 31 的值。</li><li>一連串上述值</li></ul>|

## 範例：週期排程

以下是週期排程的各種範例 – 將焦點放在 schedule 物件和其子元素。

以下排程全都假設 _interval_ 設為 1。此外，其中一個必須根據 _schedule_ 中的排程採用正確頻率 – 例如，其中一個不能使用頻率 "day"，也不能在排程中具有 "monthDays" 修改。上面說明了這類限制。

|**範例**|**說明**|
|:---|:---|
|<code>{"hours":[5]}</code>|在每天上午 5 點執行。Azure 排程器會將 "hours" 中的每個值與 "minutes" 中的每個值逐一比對，以建立一個清單，列出將執行工作的所有時間。|
|<code>{"minutes":[15],"hours":[5]}</code>|在每天上午 5:15 執行|
|<code>{"minutes":[15],"hours":[5,17]}</code>|在每天上午 5:15 和下午 5:15 執行|
|<code>{"minutes":[15,45],"hours":[5,17]}</code>|在每天上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|
|<code>{"minutes":[0,15,30,45]}</code>|每隔 15 分鐘執行一次|
|<code>{hours":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|每小時執行一次。這項工作每小時執行一次。如果建立時間已指定分鐘，或如果未指定，則分鐘會受到 _startTime_ 控制。比方說，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，則工作將會在 00:25、01:25、02:25、…、23:25 執行。排程相當於具有 _frequency_ 為 "hour"、_interval_ 為 1 且沒有 _schedule_ 的工作。差別在於此排程也可以搭配不同的 _frequency_ 和 _interval_ 使用，以建立其他工作。比方說，如果 _frequency_ 為 "month"，則排程只會一個月執行一次，而不是每天執行 (如果 _frequency_ 為 "day"|
|<code>{minutes:[0]}</code>|在每小時整點執行。這項工作也會每小時執行，但在整點 (例如上午 12 點、上午 1 點、上午 2 點等等。) 這相當於 frequency 為 "hour"、startTime 為零分鐘且沒有 schedule (如果 frequency 為 "day") 的工作，但如果 frequency 為 "week" 或 "month"，則排程會分別在一週某一天或一個月某一天執行。|
|<code>{"minutes":[15]}</code>|在過去每小時 15 分鐘執行。每小時執行，開始於上午 00:15、上午 1:15、上午 2:15 等等，並結束於下午 10:15 和下午 11:15。|
|<code>{"hours":[17],"weekDays":["saturday"]}</code>|在每週星期六下午 5 點執行|
|<code>{hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|在每週星期一、星期三、星期五下午 5 點執行|
|<code>{"minutes":[15,45],"hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|在每週星期一、星期三、星期五下午 5:15PM 和 5:45 執行|
|<code>{"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|在每週星期一、星期三、星期五上午 5 點和下午 5 點執行|
|<code>{"minutes":[15,45],"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|在每週星期一、星期三、星期五上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|在工作日每隔 15 分鐘執行一次|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|在工作日上午 9 點與下午 4:45 之間每隔 15 分鐘執行一次|
|<code>{"weekDays":["sunday"]}</code>|在星期日開始時間執行|
|<code>{"weekDays":["tuesday", "thursday"]}</code>|在星期二和星期四開始時間執行|
|<code>{"minutes":[0],"hours":[6],"monthDays":[28]}</code>|在每個月的第 28 天上午 6 點執行 (假設 frequency 為 month)|
|<code>{"minutes":[0],"hours":[6],"monthDays":[-1]}</code>|在當月最後一天上午 6 點執行。如果您想要在當月最後一天執行工作，請使用-1，而不是 28、29、30 或 31。|
|<code>{"minutes":[0],"hours":[6],"monthDays":[1,-1]}</code>|在每個月第一天和最後一天上午 6 點執行|
|<code>{monthDays":[1,-1]}</code>|在每個月第一天和最後一天的開始時間執行|
|<code>{monthDays":[1,14]}</code>|在每個月第一天和第十四天的開始時間執行|
|<code>{monthDays":[2]}</code>|在當月第二天的開始時間執行|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|在每個月第一個星期五上午 5 點執行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|在每個月第一個星期五的開始時間執行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":-3}]}</code>|在每個月倒數第三個星期五的開始時間執行|
|<code>{"minutes":[15],"hours":[5],"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|在每個月第一個和最後一個星期五上午 5:15 執行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|在每個月第一個和最後一個星期五的開始時間執行|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":5}]}</code>|在每個月第五個星期五的開始時間執行如果一個月沒有第五個星期五，則此工作不會執行，因為它是排定為只在第五個星期五執行。如果您想要在當月最後一個出現的星期五執行工作，則可以考慮對 occurrence 使用 -1 而不是 5。|
|<code>{"minutes":[0,15,30,45],"monthlyOccurrences":[{"day":"friday","occurrence":-1}]}</code>|在當月最後一個星期五每隔 15 分鐘執行一次|
|<code>{"minutes":[15,45],"hours":[5,17],"monthlyOccurrences":[{"day":"wednesday","occurrence":3}]}</code>|在每個月第 3 個星期三上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|

## 另請參閱
 
 [排程器是什麼？](scheduler-intro.md)
 
 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)
 
 [在管理入口網站中開始使用排程器](scheduler-get-started-portal.md)
 
 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)
 
 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)
 
 [排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)
 
 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)
 
 [排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)
 
 [排程器輸出驗證](scheduler-outbound-authentication.md)
 
  

<!---HONumber=July15_HO2-->