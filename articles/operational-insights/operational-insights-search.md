<properties
   pageTitle="搜尋 Operational Insights 中的資料"
   description="您可以在 Microsoft Azure Operational Insights 中使用記錄搜尋功能，尋找您正在搜尋的資料"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/02/2015"
   ms.author="banders" />

# 搜尋 Operational Insights 中的資料

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights 的核心是搜記錄尋功能，可讓您結合和相互關聯您環境內多個來源的任何電腦資料。解決方案也是由記錄搜尋發動，可讓您針對特定問題領域進行度量。

在 [搜尋] 頁面上，您可以建立查詢，然後在搜尋時，您可以使用 Facet 控制篩選結果。您也可以建立進階查詢來轉換、篩選和報告結果。

常見的記錄搜尋查詢會出現在大部分解決方案頁面中。在整個 Operational Insights 主控台中，您可以按一下磚或向內切入其他項目，使用 [記錄搜尋] 來檢視項目的詳細資料。

在本教學課程中，我們將逐步解說範例，以涵蓋您使用記錄搜尋時的所有基本項目。

我們會以簡單、實用的範例開頭，然後根據這些範例進一步說明，讓您可以了解實際的使用案例，了解如何使用語法從資料擷取您想要的觀點。

當您在 Operational Insights 中進行記錄搜尋時，您將使用下列技巧：

- 使用基本篩選器
- 使用其他篩選器
- 操控搜尋結果
- 使用 measure 命令
- 透過 measure 命令使用 max 和 min 函數
- 透過 measure 命令使用avg 函數
- 使用 where 命令

