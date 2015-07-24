<properties 
	pageTitle="如何使用 Azure 管理入口網站透過 Azure 媒體服務管理媒體內容" 
	description="了解如何在 Azure 媒體服務中管理您的媒體內容。這包括：上傳、索引、編碼、加密，以及發佈。" 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


# 使用 Azure Management Portal 利用 Azure 媒體服務管理內容

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。

本主題說明如何使用 Azure 管理入口網站在媒體服務帳戶中管理媒體內容。

本主題示範如何直接從入口網站執行下列內容作業：

- 檢視內容資訊，例如發佈狀態、發佈 URL、大小、上次更新的日期時間，以及資產是否加密。
- 上傳新的內容
- 編製內容索引
- 編碼內容
- 加密
- 發佈/取消發佈內容
- 播放內容


##<a id="upload"></a>做法：上傳內容 


1. 在[管理入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2. 選取 [內容] 頁面。 
3. 按一下頁面或入口網站底部的 [上傳] 按鈕。 
4. 在 [Upload content] 對話方塊中，瀏覽到想要的資產檔案。按一下檔案，然後按一下 [開啟] 或按 **Enter** 鍵。

	![UploadContentDialog][uploadcontent]

5. 在 [Upload Content] 對話方塊中，按一下核取按鈕以接受 [File and Content Name]。
6. 上傳隨即開始，而您可以在入口網站底部追蹤進度。  

	![JobStatus][status]

上傳完成之後，您將會看到新資產列在 [內容] 清單中。依照慣例，名稱的結尾會附加 "**-Source**"，以協助追蹤新的內容作為編碼工作的來源內容。

![ContentPage][contentpage]

如果上傳程序停止之後檔案大小值未更新，請按 [Sync Metadata] 按鈕。這樣會以儲存體中的實際檔案大小同步處理資產檔案大小，並重新整理 [內容] 頁面上的值。

##<a id="index"></a>做法：建立內容索引

Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。您可以使用管理入口網站並遵循以下所示的步驟，來編製內容的索引。不過，如果您想要更充分掌控哪些檔案和如何執行索引工作，可以使用適用於 .NET 或 REST API 的媒體服務 SDK。如需詳細資訊，請參閱[使用 Azure Media Indexer 編製媒體檔案的索引](media-services-index-content.md)。

下列步驟示範如何使用管理入口網站來編製內容索引。

1. 選取您想要編製索引的檔案。如果支援此檔案類型的索引，[內容] 頁面上底部的 [處理] 按鈕將會啟用。
1. 按下 [處理] 按鈕。
2. 在**處理**對話方塊選擇 **Azure Media Indexer** 處理器。
3. 然後，在 [處理] 對話方塊，填寫輸入媒體檔案的詳細**標題**和**描述**資訊。
	
	![Process][process]

##<a id="encode"></a>做法：編碼內容

若要透過網際網路傳遞數位視訊，您必須壓縮媒體。媒體服務提供媒體編碼程式，可讓您指定要如何為您的內容編碼 (例如，要使用的轉碼器、檔案格式、解析度及位元速率)。

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟稍後示範於本教學課程中)。
- 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整隨選串流保留單元](media-services-manage-origins.md#scale_streaming_endpoints/)。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

請注意，除了能夠使用動態封裝功能，隨選串流保留單元提供您專用傳出功能，此功能可以以 200 Mbps 為增量來購買。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買隨選串流保留單元。

本章節描述您可以採取的步驟，以使用管理入口網站，透過 Azure Media Encoder 編碼您的內容。

1.  選取您想要編碼的檔案。如果支援此檔案類型的編碼，[內容] 頁面底部的 [處理] 按鈕將會啟用。
4. 在 [處理] 對話方塊選取 ** Azure Media Encoder **處理器。5. 選擇其中一種**編碼組態**。

	![Process2][process2]

		
	[Azure Media Encoder 的工作預設字串](https://msdn.microsoft.com/library/azure/dn619392.aspx)主題說明**調適性串流 (動態封裝) 的預設**、**漸進式下載的預設**、**調適性串流的傳統預設**等類別各代表什麼意思。


	**其他**組態如下所述：

	+ **使用 PlayReady 內容保護進行編碼**。此預設格式會產生以 PlayReady 內容保護編碼的資產。  
	
	
		依預設會使用媒體服務 PlayReady 授權服務。若要指定其他可讓用戶端從中取得授權以播放 PlayReady 加密內容的服務，請使用 REST 或媒體服務 .NET SDK API。如需詳細資訊，請參閱[使用靜態加密保護您的內容]()，並在媒體編碼程式預設中設定 **licenseAcquisitionUrl** 屬性。或者，您可以使用動態加密，並依照**使用 PlayReady 動態加密和授權傳遞服務**來設定 [PlayReadyLicenseAcquisitionUrl](http://go.microsoft.com/fwlink/?LinkId=507720) 屬性。 
	+ **在 PC/Mac 上播放 (透過 Flash/Silverlight)**。此預設會產生具有下列特性的 Smooth Streaming 資產：使用 AAC 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR (96 kbps)，以及使用 H.264 主要設定檔編碼的 720p 視訊 CBR (6 位元速率，範圍從 3400 kbps 到 400 kbps)，與兩秒 GOP。
	+ **透過 HTML5 播放 (IE/Chrome/Safari)**。此預設會產生具有下列特性的單一 MP4 檔案：使用 AAC 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR (128 kbps)，以及使用 H.264 主要設定檔編碼的 720p 視訊 CBR (4500 kbps)。
	+ **在 iOS 裝置和 PC/Mac 上播放**。此預設格式會產生特性與 Smooth Streaming 資產 (如上所述) 相同、但是其格式可以用來將 Apple HLS 資料流傳遞至 iOS 裝置的資產。 

5. 接著，輸入想要的好記輸出內容名稱或接受預設值。然後，按一下核取按鈕以啟動編碼作業，而您可以在入口網站底部追蹤進度。
6. 按下 [確定]。

	編碼完成之後，[內容] 頁面會包含編碼的檔案。

	若要檢視編碼工作的進度，請切換到 [工作] 頁面。

	如果編碼完成之後檔案大小值未更新，請按 [Sync Metadata] 按鈕。這樣會以儲存體中的實際檔案大小同步處理輸出資產檔案大小，並重新整理 [內容] 頁面上的值。

##<a id="encrypt"></a>做法：加密內容

如果您想要讓媒體服務以 AES 金鑰或 PlayReady DRM 動態加密資產，請務必先執行下列動作：

- 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟示範於[編碼](#encode)區段)。
- 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整隨選串流保留單元](media-services-manage-origins.md#scale_streaming_endpoints/)。
- 設定「預設 aes 清除金鑰服務原則」或「預設 playready 授權服務原則」。如需詳細資訊，請參閱[設定內容金鑰授權原則](media-services-portal-configure-content-key-auth-policy.md)。  


	當您已經準備要啟用加密時，請按**內容**頁面底部的**加密**按鈕。

	![加密][encrypt]

	啟用加密之後，播放程式要求串流時，媒體服務便會使用 AES 或 PlayReady 加密，使用指定的金鑰動態加密您的內容。為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

##<a id="publish"></a>做法：發佈內容

###概觀

如要想提供 URL 給使用者，讓使用者可以利用這個 URL 來傳送或下載內容，請您先建立定位器來發行您的資產。定位器可以存取資產中所含的檔案。媒體服務支援兩種類型的定位器：OnDemandOrigin 定位器，用於串流媒體 (例如，MPEG DASH、HLS 或 Smooth Streaming) 和存取簽章 (SAS) 定位器，用來下載媒體檔案。

當您使用 Azure 管理入口網站發佈您的資產時，會為您建立定位器並提供 OnDemantOrigin 形式 URL (如果您的資產包含.ism 檔案) 或 SAS URL。

SAS URL 具有下列格式：

	{blob container name}/{asset name}/{file name}/{SAS signature}

資料流 URL 的格式如下，還可以用它來播放 Smooth Streaming 資產：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建立 HLS 資料流 URL，請在 URL 加上 (format=m3u8-aapl)。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建立 MPEG DASH 資料流 URL，請在 URL 加上 (format=mpd-time-csf)。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


定位器有到期日。使用入口網站來發佈您的資產時，建立的定位器會在 100 年後失效。

>[AZURE.NOTE]如果您在 2015 年 3 月前使用入口網站來建立定位器，則建立的定位器 2 年後便告失效。

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。

###Publish

若要使用入口網站發行資產，請執行下列作業：

1. 選取資產。 
2. 然後，按一下 [發佈] 按鈕。 
	
 ![PublishedContent][publishedcontent]


## 做法：從入口網站播放內容

**Azure 管理入口網站**提供內容播放程式，您可用來測試您的視訊。

按一下想要的視訊，然後按一下入口網站底部的 [播放] 按鈕。
 
適用一些考量事項：

- 確定已發佈視訊。
- **媒體服務內容播放程式**會從預設串流端點播放。如果您想要從非預設串流端點播放，請使用其他播放程式。例如，[Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
 

![AMSPlayer][AMSPlayer]

<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-manage-content/media-services-portal-player.png

<!---HONumber=62-->