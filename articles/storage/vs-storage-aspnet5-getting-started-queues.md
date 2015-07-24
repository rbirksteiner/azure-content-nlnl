<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="如何開始在 Visual Studio 的 ASP.NET 5 專案中使用 Azure 佇列儲存體" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# 開始使用 Azure 儲存體 (ASP.NET 5 專案)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。如需詳細資訊，請參閱[如何從 .NET 使用佇列儲存體](storage-dotnet-how-to-use-queues.md/ "如何使用 .NET 的佇列儲存體") (英文)。

若要以程式設計方式存取 ASP.NET 5 專案中的佇列，您需要加入下列項目 (如果尚不存在)。

1. 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 使用下列程式碼來取得組態設定。

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####取得儲存體連接字串
在您可以使用佇列執行任何動作之前，必須取得佇列將留存之儲存體帳戶的連接字串。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 ASP.NET 5 專案，您可以呼叫 Configuration 物件的 get 方法，從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

**注意：**對外向 ASP.NET 5 中的 Azure 儲存體執行呼叫的 API 是非同步的。如需詳細資訊，請參閱[使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/library/hh191443.aspx)。以下程式碼假設使用非同步程式設計方法。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####建立佇列
**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本主題的所有程式碼都使用 Azure 應用程式服務設定中所儲存的儲存體連接字串。還有其他方式可以建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") 文件。

**注意：**對外向 ASP.NET 5 中的 Azure 儲存體執行呼叫的 API 是非同步的。如需詳細資訊，請參閱[使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/library/hh191443.aspx)。以下程式碼假設使用非同步程式設計方法。

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

使用 **queueClient** 物件來取得想要使用佇列的參照。此程式碼會嘗試參考名為 “myqueue” 的佇列。 如果找不到該名稱的佇列，則會建立此佇列。

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**注意：**請將這整段程式碼用於後續小節的程式碼之前。

#####將訊息插入佇列
若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage** 物件。接著，呼叫 AddMessageAsync() 方法。您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 **CloudQueueMessage** 物件。以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####查看下一個訊息
透過呼叫 PeekMessageAsync() 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####移除下一個訊息
您的程式碼可以使用兩個步驟將訊息從佇列中移除 (清除佇列)。


1. 呼叫 GetMessageAsync() 以取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 GetMessageAsync() 傳回的訊息。依預設，此訊息會維持 30 秒的不可見狀態。 
2.	若要完成從佇列中移除訊息，請呼叫 DeleteMessageAsync()。 

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。下列程式碼會在處理完訊息之後立即呼叫 DeleteMessageAsync()。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[深入了解 Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)另請參閱[在伺服器總管中瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)和 [ASP.NET 5](http://www.asp.net/vnext)。
 

<!---HONumber=July15_HO2-->