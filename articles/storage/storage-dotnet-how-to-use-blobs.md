<properties
	pageTitle="如何使用 .NET 的 Blob 儲存體 | Microsoft Azure"
	description="了解如何使用 Microsoft Azure Blob 儲存體來上傳、下載、列出及刪除 Blob 內容。範例是以 C# 撰寫的。"
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="06/15/2015"
	ms.author="tamram"/>


# 如何使用 .NET 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 儲存體服務執行一般案例。這些範例均以 C# 撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。

> [AZURE.NOTE]本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是儲存體用戶端程式庫 4.x，可透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 或從 [Azure SDK for .NET](/downloads/) 中取得。請參閱[以程式設計方式存取 Blob 儲存體](#programmatically-access-blob-storage)，以詳細了解如何取得儲存體用戶端程式庫。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## 以程式設計方式存取 Blob 儲存體

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### 命名空間宣告
將下列命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

**CloudBlobClient** 類型可讓您擷取物件，這些物件代表 Blob 儲存體服務中儲存的容器和 Blob。下列程式碼會使用我們在前面擷取的儲存體帳戶物件來建立 **CloudBlobClient** 物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

## 建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

此範例說明如何建立尚不存在的容器：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器：

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
 	    BlobContainerPublicAccessType.Blob });

網際網路上的任何人都可以看到公用容器中的 Blob，但要有適當的存取金鑰，才能修改或刪除這些 Blob。

## 將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用該參照來取得區塊 Blob 參照。擁有 Blob 參照後，即可藉由呼叫 **UploadFromStream** 方法，將任何資料流上傳至其中。此操作會建立 Blob (如果其並不存在) 或覆寫 Blob (如果其已存在)。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## 列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參照。然後您即可使用容器的 **ListBlobs** 方法來擷取 Blob 和 (或) 其中的目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換至 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不清楚類型，可使用類型檢查來決定要將其轉換至何種類型。下列程式碼示範如何擷取和輸出 `photos` 容器中每個項目的 URI：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;

			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

如上所述，為 blob 命名時，您可以在其名稱中包含路徑資訊。這會建立虛擬目錄結構，讓您能夠組織及周遊，就像使用傳統檔案系統一樣。請注意，目錄結構僅限虛擬目錄結構 - Blog 儲存體中唯一可用的資源為容器和 blob。但是，儲存體用戶端程式庫提供 **CloudBlobDirectory** 物件來參照虛擬目錄，以及簡化透過此方式組織之 blob 的使用程序。

例如，假設名為 `photos` 的容器中有下面這一組區塊 blob：

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

當您在 'photos' 容器上呼叫 **ListBlobs** (如上面範例所示) 時，會傳回階層式清單。它包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，分別代表容器中的目錄與 blob。輸出結果看起來像這樣：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


此外，您可以選擇將 **ListBlobs** 方法的 **UseFlatBlobListing** 參數設定為 **true**。在此案例中，容器中的每個 blob 都是以 **CloudBlockBlob** 物件的方式傳回：呼叫 **ListBlobs** 會傳回簡單列表，看起來像這樣：

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

而且結果看起來像這樣：

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## 下載 Blob

若要下載 Blob，請先擷取 Blob 參照，然後呼叫 **DownloadToStream** 方法。下列範例使用 **DownloadToStream** 方法將 Blob 內容傳送給資料流物件，您接著可將該物件永久儲存成本機檔案。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

您也可以使用 **DownloadToStream** 方法，將 Blob 的內容當成文字字串下載。

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## 刪除 Blob

若要刪除 Blob，請先取得 Blob 參照，然後在該參照上呼叫 **Delete** 方法。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## 以非同步方式分頁列出 Blob

如果您要列出大量的 Blob，或是要控制在單一列出作業中傳回的結果數，您可以在結果頁面中列出 Blob。此範例說明如何以非同步方式分頁傳回結果，使執行不會因為等待大型結果集傳回而中斷。

此範例說明一般 Blob 列出方式，但您也可以執行階層式列出，方法是將 **ListBlobsSegmentedAsync** 方法的 `useFlatBlobListing` 參數設為 `false`。

範例方法會呼叫非同步方法，因此前面必須加上 `async` 關鍵字，且必須傳回 **Task** 物件。為 **ListBlobsSegmentedAsync** 方法指定的 await 關鍵字會擱置範例方法的執行，直到列出工作完成為止。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。<ul> <li>檢視 Blob 服務參照文件以了解與可用 API 有關的完整詳細資料：<ul> <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">.NET 的儲存體用戶端程式庫參考資料</a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API 參考資料</a></li> </ul> </li> <li>了解您可以使用 Azure 儲存體執行的更多進階工作 (位於<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">在 Azure 中儲存及存取資料</a>)。</li> <li>了解如何使用 <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK 簡化您要用來使用 Azure 儲存體之程式碼的撰寫程序。</li> <li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。<ul> <li>使用<a href="/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>儲存結構化資料。</li> <li>使用<a href="/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>儲存非結構化資料。</li> <li>使用 <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 儲存關聯式資料。</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
 

<!---HONumber=62-->