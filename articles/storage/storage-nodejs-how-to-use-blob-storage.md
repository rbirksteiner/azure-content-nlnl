<properties 
	pageTitle="如何使用 Node.js 中的 Blob 儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure Blob 服務來上傳、下載、列出及刪除 Blob 內容。範例以 Node.js 撰寫。" 
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
	ms.date="05/11/2015" 
	ms.author="mwasson"/>



# 如何使用 Node.js 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例使用 Node.js API 撰寫。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

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

3.  您可以手動執行 **ls** 命令，確認已建立 **node_modules** 資料夾。在該資料夾中找出 **azure-storage** 封裝，當中包含存取儲存體所需的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將以下內容新增至您要使用儲存體之應用程式的 **server.js** 檔案頂端：

    var azure = require('azure-storage');

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 `AZURE_STORAGE_ACCOUNT` 及 `AZURE_STORAGE_ACCESS_KEY`，或讀取 `AZURE_STORAGE_CONNECTION_STRING` 以取得連接 Azure 儲存體帳戶所需的資訊。如果未設定這些環境變數，則呼叫 **createBlobService** 時必須指定帳戶資訊。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]。

## 作法：建立容器

**BlobService** 物件讓您能使用容器及 blob。下列程式碼會建立 **BlobService** 物件。將下列內容新增至接近 **server.js** 的頂端：

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE]使用 **createBlobServiceAnonymous** 並提供主機位址，可匿名存取 Blob。例如，`var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

若要建立新的容器，請使用 **createContainerIfNotExists**。以下會建立一個名為 'mycontainer' 的新容器

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

如果已建立容器，`result` 將為 true。如果容器已存在，則 `result` 將為 false。`response` 將包含作業的相關資訊，包括容器的 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 資訊。

### 容器安全性

依預設，新的容器屬私人性質，無法以匿名方式存取。若要將容器變成公開，以允許匿名存取，您可以將容器的存取等級設為 **blob** 或 **container**。

* **blob** - 允許對此容器內的 Blob 內容和中繼資料的匿名讀取存取，但不包含對容器中繼資料的匿名讀取存取，例如列出容器內的所有 Blob。 

* **container** - 允許對 Blob 內容和中繼資料及容器中繼資料的匿名讀取存取。

下列範例示範將存取等級設為 **blob**：

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

或者，您可以使用 **setContainerAcl** 指定存取等級，以修改容器的存取等級。下列範例會將存取等級變更為 container：

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

result 將包含操作的相關資訊，包括容器的目前 **ETag**。

### 篩選器

可以將選用的篩選作業套用到使用 **BlobService** 執行的作業。篩選作業可包含記錄、自動重試等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是直接叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。以下會建立使用 **ExponentialRetryPolicyFilter** 的 **BlobService** 物件：

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## 作法：將 Blob 上傳到容器中

Blob 可以是區塊，或以分頁為基礎。Block 區塊可讓您更有效率地上傳大型資料，而分頁 Blob 最適合讀寫操作。如需詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

### 區塊 Blob

若要將資料上傳至區塊 Blob，請使用下列方法：

* **createBlockBlobFromLocalFile** - 建立新的區塊 Blob 並上傳檔案的內容。

* **createBlockBlobFromStream** - 建立新的區塊 Blob 並上傳串流的內容。

* **createBlockBlobFromText** - 建立新的區塊 Blob 並上傳字串的內容。

* **createWriteStreamToBlockBlob** - 提供對區塊 Blob 的寫入串流。

下列範例會將 **test.txt** 檔的內容上傳至 **myblob**。

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

這些方法傳回的 `result` 將包含作業的相關資訊，例如 Blob 的 **ETag**。

### 分頁 Blob

若要將資料上傳至分頁 Blob，請使用下列方法：

* **createPageBlob** - 建立特定長度的新分頁 Blob。

* **createPageBlobFromLocalFile** - 建立新的分頁 Blob 並上傳檔案的內容。

* **createPageBlobFromStream** - 建立新的分頁 Blob 並上傳串流的內容。

* **createWriteStreamToExistingPageBlob** - 提供對現有分頁 Blob 的寫入串流。

* **createWriteStreamToNewPageBlob** - 建立新的 Blob，然後提供串流來寫入它。

下列範例會將 **test.txt** 檔的內容上傳至 **mypageblob**。

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE]分頁 Blob 由 512 位元組的「分頁」組成。如果上傳的資料大小不是 512 的倍數，可能會發生錯誤。

## 作法：列出容器中的 Blob

若要列出容器中的 Blob，請使用 **listBlobsSegmented** 方法。若要傳回具有特定首碼的 Blob，請使用 **listBlobsSegmentedWithPrefix**。

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

`result` 將包含 `entries` 集合，此集合是描述每個 Blob 的物件陣列。若無法傳回所有 Blob，`result` 也會提供 `continuationToken`，其可做為第二個參數來擷取更多項目。

## 作法：下載 Blob

若要從 Blob 下載資料，請使用下列方法：

* **getBlobToFile** - 將 Blob 內容寫入檔案

* **getBlobToStream** - 將 Blob 內容寫入串流。

* **getBlobToText** - 將 Blob 內容寫入字串。

* **createReadStream** - 提供串流來讀取 Blob

下列範例示範使用 **getBlobToStream** 來下載 **myblob** Blob 的內容，並使用串流存放到 **output.txt** 檔案：

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result` 會包含 Blob 的相關資訊，包括 **ETag** 資訊。

