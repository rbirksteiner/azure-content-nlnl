<properties 
	pageTitle="儲存體分析" 
	description="如何管理 Blob、佇列、資料表和檔案服務的並行存取" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>

# 啟用儲存體度量和檢視度量資料

根據預設，儲存體服務未啟用 [儲存體度量]。您可以使用 Azure 管理入口網站、Windows PowerShell，或以程式設計方式透過儲存體 API，來啟用監視。

當您啟用儲存體度量時，必須選擇資料的保留期限：這段期間會決定儲存體服務用來保存度量的時間長度，以及該空間儲存它們所需的費用。一般而言，您應該針對每分鐘度量使用比每小時度量還短的保留期限，因為每分鐘度量明顯需要額外的空間。您應該選擇保留期限，讓您擁有足夠的時間來分析資料，並下載任何您想要保留以供離線分析或報告使用的度量。請記住，您還是必須支付從儲存體帳戶下載度量資料的費用。

## 如何使用 Azure 管理入口網站啟用儲存體度量

在 Azure 管理入口網站中，您可以使用儲存體帳戶的 [設定] 頁面來控制儲存體度量。如需監視，您可以針對每個 Blob、資料表和佇列設定層級和保留期限 (以天為單位)。在各個案例中，層級會是下列其中一項：


- 關閉 - 這表示不會收集任何度量。

- 最小 - 儲存體度量會收集一組針對 Blob、資料表與佇列服務彙總的基本度量 (例如，輸入流量/輸出流量、可用性、延遲和成功百分比)。

- 詳細資訊 - 除了服務層級度量之外，儲存體度量會收集一組完整的度量，其中包含每個儲存體 API 作業的相同度量。詳細資訊度量可供進一步分析在應用程式運作期間發生的問題。

請注意，管理入口網站目前無法讓您在儲存體帳戶中設定每分鐘度量；您必須使用 PowerShell 或以程式設計方式來啟用每分鐘度量。


## 如何使用 PowerShell 啟用儲存體度量

您可以在本機電腦上使用 PowerShell 來設定儲存體帳戶中的儲存體度量，做法是使用 Azure PowerShell Cmdlet Get-AzureStorageServiceMetricsProperty 來擷取目前的設定，並使用 Cmdlet Set-AzureStorageServiceMetricsProperty 來變更目前的設定。

控制儲存體度量的 Cmdlet 會使用下列參數：

- MetricsType 的可能值為 Hour 和 Minute。

- ServiceType 的可能值為 Blob、Queue 及 Table。

- MetricsLevel 的可能值為 None (相當於管理入口網站中的「關閉」)、Service (相當於管理入口網站中的「最小」)，以及 ServiceAndApi (相當於管理入口網站中的「詳細資訊」)。

例如，下列命令會在您的預設儲存體帳戶中，為 Blob 服務開啟每分鐘度量，並將保留期限設為五天：

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

## 如何以程式設計方式啟用儲存體度量

除了使用 Azure 管理入口網站或 Azure PowerShell Cmdlet 來控制儲存體度量，您也可以使用其中一個 Azure 儲存體 API。例如，如果您使用的是 .NET 語言，就可以使用儲存體用戶端程式庫。

類別 CloudBlobClient、CloudQueueClient 及 CloudTableClient 全都具備像是 SetServiceProperties 和 SetServicePropertiesAsync 的方法，可取得 ServiceProperties 物件做為參數。您可以使用 ServiceProperties 物件來設定儲存體度量。例如，下列 C# 程式碼片段示範如何變更每小時佇列度量的度量層級和保留天數：

    var storageAccount = CloudStorageAccount.Parse(connStr);
    var queueClient = storageAccount.CreateCloudQueueClient();
    var serviceProperties = queueClient.GetServiceProperties();
     
    serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;
    serviceProperties.HourMetrics.RetentionDays = 10;
     
    queueClient.SetServiceProperties(serviceProperties);
    
## 檢視儲存體度量

