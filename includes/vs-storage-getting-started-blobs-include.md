#####建立容器
就像檔案在資料夾中一樣，儲存體 Blob 位於容器中。您可以使用 **CloudBlobClient** 物件來參考現有的容器，或呼叫 CreateCloudBlobClient() 方法來建立新容器。

下列程式碼顯示如何建立新的 Blob 儲存體容器。此程式碼會先建立 **BlobClient** 物件，讓您存取物件的功能，例如建立儲存體容器。接著，程式碼嘗試參考一個稱為 “mycontainer” 的儲存體容器。 如果找不到該名稱的容器，則會建立此容器。

	// Create a blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Get a reference to a container named “mycontainer.”
	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// If “mycontainer” doesn’t exist, create it.
	container.CreateIfNotExists();

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰才能從此容器下載 Blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器。

	container.SetPermissions(
    	new BlobContainerPermissions { PublicAccess = 
	    BlobContainerPublicAccessType.Blob }); 


**注意：**請在下列區段的程式碼開頭使用此程式碼區塊。

#####將 Blob 上傳至容器
若要將 Blob 檔案上傳至容器，請取得容器參考，並使用該參考來取得 Blob 參考。擁有 Blob 參照後，即可藉由呼叫 **UploadFromStream()** 方法，將任何資料流上傳至其中。如果 Blob 不存在，此操作會建立 Blob，若已存在，則予以覆寫。下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

	// Get a reference to a blob named "myblob".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
	
	// Create or overwrite the "myblob" blob with the contents of a local file
	// named “myfile”.
	using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
	{
    	blockBlob.UploadFromStream(fileStream);
	}

#####列出容器中的 Blob
若要列出容器中的 Blob，請先取得容器參照。然後您即可使用容器的 **ListBlobs()** 方法來擷取其中的 Blob 和/或目錄。若要針對傳回的 **IListBlobItem** 存取一組豐富的屬性與方法，您必須先將它轉換至 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。如果不知道 Blob 類型，可使用類型檢查來決定要將其轉換成何種類型。下列程式碼示範如何擷取和輸出名為 “photos” 的容器中每個項目的 URI。

	// Get a reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop through items in the container and output the length and URI for each 
	// item.
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

還有其他方法可列出 Blob 容器的內容。如需詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體](../articles/storage/storage-dotnet-how-to-use-blobs.md/#list-blob) (英文)。

#####下載 Blob
若要下載 Blob，請先取得 Blob 的參考，再呼叫 DownloadToStream() 方法。下列範例使用 DownloadToStream() 方法將 Blob 內容傳送給資料流物件，您接著可將該物件儲存為本機檔案。

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	blockBlob.DownloadToStream(fileStream);
	}

還有其他方法可將 Blob 儲存為檔案。如需詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體](../articles/storage/storage-dotnet-how-to-use-blobs.md/#download-blobs) (英文)。

#####刪除 Blob
若要刪除 Blob，請先取得 Blob 的參考，再呼叫它的 Delete() 方法。

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	blockBlob.Delete();

[深入了解 Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)，另請參閱[在伺服器總管中瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)。

<!---HONumber=62-->