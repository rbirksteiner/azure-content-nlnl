<properties 
	pageTitle="使用 .NET 將檔案上傳至媒體服務帳戶" 
	description="了解如何建立並上傳資產，以將媒體內容移至媒體服務中。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>



#使用 .NET 將檔案上傳至媒體服務帳戶
[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。

在媒體服務中，您可以將數位檔案上傳 (或內嵌) 到資產。**資產**實體可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式字幕檔案 (以及這些檔案的相關中繼資料)。 上傳檔案之後，您的內容會安全地儲存在雲端，以進一步進行處理和串流處理。

資產中的檔案稱為**資產檔案**。**AssetFile** 執行個體和實際媒體檔是兩個不同的物件。AssetFile 執行個體包含媒體檔案的相關中繼資料，而媒體檔案包含實際的媒體內容。

建立資產時，您可以指定下列加密選項。

- **None** - 不使用加密。這是預設值。請注意，使用此選項時，您的內容在傳輸或儲存體中靜止時不會受到保護。如果您計劃使用漸進式下載傳遞 MP4，請使用此選項。 
- **CommonEncryption** - 如果您上傳的內容已經受到一般加密或 PlayReady DRM (例如，受到 PlayReady DRM 保護的 Smooth Streaming) 的加密保護，請使用此選項。
- **EnvelopeEncrypted** - 如果您上傳以 AES 加密的 HLS，請使用此選項。請注意，檔案必須已由 Transform Manager 編碼和加密。
- **StorageEncrypted** - 使用 AES-256 位元加密對您的內容進行本機加密，接著上傳到已靜止加密儲存的 Azure 儲存體。以儲存體加密保護的資產會自動解除加密並在編碼前放置在加密的檔案系統中，並且會在上傳為新輸出資產之前選擇性地重新編碼。儲存體加密的主要使用案例是讓您可以使用強式加密來保護磁碟中靜止的高品質輸入媒體檔。

	媒體服務可為您的資產提供磁碟上的儲存體加密，而不是在線上加密，例如數位版權管理員 (DRM)。

	如果您的資產是儲存體加密，必須設定資產傳遞原則。如需詳細資訊，請參閱[設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)。

如果您指定使用 **CommonEncrypted** 選項或 **EnvelopeEncypted** 選項加密資產，則需要建立資產與 **ContentKey** 的關聯。如需詳細資訊，請參閱[如何建立 ContentKey](media-services-dotnet-create-contentkey.md)。

如果您指定使用 **StorageEncrypted** 選項來加密資產，則 Media Services SDK for .NET 會建立資產的 **StorateEncrypted** 和 **ContentKey**。

>[AZURE.NOTE]媒體服務在建置串流內容的 URL 時使用 IAssetFile.Name 屬性的值 (例如，http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) 基於這個理由，不允許 percent-encoding。**Name** 屬性的值不能有下列任何 [percent-encoding-reserved 字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)：!*'();:@&=+$,/?%#"。而且，副檔名只能有一個 ‘.’。

本主題顯示如何使用 Media Services .NET SDK 以及 Media Services .NET SDK 延伸模組，以將檔案上傳到媒體服務資產。

 
## 使用媒體服務 .NET SDK 上傳單一檔案 

下面的範例程式碼會使用 .NET SDK 來執行下列工作：

- 建立空資產。
- 建立要與資產相關聯的 AssetFile 執行個體。
- 建立 AccessPolicy 執行個體，以定義存取資產所需的權限和規定期間。
- 建立可用來存取資產的定位器執行個體。
- 將單一媒體檔案上傳至媒體服務。 

		
		static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
		{
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
		}

##使用媒體服務 .NET SDK 上傳多個檔案 

下列程式碼將說明如何建立資產並上傳多個檔案。

此程式碼會執行下列動作：
	
- 	使用上一個步驟中所定義的 CreateEmptyAsset 方法，來建立空白資產。
 	
- 	建立 **AccessPolicy** 執行個體，以定義存取資產所需的權限和規定期間。
 	
- 	建立可用來存取資產的 **Locator** 執行個體。
 	
- 	建立 **BlobTransferClient** 執行個體。此類型代表在 Azure Blob 上作業的用戶端。在此範例中，我們會使用用戶端來監視上傳進度。
 	
- 	逐一列舉所指定目錄中的檔案，並建立每個檔案的 **AssetFile** 執行個體。
 	
- 	使用 **UploadAsync** 方法，將檔案上傳到媒體服務。
 	
>[AZURE.NOTE]使用 UploadAsync 方法確保未封鎖呼叫，並且平行上傳檔案。
 	
 	
	static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
	{
	    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();
	
	    var asset = CreateEmptyAsset(assetName, assetCreationOptions);
	
	    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                        AccessPermissions.Write | AccessPermissions.List);

	    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	    var blobTransferClient = new BlobTransferClient();
		blobTransferClient.NumberOfConcurrentTransfers = 20;
	    blobTransferClient.ParallelTransferThreadCount = 20;
	
	    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;
	
	    var filePaths = Directory.EnumerateFiles(folderPath);
	
	    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);
	
	    if (!filePaths.Any())
	    {
	        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
	    }
	
	    var uploadTasks = new List&lt;Task&gt;();
	    foreach (var filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        Console.WriteLine("Created assetFile {0}", assetFile.Name);
	                
	        // It is recommended to validate AccestFiles before upload. 
	        Console.WriteLine("Start uploading of {0}", assetFile.Name);
	        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
	    }
	
	    Task.WaitAll(uploadTasks.ToArray());
	    Console.WriteLine("Done uploading the files");
	
	    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;
	
	    locator.Delete();
	    accessPolicy.Delete();
	
	    return asset;
	}
	
	static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
	{
	    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
	    {
	        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
	    }
	}



