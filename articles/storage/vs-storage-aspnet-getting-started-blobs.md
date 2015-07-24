<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="如何在 Visual Studio 的 ASP.NET 專案中開始使用 Azure Blob 儲存體" 
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
	ms.date="06/24/2015" 
	ms.author="patshea123"/>

# 開始使用 Azure 儲存體 (ASP.NET 專案)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

Azure 二進位大型物件 (Windows Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。單一 Blob 可以是任何大小。Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。

若要開始，您需要建立 Azure 儲存體帳戶，然後在儲存體中建立一或多個容器。例如，您可以建立稱為 “Scrapbook” 的儲存體，然後在儲存體中建立稱為 “images” 的容器來儲存圖片，再建立另一個稱為 “audio” 的容器來儲存音訊檔。建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。如需以程式設計方式操作 Blob 的詳細資訊，請參閱[如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md/ "如何使用 .NET 的 Blob 儲存體")。

本文章將示範如何使用 Azure Blob 儲存體服務執行常見案例。這些範例均以 C# 撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。

如需 ASP.NET 專案的詳細資訊，請參閱 [ASP.NET](http://www.asp.net)。

## 以程式設計方式存取 Blob 儲存體

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### 命名空間宣告
將下列命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

**CloudBlobClient** 類型可讓您擷取物件，這些物件代表 Blob 儲存體服務中儲存的容器和 Blob。下列程式碼會使用我們在前面擷取的儲存體帳戶物件來建立 **CloudBlobClient** 物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

[AZURE.INCLUDE [storage-dotnet-odatalib-dependencies](../../includes/storage-dotnet-odatalib-dependencies.md)]

## 建立容器

儲存體 Blob 中的每個 Blob 必須位於一個容器中。此範例說明如何建立尚不存在的容器：

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

如上所示，Blob 服務也具備容器中之目錄的概念。正因如此，您能夠以更像資料夾的結構組織 Blob。例如，假設名為 `photos` 的容器中有下面這一組區塊 blob：

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

當您在 'photos' 容器上呼叫 **ListBlobs** (如以上範例所示)，傳回的集合將包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，其分別代表最上層所包含的目錄和 Blob。以下是最終得到的輸出：

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


此外，您可以選擇將 **ListBlobs** 方法的 **UseFlatBlobListing** 參數設定為 **true**。如此會導致不論目錄為何，都將每個 Blob 各當成一個 **CloudBlockBlob** 來傳回。以下是 **ListBlobs** 的呼叫：

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

以下則是結果：

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

如需詳細資訊，請參閱 [CloudBlobContainer.ListBlobs][]。

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

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。<ul> <li>檢視 Blob 服務參照文件以了解與可用 API 有關的完整詳細資料：<ul> <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731">.NET 的儲存體用戶端程式庫參考資料</a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API 參考資料</a></li> </ul> </li> <li>了解您可以使用 Azure 儲存體執行的更多進階工作 (位於<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">在 Azure 中儲存及存取資料</a>)。</li> <li>了解如何使用 <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK 簡化您要用來使用 Azure 儲存體之程式碼的撰寫程序。</li> <li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。<ul> <li>使用<a href="/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>儲存結構化資料。</li> <li>使用<a href="/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>儲存非結構化資料。</li> <li>使用 <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 儲存關聯式資料。</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 

<!---HONumber=July15_HO2-->