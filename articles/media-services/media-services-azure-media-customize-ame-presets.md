<properties 
	pageTitle="藉由自訂工作預設來處理編碼工作" 
	description="Azure 媒體服務編碼器可讓您將自訂的預設檔案傳遞至 Azure 媒體編碼器。本主題示範如何自訂預設檔案，以達成下列工作：將影像重疊至現有的影片，控制編碼器產生的輸出檔案名稱，編結影片。" 
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

#藉由自訂工作預設來處理編碼工作 

Azure 媒體服務編碼器可讓您將自訂的預設檔案傳遞至 Azure 媒體編碼器。本主題示範如何自訂預設檔案，以達成下列工作：

- 將影像重疊至現有的影片， 
- 控制編碼器產生的輸出檔案名稱， 
- 編結影片
- 編碼多半為語音的簡報。

##控制 Azure 媒體編碼器輸出檔名 

依照預設，Azure 媒體編碼器會透過結合輸入資產與編碼處理程序的多種屬性來建立輸出檔案名稱。每種屬性都是使用下面所述的巨集來識別。

以下是命名輸出檔案時可用之巨集的完整清單：音訊位元速率 - 編碼音訊時使用的位元速率 (kbps)。

- 音訊轉碼器 - 編碼音訊時使用的轉碼器，有效值為：AAC、WMA 及 DDP
- 聲道計數 - 編碼的音訊聲道數，有效值為：1、2 或 6
- 預設副檔名 – 預設的檔案副檔名 
- 語言 - 代表音訊中使用之語言的 BCP-47 語言代碼。此項目目前的預設值為 "und"。 
- 原始檔案名稱 - 上傳至 Azure 儲存體的檔案的名稱
- StreamId – 由預設檔案中 <StreamInfo> 元素的 streamID 屬性定義的資料流識別碼 
- 視訊轉碼器 - 編碼時使用的轉碼器，有效值為：H264 和 VC1
- 視訊位元速率 - 編碼視訊時使用的位元速率 (kbps)

這些巨集可以以任何排列方式組合，以控制媒體服務編碼器所產生之檔案的名稱。例如，預設命名慣例為：

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

檔案命名慣例是使用 [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx) 元素的 DefaultMediaOutputFileName 屬性指定。例如：

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

編碼器將會在每個巨集之間插入底線，例如上述設定會產生像這樣的檔案名稱：MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.


##建立疊加層

Azure 媒體服務編碼器可讓您重疊影像 (jpg、bmp、gif、tif)、影片或音軌 (*.wma、*.mp3、*.wav) 到現有的影片。這項功能類似於 Expression Encoder 4 (Service Pack 2) 的功能。

###與媒體服務編碼器重疊

您可以指定呈現重疊的時機、呈現重疊的持續時間，以及影像/影片重疊出現在螢幕的哪個位置。您也可以讓重疊淡入及/或淡出。重疊的音訊/視訊檔案可以包含在多個資產或單一資產中。重疊是由傳送至編碼器的預設 XML 控制。如需預設結構描述的完整說明，請參閱「Azure 媒體編碼器結構描述」。重疊在 <MediaFile> 元素中指定，如下列預設程式碼片段所示：

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###影片或影像重疊的預設

重疊可來自單一或多個資產。使用多個資產建立影片重疊時，重疊檔名是在 OverlayFileName 屬性中以 %n% 語法指定，其中 n 是編碼工作之輸入資產的以零起始的索引。當使用單一資產建立影片重疊時，重疊檔名是直接指定至 OverlayFileName 屬性，如下列預設程式碼片段所示：

範例 1：

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

範例 2：

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

影片重疊的位置和大小是由 OverlayRect 屬性控制。要重疊的內容會調整大小以符合此矩形範圍。不透明度是由 OverlayOpacity 屬性控制。有效值為 0.0-1.0，其中 1.0 為 100% 不透明。重疊會在 OverlayStartTime 屬性指定的時間顯示，並且會在 OverlayEndTime 屬性指定的時間結束。OverlayStartTime 和 OverlayEndTime 皆應該落在來源影片的時間軸內。如需有關每個重疊特定屬性的詳細資訊，請參閱「Azure Media Encoder 結構描述」。

