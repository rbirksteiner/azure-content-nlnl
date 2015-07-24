<properties 
	pageTitle="如何使用 Node.js 中的佇列儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 Node.js 撰寫。" 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>


# 如何使用 Node.js 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南示範如何使用 Microsoft Azure 佇列服務執行常見案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Node.js 應用程式

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或[使用 WebMatrix 的網站]。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要 Azure Storage SDK for Node.js，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、[終端機] (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure-storage**，該命令應能產生以下輸出：

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  您可以手動執行 **ls** 命令，確認已建立 **node_modules** 資料夾。該資料夾中有 **azure-storage** 封裝，當中包含存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure-storage');

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 AZURE_STORAGE_ACCOUNT 及 AZURE_STORAGE_ACCESS_KEY 或 AZURE_STORAGE_CONNECTION_STRING，以取得連接 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則在呼叫 **createQueueService** 時必須指定帳戶資訊。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]。

## 作法：建立佇列

下列程式碼會建立一個 **QueueService** 物件，其讓您能夠使用佇列。

    var queueSvc = azure.createQueueService();

請使用 **createQueueIfNotExists** 方法，此方法會傳回指定的佇列 (如果佇列已經存在)，或以指定的名稱建立新佇列 (如果佇列不存在)。

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

如果建立佇列， `result` 為 true。如果佇列已存在，則 `result` 為 false。

### 篩選器

可以將選用性的篩選操作套用到使用 **QueueService** 執行的操作。篩選作業可包含記錄、自動重試等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **QueueService** 物件：

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## 作法：將訊息插入佇列中

若要將訊息插入佇列，請使用 **createMessage** 方法建立新訊息，然後將該訊息加到佇列中。

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## 作法：預覽下一個訊息

透過呼叫 **peekMessages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。**peekMessages** 預設會查看單一訊息。

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

`result` 包含訊息。

> [AZURE.NOTE]當佇列中沒有任何訊息時，使用 **peekMessages** 並不會傳回錯誤，不過也不會傳回任何訊息。

## 作法：清除下一個佇列訊息

處理訊息是兩階段的過程：

1. 從佇列中清除訊息。

2. 刪除訊息。

若要從佇列中清除訊息，請使用 **getMessage**。這樣會使訊息從佇列中隱藏起來，而不讓其他用戶端處理它。當應用程式處理訊息之後，請呼叫 **deleteMessage** 從佇列中刪除它。下列範例會取得訊息，接著刪除訊息。

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE]依預設，訊息只會隱藏 30 秒，之後又會被其他用戶端看見。您可以使用具有 **getMessages** 的 `options.visibilityTimeout` 指定其他值。

> [AZURE.NOTE]當佇列中沒有任何訊息時，使用 <b> getMessages </b> 並不會傳回錯誤，不過也不會傳回任何訊息。

## 作法：變更佇列訊息的內容

您可以使用 **updateMessage** 在佇列中就地變更訊息內容。下列範例會更新訊息的文字：

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## 作法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種：

* `options.numOfMessages` - 擷取一批訊息 (最多 32 個)。
* `options.visibilityTimeout` - 設定較長或較短的隱藏逾時。

下列範例使用 **getMessages** 方法，在一次呼叫中取得 15 個訊息。接著它會使用 for 迴圈處理每個訊息。另外，對此方法傳回的所有訊息，將隱藏逾時設為五分鐘。

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## 作法：取得佇列長度

**getQueueMetadata** 會傳回佇列的中繼資料，包括在佇列中等待的大約訊息數目。

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## 作法：列出佇列

若要擷取佇列清單，請使用 **listQueuesSegmented**。若要擷取依特定首碼篩選的清單，請使用 **listQueuesSegmentedWithPrefix**。

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

若無法傳回所有佇列，`result.continuationToken` 可做為 **listQueuesSegmented** 的第一個參數，或 **listQueuesSegmentedWithPrefix** 的第二個參數，以擷取更多結果。

## 作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **deleteQueue** 方法。

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

若要從佇列中清除所有訊息但不要刪除，請使用 **clearMessages**。

## 作法：使用共用存取簽章

共用存取簽章 (SAS) 可安全地提供對佇列的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。SAS 通常用來提供對佇列的有限存取，例如允許行動應用程式提交訊息。

信任的應用程式 (例如雲端型服務) 會使用 **QueueService** 的 **generateSharedAccessSignature** 來產生 SAS，並提供它給不信任或不完全信任的應用程式。例如行動應用程式。SAS 是使用原則來產生，該原則描述 SAS 有效期間的開始和結束日期，以及授與 SAS 持有者的存取等級。

下列範例會產生新的共用存取原則，讓 SAS 持有者將訊息新增至佇列，並於建立它之後的 100 分鐘過期。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

請注意，也必須提供主機資訊，因為 SAS 持有者嘗試存取佇列時需要此資訊。

用戶端應用程式接著以 **QueueServiceWithSAS** 來使用 SAS，對佇列執行操作。下列範例會連線到佇列並建立訊息。

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

因為產生的 SAS 具有新增權限，若嘗試讀取、更新或刪除訊息，則會傳回錯誤。

### 存取控制清單

您也可以使用存取控制清單 (ACL) 來設定 SAS 的存取原則。若您要允許用戶端存取佇列，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用存取原則陣列來實作，每個原則有相關聯的識別碼。下列範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

下列範例會取得 **myqueue** 的目前 ACL，然後使用 **setQueueAcl** 來加入新的原則。此方法允許：

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

設定 ACL 之後，您可以根據原則的識別碼來建立 SAS。下列範例會建立 'user2' 的新 SAS：

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[儲存和存取在 Azure 中的資料][]。
-   造訪 [Azure 儲存體團隊部落格][] (英文)。
-   請造訪 GitHub 上的 [Azure Storage SDK for Node][] 儲存機制 (英文)。

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [建立 Node.js 應用程式並將其部署到 Azure 網站]: ../web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [使用儲存體的 Node.js Web 應用程式]: ../storage-nodejs-use-table-storage-web-site.md

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [儲存和存取在 Azure 中的資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
 [使用 WebMatrix 的網站]: ../web-sites-nodejs-use-webmatrix.md
 

<!---HONumber=July15_HO2-->