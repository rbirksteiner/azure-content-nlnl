<properties 
	pageTitle="如何使用 Java 的佇列儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 Java 撰寫。" 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# 如何使用 Java 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java][]。所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立」和「刪除」佇列。如需佇列的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

注意：已發行一套 SDK 可供在 Android 裝置上使用 Azure 儲存體的開發人員使用。如需詳細資訊，請參閱 [Azure Storage SDK for Android][]。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Java 應用程式

在本指南中，您將使用儲存體功能，這些功能可執行於本機的 Java 應用程式內，也可執行於在 Azure 中之 Web 角色或背景工作角色內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。完成此動作之後，您需要驗證開發系統符合 GitHub 上的 [Azure Storage SDK for Java][] 儲存機制中所列出的最低需求和相依性。如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。完成這些工作之後，您就能夠利用本文中的範例來建立 Java 應用程式。

## 設定您的應用程式以存取佇列儲存體

將下列 import 陳述式新增到您要在其中使用 Azure 儲存體 API 存取佇列的 Java 檔案頂端：

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## 設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串 (其中的 *AccountName* 和 *AccountKey* 值要使用您儲存體帳戶的名稱，以及在管理入口網站中針對該儲存體帳戶而列出的主要存取金鑰)。本範例將示範如何宣告靜態欄位來存放連接字串：

    // Define the connection-string with your values.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

在 Microsoft Azure 的角色內執行的應用程式中，此字串可以儲存在服務組態檔 *ServiceConfiguration.cscfg* 裡，且可以藉由呼叫 **RoleEnvironment.getConfigurationSettings** 方法來存取。以下是從服務組態檔中名為 **StorageConnectionString** 的 *Setting* 元素取得連接字串的範例：

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## 作法：建立佇列

**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。(備註：還有其他方法可建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考]中的 **CloudStorageAccount**。)

使用 **CloudQueueClient** 物件來取得想要使用佇列的參照。如果佇列不存在，您可以建立佇列。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the queue client.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Retrieve a reference to a queue.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Create the queue if it doesn't already exist.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：將訊息新增至佇列

若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage**。接著，呼叫 **addMessage** 方法。您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 **CloudQueueMessage**。以下是建立佇列 (如果佇列不存在) 並插入訊息 "Hello, World" 的程式碼。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Create the queue if it doesn't already exist.
    	queue.createIfNotExists();

    	// Create a message and add it to the queue.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：查看下一個訊息

透過呼叫 **peekMessage**，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");
			
    	// Peek at the next message.
    	CloudQueueMessage peekedMessage = queue.peekMessage();
			
    	// Output the message value.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 *n* 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

下列程式碼範例會在訊息佇列中搜尋，找出內容符合 "Hello, World" 的第一個訊息，然後修改訊息內容並結束。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32. 
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields = 
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

另外，下列程式碼範例只更新佇列上第一個可見的訊息。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage message = queue.retrieveMessage();
			
    	if (message != null)
    	{
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields = 
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：取得佇列長度

您可以取得佇列中的估計訊息數目。**downloadAttributes** 方法會向佇列服務要求數個目前值，包括計算佇列中的訊息數。由於佇列服務在回應您的要求之後可以新增或移除訊息，此計數僅為近似值。**getApproximateMessageCount** 方法會傳回呼叫 **downloadAttributes** 所擷取的最後一個值，而無需呼叫佇列服務。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Download the approximate message count from the server.
    	queue.downloadAttributes();

    	// Retrieve the newly cached approximate message count.
    	long cachedMessageCount = queue.getApproximateMessageCount();
			
    	// Display the queue length.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：清除下一個佇列訊息

您的程式碼可以使用兩個步驟來清除佇列訊息。呼叫 **retrieveMessage** 時，您會取得佇列中的下一個訊息。從 **retrieveMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。依預設，此訊息會維持 30 秒的不可見狀態。若要完成將訊息從佇列中移除，您還必須呼叫 **deleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **deleteMessage**。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();
			
    	if (retrievedMessage != null)
    	{
    		// Process the message in less than 30 seconds, and then delete the message.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## 清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例使用 **retrieveMessages** 方法，在一次呼叫中取得 20 個訊息。接著它會使用 **for** 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。請注意，系統會針對所有訊息同時開始計時五分鐘，所以從呼叫 **retrieveMessages** 開始的五分鐘後，任何尚未刪除的訊息都會重新出現。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes, 
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：列出佇列

若要取得目前佇列的清單，請呼叫 **CloudQueueClient.listQueues()** 方法，此方法會傳回 **CloudQueue** 物件的集合。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請在 **CloudQueue** 物件上呼叫 **deleteIfExists** 方法。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- [Azure Storage SDK for Java]
- [Azure 儲存體用戶端 SDK 參考]
- [Azure 儲存體 REST API]
- [Azure 儲存體團隊部落格]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure 儲存體用戶端 SDK 參考]: http://dl.windowsazure.com/storage/javadoc/
[Azure 儲存體 REST API]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=July15_HO2-->