###音訊重疊的預設

舉例來說，音訊重疊可以是任何支援的音訊檔案格式。如需支援的音訊檔案格式的完整清單，請參閱「媒體服務編碼器支援的格式」。音訊重疊也會在 <MediaFile> 元素中指定，如下列預設程式碼片段所示：

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

對於多個資產中儲存的音訊重疊，音訊重疊檔名是在 AudioOverlayFileName 屬性中以 %n% 語法指定，其中 n 是編碼工作之輸入資產集合的以零起始的索引。對於單一資產中儲存的音訊重疊，重疊檔名會直接在 AudioOverlayFileName 屬性中指定。AudioOverlayLayoutMode 會決定呈現音訊重疊的時機。當設為 "WholeSequence" 時，音訊資料軌會在視訊的整個持續期間呈現。當設定為"Custom" 時，AudioOverlayStartTime 和 AudioOverlayEndTime 屬性會決定音訊重疊開始及結束的時機。OverlayStartTime 和 OverlayEndTime 皆應該落在來源影片的時間軸內。如需有關所有音訊重疊屬性的詳細資訊，請參閱「Azure Media Encoder 結構描述」。音訊重疊會與視訊重疊結合，如下列預設程式碼片段所示：
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###提交具有重疊的工作

一旦您建立預設檔案，您必須執行下列動作：

