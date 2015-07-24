#####建立佇列
**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本主題的所有程式碼都使用 Azure 應用程式服務設定中所儲存的儲存體連接字串。還有其他方式可以建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") 文件。

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

使用 **queueClient** 物件來取得想要使用佇列的參照。此程式碼會嘗試參考名為 “myqueue” 的佇列。 如果找不到該名稱的佇列，則會建立此佇列。

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	queue.CreateIfNotExists();

**注意：**請在下列區段的程式碼開頭使用此程式碼區塊。

#####將訊息插入佇列
若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage** 物件。接著，呼叫 AddMessage() 方法。您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 **CloudQueueMessage** 物件。以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####查看下一個訊息
透過呼叫 PeekMessage() 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####移除下一個訊息
您的程式碼可以使用兩個步驟將訊息從佇列中移除 (清除佇列)。


1. 呼叫 GetMessage() 取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 GetMessage() 傳回的訊息。依預設，此訊息會維持 30 秒的不可見狀態。 
2.	若要完成從佇列中移除訊息，請呼叫 DeleteMessage()。 

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。下列程式碼會在處理完訊息之後立即呼叫 DeleteMessage()。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[深入了解 Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)，另請參閱[在伺服器總管中瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)。

<!---HONumber=62-->