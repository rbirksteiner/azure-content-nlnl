<properties 
	pageTitle="利用 Azure 媒體服務提供即時資料流" 
	description="本主題提供即時串流中涉及的主要元件的概觀。" 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>


#使用 Azure 媒體服務傳遞即時串流事件

##概觀

使用即時資料流時通常涉及下列元件：

- 相機，用來廣播事件。
- 即時視訊編碼器，它會將相機中的訊號轉換成資料流，然後再傳送至即時資料流服務。 
  
	(選擇性) 多個即時編碼器。針對某些需要相當高度可用性與高品質經驗的重要即時事件，建議使用主動對主動備援編碼器，以達成順暢容錯移轉，而不會遺失資料。
- 即時串流服務可讓您執行下列動作： 
	- 使用各種即時串流處理通訊協定 (例如 RTMP 或 Smooth Streaming) 擷取即時內容， 
	- 將您的串流編碼成調適性位元速率串流
	- 預覽您的即時串流，
	- 儲存擷取的內容以於稍後進行串流 (隨選視訊)
	- 透過一般串流通訊協定 (例如，MPEG DASH、Smooth、HLS、HDS) 直接將內容傳遞給客戶，或傳遞至內容傳遞網路 (CDN) 供進一步的發佈。 
	
		
**Microsoft Azure 媒體服務** (AMS) 提供擷取、編碼、預覽、儲存和傳遞即時串流內容的能力。

當您將內容傳遞給客戶時，您的目標是在不同的網路條件下將高品質的視訊傳遞到各種裝置。請注意品質和網路狀況，使用即時編碼器將您的串流編碼成多位元速率 (調適性位元速率) 視訊串流。請注意不同裝置上的串流，使用媒體服務[動態封裝](media-services-dynamic-packaging-overview.md)將串流動態地重新封裝至不同的通訊協定。媒體服務支援傳遞下列可調位元速率資料流技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access licensees)。

在 Azure 媒體服務中，**通道**、**程式**及 **StreamingEndpoints** 會處理所有的即時串流功能，包括內嵌、格式化、DVR、安全性、延展性和備援能力。

在 Azure 媒體服務中，**通道**代表處理即時串流內容的管線。目前，通道可以接收即時輸入串流的方式如下：


- 內部部署即時編碼器會傳送單一位元速率串流至通道，可以使用下列格式之一，以媒體服務執行即時編碼：RTP (MPEG-TS)、RTMP 或 Smooth Streaming (分散的 MP4) 。通道接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。接到要求時，媒體服務會傳遞串流給客戶。

	利用媒體服務編碼即時串流處於**預覽**狀態。
- 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到通道。您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。下列即時編碼器會輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉錄器。內嵌的串流會通過**通道**，而不需任何進一步處理。您的即時編碼器也會傳送單一位元速率串流至無法用於即時編碼的通道，但是不建議。接到要求時，媒體服務會傳遞串流給客戶。


##使用啟用的通道來以 Azure 媒體服務執行即時編碼


下圖顯示 AMS 平台的主要部分，與通道可以使用媒體服務執行即時編碼的即時串流工作流程有關。

![即時工作流程][live-overview1]

如需詳細資訊，請參閱[使用啟用的通道來以 Azure 媒體服務執行即時編碼](media-services-manage-live-encoder-enabled-channels.md)。


##使用通道，從內部部署編碼器接收多位元速率即時串流


下圖顯示即時串流工作流程中涉及的 AMS 平台主要部分。

![即時工作流程][live-overview2]

如需詳細資訊，請參閱[使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-manage-channels-overview.md)。


##相關主題

[媒體服務概念](media-services-concepts.md)

[Azure 媒體服務的分散 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=July15_HO2-->