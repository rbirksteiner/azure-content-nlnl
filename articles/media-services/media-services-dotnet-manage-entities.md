
<properties 
	pageTitle="使用媒體服務 .NET SDK 管理資產和相關的實體" 
	description="深入了解使用 Media Services SDK for .NET 管理資產和相關的實體" 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>


#使用媒體服務 .NET SDK 管理資產和相關的實體

本主題示範如何完成下列的媒體服務管理工作：

- 取得資產參考 
- 取得工作參考 
- 列出所有資產 
- 列出工作和資產 
- 列出所有存取原則 
- 列出所有定位器 
- 刪除資產 
- 刪除工作 
- 刪除存取原則 

##必要條件 

請參閱[設定環境](media-services-set-up-computer.md)

##取得資產參考

常見的工作是在媒體服務中取得現有資產的參考。下列程式碼範例顯示如何根據資產識別碼，從伺服器內容物件的資產集合取得資產參考。下列程式碼範例會使用 Linq 查詢來取得現有 IAsset 物件的參考。

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##取得工作參考

當您使用媒體服務程式碼中的處理工作時，您通常需要根據識別碼取得現有工作的參考。下列程式碼範例顯示如何從「工作」集合取得 IJob 物件的參考。警告：您可能需要在開始長時間執行編碼工作時，取得工作參考，並且需要檢查執行緒上的工作狀態。在這種情況下，當此方法從執行緒傳回時，您需要擷取作業的重新整理的參考。

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

##列出所有資產

當您在儲存體中的資產數目增加時，最好能將資產列出。下列程式碼範例顯示如何在伺服器內容物件上逐一查看「資產」集合。使用各個資產時，程式碼範例也會將它的某些屬性值寫入主控台。例如，每個資產可以包含多個媒體檔案。程式碼範例會寫出與每個資產相關聯的所有檔案。



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##列出工作和資產

重要的相關工作是在媒體服務中列出資產及其相關聯的工作。下列程式碼範例會示範如何列出每個 IJob 物件，然後它會針對每個工作，顯示作業的相關屬性、所有相關工作、所有輸入資產，以及所有輸出資產。此範例中的程式碼可用於許多其他工作。例如，如果您想要從您先前執行的一或多個編碼工作列出輸出資產，此程式碼會示範如何存取輸出資產。當您有輸出資產的參考時，可以透過下載或提供 URL，將內容傳遞給其他使用者或應用程式。

如需傳遞資產之選項的詳細資訊，請參閱[使用 Media Services SDK for .NET 傳遞資產](media-services-deliver-streaming-content.md)。

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##列出所有存取原則

在媒體服務中，您可以定義資產或其檔案的存取原則。存取原則會定義檔案或資產 (存取的類型和持續時間) 的權限。在您的媒體服務程式碼中，通常會藉由建立 IAccessPolicy 物件，然後將其與現有資產產生關聯，來定義存取原則。然後您會建立 ILocator 物件，這可讓您直接存取媒體服務中的資產。本文件系列隨附的 Visual Studio 專案包含數個程式碼範例，示範如何建立及指派存取原則和定位器給資產。

下列程式碼範例顯示如何列出伺服器上的所有存取原則，並顯示每個相關聯的權限的類型。檢視存取原則的另一個實用方式，是列出伺服器上的所有 ILocator 物件，然後針對每個定位器，您可以使用其 AccessPolicy 屬性列出其相關聯的存取原則。

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##列出所有定位器

定位器是 URL，提供存取資產的直接路徑，以及由定位器相關聯的存取原則所定義之對於資產的權限。每個資產在其定位器屬性上可以有與其相關聯的 ILocator 物件的集合。伺服器內容也有定位器集合，其中包含所有定位器。

下列程式碼範例列出伺服器上的所有定位器。針對每個定位器，它會顯示相關資產與存取原則的識別碼。也會顯示資產的權限類型、到期日和完整路徑。

請注意，資產的定位器路徑只是資產的基底 URL。若要建立使用者或應用程式可以瀏覽之個別檔案的直接路徑，您的程式碼必須將特定檔案路徑加入至定位器路徑。如需如何執行此動作的詳細資訊，請參閱[使用 Media Services SDK for .NET 傳遞資產](media-services-deliver-streaming-content.md)主題。

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}


##刪除資產

下列範例會刪除資產。

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##刪除工作

若要刪除工作，您必須檢查工作的狀態，如 State 屬性所示。可以刪除已完成或已取消的工作，而其他某些狀態，例如已佇列、已排程或處理中的工作，必須先取消，然後才能刪除。下列程式碼範例示範刪除工作的方法，方法是檢查工作狀態，然後於狀態為已完成或已取消時刪除工作。此程式碼相依於本主題中的前一節，以取得工作的參考：取得工作參考。

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##刪除存取原則

下列程式碼範例顯示如何根據原則識別碼，取得存取原則的參考，然後刪除原則。

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	

<!---HONumber=July15_HO2-->