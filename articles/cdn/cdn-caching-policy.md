<properties 
	pageTitle="媒體服務延伸模組中的 CDN 快取原則" 
	description="本主題提供媒體服務延伸模組中的 CDM 快取原則的概觀。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="juliako"/>

#媒體服務延伸模組中的 CDN 快取原則

Azure 媒體服務提供 HTTP 式「彈性資料流」和漸進式下載功能。HTTP 式資料流具有快取 Proxy 和 CDN 層，以及快取用戶端的優點，所以延展性極佳。資料流端點提供一般串流功能，以及 HTTP 快取標頭的組態。串流端點會設定 HTTP Cache-Control: max-age 和 Expires 標頭。您可以從 [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) (英文) 取得 HTTP 快取標頭的詳細資訊。

##預設快取標頭

根據預設，資料流端點會針對隨選資料流處理的資料 (實際的媒體片段/區塊) 和資訊清單 (播放清單) 套用 3 天快取標頭。如果是即時資料流，資料流端點會針對資料 (實際的媒體片段/區塊) 套用 3 天快取標頭，針對資訊清單 (播放清單) 要求套用 2 秒快取標頭。當即時節目會變成隨選 (即時封存) 時，便會套用隨選資料流處理快取標頭。

##Azure CDN 整合

Azure 媒體服務為資料流端點提供 [整合式 CDN](http://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/)。Cache-control 標頭的套用方式與將資料流端點套用支援 CDN 的資料流端點的方式相同。Azure CDN 使用資料流端點設定的快取值，定義內部快取物件的存留期，也用此值設定傳遞快取標頭。使用支援 CDN 的資料流端點時，不建議將快取值設得太小。將值設得太小會降低效能，並減少 CDN 帶來的好處。支援 CDN 的資料流端點的快取標頭值不得設為 600 秒以下。

##使用 Azure 媒體服務設定快取標頭

您可以使用 Azure 管理入口網站或 Azure 媒體服務 API，設定快取標頭的值。

1. 若要透過管理入口網站設定快取標頭，請參閱＜[如何管理資料流端點](../media-services-manage-origins.md)＞ (How to Manage Streaming Endpoints) 一節中的「設定資料流端點」中的。
2. Azure 媒體服務 REST API，[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)。
3. Azure 媒體服務 .NET SDK，[StreamingEndpointCacheControl 屬性](http://go.microsoft.com/fwlink/?LinkId=615302)。

##快取組態的優先順序

1. Azure 媒體服務的已設定快取值會覆寫預設值。
2. 如果沒有任何手動組態，系統會套用預設值。
3. 根據預設，2 秒快取標頭會套用至即時資料流資訊清單 (播放清單)，無論 Azure 媒體或 Azure 儲存體組態為何，且此值無法被覆寫。
 

<!---HONumber=62-->