設定要監視儲存體帳戶的儲存體度量後，它會在您儲存體帳戶的一組已知資料表中記錄度量。您可以在管理入口網站中使用您儲存體帳戶的 [監視] 頁面，當它們出現在圖表上時，檢視每小時度量。在管理入口網站的這個頁面上，您可以：

- 選取要在圖表上繪製哪些度量 (可用的度量選項將根據您在 [設定] 頁面上，為服務選擇的是詳細資訊或最小監視而定)。


- 為圖表上顯示的度量選取時間範圍。


- 選擇使用絕對或相對比例來繪製度量。


- 設定電子郵件警示，在特定的度量達到特定值時通知您。


如果要下載長期儲存體的度量，或在本機加以分析，您必須使用工具或撰寫程式碼來讀取資料表。您必須下載每分鐘度量以進行分析。如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。有許多協力廠商儲存體瀏覽工具可以感知這些資料表，讓您能夠直接檢視它們 (如需可用工具清單，請參閱部落格文章 [Microsoft Azure 儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx))。

### 每小時度量
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### 每分鐘度量
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### 容量
- $MetricsCapacityBlob

您可以在[儲存體分析度量資料表結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)上找到這些資料表之結構描述的完整詳細資料。下列資料列範例只會顯示可用的資料行子集，但可說明儲存體度量儲存這些度量資訊之方式的一些重要功能：

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Availability | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104.4286 | 6.857143 | 100 |
| 20140522T1100 | user;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143.8 | 7.8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

在這個每分鐘度量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。資料列索引鍵會識別資料列中儲存的資訊類型，而這是由兩部分的資訊 (存取類型及要求類型) 所組成：

- 存取類型是使用者或系統，其中使用者是指對儲存體服務所提出的所有使用者要求，而系統是指對儲存體分析所提出的要求。

- 要求類型就是全部 (在此情況下它是摘要行)，或者它會識別特定的 API，例如 QueryEntity 或 UpdateEntity。


上述範例資料會為單一分鐘 (從上午 11:00 開始) 顯示所有記錄，因此 QueryEntities 要求的數目加上 QueryEntity 要求的數目再加上 UpdateEntity 要求的數目最多七個，也就是 user:All 資料列上顯示的總數。同樣地，您可以藉由計算 ((143.8 * 5) + 3 + 9)/7，在 user:All 資料列上衍生平均的端對端延遲 104.4286。

您應該考慮在管理入口網站的 [監視] 頁面上設定警示，讓儲存體度量可自動通知您儲存體服務行為的任何重要變更。如果您使用儲存體總管工具來下載這個度量資料 (以分隔的格式)，就可以使用 Microsoft Excel 來分析資料。如需可用儲存體總管工具的清單，請參閱部落格文章 [Microsoft Azure 儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)。



## 以程式設計方式存取度量資料

下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘度量，並將結果顯示在主控台視窗中。它會使用 Azure 儲存體程式庫第 4 版，其中包含可簡化存取儲存體中之度量資料表的 CloudAnalyticsClient 類別。

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## 當您啟用儲存體度量時，會產生哪些費用？

寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。

用戶端對於度量資料的讀取和刪除要求也會以標準費率來計費。如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的度量資料時付費。不過，如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。

度量資料表所使用的容量也會列入計費：您可以使用下列項目來估計儲存計量資料所使用的容量大小：

- 如果服務每小時會使用每個服務中的每一種 API，若您已啟用服務和 API 層級摘要，則每小時大約有 148 KB 的資料將儲存於度量交易資料表中。

- 如果服務每小時會使用每個服務中的每一種 API，若您只啟用服務層級摘要，則每小時大約有 12 KB 的資料將儲存於度量交易資料表中。

- 適用於 Blob 的容量資料表每天都會新增兩個資料列 (前提是使用者已選擇記錄檔)：也就是說，這個資料表的大小每天最多大約會增加 300 個位元組。

## 後續步驟：
[啟用儲存體記錄和存取記錄檔資料](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=July15_HO1-->