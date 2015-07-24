<properties 
	pageTitle="動態封裝概觀" 
	description="本主題提供動態封裝的概觀。" 
	authors="Juliako" 
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
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#動態封裝 

##概觀

Microsoft Azure Media Services 可用來針對數種用戶端技術 (例如 iOS、XBOX、Silverlight、Windows 8) 提供許多媒體來源檔案格式、媒體串流格式和內容保護格式。這些用戶端各自使用不同的通訊協定，例如 iOS 需要 HTTP 即時串流 (HLS) V4 格式，而 Silverlight 與 Xbox 需要 Smooth Streaming。如果您有一組自動調整位元速率 (多位元速率) MP4 (ISO Base Media 14496-12) 檔案或一組自動調整位元速率 Smooth Streaming 檔案，想要傳遞給了解 MPEG DASH、HLS 或 Smooth Streaming 的用戶端，應該利用媒體服務動態封裝。

使用動態封裝，您只需要建立包含一組自動調整位元速率 MP4 檔案或自動調整位元速率 Smooth Streaming 檔案的資產。然後隨選串流伺服器會根據資訊清單或片段要求中的指定格式，確保您以自己選擇的通訊協定接收串流。因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。

下圖顯示傳統編碼和靜態封裝工作流程。

![靜態編碼](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

下圖顯示動態封裝工作流程。

![動態編碼](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]若要利用動態封裝，您必須先取得至少一個串流端點的隨選串流單位，您打算從中傳遞您的內容。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

##常見的案例

1. 上傳輸入檔案 (稱為夾層檔)。例如，H.264、MP4 或 WMV (如需支援格式清單，請參閱媒體服務編碼器所支援的格式)。
 
1. 將夾層檔編碼為 H.264 MP4 自動調整位元速率集。
 
1. 藉由建立隨選定位器，發行包含自動調整位元速率 MP4 集的資產。
 
1. 建置串流 URL 來存取和串流您的內容。
 
>[AZURE.NOTE]並非所有的 MP4 檔案格式都受動態封裝支援，如需詳細資訊，請參閱[動態封裝不支援的格式](media-services-dynamic-packaging-overview.md#unsupported_formats)。

##準備動態串流的資產

若要準備動態串流的資產，您有兩個選項：

- 上傳主檔案，並使用 Azure 媒體編碼器產生 H.264 MP4 自動調整位元速率集。
- 上傳現有的自動調整位元速率集，並使用 Media Packager 加以驗證。

###上傳主檔案，並使用 Azure 媒體編碼器產生 H.264 MP4 自動調整位元速率集

如需如何上傳與編碼資產的相關資訊，請參閱下列文章：


使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 上傳檔案。

[AZURE.INCLUDE [媒體-服務-選取器-上傳-檔案](../../includes/media-services-selector-upload-files.md)]

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [媒體-服務-選取器-編碼](../../includes/media-services-selector-encode.md)]


###上傳現有的自動調整位元速率集，並使用 Media Packager 加以驗證

如果您正在上傳一組不以媒體服務編碼器編碼的自動調整位元速率 MP4 檔案，您通常會想要執行這項工作。 [驗證以外部編碼器編碼的自動調整位元速率 MP4](https://msdn.microsoft.com/library/azure/dn750842.aspx) 主題示範如何達成這項工作。

##將內容串流到用戶端

如果您有自動調整位元速率集，您就可以藉由建立隨選定位器來發行您的資產，並撰寫 Smooth Steaming、MPEG DASH、HLS 和 HDS 的串流 URL (僅適用於 Adobe PrimeTime/存取被授權者)。

如需如何建立定位器，並使用動態封裝串流內容的資訊，請參閱下列主題：

[將內容傳遞給客戶概觀](media-services-deliver-content-overview.md)。

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [媒體-服務-選取器-資產-傳遞-原則](../../includes/media-services-selector-asset-delivery-policy.md)]

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器) 發行資產。

[AZURE.INCLUDE [媒體-服務-選取器-發行](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>動態封裝不支援的格式

動態封裝不支援下列來源檔案格式。

- Dolby digital 加上 mp4 檔案。
- Dolby digital 加上 smooth 檔案。 

<!---HONumber=July15_HO2-->