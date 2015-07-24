<properties 
	pageTitle="從 Application Insights 的遙測連續匯出" 
	description="匯出診斷和使用量資料至 Microsoft Azure 中的儲存體，並從那裡下載。" 
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
 
# 從 Application Insights 匯出遙測

想要對您的遙測執行一些自訂的分析？ 或也許您想要對具有特定屬性的事件以電子郵件寄送警示？ 連續匯出很適合此用途。在 Application Insights 入口網站中看見的事件，可以使用 JSON 格式匯出到 Microsoft Azure 中的儲存體。從那裡，您可以下載資料並編寫處理所需的任何程式碼。

在免費試用期間，或是使用[標準及高階定價方案](http://azure.microsoft.com/pricing/details/application-insights/)時，都可使用「連續匯出」功能。

(如果您只想要[一次性匯出](app-insights-metrics-explorer.md#export-to-excel)您在度量或搜尋刀鋒視窗上看到的內容，請按一下分頁頂端的 [匯出]。)

## <a name="setup"> </a> 設定連續匯出

在 Application Insights 入口網站中應用程式的 [概觀] 刀鋒視窗上，開啟 [連續匯出]：

![向下捲動並按一下 [連續匯出]](./media/app-insights-export-telemetry/01-export.png)

加入匯出，並選取您要放置資料的 [Azure 儲存體帳戶](../storage-introduction.md)：

![按一下 [加入]、[匯出目的地]、[儲存體帳戶]，然後建立新儲存區或選擇現有儲存區](./media/app-insights-export-telemetry/02-add.png)

選擇您想要匯出的事件類型：

![按一下 [選擇事件類型]](./media/app-insights-export-telemetry/03-types.png)


建立匯出之後，就會開始進行。(您只會取得建立匯出之後送抵的資料。)


如果稍後想要變更事件類型，只需要編輯匯出：

![按一下 [選擇事件類型]](./media/app-insights-export-telemetry/05-edit.png)

若要停止資料流，請按一下 [停用]。再次按一下 [啟用] 時，資料流將會以新資料重新啟動。您無法取得在停用匯出時送抵入口網站的資料。

若要永久停止資料流，請刪除匯出。這麼做不會將您的資料從儲存體刪除。

#### 無法加入或變更匯出？

* 若要加入或變更匯出，您需要擁有者、參與者或 Application Insights 參與者存取權。[了解角色][roles]。

## <a name="analyze"></a> 您取得什麼事件？

匯出的資料是我們從您的應用程式中收到的原始遙測，除了：

* 目前未包含 Web 測試結果。 
* 我們加入透過用戶端 IP 位址計算的位置資料。  

未包含計算的度量。例如，我們不會匯出平均 CPU 使用率，但我們會匯出用以計算平均的原始遙測。

## <a name="get"></a> 檢查資料

使用[伺服器總管](http://msdn.microsoft.com/library/azure/ff683677.aspx)之類的工具開啟 Blob 儲存區時，您會看到具有一組 Blob 檔案的容器。每個檔案的 URI 為 application-id/telemetry-type/date/time。

![使用適合的工具檢查 Blob 儲存區](./media/app-insights-export-telemetry/04-data.png)

日期和時間為 UTC，並且是遙測存放在儲存區的時間 - 而不是產生的時間。因此，如果您編寫程式碼來下載資料，它可以透過資料線性地移動。



## <a name="format"></a> 資料格式

* 每個 Blob 是包含多個以 '\n' 分隔的列的文字檔案。
* 每列是未格式化的 JSON 文件。如果您想要坐一旁並盯著它，請嘗試使用 Notepad++ 之類的檢視器搭配 JSON 外掛程式：

![使用合適的工具檢視遙測](./media/app-insights-export-telemetry/06-json.png)

時間期間依刻度為單位，10000 刻度 = 1 毫秒。例如，這些值顯示從瀏覽器傳送要求用了 10 毫秒的時間，接收它用了 30 毫秒，以及在瀏覽器中處理頁面用了 1.8 秒：

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## 處理資料

就小型規模而言，您可以編寫一些程式碼來取出您的資料，將它讀取為試算表等等。例如：

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

如需較大型的程式碼範例，請參閱[使用背景工作角色][exportasa]。

#### 匯出至 SQL

另一種選擇是將資料移動至 SQL Database，您可以在其中執行更強大的分析功能。

以下範例說明，要將 Blob 儲存體中的資料移至資料庫，有兩種替代方法可以選擇：

* [使用背景工作角色匯出至 SQL][exportcode]
* [使用串流分析匯出至 SQL][exportasa]


就更大型規模而言，請考慮 [HDInsight](http://azure.microsoft.com/services/hdinsight/) - 雲端中的 Hadoop 叢集。HDInsight 提供各種技術，用於管理和分析巨量資料。

## <a name="delete"></a>刪除舊資料
請注意，您負責管理儲存容量，以及在必要時刪除舊資料。

## 如果您重新產生儲存體金鑰...

如果您變更儲存體的金鑰，連續匯出將停止運作。您將在 Azure 帳戶中看到通知。

開啟 [連續匯出] 分頁，並編輯您的匯出。編輯 [匯出目的地]，但只保留選取相同的儲存體。按一下 [確定] 以確認。

![編輯連續匯出，開啟並關閉匯出目的地。](./media/app-insights-export-telemetry/07-resetstore.png)

連續匯出將重新開始。


## 問答集

* *但我想要的只是一次性下載圖表。*  
 
    是的，您可以這麼做。在刀鋒視窗頂端，按一下 [[匯出資料](app-insights-metrics-explorer.md#export-to-excel)]。

* *我設定匯出，但我的儲存區中沒有資料。*

    自從設定匯出之後，Application Insights 是否從您的應用程式收到任何遙測？ 您將只會收到新資料。

* *我嘗試設定匯出，但被拒絕存取*

    如果帳戶是組織所擁有，您必須是擁有者或參與者群組的成員。

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *我是否能直接匯出到我自己的內部部署儲存區？*

    否，抱歉。我們的匯出引擎目前僅適用於 Azure 儲存體。

* *放置在我的儲存區中的資料量有任何限制？*

    沒有。我們將持續送入資料，直到刪除匯出為止。如果我們到達 Blob 儲存體的外部限制，將會停止，但那個限制很大。您可以自行控制使用的儲存體數量。

* *我對我的儲存體重新產生了金鑰，或變更了容器的名稱，現在匯出沒有作用。*

    編輯匯出並開啟匯出目的地分頁。照舊保留選取相關的儲存體，並按一下 [確定] 來確認。匯出將重新開始。如果變更是在最近幾天內，您不會遺失資料。

* *我可以暫停匯出嗎？*

    是。按一下 [停用]。


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=62-->