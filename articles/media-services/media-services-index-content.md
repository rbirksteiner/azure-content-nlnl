<properties 
	pageTitle="使用 Azure Media Indexer 編輯媒體檔案索引" 
	description="Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。本主題說明如何使用 Media Indexer。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="juliako"/>


# 使用 Azure Media Indexer 編輯媒體檔案索引

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。

Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。您可以處理一份媒體檔或是批次處理多個媒體檔案。

>[AZURE.NOTE]在編製內容索引時，請務必使用語音非常清楚的媒體檔案 (不含背景音樂、噪音、效果或麥克風雜音)。適當內容的一些範例有：錄製的會議、演講或簡報。下列內容可能不適合用來編製索引：電影、電視節目、任何具有混合音訊與音效的內容、錄製效果不良有背景噪音 (雜音) 的內容。


編製索引工作會為每個要編製索引的檔案產生四個輸出：

- SAMI 格式的隱藏式字幕檔案。
- Timed Text Markup Language (TTML) 格式的隱藏式字幕檔案。

	SAMI 和 TTML 都包含稱為 Recognizability 的標記，它會根據來源視訊中的語音可辨識度來為索引工作評分。您可以使用 Recognizability 值篩選輸出檔的實用性。較低的分數表示由於音訊品質所致的不良索引結果。
- 關鍵字檔案 (XML)。
- 與 SQL Server 搭配使用的音訊編製索引 blob 檔案 (AIB)。
	
	如需詳細資訊，請參閱[搭配 Azure Media Indexer 和 SQL Server 使用 AIB 檔案](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)。


本主題示範如何建立索引工作**建立資產的索引**和**建立多個檔案的索引**。

