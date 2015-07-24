<properties
	pageTitle="使用 .NET SDK 傳遞點播視訊 (VoD) 使用者入門"
	description="本教學課程會逐步完成使用 .NET　實作含 Azure 媒體服務的點播視訊 (VoD) 內容傳遞應用程式。"
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
	ms.topic="hero-article" 
	ms.date="04/16/2015"
	ms.author="juliako"/>


# 使用 .NET SDK 傳遞點播視訊 (VoD) 使用者入門

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

本教學課程會逐步完成使用 Azure Media Services (AMS) SDK for .NET 實作點播視訊 (VoD) 內容傳遞應用程式。


教學課程中介紹基本的媒體服務工作流程，以及媒體服務開發最常用的程式設計物件和必要工作。完成本教學課程時，您將能夠串流或漸進式下載您已上傳、編碼和下載的範例媒體檔案。

## 必要條件
需要有下列必要條件，才能開始使用 Media Services SDK for .NET 進行開發。

- 作業系統：Windows 7、Windows 2008 R2、Windows 8 或更新版本。
- .NET Framework 4.5 或 .NET Framework 4.0
- Visual Studio 2013、Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express)。


本快速入門會顯示下列工作。

1.  使用入口網站建立媒體服務帳戶
2.  使用入口網站設定串流端點
3.  建立和設定 Visual Studio 專案
5.  連線到媒體服務帳戶
1.  建立新資產並上傳視訊檔案
1.  將來源檔案編碼為一組調適性位元速率 MP4 檔案
1.  設定編碼資產的傳遞原則
1.  發佈資產並取得串流和漸進式下載 URL  
1.  播放您的內容


##使用入口網站建立媒體服務帳戶

