<properties 
	pageTitle="利用 Azure 媒體服務提供點播媒體" 
	description="本主題說明利用 Azure 媒體服務傳遞點播媒體時常見的案例。" 
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
	ms.date="06/08/2015" 
	ms.author="juliako"/>


#利用 Azure 媒體服務提供點播媒體

##概觀

本主題說明 Azure 媒體服務 (AMS) 隨選視訊的一般工作流程步驟。每個步驟會連結至相關主題。針對可以使用不同技術達成的工作，有幾個按鈕連結至您選擇的技術 (例如 .NET 或 REST)。

請注意，您可以將媒體服務與現有工具和程序整合。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力商廠 CDN 進行傳遞。

下圖顯示在點播視訊工作流程中牽涉到的媒體服務平台的主要部分。![VoD 工作流程][vod-overview]

##<a id="vod_scenarios"></a>常見案例：提供點播媒體。 

###保護儲存體中的內容並提供不加密的串流媒體

1. 將高品質夾層檔上傳到資產。
	
	建議您將儲存體加密選項套用到資產，以便內容在上傳時以及在儲存體中存放時可以得到保護。 
1. 編碼為調適性位元速率 MP4 集。 

	建議您將儲存體加密選項套用到輸出資產，以便保護存放的內容。
	
1. 設定資產傳遞原則 (用於動態封裝)。
	
	如果您的資產是已加密的儲存體，**必須**設定資產傳遞原則。

1. 藉由建立 OnDemand 定位器的方法來發行資產。

	確定負責傳送內容的串流端點上，至少有一個串流保留單位。

1. 串流發佈的內容。

###保護儲存體中的內容、提供動態加密串流處理媒體  

要想使用動態加密，您必須從負責傳送加密內容的資料流端點至少取得一個資料流保留單位。

1. 將高品質夾層檔上傳到資產。將儲存體加密選項套用到資產。
1. 編碼為調適性位元速率 MP4 集。將儲存體加密選項套用到輸出資產。
1. 針對您想要在播放期間動態加密的資產，建立加密內容金鑰。
2. 設定內容金鑰授權原則。
1. 設定資產傳遞原則 (供動態封裝和動態加密使用)。
1. 透過建立 OnDemand 定位器，來發佈資產。
1. 串流發佈的內容。 

###編製內容索引

1. 將高品質夾層檔上傳到資產。
1. 編製內容索引。

	索引會產生一些檔案，而它們可以當做播放視訊時會用到的隱藏式字幕 (CC)。它也會產生一些檔案，讓您可以在視訊中進行搜尋，然後跳至視訊的正確位置。

1. 使用索引內容。


###提供漸進式下載 

1. 將高品質夾層檔上傳到資產。
1. 編碼為調適性位元速率 MP4 集或單一 MP4。
1. 藉由建立 OnDemand 或 SAS 定位器的方法來發行資產。

	如果使用 OnDemand 定位器，請在您計劃進行漸進式下載內容的串流端點上，確定至少有一個串流保留單位。

	如果使用 SAS 定位器，內容是從 Azure blob 儲存體下載的。在此情況下，您不需要串流保留單位。
  
1. 漸進式下載內容。

本文章提供一些連結，為您示範如何設定開發環境以及執行上述工作。


##概念

如需瞭解如何傳遞點播內容的概念，請參閱[媒體服務概念](media-services-concepts.md)。

##一般工作：提供點播媒體

###建立媒體服務帳戶

使用 **Azure 管理入口網站**[建立 Azure 媒體服務帳戶](media-services-create-account.md)。

###設定開發環境  

為開發環境選擇 **.NET** 或 **REST API**。

[AZURE.INCLUDE [媒體-服務-選取器-設定](../../includes/media-services-selector-setup.md)]

###以程式設計方式連接  

選擇 **.NET** 或 **REST API** 以程式設計方式連接到 Azure 媒體服務。

[AZURE.INCLUDE [媒體-服務-選取器-連接](../../includes/media-services-selector-connect.md)]


###設定串流端點

如需串流端點的概觀以及如何管理它們的詳細資訊，請參閱 [如何在媒體服務帳戶中管理串流端點](media-services-manage-origins.md)。

###上傳媒體 

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 上傳檔案。

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###建立工作 \ 作業

工作 (Job) 是一個實體，其中包含一組工作 (Task，例如編碼或編製索引) 的相關中繼資料。每一項工作 (Task) 會對輸入資產執行不可部分完成的作業。如需如何建立編碼工作 (Job) 的範例，請參閱：

如需概觀，請參閱[使用 Azure 媒體服務工作](media-services-jobs.md)。

利用 **.NET** 或 **REST API** 為您的工作取得合適的媒體處理器。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

下列範例會利用 **Azure 管理入口網站**、**.NET** 或 **REST API** 建立編碼工作。

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####編製索引

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

####編碼 

**概觀**：

- [動態封裝概觀](media-services-dynamic-packaging-overview.md)
- [透過 Azure 媒體服務編碼的隨選內容](media-services-encode-asset.md)。

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

利用 **Media Encoder Premium Workflow** 並搭配 **.NET** 進行進階編碼。

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

####監視工作進度

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 監視工作進度。

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

###保護內容 

**概觀**：

[內容保護概觀](media-services-content-protection-overview.md)

如果您想要使用進階加密標準 (AES) (使用 128 位元加密金鑰) 或 PlayReady DRM 加密資產，您必須建立內容金鑰。

使用 **.NET** 或 **REST API** 來建立金鑰。

[AZURE.INCLUDE [媒體-服務-選取器-建立-contentkey](../../includes/media-services-selector-create-contentkey.md)]

一旦您建立內容金鑰之後，您就可以使用 **.NET** 或 **REST API** 設定金鑰授權原則。

[AZURE.INCLUDE [媒體-服務-選取器-內容-金鑰-auth-原則](../../includes/media-services-selector-content-key-auth-policy.md)]


使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


####與夥伴整合

[使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)

###發行及傳遞資產

動態封裝概觀

> [AZURE.SELECTOR]
- [Overview](media-services-dynamic-packaging-overview.md)


傳遞內容概觀

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

Publish assets (by creating Locators) 使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 發行資產 (藉由建立定位器)。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

###啟用 Azure CDN

媒體服務支援與 Azure CDN 整合。如需如何啟用 Azure CDN 的資訊，請參閱[如何管理媒體服務帳戶中的串流端點](media-services-manage-origins.md#enable_cdn)。

###調整媒體服務帳戶

您可以指定要佈建給帳戶的**串流保留單元**和**編碼保留單元**，藉以調整**媒體服務**。

您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。每個儲存體帳戶的限制為 500 TB。若想將儲存空間擴大為超過預設限制，您可以選擇將多個儲存體帳戶附加至單一媒體服務帳戶。

[本主題](media-services-how-to-scale.md)會連結相關主題。

###使用現有播放器來播放您的內容

如需詳細資訊，請參閱[使用現有播放器來播放您的內容](media-services-playback-content-with-existing-players.md)。


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=62-->