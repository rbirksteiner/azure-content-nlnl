<properties 
	pageTitle="將現有的 blob 複製到媒體服務資產" 
	description="本主題說明如何將現有的 blob 複製到媒體服務資產。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#將現有的 blob 複製到媒體服務資產

本主題說明如何將 blob 從儲存體帳戶複製到新的 Microsoft Azure 媒體服務資產。

您的 blob 可存在於與媒體服務帳戶相關聯的儲存體帳戶，或不與媒體服務帳戶相關聯的儲存體帳戶中。本主題示範如何將 blob 從儲存體帳戶複製到媒體服務資產。請注意，您也可以在資料中心之間複製。不過，這麼做可能會向您索取費用。如需有關定價的詳細資訊，請參閱[資料傳輸](http://azure.microsoft.com/pricing/#header-11)。

>[AZURE.NOTE]請勿在不使用媒體服務 API 的情況下，嘗試變更由媒體服務所產生的 blob 容器內容。

##必要條件

- 在新的或現有的 Azure 訂用帳戶中有兩個媒體服務帳戶。請參閱主題＜[如何建立媒體服務帳戶](media-services-create-account.md)＞ (英文)。
- 作業系統：Windows 7、Windows 2008 R2 或 Windows 8。
- .NET Framework 4.5。
- Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express)。

##設定專案

在本節中，您將建立 C# Console Application 專案。

1. 使用 Visual Studio 2012 或 Visual Studio 2010 SP1 建立一個包含 C# Console Application 專案的新方案。 
2. 輸入 CopyExistingBlobsIntoAsset 做為名稱，然後按一下 [確定]。
1. 使用 Nuget 將參考新增至媒體服務相關的 DLL。在 Visual Studio 主要功能表中，選取 [工具] -> [Library Package Manager] -> [Package Manager Console]。在主控台視窗中輸入 Install-package windowsazure.mediaservices，然後按下 Enter。
1. 新增此專案所需的其他參考：System.Configuration。
1. 將預設新增至 Programs.cs 檔的 using 陳述式取代為下列陳述式：
		
		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure;
		using System.Web;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Microsoft.WindowsAzure.Storage.Auth;

1. 將 AppSettings 區段新增至 .config 檔中，並根據媒體服務與儲存體金鑰與名稱值將值更新。

		<appSettings>
		  <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
		  <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
		  <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
		  <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
		  <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
		  <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
		</appSettings>


##將 blob 從儲存體帳戶複製至媒體服務資產

下列程式碼範例會執行下列工作：

1. 建立 CloudMediaContext 執行個體。 
1. 建立 CloudStorageAccount 執行個體：_sourceStorageAccount 和 _destinationStorageAccount。1. 將 Smooth Streaming 檔案從本機目錄上傳至位於 _sourceStorageAccount 的 blob 容器中。1. 建立新資產。為此資產建立的 blob 容器位於 _destinationStorageAccount 中。1. 使用 Azure 儲存體 SDK 將指定的 blob 複製到與資產相關聯的容器。

>[AZURE.NOTE]如果定位器已過期，複製作業不會擲回例外狀況。