- 上傳您的資產
- 載入預設組態 (注意：下列程式碼會假設上述的預設。
- 建立工作
- 取得媒體服務編碼器的參考
- 建立工作，指定輸入資產、預設組態、媒體編碼器以及輸出資產的集合
- 提交工作

下列程式碼片段顯示如何執行這些步驟：

	static public void CreateOverlayJob()
	{
	_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to overlay
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
	
	       // Load the preset configuration
	       string presetFileName = "OverlayPreset.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME overlay job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
	
	    // Add the input assets
	           task.InputAssets.Add(inputAsset1);
	           task.InputAssets.Add(inputAsset2);
	           task.InputAssets.Add(inputAsset3);
	
	     // Add the output asset
	            task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
	
	     // Submit the job
	            job.Submit();
	}



>[AZURE.NOTE]為了簡單起見，此程式碼片段會循序載入每個資產。在實際執行環境中，資產會大量載入。如需大量上傳多個資產的詳細資訊，請參閱[使用 Media Services SDK for .NET 大量嵌入資產](media-services-dotnet-upload-files.md#ingest_in_bulk)。

如需完整範例程式碼，請參閱[使用媒體服務編碼器建立重疊](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47)。

###錯誤狀況

當編輯預設字串時，您必須確定下列各項：

- 對於影片/影像重疊，重疊矩形必須完全符合來源影片的維度
- 重疊的開始和結束時間應該在來源影片的時間軸內
- 如果預設 XML 包含 ?OverlayFileName=”%n%” 的參考，則工作的 InputAssets 集合應該包含至少 n+1 個資產



##編結影片區段

媒體服務編碼器提供將一組影片編結在一起的支援。影片可以端對端編結在一起，或者您可以指定要編結在一起的一部影片或兩部影片的部分。編結的結果是單一輸出資產，其中包含輸入資產的指定影片。要編結的影片可以包含在多個資產或單一資產中。編結是由傳送至編碼器的預設 XML 控制。如需預設結構描述的完整說明，請參閱 [Azure Media Encoder 結構描述](https://msdn.microsoft.com/library/azure/dn584702.aspx)。

###使用媒體服務編碼器編結

編結是在 <MediaFile> 元素中控制，如下列預設程式碼片段所示：
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

對於要編結的每個影片，<Source> 元素會加入至 <Sources> 元素。每個 <Source> 元素都包含 <Clips> 元素。每個 <Clips> 元素都包含一或多個 <Clip> 元素，藉由指定開始和結束時間，來指定要將多少影片編結至輸出資產。<Source> 項目會參考其在上面作用的資產。參考的格式取決於要編結的影片是在個別資產或單一資產中。如果您想要編結整個影片，只要省略 <Clips> 元素即可。

###從多個資產編結影片

當從多個資產編結影片時，會針對 <Source> 元素的 MediaFile 屬性使用以零起始的索引，來識別 <Source> 元素對應至哪個資產。未指定零索引，未指定 MediaFile 屬性的 <Source> 元素會參考第一個輸入資產。所有其他 <Source> 元素都必須使用 %n% 語法，指定其參考之輸入資產的以零起始的索引，其中 n 是輸入資產的以零起始的索引。在上述範例中，第一個 <Source> 元素會指定第一個輸入資產，第二個 <Source> 元素會指定第二個輸入資產，依此類推。輸入資產不需要依序進行參照，例如：
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

此範例將第二個、第一個及第三個輸入資產的部分編結在一起。請注意，由於每個影片是由以零起始的索引所參考，所以很有可能會將具有相同名稱的兩段影片編結在一起。一旦您建立預設檔案，您必須執行下列動作：
 
- 上傳您的資產
- 載入預設組態
- 建立工作
- 取得媒體服務編碼器的參考
- 建立工作，指定輸入資產、預設組態、媒體編碼器以及輸出資產
- 提交工作

下列程式碼片段顯示如何執行這些步驟：
	
	static public void StitchWithMultipleAssets()
	{
    		_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to stitch
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
	
	       // Load the preset configuration
	       string presetFileName = "StitchingWithMultipleAssets.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
	
			// Add the input assets
	       task.InputAssets.Add(inputAsset1);
	       task.InputAssets.Add(inputAsset2);
	       task.InputAssets.Add(inputAsset3);
	
	       // Add the output asset
	        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
	
	       // Submit the job
	        job.Submit();
	} 


為了簡單起見，此程式碼片段會循序載入每個資產。在實際執行環境中，資產會大量載入。如需大量上傳多個資產的詳細資訊，請參閱[使用 Media Services SDK for .NET 大量嵌入資產](media-services-dotnet-upload-files.md#ingest_in_bulk)。如需完整範例程式碼，請參閱[使用媒體服務編碼器編結](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203)。

###從單一資產編結影片

在單一資產內編結影片時，每個影片必須有唯一的名稱。影片是使用 MediaFile 屬性指定，使用檔名做為屬性的值。例如：
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

此預設會將 video1.mp4、video2.wmv 及 video3.wmv 的部分編結至輸出資產。建立工作 (Job) 和工作 (Task) 與從多個資產編結影片相同，您只需要上傳單一資產，如下列程式碼所示：

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);
        _context = new CloudMediaContext(_accountName, _accountKey);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##編碼多半為語音的簡報或音訊串流
 
當編碼其音訊資料軌多半為語音的影片時，預設的編碼器預設可能讓背景噪音在編碼資產中放大。這種行為是因 NormalizeAudio 屬性設為 true 所造成。

###編碼多半為語音的簡報

若要防止放大背景噪音，請執行下列動作：

1. 將您使用之編碼器預設的內容複製到 XML 檔案。編碼器預設可以在下列位置找到：Azure Media Encoder 結構描述
1. 刪除 NormalizeAudio 屬性，該屬性可以在 <MediaFile> 元素底下預設檔案的頂端附近找到：
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. 將修改過的預設檔案儲存到本機硬碟機，並且使用如下的程式碼以自訂預設進行編碼：
	
	// Upload file and create asset IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\\TEMP\\Original.mp4");
	 
	string inputPresetFile = @"C:\\TEMP\\H264 Broadband 720p NoAudioNorm.xml"; string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
	 
	IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	// Get a media processor reference, and pass to it the name of the processor to use for the specific task.IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
	 
	// Read the configuration data into a string. string configuration = File.ReadAllText(inputPresetFile);
	 
	// Create a task with the encoding details, using a string preset.ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration, Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	 
	// Specify the input asset to be encoded. task.InputAssets.Add(asset);
	 
	// Add an output asset to contain the results of the job. task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
	 
	// Launch the job. job.Submit();

##另請參閱

[Azure Media Encoder XML 結構描述](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=July15_HO2-->