熟悉搜尋技術之後，您可以檢閱[搜尋語法參考](#search-syntax-reference)和[搜尋欄位和 facet 參考](#search-field-and-facet-reference)。

## 使用基本篩選器

首先要知道的事情是搜尋查詢的第一個部分，在任何 "|" 分隔號字元之前，一律為*篩選器*。您可以將它當做 TSQL 中的 WHERE 子句--它會判斷要從 Operational Insights 資料存放區提取*哪個*資料子集。從資料存放區搜尋主要是有關指定您想要擷取的資料特性，因此查詢會很自然地以 WHERE 子句開頭。

您可以使用之最基本的篩選器為*關鍵字*，例如 'error' 或 'timeout' 或電腦名稱。這些簡單的查詢類型通常會在相同的結果集內傳回各種資料圖形。這是因為 Operational Insights 在系統中有不同*類型*的資料。


### 進行簡單搜尋
1. 在 Operational Insights 入口網站中，按一下 [**搜尋資料總管**]。![搜尋磚](./media/operational-insights-search/overview-search.png)
2. 在查詢欄位中，輸入 `error`，然後按一下 [**搜尋**]。![搜尋錯誤](./media/operational-insights-search/search-error.png) 例如，在下列映像中，`error` 的查詢會傳回 100,000 個 **Event** 記錄 (由記錄檔管理所收集)，18 個 **Alert** 記錄 (由組態評估所產生)，以及 12 個 **ConfigurationChange** 記錄 (由變更追蹤所擷取)。![搜尋結果](./media/operational-insights-search/results01.png)

這些篩選器不一定是物件類型/類別。*類型*只是附加到資料片段的標記或屬性，或字串/名稱/類別。系統中的某些文件會標記為 **Type:Alert**，而某些會標記為 **Type:PerfHourly** 或標記為 **Type:Event**，依此類推。每個搜尋結果、文件、記錄或項目都會顯示所有未經處理的屬性及它們針對每個資料片段的値，當您只想擷取欄位在其中具有指定値的記錄時，您可以在篩選器中使用那些欄位名稱來指定。

*類型*其實只是所有記錄都有的欄位，與任何其他欄位並沒有不同。這是根據 [類型] 欄位的值所建立的。該記錄會有不同的圖形或形式。順帶一提，**Type=PerfHourly** 或 **Type=Event** 也是您需要了解以查詢每小時效能資料彙總或事件的語法。

您可以在欄位名稱之後和值之前使用冒號 (:) 或等號 (=)。**Type:Event** 和 **Type=Event** 在意義上是相等的，您可以選擇您偏好的樣式。

因此，如果 Type=PerfHourly 記錄具有名為 'CounterName' 的欄位，您就可以撰寫類似於 `Type=PerfHourly CounterName="% Processor Time"` 的查詢。

如此可以只提供您效能資料，其中的效能計數器名稱是"%Processor Time"。

### 搜尋處理器時間效能資料
- 在搜尋查詢欄位中，輸入 `Type=PerfHourly CounterName="% Processor Time"`

您也可以更明確，並在查詢中使用 **InstanceName=_'Total'**，此為 Windows 效能計數器。您也可以選取 facet 和另一個 **field:value**。篩選器會自動加入至查詢列的篩選器中。您可以在下列映像中看到此情況。它會向您顯示要在哪裡按一下，以將 **InstanceName:’_Total’** 加入至查詢而不需要輸入任何項目。

![搜尋 facet](./media/operational-insights-search/search-facet.png)

您的查詢現在已成為 `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`

在此範例中，您不必指定 **Type=PerfHourly** 來取得這個結果。因為 CounterName 和 InstanceName 欄位只會為 Type=PerfHourly 的記錄而存在，所以特定的查詢會傳回的結果和先前較長的結果相同：```
CounterName=”% Processor Time” InstanceName=”_Total”
```

這是因為查詢中的所有篩選器都會彼此評估為 *AND*。事實上，您將愈多欄位加入至準則，您得到的更特定且精緻的結果愈少。

例如，查詢 `Type=Event EventLog="Windows PowerShell"` 等同於 `Type=Event AND EventLog="Windows PowerShell"`。它會傳回記錄於 Windows PowerShell 事件記錄檔中以及從該事件記錄檔收集到的所有事件。如果您藉由重複選取相同的 facet 以多次加入篩選器，此問題會相當明確--它可能會干擾 [搜尋] 列，但它仍會傳回相同的結果，因為隱含 AND 運算子一律都會在其中。

您可以藉由明確使用 NOT 運算子，輕鬆地反轉隱含 AND 運算子。例如：

`Type:Event NOT(EventLog:"Windows PowerShell")` 或其相等的 `Type=Event EventLog!="Windows PowerShell"` 會從所有其他不是 Windows PowerShell 的記錄傳回所有事件。

或者，您可以使用其他布林運算子，例如 'OR'。下列查詢會傳回 EventLog 為應用程式或系統的記錄。

```
EventLog=Application OR EventLog=System
```

使用上述查詢，您會在相同的結果集中取得兩個記錄的項目。

不過，如果您藉由保留隱含 AND 以移除 OR，下列查詢就不會產生任何結果，因為沒有屬於這兩個記錄的事件記錄項目。每個事件記錄項目只會寫入至兩個記錄之一。

```
EventLog=Application EventLog=System
```


## 使用其他篩選器

下列查詢會傳回已傳送資料的所有電腦的 2 個事件記錄的項目。

```
EventLog=Application OR EventLog=System
```

![搜尋結果](./media/operational-insights-search/search-results03.png)

選取其中一個欄位或篩選器會縮小查詢範圍到特定的電腦，並排除所有其他電腦。產生的查詢如下所示。

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

因為隱含 AND 的緣故，這相當於下列內容。

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

每個查詢都會以下列明確的順序進行評估。請注意括號。

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

就像事件記錄欄位一般，您可以藉由新增 OR，即可只擷取一組特定電腦的資料。例如：

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

同樣地，下列查詢只會傳回已選取之兩部電腦的 **% CPU 時間**。

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### 布林運算子
利用日期時間和數字欄位，您可以使用*大於*、*小於*和*小於或等於*來搜尋值。您可以使用簡單的運算子，例如查詢搜尋列中的 >、<、>=、<=、!=。


您可以查詢特定一段時間的特定事件記錄。例如，過去 24 小時會使用下列助憶鍵運算式表示。

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### 使用布林運算子來搜尋
- 在搜尋查詢欄位中，輸入 `EventLog=System TimeGenerated>NOW-24HOURS"` ![使用布林值的搜尋](./media/operational-insights-search/search-boolean.png)

雖然您可以以圖形方式控制時間間隔，而且大多數時候您可能會想這麼做，但是將時間篩選器直接包含在查詢中還是有其優點。例如，這非常適合儀表板，無論儀表板頁面上的*全域*時間選取器為何，您都可以在其中覆寫每個磚的時間。如需詳細資訊，請參閱[時間對儀表板很重要](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)。

藉由時間篩選時，請記住，您會得到兩個時間週期的*交集*結果：一個時間週期指定於 Operational Insights 入口網站 (S1)，另一個時間週期指定於查詢 (S2) 中。

![交集](./media/operational-insights-search/intersection.png)

這表示，如果時間週期不交集，例如，位於您在其中選擇**本週**的 Operational Insights 入口網站中，以及位於您定義**上週**的查詢中，則沒有交集，且您不會收到任何結果。

用於 TimeGenerated 欄位的比較運算子在其他情況下也很有用。例如，用於數值欄位。

例如，假設組態評估的警示有下列嚴重性值：

- 0 = 資訊
- 1 = 警告
- 2 = 重大

您可以查詢警告和重大警示，並且排除具備下列查詢的資訊警示：

```
Type=ConfigurationAlert  Severity>=1
```


您也可以使用範圍查詢。這表示您可以序列提供値的開始和結束範圍。例如，如果您想要來自 Operations Manager 事件記錄 (其中 EventID 大於或等於 2100，但小於 2199) 的事件，下列查詢就會傳回它們。

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]您必須使用的範圍語法是冒號 (:) field:value 分隔符號，*不*是等號 (=)。用方括號括住範圍的下限和上限結尾，並使用兩個句點 (..) 隔開它們。

## 操控搜尋結果

當您在搜尋資料時，您會想要精簡搜尋查詢，並已經對結果有相當程度的控制。擷取結果時，您可以套用命令以將其轉換。

Operational Insights 搜尋中的命令*必須*跟在分隔號字元 (|) 之後。篩選器一律為查詢字串的第一個部分。它會定義您正在使用的資料集，然後將那些結果「輸送」入命令。然後您可以使用管道來新增其他命令。這樣有點類似 Windows PowerShell 管線。

一般而言，Operational Insights 搜尋語言會嘗試遵循 PowerShell 樣式和指導方針，使它類似於 IT 專業人員，並簡化學習曲線。

命令具有動詞的名稱，因此您可以輕易地分辨它們執行的動作。

### 排序

sort 命令可讓您利用一或多個欄位來定義排序順序。即使您不使用它，根據預設，會強制執行時間遞減順序。最新的結果永遠在搜尋結果的頂端。這表示當您執行搜尋時，真正利用 `Type=Event EventID=1234` 執行的內容如下：

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

這是因為它是您在記錄中熟悉的經驗類型。例如，在 Windows 事件檢視器中。

您可以使用 Sort 來變更傳回結果的方式。下列範例顯示此運作方式。

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


