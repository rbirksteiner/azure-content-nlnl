<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="如何開始在 Visual Studio 的 ASP.NET 5 專案中使用 Azure Blob 儲存體" 
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
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure 二進位大型物件 (Windows Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。單一 Blob 可以是任何大小。Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。

若要開始，您需要建立 Azure 儲存體帳戶，然後在儲存體中建立一或多個容器。例如，您可以建立稱為 “Scrapbook” 的儲存體，然後在儲存體中建立稱為 “images” 的容器來儲存圖片，再建立另一個稱為 “audio” 的容器來儲存音訊檔。建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。如需以程式設計方式操作 Blob 的詳細資訊，請參閱[如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md "如何使用 .NET 的 Blob 儲存體")。

若要以程式設計方式存取 ASP.NET 5 專案中的 Blob，您需要加入下列項目 (如果尚不存在)。

1. 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 使用下列程式碼來取得組態設定。

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####取得儲存體連接字串
在您可以使用 Blob 執行任何動作之前，必須取得 Blob 將留存之儲存體帳戶的連接字串。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 ASP.NET 5 專案，您可以呼叫 Configuration 物件的 get 方法，從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####建立容器
就像檔案在資料夾中一樣，儲存體 Blob 位於容器中。您可以使用 **CloudBlobClient** 物件來參考現有的容器，或呼叫 CreateCloudBlobClient() 方法來建立新容器。

下列程式碼顯示如何建立新的 Blob 儲存體容器。此程式碼會先建立 **BlobClient** 物件，讓您存取物件的功能，例如建立儲存體容器。接著，程式碼嘗試參考一個稱為 “mycontainer” 的儲存體容器。 如果找不到該名稱的容器，則會建立此容器。

**注意：**對外向 ASP.NET 5 中的 Azure 儲存體執行呼叫的 API 是非同步的。如需詳細資訊，請參閱[使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/library/hh191443.aspx)。以下程式碼假設使用非同步程式設計方法。

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器。

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**注意：**請將這整段程式碼用於後續小節的程式碼之前。

#####將 Blob 上傳至容器
若要將 Blob 檔案上傳至容器，請取得容器參考，並使用該參考來取得 Blob 參考。擁有 Blob 參考後，即可藉由呼叫 **UploadFromStreamAsync()** 方法，將任何資料流上傳至其中。如果 Blob 不存在，此操作會建立 Blob，若已存在，則予以覆寫。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####列出容器中的 Blob
若要列出容器中的 Blob，請先取得容器參照。然後您可以使用容器的 **ListBlobsSegmentedAsync()** 方法來擷取其中的 Blob 和/或目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換至 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不知道 Blob 類型，可使用類型檢查來決定要將其轉換成何種類型。下列程式碼示範如何擷取和輸出容器中每個項目的 URI。

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

還有其他方法可列出 Blob 容器的內容。如需詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體](storage-dotnet-how-to-use-blobs.md/#list-blob) (英文)。

#####下載 Blob
若要下載 Blob，請先取得 Blob 的參考，然後呼叫 **DownloadToStreamAsync()** 方法。下列範例會使用 **DownloadToStreamAsync()** 方法，將 Blob 內容傳送給資料流物件，您接著可將該物件儲存為本機檔案。

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

還有其他方法可將 Blob 儲存為檔案。如需詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體](storage-dotnet-how-to-use-blobs.md/#download-blobs) (英文)。

#####刪除 Blob
若要刪除 Blob，請先取得 Blob 的參考，然後呼叫它的 **DeleteAsync()** 方法。

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[深入了解 Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)另請參閱[在伺服器總管中瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)和 [ASP.NET 5](http://www.asp.net/vnext)。
 

<!---HONumber=July15_HO2-->