上傳大量資產時，請考慮下列項目。

- 為每個執行緒建立新的 **CloudMediaContext** 物件。**CloudMediaContext** 類別不具備執行緒安全。
 
- 將 NumberOfConcurrentTransfers 從預設值 2 增加為較高的值 (例如 5)。設定此屬性會影響所有 **CloudMediaContext** 執行個體。
 
- 將 ParallelTransferThreadCount 保持為預設值 10。
 
##<a id="ingest_in_bulk"></a>使用媒體服務 .NET SDK 大量擷取資產 

上傳大型資產檔案可能會在建立資產期間造成瓶頸。大量內嵌資產或「大量內嵌」包含透過上傳程序來解除結合資產建立。若要使用大量內嵌方式，請建立可描述資產及其相關檔案的資訊清單 (IngestManifest)。然後使用您選擇的上傳方法，將相關的檔案上傳至資訊清單的 Blob 容器。Microsoft Azure 媒體服務會監看與資訊清單相關聯的 Blob 容器。將檔案上傳至 Blob 容器之後，Microsoft Azure 媒體服務會根據資訊清單 (IngestManifestAsset) 中的資產組態來完成資產建立。


若要建立新的 IngestManifest，請呼叫 CloudMediaContext 上 IngestManifests 集合所公開的 Create 方法。此方法會使用您提供的資訊清單名稱來建立新的 IngestManifest。

	IIngestManifest manifest = context.IngestManifests.Create(name);

建立將與大量 IngestManifest 相關聯的資產。對資產設定想要的加密選項，以進行大量內嵌。

	// Create the assets that will be associated with this bulk ingest manifest
	IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
	IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

IngestManifestAsset 會建立資產與大量 IngestManifest 的關聯，以進行大量內嵌。它也會建立構成每個資產之 AssetFile 的關聯。若要建立 IngestManifestAsset，請在伺服器內容上使用 Create 方法。

下列範例示範如何新增兩個新的 IngestManifestAsset，以建立先前建立之兩個資產與大量內嵌資訊清單的關聯。每個 IngestManifestAsset 也會關聯在大量內嵌期間針對每個資產所上傳的一組檔案。

	string filename1 = _singleInputMp4Path;
	string filename2 = _primaryFilePath;
	string filename3 = _singleInputFilePath;
	
	IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
	IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
	
您可以使用任何高速用戶端應用程式，而高速用戶端應用程式可以將資產檔案上傳至 IngestManifest 之 **IIngestManifest.BlobStorageUriForUpload** 屬性所提供的 Blob 儲存體容器 URI。一個著名的高速上傳服務是 [Aspera On Demand for Azure Application](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6)。您也可以撰寫程式碼來上傳資產檔案 (如下列程式碼範例所示)。
	
	static void UploadBlobFile(string destBlobURI, string filename)
	{
	    Task copytask = new Task(() =>
	    {
	        var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
	        CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
	        CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
	
	        string[] splitfilename = filename.Split('');
	        var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
	
	        using (var stream = System.IO.File.OpenRead(filename))
	            blob.UploadFromStream(stream);
	
	        lock (consoleWriteLock)
	        {
	            Console.WriteLine("Upload for {0} completed.", filename);
	        }
	    });
	
	    copytask.Start();
	}

下列程式碼範例顯示上傳本主題中所用範例之產資檔案的程式碼。
	
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
	

輪詢 **IngestManifest** 的 Statistics 屬性，即可判斷與 **IngestManifest** 相關聯之所有資產的大量內嵌進度。若要更新進度資訊，每次輪詢 Statistics 屬性時，都必須使用新的 **CloudMediaContext**。

下列範例示範如何依 **Id** 輪詢 IngestManifest。
	
	static void MonitorBulkManifest(string manifestID)
	{
	   bool bContinue = true;
	   while (bContinue)
	   {
	      CloudMediaContext context = GetContext();
	      IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
	
	      if (manifest != null)
	      {
	         lock(consoleWriteLock)
	         {
	            Console.WriteLine("\nWaiting on all file uploads.");
	            Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
	            Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
	            Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
	
	            if (manifest.Statistics.PendingFilesCount == 0)
	            {
	               Console.WriteLine("Completed\n");
	               bContinue = false;
	            }
	         }
	
	         if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
	            Thread.Sleep(60000);
	      }
	      else // Manifest is null
	         bContinue = false;
	   }
	}
	


##使用 .NET SDK 延伸模組上傳檔案 

下列範例顯示如何使用 .NET SDK 延伸模組上傳單一檔案。在此情況下，會使用 **CreateFromFile** 方法，但也會提供非同步版本 (CreateFromFileAsync)。**CreateFromFile** 方法可讓您指定檔案名稱、加密選項和回呼，以報告檔案的上傳進度。


	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}

下列範例會呼叫 UploadFile 函數，並指定儲存體加密做為資產建立選項。


	var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##後續步驟
您已將資產上傳至媒體服務，現在請移至[如何取得媒體處理器][] (英文) 主題。

[如何取得媒體處理器]: media-services-get-media-processor.md
 

<!---HONumber=July15_HO1-->