上述的簡單範例顯示命令的運作方式--它們會變更篩選器傳回結果的圖形。

### Limit 和 top
另一個較少人知道的命令是 LIMIT。Limit 為類似 PowerShell 的動詞。Limit 在功能上和 TOP 命令相同。下列查詢會傳回相同的結果。

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### 使用 top 搜尋
- 在搜尋查詢欄位中，輸入 `Type=Event EventID=2110 | Top 1` ![搜尋 top](./media/operational-insights-search/search-top.png)

在上述映像中，有 988 個附帶 EventID=2110 的記錄。左邊的欄位、facet 和篩選器永遠會顯示查詢的*篩選器部分*所傳回的結果資訊，也就是任何縱線字元之前的部分。[**結果**] 窗格只會傳回最新的 1 個結果，因為範例命令會圖形化和轉換結果。

### 選取

SELECT 命令的行為類似 PowerShell 中的 Select-Object。它會傳回不包含所有原始屬性的篩選結果。相反地，它只會選取您所指定的屬性。

#### 使用 select 命令執行搜尋

1. 在搜尋中，輸入 `Type=Event`，然後按一下 [**搜尋**]。
2. 按一下其中一種結果中的 [**+ 顯示更多**]，以檢視結果具有的屬性。
3. 明確地選取部分，查詢會變更為 `Type=Event | Select Computer,EventID,RenderedDescription`。![搜尋 select](./media/operational-insights-search/search-select.png)

當您想要控制搜尋輸出，並且只選擇對探索真的很重要的資料部分 (通常不是完整記錄)，這會是特別有用的命令。當不同類型的記錄有*部分*共用的屬性，但不是*所有*屬性都共用時，這也非常有用。然後，您可以產生看起來就像資料表一般自然的輸出，或在匯出至 CSV 檔案，然後傳遞訊息至 Excel 中時正常運作。

## 使用 measure 命令

Measure 是 Operational Insights 搜尋中最具彈性的命令之一。它可讓您將統計*函數*套用至資料，並彙總透過指定欄位分組的結果。Measure 支援多個統計函數。

### Measure count()

第一個要使用的統計函數，也是最容易了解的統計含數之一就是 *count ()* 函數。

來自任何搜尋查詢的結果 (例如 `Type=Event`) 會在搜尋結果的左邊顯示稱為 facet 的篩選器。篩選器會在執行的搜尋中透過指定的結果欄位顯示值的分佈。

![搜尋 measure count](./media/operational-insights-search/search-measure-count01.png)

例如，在上述的映像中，您會看到 [**電腦**] 欄位，它會顯示在結果中將近 3 百萬個事件內，那些記錄中的 [**電腦**] 欄位有 20 個唯一且不同的值。磚只會顯示前 5 個値 (也就是會寫入 [**電腦**] 欄位中最常見的 5 個值)，根據在該欄位中指定値的文件數目來排序。在映像中，您可以看到 – 在將近 3 百萬個事件之中 - 88 萬個事件來自 DM 電腦，60 萬 2 千個事件來自 DE 電腦，依此類推。


由於磚只會顯示前 5 個値，如果要查看所有的值怎麼辦？

measure 命令可以使用 count () 函數達成。此函數不會使用任何參數。您只需要指定您想要依其分組的欄位即可 – 在此案例中為 [**電腦**] 欄：

`Type=Event | Measure count() by Computer`

![搜尋 measure count](./media/operational-insights-search/search-measure-count-computer.png)

不過，[**電腦**] 只是*在*資料片段中所使用的欄位 - 沒有相關的關聯式資料庫，而且沒有個別**電腦**物件在任何位置。只有*在*資料中的値可以描述產生它們的實體，以及資料的其他特性和方面的數目 - 因此也能描述 *facet* 一詞。不過，您也可以根據其他欄位分組。因為輸送到 measure 命令的將近 3 百萬個事件的原始結果也具有稱為 **EventID** 的欄位，所以您可以套用相同的技巧，以根據該欄位分組，並取得根據 EventID 的事件計數：

```
Type=Event | Measure count() by EventID
```

如果您對實際的記錄計數 (包含特定値) 不感興趣，而是只想要值本身的清單，您可以在値的尾端加入 *Select* 命令，並只選取第一個資料行：

```
Type=Event | Measure count() by EventID | Select EventID
```

然後您就可以在查詢中取得更複雜和預先排序的結果，或者您也可以只按一下方格中的資料行。

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### 使用 measure count 搜尋

- 在搜尋查詢欄位中，輸入 `Type=Event | Measure count() by EventID`
- 將 `| Select EventID` 附加至查詢的結尾。
- 最後，將 `| Sort EventID asc` 附加至查詢的結尾。


有一些要注意與強調的重點：

首先，您看到的結果不是未經處理的結果了。相反地，它們是彙總的結果 – 基本上是結果的群組。這不是問題，但您應該了解您正與非常不同的資料圖形互動，其差異在於即時建立為彙總/統計函數結果未經處理的圖形。

第二，**Measure count** 目前只會傳回前 100 個不同結果。這項限制不適用於其他統計函數。因此，您通常必須先使用更精確的篩選器來搜尋特定項目，然後再套用 measure count()。

## 透過 measure 命令使用 max 和 min 函數

有 **Measure Max()** 和 **Measure Min()** 在其中很有用的各種案例。不過，由於每個函數彼此相反，我們將說明 Max ()，而您可以自己實驗 Min ()。

如果您查詢組態評估警示，他們具有分別代表資訊、警告和重要的**嚴重性**屬性 0、1 或 2。例如：

```
Type=Alert
```

