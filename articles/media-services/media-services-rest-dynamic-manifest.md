
<properties 
	pageTitle="使用媒體服務 REST API 建立篩選器" 
	description="本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。媒體服務會建立動態資訊清單來封存此選擇性資料流。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#使用媒體服務 REST API 建立篩選器

從 2.11 版開始，媒體服務可讓您為資產定義篩選器。這些篩選器是伺服器端規則，可讓您的客戶選擇執行如下的動作：只播放一段視訊 (而非播放完整視訊)，或只指定您客戶裝置可以處理的一部分音訊和視訊轉譯 (而非與該資產相關的所有轉譯)。透過在您客戶要求下建立的**動態資訊清單**可達成對資訊進行這樣的篩選，藉此根據指定的篩選器來串流視訊。

如需篩選器與動態資訊清單的詳細資訊，請參閱[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)。

本主題說明如何使用 REST API 建立、更新與刪除篩選器。

##用於建立篩選器的類型

建立篩選器時會使用下列類型：

- [Filter](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- [AssetFilter](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- [PresentationTimeRange](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- [FilterTrackSelect 和 FilterTrackPropertyCondition](https://msdn.microsoft.com/library/azure/hh973617.aspx)



>[AZURE.NOTE]使用媒體服務 REST API 時，適用下列考量事項：
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。如需詳細資訊，請參閱[媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。

>成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。您必須依照[使用 REST API 連線至媒體服務](media-services-rest-connect_programmatically.md)所述，對新的 URI 進行後續呼叫。



##建立篩選器

###建立全域篩選器

若要建立全域篩選器，請使用下列 HTTP 要求：

####HTTP 要求

要求標頭

	POST https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host:media.windows.net 

Request body

	{  
	   "Name":"GlobalFilter",
	   "PresentationTimeRange":{  
	      "StartTimestamp":"0",
	      "EndTimestamp":"9223372036854775807",
	      "PresentationWindowDuration":"12000000000",
	      "LiveBackoffDuration":"0",
	      "Timescale":"10000000"
	   },
	   "Tracks":[  
	      {  
	         "PropertyConditions":
	              [  
	            {  
	               "Property":"Type",
	               "Value":"audio",
	               "Operator":"Equal"
	            },
	            {  
	               "Property":"Bitrate",
	               "Value":"0-2147483647",
	               "Operator":"Equal"
	            }
	         ]
	      }
	   ]
	}




####HTTP 回應
	
	HTTP/1.1 201 Created 

###建立區域 AssetFilter

若要建立區域 AssetFilter，請使用下列 HTTP 要求：

####HTTP 要求

要求標頭

	POST https://media.windows.net/API/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net  

Request body

	{   
	   "Name":"AssetFilter", 
	   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
	   "PresentationTimeRange":{   
	      "StartTimestamp":"0", 
	      "EndTimestamp":"9223372036854775807", 
	      "PresentationWindowDuration":"12000000000", 
	      "LiveBackoffDuration":"0", 
	      "Timescale":"10000000" 
	   }, 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	              [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

####HTTP 回應 

	HTTP/1.1 201 Created 
	. . . 

##清單篩選器

###取得 AMS 帳戶中所有的全域**篩選器**

若要列出篩選器，請使用下列 HTTP 要求：

####HTTP 要求
	 
	GET https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 
	
### 取得與資產相關聯的 **AssetFilter**

####HTTP 要求

	GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 

###根據識別碼取得 **AssetFilter**

####HTTP 要求

	GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000


##更新篩選器
 
使用 PATCH、PUT 或 MERGE 以新的屬性值更新篩選器。如需這些作業的詳細資訊，請參閱 [PATCH、PUT、MERGE](http://msdn.microsoft.com/library/dd541276.aspx)。
 
如果您更新篩選器，則資料流端點需要 2 分鐘的時間來重新整理規則。如果內容是使用此篩選器提供的 (並快取在 Proxy 與 CDN 快取中)，則更新此篩選器會造成播放程式失敗。建議在更新篩選器之後清除快取。如果這個選項無法執行，請考慮使用不同的篩選器。
 
###更新全域篩選器

若要更新全域篩選器，請使用下列 HTTP 要求：

####HTTP 要求
 
要求標頭：

	MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	Content-Length: 384
	
要求本文：
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

###上傳區域 AssetFilter

若要更新區域篩選器，請使用下列 HTTP 要求：

####HTTP 要求

要求標頭：

	MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	
要求本文：
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 


##刪除篩選器


###刪除全域篩選器

若要刪除全域篩選器，請使用下列 HTTP 要求：
	
####HTTP 要求

	DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 


###刪除區域 AssetFilter

若要刪除區域 AssetFilter，請使用下列 HTTP 要求：

####HTTP 要求

	DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 

##建置使用篩選器的資料流 URL

如需如何發佈與傳遞資產的相關資訊，請參閱[將內容傳遞給客戶概觀](media-services-deliver-content-overview.md)。


下列範例顯示如何將篩選器新增至資料流 URL。

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)



##另請參閱 

[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)
 

 

<!---HONumber=July15_HO2-->