## 作法：刪除 Blob

最後，呼叫 **deleteBlob** 以刪除 blob。下列範例會刪除名為 **myblob** 的 Blob。

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## 作法：並行存取

若要支援從多個用戶端或多個程序執行個體並行存取 Blob，您可以使用 **ETag** 或「租用」。

* **Etag** - 提供方法來偵測 Blob 或容器已被另一個程序修改過。

* **租用** - 提供方法來取得在一段時間內對 Blob 的獨佔、可更新、寫入或刪除存取權。

### ETag

若您需要允許多個用戶端或執行個體同時寫入 Blob，則應該使用 ETag。ETag 可讓您判斷容器或 Blob 自從您最初讀取或建立它之後是否已修改，這樣可讓您避免覆寫另一個用戶端或程序已認可的變更。

若要設定 ETag 條件，可選擇性使用的 `options.accessConditions` 參數。只有在 Blob 已存在，且 `etagToMatch` 中包含 ETag 值時，下列範例才會上傳 **test.txt** 檔案。

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

使用 ETag 時的一般模式為：

1. 取得執行建立、列出或取得操作之後的 ETag。

2. 執行動作，檢查 ETag 值未被修改。

若值已被修改，這表示另一個用戶端或執行個體自從您取得 ETag 值之後已修改 Blob 或容器。

### 租用

若要取得新的租用，可使用 **acquireLease** 方法，並指定您要取得租用的 Blob 或容器。例如，以下會取得 **myblob** 的租用。

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

**myblob** 的後續作業必須提供 `options.leaseId`參數。租用識別碼會從 **acquireLease** 傳回 `result.id`。

> [AZURE.NOTE]依預設，租用期間無限制。若要指定有限期間 (15 到 60 秒)，您可以提供 `options.leaseDuration` 參數。

若要移除租用，請使用 **releaseLease**。若要中止租用，但在原始期間到期之前不讓其他人取得新的租用，請使用 **breakLease**。

## 作法：使用共用存取簽章

共用存取簽章 (SAS) 可安全地提供對 Blob 和容器的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。SAS 通常用來提供對資料的有限存取，例如允許行動應用程式存取 Blob。

> [AZURE.NOTE]雖然您也可以用匿名方式存取 Blob，但 SAS 可讓您提供更受控制的存取，因為您必須產生 SAS。

信任的應用程式 (例如雲端型服務) 會使用 **BlobService** 的 **generateSharedAccessSignature** 來產生 SAS，並提供它給不信任或不完全信任的應用程式。例如行動應用程式。SAS 是使用原則來產生，該原則描述 SAS 有效期間的開始和結束日期，以及授與 SAS 持有者的存取等級。

下列範例會產生新的共用存取原則，讓 SAS 持有者對 **myblob** Blob 執行讀取操作，並於建立它之後的 100 分鐘過期。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

請注意，也必須提供主機資訊，因為 SAS 持有者嘗試存取容器時需要此資訊。

用戶端應用程式接著以 **BlobServiceWithSAS** 來使用 SAS，對 Blob 執行操作。以下會取得 **myblob** 的相關資訊。

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

因為產生的 SAS 只有讀取權限，若嘗試修改 Blob，則會傳回錯誤。

### 存取控制清單

您也可以使用存取控制清單 (ACL) 來設定 SAS 的存取原則。若您要允許用戶端存取容器，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用存取原則陣列來實作，每個原則有相關聯的識別碼。下列範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

下列範例會取得 **mycontainer** 的目前 ACL，然後使用 **setBlobAcl** 來加入新的原則。此方法允許：

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

設定 ACL 之後，您可以根據原則的識別碼來建立 SAS。下列範例會建立 'user2' 的新 SAS：

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 [適用於節點 API 之 Azure 儲存體 SDK 的參考資料][]
-   請參閱 MSDN 參考資料：[儲存和存取在 Azure 中的資料][]。
-   造訪 [Azure 儲存體團隊部落格][] (英文)。
-   請造訪 GitHub 上的 [Azure Storage SDK for Node][] 儲存機制 (英文)。

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
[建立 Node.js 應用程式並將其部署到 Azure 網站]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[使用儲存體的 Node.js Web 應用程式]: ../storage-nodejs-use-table-storage-web-site.md
[使用 WebMatrix 的網站]: ../web-sites-nodejs-use-webmatrix.md
[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Management Portal]: http://manage.windowsazure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[儲存和存取在 Azure 中的資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[適用於節點 API 之 Azure 儲存體 SDK 的參考資料]: http://dl.windowsazure.com/nodestoragedocs/index.html
 

<!---HONumber=July15_HO2-->