<properties 
	pageTitle="如何使用 .NET 檢查工作進度" 
	description="了解如何使用事件處理常式程式碼來追蹤工作進度及傳送狀態更新。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/25/2015" 
	ms.author="juliako"/>

#作法：檢查工作進度

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。

執行作業時，您通常需要設法追蹤作業進度。[定義 StateChanged 事件處理常式](#statechange_event_handler)或[使用 Azure 佇列儲存體監視媒體服務工作通知](#check_progress_with_queues)，即可檢查進度。本主題描述這兩種方法。

##<a id="statechange_event_handler"></a>定義 StateChanged 事件處理常式來監視工作進度

下列程式碼定義 StateChanged 事件處理常式。此事件處理常式可追蹤作業進度，並根據狀態來提供更新的狀態。程式碼也定義 LogJobStop 方法。此協助程式方法會記錄錯誤詳細資料。

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
	            LogJobStop(job.Id);
	            break;
	        default:
	            break;
	    }
	}
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
	    // Log job errors if they exist.  
	    if (job.State == JobState.Error)
	    {
	        builder.Append("Error Details: \n");
	        foreach (ITask task in job.Tasks)
	        {
	            foreach (ErrorDetail detail in task.ErrorDetails)
	            {
	                builder.AppendLine("  Task Id: " + task.Id);
	                builder.AppendLine("    Error Code: " + detail.Code);
	                builder.AppendLine("    Error Message: " + detail.Message + "\n");
	            }
	        }
	    }
	    builder.AppendLine("***************************\n");
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



##<a id="check_progress_with_queues"></a>使用 Azure 佇列儲存體監視媒體服務工作通知