如需最新的 Azure Media Indexer 更新，請參閱[媒體服務部落格](http://azure.microsoft.com/blog/topics/media-services/)。

##針對索引工作使用組態和資訊清單檔

您可以使用工作組態來為索引工作指定更多詳細資料。例如，您可以指定要用於媒體檔案的中繼資料。語言引擎會使用此中繼資料來擴充其詞彙，並大幅提升語音辨識準確度。

您也可以使用資訊清單檔，一次處理多個媒體檔案。

如需詳細資訊，請參閱 [Azure Media Indexer 的工作預設](https://msdn.microsoft.com/library/azure/dn783454.aspx)。

##編製資產索引

下列方法會將媒體檔案上傳為資產，並建立工作來編製資產索引。

請注意，如果未指定組態檔案，將會使用所有預設設定編製媒體檔案的索引。
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
###<a id="output_files"></a>輸出檔案

索引工作會產生下列輸出檔案。檔案會儲存在第一個輸出資產。


<table border="1">
<tr><th>檔案名稱</th><th>說明</th></tr>
<tr><td>InputFileName.aib </td>
<td>音訊索引 blob 檔案。<br/><br/>
音訊索引 Blob (AIB) 檔案是二進位檔案，可以使用全文檢索搜尋在 Microsoft SQL Server 中搜尋。AIB 檔比簡單的字幕檔案強大，因為它包含每個字的替代字，允許更豐富的搜尋經驗。
<br/>
<br/>
它需要在執行 Microsoft SQL 2008 或更新版本的電腦上安裝 Indexer SQL 附加元件。使用 Microsoft SQL Server 全文檢索搜尋來搜尋 AIB 可以比搜尋 WAMI 產生之隱藏式字幕檔案提供更正確的搜尋結果。這是因為 AIB 包含發音類似的替代字，而隱藏式字幕檔案則包含音訊每一節的最高信賴字。如果搜尋說的話重要性最高，則建議一起使用 AIB 和 Microsoft SQL Server。
<br/><br/>
若要下載附加元件，請按一下 <a href="http://aka.ms/indexersql">Azure Media Indexer SQL 附加元件</a>。
<br/><br/>
也可以利用其他搜尋引擎，例如 Apache Lucene/Solr，只根據隱藏式字幕和關鍵字 XML 檔案編製視訊的索引，但這會導致搜尋結果較不正確。</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>SAMI 和 TTML 格式的隱藏字幕 (CC) 檔案。
<br/><br/>
它們可以用來讓具有聽力障礙的人存取音訊和視訊檔案。
<br/><br/>
SAMI 和 TTML 都包含稱為 <b>Recognizability</b> 的標記，它會根據來源視訊中的語音可辨識度來為索引工作評分。您可以使用 <b>Recognizability</b> 值篩選輸出檔的實用性。較低的分數表示由於音訊品質所致的不良索引結果。</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>關鍵字檔案。
<br/><br/>
關鍵字檔案是 XML 檔案，其中包含從語音內容擷取的關鍵字，以及頻率和位移的資訊。
<br/><br/>
檔案可以用於數個用途，例如執行語音分析，或公開至如 Bing、Google 或 Microsoft SharePoint 等搜尋引擎，使媒體檔案更容易被找到，或用來提供更多相關的廣告。</td></tr>
</table>

如果不是所有輸入媒體檔案都成功編製索引，則索引工作將會失敗，錯誤碼為 4000。如需詳細資訊，請參閱[錯誤碼](#error_codes)。

##編製多個檔案的索引

下列方法會將多個媒體檔案上傳為資產，並建立工作來批次編製這些檔案的索引。

會建立 .lst 副檔名的資訊清單檔，並上傳到資產。資訊清單檔案包含所有資產檔案的清單。如需詳細資訊，請參閱 [Azure Media Indexer 的工作預設](https://msdn.microsoft.com/library/azure/dn783454.aspx)。
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###輸出檔案

當有多個輸入媒體檔案時，WAMI 會產生工作輸出的資訊清單檔，名為 'JobResult.txt'。針對每個輸入媒體檔案，產生的 AIB、SAMI、TTML 以及關鍵字檔案會循序編號，如下所示。

如需輸出檔案的描述，請參閱[輸出檔案](#output_files)。


<table border="1">
<tr><th>檔案名稱</th><th>說明</th></tr>
<tr><td>JobResult.txt</td>
<td>輸出資訊清單
<br/><br/>以下是輸出資訊清單檔案 (JobResult.txt) 的格式。
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>錯誤</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
每個資料列都代表一個輸入媒體檔案：
<br/><br/>
InputFile：資產檔案名稱或輸入媒體檔案的 URL。
<br/><br/>
Alias：對應輸出檔案名稱。
<br/><br/>
MediaLength：輸入媒體檔案的長度 (秒)。如果此輸入發生錯誤，可能是 0。
<br/><br/>
Error：表示這個媒體檔案是否已成功編製索引。0 代表成功，其他則代表失敗。如需具體的錯誤資訊，請參閱<a href="#error_codes">錯誤碼</a>。
</td></tr>
<tr><td>Media_1.aib </td>
<td>檔案 #0 - 音訊索引 blob 檔案。</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>檔案 #0 - SAMI 和 TTML 格式的隱藏式字幕 (CC) 檔案。</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>檔案 #0 - 關鍵字檔案。</td></tr>
<tr><td>Media_2.aib </td>
<td>檔案 #1 - 音訊索引 blob 檔案。</td></tr>
</table>

如果不是所有輸入媒體檔案都成功編製索引，則索引工作將會失敗，錯誤碼為 4000。如需詳細資訊，請參閱[錯誤碼](#error_codes)。

###部分成功的工作

如果不是所有輸入媒體檔案都成功編製索引，則索引工作將會失敗，錯誤碼為 4000。如需詳細資訊，請參閱[錯誤碼](#error_codes)。


會產生 (與成功工作) 相同的輸出。您可以參閱輸出資訊清單檔，根據 Error 欄位值找出哪些輸入檔案失敗。針對失敗的輸入檔案，將不會產生結果的 AIB、SAMI、TTML 和關鍵字檔案。


### <a id="error_codes"></a>錯誤碼


<table border="1">
<tr><th>代碼</th><th>名稱</th><th>可能的原因</th></tr>
<tr><td>2000</td><td>無效的組態</td><td>無效的組態</td></tr>
<tr><td>2001</td><td>無效的輸入資產</td><td>遺漏輸入資產或資產是空的。</td></tr>
<tr><td>2002</td><td>無效的資訊清單</td><td>資訊清單是空的或資訊清單包含無效的項目。</td></tr>
<tr><td>2003</td><td>無法下載媒體檔案</td><td>資訊清單檔中的 URL 無效。</td></tr>
<tr><td>2004</td><td>不支援的通訊協定</td><td>不支援媒體 URL 的通訊協定。</td></tr>
<tr><td>2005</td><td>不支援的檔案類型</td><td>不支援輸入媒體檔案類型。</td></tr>
<tr><td>2006</td><td>太多輸入檔案</td><td>輸入資訊清單中有超過 10 個檔案。</td></tr>
<tr><td>3000</td><td>無法解碼媒體檔案</td>
<td>不支援的媒體轉碼器。
<br/>或<br/>
損毀的媒體檔案。
<br/>或<br/>
輸入媒體沒有音訊資料流。</td></tr>
<tr><td>4000</td><td>批次編製索引已部分成功</td><td>某些輸入媒體檔案無法編製索引。如需詳細資訊，請參閱<a href="output_files">輸出檔案</a>。</td></tr>
<tr><td>其他</td><td>內部錯誤</td><td>請連絡技術支援小組。</td></tr>
</table>


##<a id="supported_languages"></a>支援的語言

目前支援英文和西班牙文。如需詳細資訊，請參閱 [Azure Media Indexer (西班牙文)](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。

##相關連結

[搭配 Azure Media Indexer 和 SQL Server 使用 AIB 檔案](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=July15_HO2-->