1. 在[管理入口網站][]中，依序按一下 [新增]、[媒體服務] 和 [快速建立]。

	![Media Services Quick Create](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. 在 [名稱] 中，輸入新帳戶的名稱。媒體服務帳戶名稱是全部小寫且不含空格的數字或字母，而且長度是 3 到 24 個字元。

3. 在 [區域] 中，選取將用來儲存您媒體服務帳戶之中繼資料記錄的地理區域。只有可用的媒體服務區域才會出現在下拉式清單中。

4. 在 [儲存體帳戶] 中，選取儲存體帳戶以從媒體服務帳戶提供媒體內容的 Blob 儲存體。您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。新的儲存體帳戶會建立於相同的區域中。

5. 如果您已建立新的儲存體帳戶，請在 [NEW STORAGE ACCOUNT NAME] 中輸入儲存體帳戶的名稱。儲存體帳戶名稱的規則會與媒體服務帳戶相同。

6. 按一下表單底部的 [快速建立]。

	您可以在視窗底端的訊息區域監視程序的狀態。

	成功建立帳戶之後，狀態會變更為作用中。

	在頁面底部會顯示 [管理金鑰] 按鈕。當您按一下此按鈕時，會顯示對話方塊，其中具有媒體服務帳戶名稱和主要和次要金鑰。您將需要帳戶名稱和主要金鑰資訊，以便以程式設計方式存取媒體服務帳戶。


	![Media Services Page](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	當您按兩下帳戶名稱時，預設會顯示 [快速入門] 頁面。此頁面可讓您執行一些在入口網站的其他頁面也可以執行的管理工作。例如，您可以從此頁面上傳視訊檔案，也可以從 [內容] 頁面上傳視訊檔案。


##使用入口網站設定串流端點

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 將夾層 (來源) 檔案編碼或轉碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (本教學課程稍後會示範編碼步驟)、  
- 為您計畫從該處傳遞內容的**串流端點**至少取得一個串流單元。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

若要變更串流保留單元的數目，請執行下列動作：

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [媒體服務]。接著，按一下媒體服務的名稱。

2. 選取 [串流端點] 頁面。然後，按一下您要修改的串流端點。

3. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [reserved capacity] 滑動軸。

	![Scale page](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	配置任何新的單元需要大約 20 分鐘的時間才能完成。


	>[AZURE.NOTE]目前，如果串流單元從任何正值到無，可能會停用串流長達一小時。
	>
	> 計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱＜[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)＞。



##建立和設定 Visual Studio 專案

1. 在 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 中，建立新的 C# 主控台應用程式。輸入 [名稱]、[位置] 和 [方案名稱]，然後按一下 [確定]。

2. 使用 [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) Nuget 封裝來安裝 **Azure Media Services .NET SDK 延伸模組**。Media Services .NET SDK 延伸模組是一組延伸方法和協助程式函數，能夠簡化您的程式碼，並輕鬆地以媒體服務開發。安裝這個封裝，也會安裝 **Media Services .NET SDK**，並新增所有其他必要相依性。

3. 加入 System.Configuration 組件的參考。此組件包含用來存取組態檔 (例如，App.config) 的 System.Configuration.ConfigurationManager 類別。

4. 開啟 App.config 檔案 (如果尚未新增，則預設會將檔案新增至您的專案)，並將 *appSettings* 區段新增至此檔案。設定 Azure 媒體服務帳戶名稱和帳戶金鑰的值 (如下列範例所示)。若要取得帳戶名稱和金鑰資訊，請開啟 Azure 管理入口網站，並選取媒體服務帳戶，然後按一下 [**管理金鑰**] 按鈕。


	<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>


5. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 using 陳述式。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. 在 projects 目錄下建立新的資料夾，並複製您想要編碼和串流處理或漸進式下載的 .mp4 或 .wmv 檔案。在此範例中，使用 "C:\\VideoFiles" 路徑。

##連線到媒體服務帳戶

搭配使用媒體服務與 .NET 時，您必須將 **CloudMediaContext** 類別用於大部分的媒體服務程式設計工作：連線到媒體服務帳戶；建立、更新、存取和刪除下列物件：資產、資產檔案、工作、存取原則、定位器等。

將預設 Program 類別覆寫為下列程式碼。此程式碼示範如何讀取 App.config 檔案中的連線值以及如何建立 CloudMediaContext 物件來連線到媒體服務。如需連線到媒體服務的詳細資訊，請參閱使用 [Media Services SDK for .NET 連線到媒體服務](http://msdn.microsoft.com/library/azure/jj129571.aspx)。

**Main** 函數會呼叫未來將在此區段中定義的方法。

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##建立新資產並上傳視訊檔案

在媒體服務中，您可以將數位檔案上傳 (或內嵌) 到資產。**資產**實體可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式字幕檔案 (以及這些檔案的相關中繼資料)。 上傳檔案之後，您的內容會安全地儲存在雲端，以進一步進行處理和串流處理。資產中的檔案稱為**資產檔案**。

下面所定義的 **UploadFile** 方法會呼叫 **CreateFromFile** (定義於 .NET SDK 延伸模組中)。**CreateFromFile** 會建立要在其中上傳所指定來源檔案的新資產。

**CreateFromFile** 方法會採用 **AssetCreationOptions**，以讓您指定下列其中一個資產建立選項：

- **None** - 不使用加密。這是預設值。請注意，使用此選項時，您的內容在傳輸或儲存體中靜止時不會受到保護。如果您計劃使用漸進式下載傳遞 MP4，請使用此選項。
- **StorageEncrypted** - 使用 AES-256 位元加密對您的內容進行本機加密，接著上傳到已靜止加密儲存的 Azure 儲存體。以儲存體加密保護的資產會自動解除加密並在編碼前放置在加密的檔案系統中，並且會在上傳為新輸出資產之前選擇性地重新編碼。儲存體加密的主要使用案例是讓您可以使用強式加密來保護磁碟中靜止的高品質輸入媒體檔。
- **CommonEncryptionProtected** - 如果您要上傳已經使用一般加密或 PlayReady DRM (例如使用 PlayReady DRM 保護的 Smooth Streaming) 加密及保護的內容，請使用這個選項。
- **EnvelopeEncryptionProtected** – 如果您要上傳使用 AES 加密的 HLS，請使用這個選項。請注意，檔案必須已由 Transform Manager 編碼和加密。

**CreateFromFile** 方法也可讓您指定回呼，以報告檔案的上傳進度。

下列範例指定 **None** 做為資產選項。

將下列方法新增至 Program 類別。

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


##將來源檔案編碼為一組調適性位元速率 MP4 檔案

將資產內嵌到媒體服務之後，可以先將媒體編碼、轉碼多工處理、加上浮水印等，再傳遞給用戶端。這些活動會針對多個背景角色執行個體排定和執行，以確保高效能與可用性。這些活動稱為工作，每個工作包含對資產檔案執行實際工作的不可部分完成的工作。

如稍早所提及，使用 Azure 媒體服務時，其中一個最常見的案例是將調適性位元速率串流傳遞給用戶端。媒體服務可以以下列其中一種格式動態封裝一組可調位元速率 MP4 檔案：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/存取使用權)。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼或轉換為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案。  
- 為您計畫從該處傳遞內容的串流端點至少取得一個串流單元。

下列程式碼顯示如何提交編碼工作。此工作 (Job) 包含一項工作 (Task)，指定使用 **Azure Media Encoder** 將夾層檔轉碼為一組調適性位元速率 MP4。此程式碼會提交工作，並等到工作完成。

工作完成之後，就可以串流處理資產，或漸進式下載轉碼後所建立的 MP4 檔案。請注意，您不需要擁有任何串流單元，即可漸進式下載 MP4 檔案。


將下列方法新增至 Program 類別。

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);

		Console.WriteLine("Submitting transcoding job...");


	    // Submit the job and wait until it is completed.
	    job.Submit();

	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;

	    Console.WriteLine("Transcoding job finished.");

	    IAsset outputAsset = job.OutputMediaAssets[0];

	    return outputAsset;
	}

##設定編碼資產的傳遞原則

媒體服務內容傳遞工作流程中的其中一個步驟是設定資產傳遞原則。資產傳遞原則設定包括一些事項：什麼通訊協定可以用來傳遞資產 (例如，MPEG DASH、HLS、HDS、Smooth Streaming 或 all)，您是否想要以動態方式加密資產，以及加密方法 (信封或一般加密)。

下列 **ConfigureClearAssetDeliveryPolicy** 方法指定不套用動態加密，以及使用下列任何通訊協定來傳遞資料流：MPEG DASH、HLS 和 Smooth Streaming 通訊協定。

將下列方法新增至 Program 類別。

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption,
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }


