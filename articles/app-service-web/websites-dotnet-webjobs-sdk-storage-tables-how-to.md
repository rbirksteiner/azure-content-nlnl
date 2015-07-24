<properties 
	pageTitle="如何透過 WebJobs SDK 使用 Azure 資料表儲存體" 
	description="了解如何透過 WebJobs SDK 使用 Azure 資料表儲存體。建立資料表、將實體新增至資料表以及讀取現有資料表。" 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# 如何透過 WebJobs SDK 使用 Azure 資料表儲存體

## 概觀

本指南提供 C# 程式碼範例，示範如何使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 1.x 版讀取和寫入 Azure 儲存體資料表。

本指南假設您知道[如何使用指向您儲存體帳戶的連接字串，在 Visual Studio 中建立 WebJob 專案](websites-dotnet-webjobs-sdk-get-started.md)。
		
有一些程式碼片段顯示 `Table` 屬性用於以[手動方式呼叫](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)的函式中，也就是說使用的並非觸發屬性。

## <a id="ingress"></a> 如何將實體新增至資料表

若要將實體新增至資料表，請將 `Table` 屬性搭配 `ICollector<T>` 或 `IAsyncCollector<T>` 參數使用，其中 `T` 會指定您想要加入之實體的結構描述。屬性建構函式採用字串參數，來指定資料表的名稱。

以下程式碼範例會將 `Person` 實體加入名為 *Ingress* 的資料表。

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

您和 `ICollector` 一起使用的型別通常衍生自 `TableEntity` 或實作 `ITableEntity`，但並非必要。下列任一個 `Person` 類別都能搭配前面 `Ingress` 方法中所示的程式碼使用。

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

如果您想要直接使用 Azure 儲存體 API，您可以將 `CloudStorageAccount` 參數新增至方法簽章。。

## <a id="monitor"></a> 即時監視

因為資料外送流量函式經常處理大量資料，所以 WebJobs SDK 儀表板提供即時監視資料。[引動過程記錄] 區段可告訴您是否仍有執行中的函式。

![輸入函式執行中](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

[引動過程詳細資料] 頁面會報告執行中函式的進度 (寫入的實體數目)，並讓您有機會將它中止。

![輸入函式執行中](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

當函式完成時，[引動過程詳細資料] 頁面會報告寫入的資料列數目。

![輸入函式已完成](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> 如何讀取資料表中的多個實體

若要讀取資料表，請使用 `Table` 屬性搭配 `T` 型別衍生自 `TableEntity` 的 `IQueryable<T>` 參數或實作 `ITableEntity`。

下列程式碼範例會讀取並記錄 `Ingress` 資料表中的所有資料列：
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> 如何讀取資料表中的單一實體

`Table` 屬性建構函式搭配兩個額外參數，可在想要繫結到單一資料表實體時，讓您指定資料分割索引鍵與資料列索引鍵。

下列程式碼範例會根據在佇列訊息中收到的資料分割索引鍵與資料列索引鍵值，來讀取 `Person` 實體的資料表列：

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


本範例中的 `Person` 類別不必實作 `ITableEntity`。

## <a id="storageapi"></a> 如何直接利用 .NET 儲存體 API 來使用資料表

您也可以使用 `Table` 屬性搭配 `CloudTable` 物件，以更有彈性的方式使用資料表。

下列程式碼範例使用 `CloudTable` 物件將單一實體新增至 *Ingress* 資料表。
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

如需如何使用 `CloudTable` 物件的詳細資訊，請參閱[如何從 .NET 使用資料表儲存體](../storage-dotnet-how-to-use-tables.md)。

## <a id="queues"></a>佇列操作說明文章所涵蓋的相關主題

如需如何處理佇列訊息所觸發的資料表處理的相關資訊，或是非資料表處理特有的 WebJobs SDK 案例，請參閱[如何透過 WebJobs SDK 使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。

該文章涵蓋下列主題：

* Async 函數
* 多個執行個體
* 正常關機
* 在函式主體中使用 WebJobs SDK 屬性
* 在程式碼中設定 SDK 連接字串
* 在程式碼中設定 WebJobs SDK 建構函式參數的值
* 手動觸發函式
* 寫入記錄檔

## <a id="nextsteps"></a> 後續步驟

本指南提供的程式碼範例示範如何處理使用 Azure 資料表的常見案例。如需 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
 

<!---HONumber=62-->