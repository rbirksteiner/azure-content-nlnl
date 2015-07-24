
<properties 
  pageTitle="設定和擷取 Blob 儲存體的屬性與中繼資料 | Microsoft Azure" 
  description="了解如何設定和擷取 Azure 儲存體容器和 blob 的屬性和中繼資料。" 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="04/21/2015" 
  ms.author="tamram"/>


# 設定與擷取屬性和中繼資料 #

## 概觀

容器和 Blob 支援兩種形式的相關聯資料，除了資料之外，它們還包含：

*   **系統屬性。** 系統屬性存在於每個容器或 Blob 資源上。其中有些系統屬性可以讀取或設定，有些則是唯讀的。實際上，有些系統屬性會對應至特定的標準 HTTP 標頭，Azure Managed 程式庫會為您維護這些 HTTP 標頭。  

*   **使用者定義的中繼資料。** 使用者定義的中繼資料是您針對給定的資源，以名稱/值組的形式指定的中繼資料。您可以使用中繼資料將額外的值與容器或 Blob 一同儲存。這些值僅供自己的用途使用，不會影響容器或 Blob 的運作方式。

> [AZURE.IMPORTANT]擷取資源的屬性和中繼資料值是兩個步驟的程序。在您可以讀取這些值之前，必須針對 [CloudBlobContainer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx)、[CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) 或 [CloudPageBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.aspx) 物件，明確地擷取這些值。若要同步擷取屬性和中繼資料，請針對容器或 Blob 呼叫 **FetchAttributes** 方法。若要以非同步方式擷取它們，則呼叫 **FetchAttributesAsync**。

## 設定與擷取屬性

容器只有唯讀屬性，而 Blob 則同時有唯讀和讀寫屬性。若要設定 Blob 的屬性，指定屬性值，然後呼叫 [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.setproperties.aspx) 方法或 [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.setproperties.aspx) 方法。

若要讀取容器或 Blob 的屬性，呼叫 **FetchAttributes** 方法，然後擷取屬性值。

下列程式碼範例會建立一個容器和一個 Blob，並將屬性值寫入至主控台視窗。此範例會使用儲存體模擬器，因此模擬的儲存體服務必須正在執行，程式碼才能運作：

	// Use the storage emulator account.
	CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

	// As an alternative, you can create the credentials from the account name and key.
	// string accountName = "myaccount";
	// string accountKey = "SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";
	// StorageCredentials credentials = new StorageCredentials(accountName, accountKey);
	// CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	CloudBlockBlob blob = container.GetBlockBlobReference(<span style="color:#A31515;">"myblob.txt");

	// Create or overwrite the "myblob.txt" blob with contents from a local file.
	<span style="color:Blue;">using (<span style="color:Blue;">var fileStream = System.IO.File.OpenRead(<span style="color:#A31515;">@"c:\test\myblob.txt"))
	{
	   blob.UploadFromStream(fileStream);
	} 

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	Uri blobUri =<span style="color:Blue;">new UriBuilder(containerUri.AbsoluteUri + <span style="color:#A31515;">"/ablob.txt").Uri;
	CloudPageBlob blob = <span style="color:Blue;">new CloudPageBlob(blobUri, credentials);
	blob.Create(1024);
				

	// Set the CacheControl property.
	blob.Properties.CacheControl = <span style="color:#A31515;">"public, max-age=31536000";
	blob.SetProperties();

	// Fetch blob attributes.
	blob.FetchAttributes();

	Console.WriteLine(<span style="color:#A31515;">"Read-only properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"BlobType: " + blob.Properties.BlobType);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + blob.Properties.ETag);
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUtc: " + blob.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"Length: " + blob.Properties.Length);
	Console.WriteLine();

	Console.WriteLine(<span style="color:#A31515;">"Read-write properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"CacheControl: " +
	   (blob.Properties.CacheControl == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.CacheControl));
	Console.WriteLine(<span style="color:#A31515;">"ContentEncoding: " +
	   (blob.Properties.ContentEncoding == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentEncoding));
	Console.WriteLine(<span style="color:#A31515;">"ContentLanguage: " +
	   (blob.Properties.ContentLanguage == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentLanguage));
	Console.WriteLine(<span style="color:#A31515;">"ContentMD5: " +
	   (blob.Properties.ContentMD5 == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentMD5));
	Console.WriteLine(<span style="color:#A31515;">"ContentType: " +
	   (blob.Properties.ContentType == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentType));

	// Clean up.
	blob.DeleteIfExists();
	container.Delete();
## 設定與擷取中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。若要設定中繼資料，將名稱/值組加入至資源的**中繼資料**集合，然後呼叫 **SetMetadata** 方法，將這些值儲存至服務。

> [AZURE.NOTE]：您的中繼資料名稱必須符合 C# 識別碼的命名慣例。
 
若要擷取 Blob 或容器的中繼資料，請呼叫 **FetchAttributes** 方法以填入**中繼資料**集合，然後讀取這些值。

下列程式碼範例會建立一個新的容器，並為其設定中繼資料，然後讀回中繼資料值：

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## 另請參閱  

- [Azure Storage Client Library 參考](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [開始使用適用於 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=July15_HO2-->