Microsoft Azure 媒體服務能夠在處理媒體工作時，傳送通知訊息給 [Azure 佇列儲存體](../storage-dotnet-how-to-use-queues.md#what-is)。本主題示範如何從佇列儲存體取得這些通知訊息。

使用者可以從世界各個角落存取之前已傳送至佇列儲存體的訊息。Azure 佇列訊息架構十分可靠，而且具有高擴充性。建議利用其他方法輪詢佇列儲存體。

舉一個常見的接聽媒體服務通知案例：您正在設計一套內容管理系，而且當程式碼設計好之後，這套系統需要執行其他一些工作 (例如, 觸發工作流程的下一個步驟或者發佈內容)。

###注意事項

當您設計的媒體服務應用程式會使用 Azure 儲存體佇列時，請考慮下列幾點。

- 佇列服務不保證會按照先進先出 (FIFO) 的順序傳遞訊息。如需詳細資訊，請參閱 [Azure 佇列和 Azure 服務匯流排佇列的比較和對比](https://msdn.microsoft.com/library/azure/hh767287.aspx)。
- Azure 儲存體佇列不是推播服務；您必須輪詢佇列。 
- 您可以有任意數目的佇列。如需詳細資訊，請參閱[佇列服務 REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)。
- Azure 儲存體佇列存在某些限制，如需具體的描述，請參閱以下文章：[Azure 佇列和 Azure 服務匯流排佇列 - 比較和對比](https://msdn.microsoft.com/library/azure/hh767287.aspx)。

###程式碼範例

本節的程式碼會執行下列動作：

1. 定義一個會對應至通知訊息格式的 **EncodingJobMessage** 類別。程式碼會將那些從佇列接收到的訊息還原序列化，然後變成 **EncodingJobMessage** 類型的物件。
1. 從 app.config 檔案載入媒體服務和儲存體帳戶資訊。使用此資訊來建立 **CloudMediaContext** 和 **CloudQueue** 物件。
1. 建立一個會接收編碼工作相關通知訊息的佇列。
1. 建立一個會對應到佇列的通知端點。
1. 將通知端點附加至工作，然後提交編碼工作。您可以將多個通知端點附加至工作。
1. 在這個範例中，我們只想知道工作的最終狀態，所以我們將 **NotificationJobState.FinalStatesOnly** 傳遞給 **AddNew** 方法。 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. 如果您傳遞 NotificationJobState.All，表示您想取得所有的狀態變更通知：[已排入佇列] -> [已排程] -> [處理中] -> [已完成]。不過，如先前所述，Azure 儲存體佇列服務不保證會按照順序傳遞。您可以使用 Timestamp 屬性 (定義在以下範例中的 EncodingJobMessage 類型) 來排序訊息。您可能會收到重複的通知訊息。請使用 ETag 屬性 (定義在 EncodingJobMessage 類型上) 來檢查重複的通知訊息。請注意，某些狀態變更通知也有可能被略過。 
1. 每隔 10 秒檢查佇列一次，等候工作進入「已完成」狀態。處理好訊息之後，請予以刪除。
1. 刪除佇列和通知端點。

>[AZURE.NOTE]要想監視工作的狀態，建議您接聽通知訊息，如下列範例所示。
>
>或者，使用 **IJob.State** 屬性檢查工作狀態。請注意，**IJob** 的狀態尚未設定成 [**已完成**] 之前，您可能會先收到一則有關工作已完成的通知訊息。**IJob.State** 屬性會延遲片刻再反映正確的狀態。

	
	using System;
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Web;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;
	using System.Runtime.Serialization.Json;
	
	namespace JobNotification
	{
	    public class EncodingJobMessage
	    {
	        // MessageVersion is used for version control. 
	        public String MessageVersion { get; set; }
	    
	        // Type of the event. Valid values are 
	        // JobStateChange and NotificationEndpointRegistration.
	        public String EventType { get; set; }
	    
	        // ETag is used to help the customer detect if 
	        // the message is a duplicate of another message previously sent.
	        public String ETag { get; set; }
	    
	        // Time of occurrence of the event.
	        public String TimeStamp { get; set; }
	
	        // Collection of values specific to the event.
	
	        // For the JobStateChange event the values are:
	        //     JobId - Id of the Job that triggered the notification.
	        //     NewState- The new state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	        //     OldState- The old state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	
	        // For the NotificationEndpointRegistration event the values are:
	        //     NotificationEndpointId- Id of the NotificationEndpoint 
	        //          that triggered the notification.
	        //     State- The state of the Endpoint. 
	        //          Valid values are: Registered and Unregistered.
	
	        public IDictionary<string, object> Properties { get; set; }
	    }
	
	    class Program
	    {
	        private static CloudMediaContext _context = null;
	        private static CloudQueue _queue = null;
	        private static INotificationEndPoint _notificationEndPoint = null;
	
	        private static readonly string _singleInputMp4Path =
	            Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
	
	        static void Main(string[] args)
	        {
	            // Get the values from app.config file.
	            string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	            string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	            string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
	
	
	            string endPointAddress = Guid.NewGuid().ToString();
	
	            // Create the context. 
	            _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
	
	            // Create the queue that will be receiving the notification messages.
	            _queue = CreateQueue(storageConnectionString, endPointAddress);
	
	            // Create the notification point that is mapped to the queue.
	            _notificationEndPoint = 
	                    _context.NotificationEndPoints.Create(
	                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
	
	
	            if (_notificationEndPoint != null)
	            {
	                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
	                WaitForJobToReachedFinishedState(job.Id);
	            }
	
	            // Clean up.
	            _queue.Delete();      
	            _notificationEndPoint.Delete();
	       }
	
	
	        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
	        {
	            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
	
	            // Create the queue client
	            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
	
	            // Retrieve a reference to a queue
	            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
	
	            // Create the queue if it doesn't already exist
	            queue.CreateIfNotExists();
	
	            return queue;
	        }
	 
	
	        // Upload a video file, and encode to Smooth Streaming format
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
	                processor,
	                "H264 Smooth Streaming 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Add a notification point to the job. You can add multiple notification points.  
	            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
	                _notificationEndPoint);
	
	            job.Submit();
	
	            return job;
	        }
	
	        public static void WaitForJobToReachedFinishedState(string jobId)
	        {
	            int expectedState = (int)JobState.Finished;
	            int timeOutInSeconds = 600;
	
	            bool jobReachedExpectedState = false;
	            DateTime startTime = DateTime.Now;
	            int jobState = -1;
	
	            while (!jobReachedExpectedState)
	            {
	                // Specify how often you want to get messages from the queue.
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	
	                foreach (var message in _queue.GetMessages(10))
	                {
	                    using (Stream stream = new MemoryStream(message.AsBytes))
	                    {
	                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
	                        settings.UseSimpleDictionaryFormat = true;
	                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
	                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
	
	                        Console.WriteLine();
	
	                        // Display the message information.
	                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
	                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
	                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
	                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
	                        foreach (var property in encodingJobMsg.Properties)
	                        {
	                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
	                        }
	
	                        // We are only interested in messages 
	                        // where EventType is "JobStateChange".
	                        if (encodingJobMsg.EventType == "JobStateChange")
	                        {
	                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
	                            if (JobId == jobId)
	                            {
	                                string oldJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
	                                string newJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;
	
	                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
	                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
	
	                                if (newJobState == (JobState)expectedState)
	                                {
	                                    Console.WriteLine("job with Id: {0} reached expected state: {1}", 
	                                        jobId, newJobState);
	                                    jobReachedExpectedState = true;
	                                    break;
	                                }
	                            }
	                        }
	                    }
	                    // Delete the message after we've read it.
	                    _queue.DeleteMessage(message);
	                }
	
	                // Wait until timeout
	                TimeSpan timeDiff = DateTime.Now - startTime;
	                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
	                if (timedOut)
	                {
	                    Console.WriteLine(@"Timeout for checking job notification messages, 
	                                        latest found state ='{0}', wait time = {1} secs",
	                        jobState,
	                        timeDiff.TotalSeconds);
	
	                    throw new TimeoutException();
	                }
	            }
	        }
	   
	        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
	        {
	            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
	                assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading of {0}", assetFile.Name);
	
	            return asset;
	        }
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}

上述範例會產生下列輸出。您的值會不一樣。
	
	Created assetFile BigBuckBunny.mp4
	Upload BigBuckBunny.mp4
	Done uploading of BigBuckBunny.mp4
	
	EventType: NotificationEndPointRegistration
	MessageVersion: 1.0
	ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
	TimeStamp: 2013-05-14T20:22:37
	    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
	    State: Registered
	    Name: dde957b2-006e-41f2-9869-a978870ac620
	    Created: 2013-05-14T20:22:35
	
	EventType: JobStateChange
	MessageVersion: 1.0
	ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
	TimeStamp: 2013-05-14T20:24:40
	    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
	    JobName: My MP4 to Smooth Streaming encoding job
	    NewState: Finished
	    OldState: Processing
	    AccountName: westeuropewamsaccount
	job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
	State: Finished
	
 

<!---HONumber=62-->