##發佈資產並取得串流和漸進式下載 URL

若要串流處理或下載資產，您必須先建立定位器來「發佈」它。定位器可以存取資產中所含的檔案。媒體服務支援兩種類型的定位器：OnDemandOrigin 定位器，用於串流媒體 (例如，MPEG DASH、HLS 或 Smooth Streaming) 和存取簽章 (SAS) 定位器，用來下載媒體檔案。

建立定位器之後，您便可以建立用來串流或下載檔案的 URL。


Smooth Streaming 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

用來下載檔案的 SAS URL 具有下列格式：

	{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK 延伸模組提供便利的協助程式方法，來傳回所發佈資產的格式化 URL。

下列程式碼使用 .NET SDK 延伸模組來建立定位器、取得串流，以及漸進式下載 URL。此程式碼也會顯示如何將檔案下載到本機資料夾。

將下列方法新增至 Program 類別。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get progressive download URLs for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the progressive download URLs.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##播放您的內容  

執行上一節中所定義的程式之後，主控台視窗中會顯示與下面類似的 URL。

調適性串流 URL：

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

漸進式下載 URL (音訊和視訊)。

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


若要串流您的視訊，請使用 [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

若要測試漸進式下載，請將 URL 貼入瀏覽器 (例如，IE、Chrome、Safari)。

##後續步驟

深入了解建置點播視訊應用程式的相關資訊[建置 VoD 應用程式](media-services-video-on-demand-workflow.md)

###其他資源
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理入口網站]: http://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->