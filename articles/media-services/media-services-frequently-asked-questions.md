<properties 
	pageTitle="常見問題集" 
	description="常見問題集 (FAQ)" 
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
	ms.date="06/17/2015" 
	ms.author="juliako"/>


#常見問題集  

##概觀

問：如何調整索引？

答：編碼與編製索引工作的保留單位都相同。請依照[如何調整編碼保留單位](media-services-how-to-scale.md)的指示進行。**請注意**，Indexer 效能不受保留單元類型影響。

問：我已上傳、編碼以及發佈視訊。當我試著串流處理視頻時，為什麼不會播放視頻？

答：最常見的原因之一是您未在負責播放視頻的串流端點上設定任何保留串流單位。請依照[如何調整串流保留單元](media-services-how-to-scale.md)的指示進行。

問：我可以編輯即時資料流？

答：Azure 媒體服務目前尚未開放即時資料流的編輯功能，所以請您事先在電腦上編輯。

問：Azure CDN 可以搭配即時資料流使用？

答：媒體服務支援 Azure CDN 整合 (如需詳細資訊，請參閱[如何在媒體服務帳戶中管理串流端點](media-services-manage-origins.md#enable_cdn))。即時資料流可以搭配 CDN 使用。Azure 媒體服務提供 Smooth Streaming、HLS 和 MPEG-DASH 輸出。所有這些格式都使用 HTTP 來傳送資料以及獲得 HTTP 快取的優點。在即時資料流實際視訊/音訊資料是分割成片段，而且這個獨立片段會快取到 CDN。唯一要重新整理的資料是資訊清單資料。CDN 會定期重新整理資訊清單資料。

問：Azure 媒體服務支援儲存影像？

答：如果您只想要儲存 JPEG 或 PNG 影像，請儲存至 Azure Blob 儲存體。除非您想維持影像與視訊或音訊資產之間的關聯，否則將影像保存在媒體服務帳戶中，實際上一點用處也沒有。或者，影像會在視訊編碼器中當作疊加層使用。媒體服務編碼器可以在視訊最上層疊加影像，而且在支援的輸出格式中會列出 JPEG 和 PNG。如需詳細資訊，請參閱[建立重疊](https://msdn.microsoft.com/library/azure/dn640496.aspx)。

問：我如何將資產從一個媒體服務帳戶複製到另一個帳戶。

答：若要將資產從一個媒體服務帳戶複製到另一個帳戶，請使用 [Azure Media Services.NET SDK 延伸](https://github.com/Azure/azure-sdk-for-media-services-extensions/)儲存機制中可用的 [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 延伸。如需詳細資訊，請參閱[這個](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices)論壇執行緒。

<!---HONumber=July15_HO2-->