![搜尋 measure count 開始](./media/operational-insights-search/search-measure-max01.png)

如果您想要在指定共用「電腦」(根據欄位的分組) 的情況下，檢視所有警示的最高值，您可以使用

```
Type=Alert | Measure Max(Severity) by Computer
```

![搜尋 measure max 電腦](./media/operational-insights-search/search-measure-max02.png)

它會像具有**警示**記錄的電腦顯示該警示，大部分的電腦都有至少一個重要警示，而且 Bacc 電腦會將警告視為其最差的嚴重性。

```
Type=Alert | Measure Max(Severity) by Computer
```

![搜尋 measure max 時間產生電腦](./media/operational-insights-search/search-measure-max03.png)

此函數適用於數字，但它也可以使用日期時間欄位。您最好檢查最後一個或最新的時間戳記，是否有任何為每台電腦編製索引的資料片段位。例如：每部機器的變更追蹤解決方案會在何時回報最新的組態變更？

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## 透過 measure 命令使用avg 函數

和 measure 搭配使用的 Avg () 統計函數可讓您計算某些欄位的平均值，並且根據相同或其他欄位將結果分組。這適用於各種情況，例如效能資料。

我們將從效能等級開始介紹。不過請注意，Operational Insights 目前只會收集 Virtual Machine Manager 和 Hyper-V 主機之與特定架構有關的效能計數器，做為容量管理解決方案的一部分。

若要搜尋*所有*效能資料，最基本的查詢如下：

```
Type=PerfHourly
```

![搜尋 avg 開始](./media/operational-insights-search/search-avg01.png)

您會注意到的第一件事是 Operational Insights 會顯示效能計數器的圖表。在結果的底部，您會看到圖表背後的實際資料。

![搜尋 avg 開始](./media/operational-insights-search/search-avg02.png)

在上述映像中，有兩組標示的欄位，指出下列事項：

- 第一組會識別查詢篩選器中的 Windows 效能計數器名稱、物件名稱和執行個體名稱。這些是您可能會最常用來做為 facet/篩選器的欄位
- **SampleValue** 是計數器的實際值
- 在查詢中，**Type=PerfHourly** 為每小時的彙總
- **TimeGenerated** 為 21:00，採用 24 小時制的時間格式。它是 20:00 到 21:00 之間，每小時的彙總。
- **SampleCount** 是使用 12 個範例計算的彙總 (每隔 5 分鐘一個範例)
- 在此虛擬機器記憶體的範例中，每小時間隔的 **min**、**max** 和 **Percentile95** 為 6144 (MB)
- **SampleValue** 為每小時的彙總，其中已填入每小時間隔的*平均*，並用來繪製效能圖表

閱讀 PerfHourly 記錄圖形，並閱讀其他搜尋技術之後，您可以使用measure Avg () 來彙總此類型的數值資料。

以下是簡單的範例：

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![搜尋 avg samplevalue](./media/operational-insights-search/search-avg03.png)

在此範例中，您可以選取 CPU 時間總計效能計數器並根據電腦來平均。因為 **SampleValue** 已經是平均，您實際上查詢的是平均的平均。此時這對於 Type=PerfHourly 而言是正確的。您應該一律使用 TimeGenerated 上的篩選器限制小型或近期的資料集，例如過去 4 小時--而不是 7 天。

所以上述查詢會變成：

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### 搭配使用 avg 函數和 measure 命令進行搜尋
1. 在 [搜尋查詢] 方塊中，輸入 `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`。
2. 請注意，近期的平均通常會較高。
3. 利用 `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer` 修改您的搜尋查詢，以計算每小時值的上限平均

您可以*跨*電腦彙總資料，並將其相互關聯。例如，假設您有一組某種伺服器陣列的主機，其中的節點彼此相等，而且它們執行的工作類型完全相同，負載也應該大致平衡。您可以利用下列查詢一次取得所有計數器，並取得整個伺服器陣列的平均。您可以藉由選擇電腦，以下列範例開頭：

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

您擁有電腦之後，也只會想要選取兩個關鍵效能指標 (KPI)：% CPU 使用量和 % 可用磁碟空間。因此，查詢的該部分會變成：

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

您現在可以利用下列範例新增電腦和計數器：

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

因為您有非常特定的選取範圍，**measure Avg ()** 命令可能會傳回平均，不是由電腦進行，而是跨伺服器陣列，只根據 CounterName 分組。例如：

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

這可提供您數個環境 KPI 的實用精簡檢視。

![搜尋 avg grouping](./media/operational-insights-search/search-avg04.png)


您可以在儀表板中輕鬆使用它。若要深入了解如何使用儀表板，請參閱 [Operational Insights 儀表板](operational-insights-use-dashboards)。

![搜尋 avg 儀表板](./media/operational-insights-search/search-avg05.png)

### 透過 measure 命令使用 sum 函數

sum 函數和 measure 命令的他函數類似。您可以在 [Microsoft Azure Operational Insights 中的 W3C IIS 記錄搜尋](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)查看如何使用 sum 函數的範例。

您可以搭配使用 Max() 和 Min() 與數字、日期時間和文字字串。透過文字字串，它們會依字母順序排序，而您會取得第一個和最後一個。

不過，您無法搭配使用 Sum() 與數值欄位以外的任何項目。這也適用於 Avg()。

## 使用 where 命令

where 命令的運作方式和篩選器類似，但它可以套用在管線中，以進一步篩選由 Measure 命令產生的彙總結果 - 而不是在查詢開頭篩選過的未經處理的結果。

例如：

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

您可以利用下列範例，加入另一個縱線 "|" 字元以及 Where 命令，即可只取得平均 CPU 高於 80% 的電腦：

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