1. 將 .ism 檔設為主要檔案。
1. 為與該資產相關聯的 OnDemandOrigin 定位器建立 Smooth Streaming URL。 
		
		class Program
		{
		    // Read values from the App.config file. 
		    static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
		    static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		    static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
		    static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
		    static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
		    static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
		
		    private static MediaServicesCredentials _cachedCredentials = null;
		    private static CloudMediaContext _context = null;
		
		    private static CloudStorageAccount _sourceStorageAccount = null;
		    private static CloudStorageAccount _destinationStorageAccount = null;
		
		    static void Main(string[] args)
		    {
		        _cachedCredentials = new MediaServicesCredentials(
		                        _accountName,
		                        _accountKey);
		        // Use the cached credentials to create CloudMediaContext.
		        _context = new CloudMediaContext(_cachedCredentials);
		
		        // In this example the storage account from which we copy blobs is not 
		        // associated with the Media Services account into which we copy blobs.
		        // But the same code will work for coping blobs from a storage account that is 
		        // associated with the Media Services account.
		        //
		        // Get a reference to a storage account that is not associated with a Media Services account
		        // (an external account).  
		        StorageCredentials externalStorageCredentials =
		            new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
		        _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
		
		        //Get a reference to the storage account that is associated with a Media Services account. 
		        StorageCredentials mediaServicesStorageCredentials =
		            new StorageCredentials(_storageAccountName, _storageAccountKey);
		        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
		
		        // Upload Smooth Streaming files into a storage account.
		        string localMediaDir = @"C:\supportFiles\streamingfiles";
		        CloudBlobContainer blobContainer =
		            UploadContentToStorageAccount(localMediaDir);
		
		        // Create a new asset and copy the smooth streaming files into 
		        // the container that is associated with the asset.
		        IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
		
		        // Get the streaming URL for the smooth streaming files 
		        // that were copied into the asset.   
		        string urlForClientStreaming = CreateStreamingLocator(asset);
		        Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
		
		        Console.ReadLine();
		    }
		
		    /// <summary>
		    /// Uploads content from a local directory into the specified storage account.
		    /// In this example the storage account is not associated with the Media Services account.
		    /// </summary>
		    /// <param name="localPath">The path from which to upload the files.</param>
		    /// <returns>The container that contains the uploaded files.</returns>
		    static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
		    {
		        CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
		        CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
		
		        externalMediaBlobContainer.CreateIfNotExists();
		
		        // Upload files to the blob container.  
		        DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
		        foreach (var file in uploadDirectory.EnumerateFiles())
		        {
		            CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
		
		            blob.UploadFromFile(file.FullName, FileMode.Open);
		        }
		
		        return externalMediaBlobContainer;
		    }
		
		    /// <summary>
		    /// Creates a new asset and copies blobs from the specifed storage account.
		    /// </summary>
		    /// <param name="mediaBlobContainer">The specified blob container.</param>
		    /// <returns>The new asset.</returns>
		    static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
		    {
		        // Create a new asset. 
		        IAsset asset = _context.Assets.Create("Burrito_" + Guid.NewGuid(), AssetCreationOptions.None);
		
		        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy", TimeSpan.FromHours(24), AccessPermissions.Write);
		        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
		
		        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
		
		        // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
		        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
		
		        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);
		
		        if (assetContainer.CreateIfNotExists())
		        {
		            assetContainer.SetPermissions(new BlobContainerPermissions
		            {
		                PublicAccess = BlobContainerPublicAccessType.Blob
		            });
		        }
		
		        var blobList = mediaBlobContainer.ListBlobs();
		        foreach (var sourceBlob in blobList)
		        {
		            var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
		            CopyBlob(sourceBlob as ICloudBlob, assetContainer);
		            assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
		            assetFile.Update();
		        }
		
		        destinationLocator.Delete();
		        writePolicy.Delete();
		
		        // Since we copied a set of Smooth Streaming files, 
		        // set the .ism file to be the primary file. 
		        SetISMFileAsPrimary(asset);
		
		        return asset;
		    }
		
		    /// <summary>
		    /// Creates the OnDemandOrigin locator in order to get the streaming URL.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    /// <returns>The streaming URL.</returns>
		    static public string CreateStreamingLocator(IAsset asset)
		    {
		        var ismAssetFile = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
		
		        // Create a 30-day readonly access policy. 
		        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		            TimeSpan.FromDays(30),
		            AccessPermissions.Read);
		
		        // Create a locator to the streaming content on an origin. 
		        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		            policy,
		            DateTime.UtcNow.AddMinutes(-5));
		
		        return originLocator.Path + ismAssetFile.Name + "/manifest";
		    }
		
		    /// <summary>
		    /// Copies the specified blob into the specified container.
		    /// </summary>
		    /// <param name="sourceBlob">The source container.</param>
		    /// <param name="destinationContainer">The destination container.</param>
		    static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
		    {
		        var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
		        {
		            Permissions = SharedAccessBlobPermissions.Read,
		            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
		        });
		
		        ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);
		
		        if (destinationBlob.Exists())
		        {
		            Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
		        }
		        else
		        {
		            try
		            {
		                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
		                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine(string.Format("Error copying blob '{0}': {1}", sourceBlob.Name, ex.Message));
		            }
		        }
		    }
		
		    /// <summary>
		    /// Sets a file with the .ism extension as a primary file.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    static private void SetISMFileAsPrimary(IAsset asset)
		    {
		        var ismAssetFiles = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
		
		        if (ismAssetFiles.Count() != 1)
		            throw new ArgumentException("The asset should have only one, .ism file");
		
		        ismAssetFiles.First().IsPrimary = true;
		        ismAssetFiles.First().Update();
		    }
		}
 

<!---HONumber=July15_HO2-->