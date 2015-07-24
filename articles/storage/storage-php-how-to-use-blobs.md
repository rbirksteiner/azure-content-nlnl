<properties 
	pageTitle="如何使用 PHP 中的 Blob 儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure Blob 服務來上傳、列出、下載及刪除 Blob。程式碼範例以 PHP 撰寫。" 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="tomfitz"/>

# 如何使用 PHP 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例均是以 PHP 撰寫，並使用 [Azure SDK for PHP][download] (英文)。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 PHP 應用程式

若要建立 PHP 應用程式並使其存取 Azure Blob 服務，唯一要求就是在您的程式碼中參考 Azure SDK for PHP 中的類別。您可以使用任何開發工具來建立應用程式 (包括 [記事本])。

在本指南中，您將使用可從 PHP 應用程式內本機呼叫的服務功能，或可在 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的服務功能。

## 取得 Azure 用戶端程式庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## 設定您的應用程式以存取 Blob 服務

若要使用 Azure Blob 服務 API，您必須：

1. 參考使用 [require_once][require_once] 陳述式的自動換片器檔案，以及
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動換片器檔案及參考 **ServicesBuilder** 類別。

> [AZURE.NOTE]此範例 (和本文中的其他範例) 假設您已透過 Composer 安裝 PHP Client Libraries for Azure。如果您手動或透過 PEAR 封裝安裝程式庫，則必須參考 `WindowsAzure.php` 自動換片器檔案。

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


在下列各範例中，一律會顯示 `require_once` 陳述式，但只會參考要執行之範例所需的類別。

## 設定 Azure 儲存體連接

若要具現化 Azure Blob 服務用戶端，您必須具備有效的連接字串。Blob 服務的連接字串格式為：

用於存取即時服務：

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

用於存取模擬器儲存體：

	UseDevelopmentStorage=true


若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。您可以：

* 直接將連接字串傳遞給它，或
* 使用 **CloudConfigurationManager (CCM)** 到多種外部來源檢查連接字串：
	* 預設已支援一種外部來源，即環境變數
	* 您可以擴充 **ConnectionStringSource** 類別以加入新來源

在本文的各範例中，將會直接傳遞連接字串。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## 作法：建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**BlobRestProxy** 物件可讓您使用 **createContainer** 方法建立 Blob 容器。建立容器時，您可以在容器上設定選項，但這並非必要動作。(下列範例顯示如何設定容器 ACL 和容器中繼資料)。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

呼叫 **setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS)** 可讓容器和 Blob 資料開放透過匿名要求來存取。呼叫 **setPublicAccess(PublicAccessType::BLOBS_ONLY)** 可讓 Blob 資料開放透過匿名要求來存取。如需容器 ACL 的詳細資訊，請參閱[設定容器 ACL (REST API)][container-acl] (英文)。

如需 Blob 服務錯誤碼的詳細資訊，請參閱 [Blob 服務錯誤碼][error-codes] (英文)。

## 作法：將 Blob 上傳到容器中

若要將檔案當作 Blob 上傳，請使用 **BlobRestProxy->createBlockBlob** 方法。如果 Blob 不存在，此作業將予以建立，若已存在，則予以覆寫。下列程式碼範例假設已建立容器，並使用 [fopen][fopen] (英文) 將檔案當作串流開啟。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

請注意，上述範例會以串流方式上傳 Blob。不過，也可以使用 [file_get_contents][file_get_contents] 之類的函數將 Blob 當作字串上傳。若要這樣做，請將上述範例中的 `$content = fopen("c:\myfile.txt", "r");` 變更為 `$content = file_get_contents("c:\myfile.txt");`。

## 作法：列出容器中的 Blob

若要列出容器中的 Blob，請搭配使用 **BlobRestProxy->listBlobs** 方法與 **foreach** 迴圈，對結果進行迴圈。下列程式碼會將容器中每個 Blob 的名稱與 URI 輸出至瀏覽器。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## 作法：下載 Blob

若要下載 Blob，請呼叫 **BlobRestProxy->getBlob** 方法，然後在結果產生的 **GetBlobResult** 物件上呼叫 **getContentStream** 方法。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

請注意，以上範例會以串流資源形式 (預設行為) 取得 Blob。不過，您可以使用 [stream_get_contents][stream-get-contents] 函數將傳回的串流轉換成字串。

## 作法：刪除 Blob

若要刪除 Blob，請將容器名稱和 Blob 名稱傳遞至 **BlobRestProxy->deleteBlob**。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 作法：刪除 Blob 容器

最後，若要刪除 Blob 容器，請將容器名稱傳遞至 **BlobRestProxy->deleteContainer**。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 後續步驟

了解 Azure Blob 服務的基礎概念之後，請參考下列連結以深入了解更複雜的儲存工作。

- 請參閱 MSDN 參考資料：[Azure 儲存體](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- 造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
- 請參閱 <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php> 中的 PHP 區塊 Blob 範例。
- 請參閱 <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php> 中的 PHP 分頁 Blob 範例

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
 

<!---HONumber=July15_HO2-->