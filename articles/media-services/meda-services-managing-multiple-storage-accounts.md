<properties 
	pageTitle="跨多個儲存體帳戶管理媒體服務資產" 
	description="此文件指引您如何管理跨多個儲存體帳戶的媒體服務資產。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#跨多個儲存體帳戶管理媒體服務資產

從 Microsoft Azure 媒體服務 2.2 版起，您可以將多個儲存體帳戶附加至單一媒體服務帳戶。將多個儲存體帳戶附加到媒體服務帳戶的能力提供下列優點：在多個儲存體帳戶之間平衡您資產的負載。

調整媒體服務進行大量的內容處理 (因為目前單一儲存體帳戶的最大上限為 500 TB)。

本主題示範如何將多個儲存體帳戶附加到媒體服務帳戶使用 [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)。同時也會示範如何在使用媒體服務 SDK 建立資產時，指定不同的儲存體帳戶。

##注意事項

當您在媒體服務帳戶中附加多個儲存體帳戶時，適用下列考量事項：

- 所有附加到媒體服務帳戶的儲存體帳戶必須與該媒體服務帳戶位於相同的資料中心。
- 目前，一旦儲存體帳戶附加到指定的媒體服務帳戶後，將無法卸離。
- 在建立媒體服務帳戶時指出的儲存體帳戶，即為主要儲存體帳戶。目前您無法變更預設的儲存體帳戶。 

其他考量：

媒體服務在建置串流內容的 URL 時使用 **IAssetFile.Name** 屬性的值 (例如，http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) 基於這個理由，不允許 percent-encoding。Name 屬性的值不能有下列任何[保留的百分比編碼字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)：!*'();:@&=+$,/?%#"。而且，副檔名只能有一個 ‘.’。

##使用 Azure 服務管理 REST API 附加儲存體帳戶

目前附加多個儲存體帳戶的唯一方式，是使用 [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)。在＜[做法：使用媒體服務管理 REST API](https://msdn.microsoft.com/library/azure/dn167656.aspx)＞主題中的程式碼範例，定義 **AttachStorageAccountToMediaServiceAccount** 方法能將儲存體帳戶附加到指定的媒體服務帳戶。相同主題中的程式碼定義 **ListStorageAccountDetails** 方法，能列出所有附加到指定的媒體服務帳戶的儲存體帳戶。


##管理跨多個儲存體帳戶的媒體服務資產

下列程式碼會使用最新的媒體服務 SDK，執行下列工作：

1. 顯示所有與指定媒體服務帳戶相關聯的儲存體帳戶。
1. 擷取預設儲存體帳戶的名稱。
1. 在預設儲存體帳戶中建立新資產。
1. 在指定的儲存體帳戶內建立編碼工作的輸出資產。

	using Microsoft.WindowsAzure.MediaServices.Client; using System; using System.Collections.Generic; using System.Configuration; using System.IO; using System.Linq; using System.Text; using System.Threading; using System.Threading.Tasks;
	
	namespace MultipleStorageAccounts { class Program { // 您想要進行編碼的媒體檔案位置。private static readonly string _singleInputFilePath = Path.GetFullPath(@"../..\\supportFiles\\multifile\\interview2.wmv");
	
	        private static readonly string MediaServicesAccountName = 
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string MediaServicesAccountKey = 
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        private static CloudMediaContext _context;
	        private static MediaServicesCredentials _cachedCredentials = null;

	        static void Main(string[] args)
	        {

	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            MediaServicesAccountName,
	                            MediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);

	
	            // Display the storage accounts associated with 
	            // the specified Media Services account:
	            foreach (var sa in _context.StorageAccounts)
	                Console.WriteLine(sa.Name);
	
	            // Retrieve the name of the default storage account.
	            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
	            Console.WriteLine("Name: {0}", defaultStorageName.Name);
	            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);
	
	            // Retrieve the name of a storage account that is not the default one.
	            var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
	            Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
	            Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);
	            
	            // Create the original asset in the default storage account.
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
	                defaultStorageName.Name, _singleInputFilePath);
	            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);
	            
	            // Create an output asset of the encoding job in the other storage account.
	            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
	            if(outputAsset!=null)
	                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            
	            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
	            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            return asset;
	        }
	
	        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My encoding job");
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("My encoding task",
	                processor,
	                "H264 Broadband 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is not encrypted. 
	            task.OutputAssets.AddNew("Output asset", storageName,
	                AssetCreationOptions.None);
	
	            // Use the following event handler to check job progress.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Launch the job.
	            job.Submit();
	
	            // Check job execution and wait for job to finish. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // Get an updated job reference.
	            job = GetJob(job.Id);
	
	            // If job state is Error the event handling 
	            // method for job progress should log errors.  Here we check 
	            // for error state and exit if needed.
	            if (job.State == JobState.Error)
	            {
	                Console.WriteLine("\nExiting method due to job error.");
	                return null;
	            }
	
	            // Get a reference to the output asset from the job.
	            IAsset outputAsset = job.OutputMediaAssets[0];
	
	            return outputAsset;
	        }
	
	
	        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	
	        private static void StateChanged(object sender, JobStateChangedEventArgs e)
	        {
	            Console.WriteLine("Job state changed event:");
	            Console.WriteLine("  Previous state: " + e.PreviousState);
	            Console.WriteLine("  Current state: " + e.CurrentState);
	
	            switch (e.CurrentState)
	            {
	                case JobState.Finished:
	                    Console.WriteLine();
	                    Console.WriteLine("********************");
	                    Console.WriteLine("Job is finished.");
	                    Console.WriteLine("Please wait while local tasks or downloads complete...");
	                    Console.WriteLine("********************");
	                    Console.WriteLine();
	                    Console.WriteLine();
	                    break;
	                case JobState.Canceling:
	                case JobState.Queued:
	                case JobState.Scheduled:
	                case JobState.Processing:
	                    Console.WriteLine("Please wait...\n");
	                    break;
	                case JobState.Canceled:
	                case JobState.Error:
	                    // Cast sender as a job.
	                    IJob job = (IJob)sender;
	                    // Display or log error details as needed.
	                    Console.WriteLine("An error occurred in {0}", job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static IJob GetJob(string jobId)
	        {
	            // Use a Linq select query to get an updated 
	            // reference by Id. 
	            var jobInstance =
	                from j in _context.Jobs
	                where j.Id == jobId
	                select j;
	            // Return the job reference as an Ijob. 
	            IJob job = jobInstance.FirstOrDefault();
	
	            return job;
	        }
	    }
	}
 

<!---HONumber=July15_HO2-->