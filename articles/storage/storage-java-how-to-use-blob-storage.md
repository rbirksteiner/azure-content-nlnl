<properties 
	pageTitle="如何使用 Java 的 Blob 儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure Blob 服務來上傳、下載、列出及刪除 Blob 內容。範例以 Java 撰寫。" 
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

# 如何使用 Java 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Microsoft Azure Blob 儲存體服務執行一般案例。相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java][]。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

> [AZURE.NOTE]有一套 SDK 可供在 Android 裝置上使用 Azure 儲存體的開發人員使用。如需詳細資訊，請參閱 [Azure Storage SDK for Android][]。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Java 應用程式

在本指南中，您將使用儲存體功能，這些功能可執行於本機的 Java 應用程式內，也可執行於在 Azure 中之 Web 角色或背景工作角色內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。完成此動作之後，您需要驗證開發系統符合 GitHub 上的 [Azure Storage SDK for Java][] 儲存機制中所列出的最低需求和相依性。如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。完成這些工作之後，您就能夠利用本文中的範例來建立 Java 應用程式。

## 設定您的應用程式以存取 Blob 儲存體

在您要使用 Azure 儲存體 API 來存取 Blob 的 Java 檔案頂端，新增下列 import 陳述式：

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## 設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串 (其中的 *AccountName* 和 *AccountKey* 值要使用您儲存體帳戶的名稱，以及在管理入口網站中針對該儲存體帳戶而列出的主要存取金鑰)。本範例將示範如何宣告靜態欄位來存放連接字串：

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

在 Microsoft Azure 的角色內執行的應用程式中，此字串可以儲存在服務組態檔 *ServiceConfiguration.cscfg* 裡，且可以藉由呼叫 **RoleEnvironment.getConfigurationSettings** 方法來存取。以下是從服務組態檔中名為 **StorageConnectionString** 的 *Setting* 元素取得連接字串的範例：

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## 作法：建立容器

CloudBlobClient 物件可讓您取得容器和 Blob 的參考物件。下列程式碼將建立 **CloudBlobClient** 物件。(注意：還有其他方式可建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考]中的 **CloudStorageAccount**)。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

使用 **CloudBlobClient** 物件來取得想要使用容器的參考。如果容器不存在，可以使用 **createIfNotExists** 方法建立，如果存在，此方法則會傳回現有的容器。根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如上面所做過的) 才能從此容器下載 blob。

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Get a reference to a container.
	   // The container name must be lower case
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

	   // Create the container if it does not exist.
    	container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### 選擇性步驟：設定公用存取的容器

依預設會將容器的權限設定為私人存取，但針對網際網路上的所有使用者，您可以輕鬆地將容器的權限設定為公用、唯讀存取：

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## 作法：將 Blob 上傳到容器中

若要將檔案上傳至 Blob，請取得容器參考，並使用該參考來取得 Blob 參考。擁有 Blob 參考後，即可在 Blob 參考上呼叫 upload，上傳任何資料流。如果 Blob 不存在，此作業將予以建立，若已存在，則予以覆寫。此程式碼範例顯示此點，並假設已經建立好容器。

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

    	// Create or overwrite the "myimage.jpg" blob with contents from a local file.
    	CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    	File source = new File(filePath);
    	blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參考，就像上傳 Blob 那樣。您可以使用容器的 **listBlobs** 方法搭配 **for** 迴圈。下列程式碼將容器中每個 Blob 的 URI 輸出到主控台。

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    	// Retrieve reference to a previously created container.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
    	// Loop over blobs within the container and output the URI to each of them.
    	for (ListBlobItem blobItem : container.listBlobs()) {
	       System.out.println(blobItem.getUri());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Blob 服務也具備容器中之目錄的概念。正因如此，您能夠以更像資料夾的結構組織 Blob。

例如，您可能有名為 "photos" 的容器，當中您可能上傳名為 "rootphoto1"、"2010/photo1"、"2010/photo2" 和 "2011/photo1" 的 Blob。這會在 "photos" 容器內建立虛擬目錄 "2010" 和 "2011"。當您在 "photos" 容器上呼叫 **ListBlobs** 時，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlob** 物件，其分別代表最上層所包含的目錄和 Blob。在此情況下，系統會傳回目錄 "2010" 和 "2011"，以及照片 "rootphoto1"。您可以使用 **instanceof** 運算子來區別這些物件。

您可以選擇性地將參數傳入 **listBlobs** 方法，將 **useFlatBlobListing** 參數設為 true。如此會導致不論目錄為何，都會傳回每個 Blob。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考] 中的 **CloudBlobContainer.listBlobs**。

## 作法：下載 blob

若要下載 Blob，請遵循與上傳 Blob 相同的步驟，以取得 Blob 參考。在上傳範例中，您對 blob 物件呼叫了 upload。在下列範例中，呼叫 download 將 Blob 內容傳到串流物件，例如 **FileOutputStream**，您可以用串流物件來將 Blob 永久儲存到本機檔案。

    try
    {
    	// Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Loop through each blob item in the container.
	   for (ListBlobItem blobItem : container.listBlobs()) {
	       // If the item is a blob, not a virtual directory.
	       if (blobItem instanceof CloudBlob) {
	           // Download the item and save it to a file with the same name.
    	        CloudBlob blob = (CloudBlob) blobItem;
    	        blob.download(new FileOutputStream("C:\\mydownloads\" + blob.getName()));
    	    }
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：刪除 Blob

若要刪除 Blob，請取得 Blob 參考，然後呼叫 **deleteIfExists**。

    try
    {
	   // Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Retrieve reference to a blob named "myimage.jpg".
	   CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

	   // Delete the blob.
	   blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 作法：刪除 Blob 容器

最後，若要刪除 blob 容器，請取得 blob 容器參考，然後呼叫 **deleteIfExists**。

    try
    {
	   // Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Delete the blob container.
	   container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

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