如果您已熟悉 Microsoft System Center Operations Manager，您可以考慮管理組件詞彙中的 where 命令。如果此範例是一項規則，查詢的第一個部分會是資料來源，而 where 命令會是條件偵測。

您可以使用查詢做為 [**我的儀表板**] 中的磚，做為排序的監視器，可查看電腦 CPU 何時過度使用。若要深入了解儀表板，請參閱 [Operational Insights 儀表板](operational-insights-use-dashboards)。您也可以使用行動應用程式建立和使用儀表板。如需詳細資訊，請參閱 [Azure Operational Insights 行動應用程式](http://www.windowsphone.com/zh-tw/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)。在下列映像底部的兩個磚中，您可以看到監視器顯示清單並做為一個數字。基本上，您一定想要此數字為零且清單是空的。否則，它會指出警示條件。如有需要，您可以使用它來看看那些電腦承受壓力。

![行動儀表板](./media/operational-insights-search/search-mobile.png)

## 搜尋語法參考

下列關於搜尋語言的參考章節描述一般查詢語法選項，您可以在搜尋資料及篩選運算式時用來幫助您縮小搜尋範圍。它也會描述您可以用來在已擷取的資料上採取動作的命令。

您可以在[搜尋欄位和 facet 參考](#Search-field-and-facet-reference)中閱讀搜尋中傳回的欄位，以及協助您深入鑽研相似資料類別的 facet。

### 一般查詢語法

語法：

filterExpression | command1 | command2 …

篩選運算式 (**filterExpression**) 會定義查詢的 "where" 條件。命令會套用至查詢所傳回的結果。多個命令必須以縱線字元 ( | ) 分隔。

#### 一般語法範例

範例：

	system

此查詢會傳回在已編製索引的任何欄位中進行全文檢索或詞彙搜尋時包含 "system" 這個單字的結果。

>[AZURE.NOTE]並非所有的欄位都以此方式編製索引，不過最常見的文字欄位 (例如描述和名稱) 通常都是。

	system error

此查詢會傳回包含 "system" 和 "error" 等單字的結果。

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

此查詢會傳回包含 "system" 和 "error" 等單字的結果。 然後會根據 **ManagementGroupName** 欄位 (依遞增順序)，再以 **TimeGenerated** (依遞減順序) 排序結果。它只會採用前 10 個結果。

>[AZURE.IMPORTANT]所有的欄位名稱以及字串和文字欄位的值都會區分大小寫。

### 篩選運算式

下列小節說明篩選運算式。

#### 字串常值

字串常值是未經由剖析器辨識為關鍵字或預先定義資料類型 (例如，數字或日期) 的任何字串。

範例：

	These all are string literals


此查詢會搜尋包含五個字全都出現的結果。若要執行複雜的字串搜尋，請將字串常值以引號括起來，例如：

	" Windows Server"

只傳回結果為 "Windows Server" 的完全相符項目

#### 數字

剖析器支援數值欄位的十進位整數和浮點數語法。

範例：

	Type:PerfHourly 0.5


	HTTP 500

#### 日期/時間

系統中的每個資料片段都有 **TimeGenerated** 屬性，代表記錄的原始日期和時間。某些資料類型可以另外有更多的日期/時間欄位 (例如，**LastModified**)。

Operational Insights 中的時間表/時間選取器會顯示經過一段時間的結果分佈 (根據目前執行的查詢)，以 **TimeGenerated** 欄位為基礎。日期/時間欄位具有特定字串格式，可以在查詢中用來將查詢限制在特定時間範圍內。您也可以使用語法來參考相對的時間間隔 (例如，「3 天前和 2 小時前之間」)。

語法：

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



範例：

	TimeGenerated:2013-10-01T12:20

前一個命令只會傳回記錄與 2013 年 10 月 1 日 12:20 的精確 **TimeGenerated** 值。它不太可能會提供任何結果，但您可以了解這個概念。

剖析器也支援助憶鍵的日期/時間值，NOW。

範例：


同樣地，這也不太可能產生任何結果，因為資料無法這麼快速地通過系統。

這些範例是用於相對與絕對日期的建置組塊。在接下來的三個子節中，我們將透過使用相對日期範圍的範例，說明如何在更進階的篩選器中使用它們。

#### 日期/時間數學

使用日期/時間數學運算子，透過簡單的計算位移或四捨五入日期/時間值。

語法：

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>運算子</th>
		<th>說明</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>將日期/時間四捨五入到指定的單位。</p>
		<p>範例：NOW/DAY 會將目前的日期/時間四捨五入至目前日期的午夜。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ 或 -</p>
		</td>
		<td>
		<p>將日期/時間位移指定的單位數</p>
		<p>範例：&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR 會將目前的日期/時間位移早一個小時。<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS 會將日期值往回位移 10 天。</li>
		</ul>
		</td>
	</tr>
</table>





您可以將日期/時間數學運算子鍊結在一起，例如：

	NOW+1HOUR-10MONTHS/MINUTE

下表列出受支援的日期/時間單位。

<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>日期/時間單位 </th> <th>描述 </th> </tr> <tr> <td> <p>YEAR, YEARS</p> </td> <td> <p>四捨五入為目前的年份或位移指定的年數。</p> </td> </tr> <tr> <td> <p>MONTH, MONTHS</p> </td> <td> <p>四捨五入為目前的月份，或位移指定的月數。</p> </td> </tr> <tr> <td> <p>DAY, DAYS, DATE</p> </td> <td> <p>四捨五入為目前月份的日期，或位移指定的天數。</p> </td> </tr> <tr> <td> <p>HOUR, HOURS</p> </td> <td> <p>四捨五入為目前的小時或位移指定的小時數。</p> </td> </tr> <tr> <td> <p>MINUTE, MINUTES</p> </td> <td> <p>四捨五入為目前的分鐘數，或位移指定的分鐘數。</p> </td> </tr> <tr> <td> <p>SECOND、 SECONDS</p> </td> <td> <p>四捨五入為目前的秒數或位移指定的秒數。</p> </td> </tr> <tr> <td> <p>MILLISECOND, MILLISECONDS, MILLI, MILLIS</p> </td> <td> <p>四捨五入為目前的毫秒數，或位移指定的毫秒數。</p> </td> </tr> </table>


#### 欄位 facet

藉由使用欄位 facet，您可以指定特定欄位的搜尋條件及其實際值，而非對整個索引的各種詞彙撰寫「任意文字」查詢。我們已在前幾個段落的數個範例中使用過此語法。在這裡，我們提供更複雜的範例。

**語法**

*field:value*

*field=value*

**說明**

搜尋特定值的欄位。此值可以是字串常值、數字或日期/時間。

範例：


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**語法**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**說明**

提供比較。

範例：

	TimeGenerated>NOW+2HOURS


**語法**

*field:[from..to]*

**說明**

提供範圍面相化。

範例：

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### 邏輯運算子

查詢語言分別支援邏輯運算子 (AND、OR 和 NOT) 和它們的 C 樣式別名 (&&、|| 和 !)。您可以使用括號來分組這些運算子。

範例：

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
您可以略過最上層篩選引數的邏輯運算子。在此情況下，會假設採用 AND 運算子。


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>篩選運算式</th> <th>相當於</th> </tr> <tr> <td> <p>系統錯誤</p> </td> <td> <p>系統 AND 錯誤</p> </td> </tr> <tr> <td> <p>系統 &quot; Windows Server&quot; OR 嚴重性：1</p> </td> <td> <p>系統 AND (&quot; Windows Server &quot; OR 嚴重性：1)</p> </td> </tr> </table>



### 命令

命令會套用至查詢所傳回的結果。使用縱線字元 ( | ) 將命令套用至擷取的結果。多個命令必須以縱線字元分隔。

>[AZURE.NOTE]命令名稱可以大寫或小寫撰寫，與欄位名稱和資料不同。

#### 排序

語法：

	sort field1 asc|desc, field2 asc|desc, …

依特定欄位排序結果。asc/desc 前置詞是選擇性的。如果將其省略，則會假設採用 "asc" 排序順序。如果查詢不明確使用 **Sort** 命令，Sort **TimeGenerated** desc 為預設行為，且一律會先傳回最新的結果。

#### Top/Limit

語法：

	top number


	limit number
將回應限制為前 N 個結果。

範例：

	Type:Alert errors detected | top 10

傳回前 10 個比對結果。

#### Skip

語法：

	skip number

略過列出結果的數目。

範例：

	Type:Alert errors detected | top 10 | skip 200

從結果 200 開始傳回 10 個比對結果。

#### 選取

語法：

	select field1, field2, ...

將結果限制為您選擇的欄位。

範例：

	Type:Alert errors detected | select Name, Severity

將傳回的結果欄位限制為**名稱**和**嚴重性**。

#### Measure

**measure** 命令可用來將統計函數套用至未經處理的搜尋結果。對於透過資料取得*群組依據*而言，這是非常有用的。當您使用 **measure** 命令時，Operational Insights 會顯示內附彙總結果的資料表。

語法：

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

根據 **groupField** 彙總結果，並使用 **aggregatedField** 計算彙總的測量值。


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Measure 統計函數 </th> <th>描述 </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>彙總函數的名稱 (不區分大小寫)。支援下列彙總函式：</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>彙總的欄位。對於 COUNT 彙總函式而言，此欄位是選擇性的，但必須是 SUM、MAX、MIN、AVG 或 STDDEV 的現有數值欄位。</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>計算的彙總值 (選擇性) 別名。如果未指定，欄位名稱就是 <em>AggregatedValue。</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>結果集以其為分組根據的欄位名稱。</p> </td> </tr> <tr> <td> <p><em>間隔</em> </p> </td> <td> <p>時間間隔的格式：</p> <p><em>nnnNAME</em> </p> <p></p> <p>其中：</p> <p>nnn 是正整數</p> <p><em>NAME</em> 是間隔名稱</p> <p>受支援的間隔名稱包含 (區分大小寫)：</p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



間隔選項只用於日期/時間讓群組欄位中 (例如 **TimeGenerated** 和 **TimeCreated**)。目前，服務不會強制執行，但是沒有傳遞至後端之日期/時間的欄位會造成執行階段錯誤。實作結構描述驗證時，服務 API 會拒絕某些查詢，其使用的欄位沒有間隔彙總的日期/時間。目前的 **Measure** 實作只支援 **Count** 彙總函數的間隔分組。

如果省略 BY 子句，但指定間隔 (做為第二個語法)，根據預設會假設採用 **TimeGenerated** 欄位。

範例：

**範例 1**

	Type:Alert | measure count() as Count by ObjectId

*說明*

根據 **ObjectID** 分組警示，並計算每個群組的警示數目。彙總的值會傳回做為 **Count** 欄位 (別名)。

**範例 2**

	Type:Alert | measure count() interval 1HOUR

*說明*

使用 **TimeGenerated** 欄位，根據 1 小時間隔分組警示，並傳回每個間隔中的警示數目。

**範例 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*說明*

如同上述範例，但使用彙總欄位別名 (**AlertsPerHour**)。

**範例 4**

	* | measure count() by TimeCreated interval 5DAYS

*說明*

使用 **TimeCreated** 欄位，根據 5 天間隔分組結果，並傳回每個間隔中的結果數目。

**範例 5**

	Type:Alert | measure max(Severity) by WorkflowName

*說明*

跟據工作負載名稱分組警示，並傳回每個工作流程的最大警示嚴重性值。

**範例 6**

	Type:Alert | measure min(Severity) by WorkflowName

*說明*

如同上述範例，但使用 **Min** 彙總函數。

**範例 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*說明*

根據 ObjectId 分組 PerfHourly，並計算平均 (avg)。

**範例 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*說明*

如同上述範例，但使用 **Sum**。

**範例 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*說明*

如同上述範例，但使用 **STDDEV**。

**範例 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*說明*

取得最大數目警示的前五個工作流程。

#### Where

語法：

**where** AggregatedValue > 20

只用於 **Measure** 命令之後，以進一步篩選 **Measure** 彙總函數所產生的彙總結果。

範例：

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## 搜尋欄位和 Facet 參考 (英文)

當您使用 Search 來尋找資料時，結果會顯示各種欄位和 facet。不過，您會看到資訊的某些部分可能不太清楚。您可以使用下列資訊來協助了解結果。

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>欄位</b></th>
		<th><b>搜尋類型<b></th>
		<th><b>說明</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>全部</p>
		</td>
		<td>
		<p>用來分割資料</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>全部</p>
		</td>
		<td>
		<p>用來驅動時間軸，timeselectors (在搜尋和其他螢幕中)。它代表資料片段的產生 (通常在代理程式上) 時間。時間以 ISO 格式表示，且一律為 UTC。如果「類型」是以現有的檢測 (也就是記錄中的事件) 為基礎，這通常就是記錄項目/列/記錄所記錄的即時時間。對於某些其他透過管理組件或在雲端中產生的類型 - 也就是建議/警示/更新代理程式/等等，這是收集新的資料片段和某種組態之快照集的時間，或者根據它產生建議/警示的時間</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>Windows 事件記錄檔中的 EventID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>Windows 在其中記錄事件的事件記錄檔</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>重大 / 警告 / 資訊 / 成功</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>重大 / 警告 / 資訊 / 成功 的數值 (對於更容易/更適合閱讀的查詢，請改用 EventLevelName)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>全部</p>
		</td>
		<td>
		<p>資料來自何處 (根據服務的「附加」模式 - 也就是 Operations Manager，Operational Insights (= 雲端中產生的資料)，Azure 儲存體 (來自 WAD 的資料) 等等</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 效能物件名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 效能計數器執行個體名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 效能計數器名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>顯示在 Operations Manager 中效能集合規則以其為目標的物件名稱，或由 Operational Insights 探索的物件名稱，或針對其產生警示的物件名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>顯示在 Operations Manager 中效能集合規則以其為目標之物件父系的父系名稱 (在雙主控關聯性中：也就是由 Windows 電腦所主控之 SqlInstance 所主控的 SqlDatabase)，或由 Operational Insights 探索的物件名稱，或針對其產生警示的物件名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>電腦</p>
		</td>
		<td>
		<p>大部分的類型 </p>
		</td>
		<td>
		<p>資料所屬的電腦名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>資料所屬的電腦名稱 (如同 'Computer')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>威脅狀態排名是威脅狀態的數值表示法，類似於 HTTP 回應碼，我們在數字之間留了間距 (這就是為什麼沒有威脅是 150，而不是 100 或 0)，讓我們有一些空間可以加入新的狀態。當我們執行威脅狀態和防護狀態的彙總套件時，我們想要顯示電腦在選取時間間隔中所經歷的最差狀態。我們可以使用數字來排名不同的狀態，讓我們查看最高數字的記錄。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>ThreatStatus 的描述，與 ThreatStatusRank 1:1 對應。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>在電腦中偵測到的反惡意程式碼產品：無、Microsoft 惡意程式碼移除工具、Forefront 等等</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus、RequiredUpdate</p>
		</td>
		<td>
		<p>這台電腦之代理程式的 HealthService 識別碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>大部分的類型 </p>
		</td>
		<td>
		<p>這台電腦之代理程式的 HealthService 識別碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>大部分的類型 </p>
		</td>
		<td>
		<p>Operations Manager 附加代理程式的管理群組名稱；否則它將會是 null/空白</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>由 Operational Insights 組態評估所探索的物件類型 (例如 Operations Manager 管理組件的 「類型」/類別)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>找到未安裝的更新名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新何時在 Microsoft Update 上發行？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>伺服器</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>資料所屬的電腦名稱 (如同 'Computer')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>產品</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新適用的產品</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>更新類型 (更新彙總套件、Service Pack 等等)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>描述此最佳作法或更新的 KB 文章識別碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>產生警示之規則或監視器的名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>嚴重性</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>警示的嚴重性</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>優先順序</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>警示的優先順序</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>此警示由監視器 (true) 或規則 (false) 所產生？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>使用 Operational Insights 警示之參數的 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>使用 Operational Insights 警示之「內容」的 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>工作負載</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>警示所參考的技術或「工作負載」 </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>建議</p>
		</td>
		<td>
		<p>建議所參考的技術或「工作負載」</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>說明</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>警示描述 (簡短)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>此代理程式在幾天前 (相對於記錄的 'TimeGenerated') 從 WSUS/Microsoft Update 安裝了任何更新？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>根據 DaysSinceLastUpdate，多久以前的「時間值區」分類是上一次從 WSUS/Microsoft Update 安裝任何更新的電腦</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>自動更新檢查是在此代理程式上啟用或停用的嗎？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>自動更新檢查會設為自動下載和安裝、只下載，或只檢查嗎？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Microsoft Update 代理程式的版本號碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>此更新代理程式的目標是哪個 WSUS 伺服器？</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>此更新代理程式在其中執行的作業系統版本</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>名稱</p>
		</td>
		<td>
		<p>建議，ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>來自 Operational Insights 組態評估的建議名稱/標題或屬性名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>值</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>來自 Operational Insights 組態評估的屬性值</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>建議</p>
		</td>
		<td>
		<p>描述此最佳作法或更新的 KB 文章 URL</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>建議</p>
		</td>
		<td>
		<p>建議為一些類型，其記錄會「更新」，而不只是加入至搜尋索引。此狀態會變更建議是否為作用中/開啟，或者 Operational Insights 是否會偵測到狀態已解決</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>Windows 事件的呈現描述 (具有填入參數的重複使用文字)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>使用 Windows 事件「資料」區段中之參數的 XML (如同在事件檢視器中所見)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>使用 Windows 事件之完整「資料」區段的 XML (如同在事件檢視器中所見)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>來源</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>產生事件的事件記錄檔來源</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>事件的類別，直接來自 Windows 事件記錄檔</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>事件</p>
		</td>
		<td>
		<p>Windows 事件的使用者名稱 (通常是 NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>效能計數器每小時彙總的平均值 </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>效能計數器每小時彙總的每小時間隔內最小值</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>效能計數器每小時彙總的每小時間隔內最大值</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>效能計數器每小時彙總的每小時間隔內第 95 個百分位數</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>多少「未經處理的」效能計數器範例用來產生此每小時彙總記錄</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Threat</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>找到的惡意程式碼名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>從其中讀取記錄的 Azure 儲存體帳戶</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>記錄所屬的雲端服務之 Azure 部署識別碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>角色</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>記錄所屬的 Azure 雲端服務之角色</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>記錄所屬的 Azure 角色之 RoleInstance</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>記錄所屬的 IIS 網站 (metabase 標記法)；在原始記錄中的 s-sitename 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>在原始記錄中的 s-computername 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求被定址的目標伺服器 IP 位址。在原始記錄中的 s-ip 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>用戶端在 HTTP 要求中使用的 HTTP 方法 (GET/POST/等等)。在原始記錄中的 cs-method</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 要求的來源用戶端 IP 位址。在原始記錄中的 c-ip 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>由用戶端宣告的 HTTP 使用者代理程式 (瀏覽器或其他方式)。在原始記錄中的 cs-user-agent</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>由伺服器傳回給用戶端的 HTTP 狀態碼 (200/403/500/等等)。在原始記錄中的 cs-status</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>完成要求所花費的時間 (以毫秒為單位)。在原始記錄中的 timetaken 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求的相對 Uri (沒有主機位址，也就是 '/search' ) 的要求。在原始記錄中的 cs-uristem 欄位</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>URI 查詢。只有 ASP 頁面等動態頁面才需要 URI 查詢，所以此欄位通常包含靜態網頁的連字號。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>傳送 HTTP 要求的目標伺服器連接埠 (也是 IIS 接聽的目標，因為它會挑選它)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>已驗證的使用者名稱，如果要求已通過驗證且不匿名</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>使用於要求中的 HTTP 通訊協定版本 (也就是'HTTP/1.1')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Cookie 資訊</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>使用者上次造訪的網站。這個網站提供目前網站的連結。</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>要求的主機標頭 (也就是 'www.mysite.com')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>子狀態錯誤碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Windows 狀態碼</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>在要求中從用戶端傳送到伺服器的位元組</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>在回應中從伺服器傳回給用戶端的位元組</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>變更的類型 (WindowsServices / Software / 等等)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>變更的類別 (已修改 / 已新增 / 已移除)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>軟體的類型 (更新 / 應用程式)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>軟體的名稱 (僅適用於軟體變更)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>發行者 </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>發行軟體的供應商 (僅適用於軟體變更)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>已套用在 Windows 服務的變更類型 (State / StartupType / Path / ServiceAccount) - 僅適用於 Windows 服務變更</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>顯示已變更的服務名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>已變更的服務名稱</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>要求的新 (目前) 狀態</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>服務的上一個已知狀態 (僅適用於服務狀態變更時)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>服務啟動類型</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>上一個服務啟動類型 (僅適用於服務啟動類型變更時)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>服務帳戶</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>先前的服務帳戶 (僅適用於服務帳戶變更時)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows 服務的可執行檔路徑</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows 服務先前的可執行檔路徑 (僅適用於其變更時)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>服務的描述</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>上一步 </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>此軟體先前的狀態 (已安裝 / 未安裝 / 上一個版本)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>此軟體最新的狀態 (已安裝 / 未安裝 / 上一個版本)</p>
		</td>
	</tr>
</table>

## 部落格文章 - 搜尋使用案例
- [Microsoft Azure Operational Insights 中的 W3C IIS 記錄檔搜尋](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [使用 Azure Operational Insights 搜尋和儀表板監視 SQL 備份失敗](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [IIS 管理套件事件警示規則的 OpInsights 搜尋對等項目](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [有用的 Operational Insights 搜尋查詢集合](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## 其他資源
Stefan Roth 建立好用的搜尋小祕技。查看他的[部落格](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/)以深入了解並下載他的小祕技。

<!---HONumber=July15_HO2-->