<properties 
	pageTitle="如何從媒體服務傳遞串流內容" 
	description="了解如何建立定位器，用來建置串流 URL。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#作法：傳遞串流內容


這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

##概觀

您可以建立隨選串流定位器及建置串流 URL，串流處理調適性位元速率 MP4 集。[為資產編碼](media-services-encode-asset.md)主題說明如何編碼為調適性位元速率 MP4 集。建立定位器之前，您應該如[此](media-services-dotnet-configure-asset-delivery-policy.md)主題所述設定資產傳遞原則。

您也可以使用隨選串流定位器來建置指向可漸進式下載之 MP4 檔案的 URL。

本主題說明如何建立隨選串流定位器以發佈資產及建置 Smooth、MPEG DASH 和 HLS 串流 URL。它也會示範如何建置漸進式下載 URL。
  	 
##建立隨選串流定位器

若要建立隨選串流定位器並取得 URL，您需要執行下列動作：

   1. 定義存取原則。
   2. 建立隨選串流定位器。
   3. 如果您想要串流處理，請取得資產內的串流資訊清單檔案 (.ism)。 
   		
	如果您想要漸進式地下載，請取得資產中的 MP4 檔案名稱。  
   4. 建置資訊清單檔或 MP4 檔案的 URL。 
   

###使用 Media Services .NET SDK 

建置串流 URL

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

此程式碼會輸出：
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

建置漸進式下載 URL

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

此程式碼會輸出：
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

###使用 Media Services .NET SDK 延伸模組

下列程式碼會呼叫 .NET SDK 延伸模組方法，以針對調適性串流建立定位器並產生 Smooth Streaming、HLS 和 MPEG-DASH URL。

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);
 

<!---HONumber=July15_HO1-->