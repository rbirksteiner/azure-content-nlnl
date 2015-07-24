<properties 
 pageTitle="如何管理 Azure 內容傳遞網路 (CDN) 中雲端服務內容的到期" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>

#如何管理 Azure 內容傳遞網路 (CDN) 中雲端服務內容的到期

充分利用 Azure CDN 快取中獲益的物件是在其存留時間 (TTL) 期限期間經常存取。物件在 TTL 期限內存留於快取中，然後會在經過該時間之後從雲端服務進行重新整理。然後重複執行此程序。

如果您未提供快取值，則物件的 TTL 為 7 天。

針對靜態內容 (例如影像和樣式表)，您可以控制更新頻率，方法是在 CDN 資料夾中包括含有內容的 web.config，並修改 **clientCache** 設定以控制您內容的 Cache-Control 標頭。web.config 設定會影響資料夾和其所有子資料夾中的所有項目，除非未來在另一個子資料夾中覆寫。例如，您可以設定根目錄的預設存留時間具有快取 3 天的所有靜態內容，但是有一個子資料夾具有快取設定為 6 小時的更多變數內容。

下列 XML 說明和設定 **clientCache** 指定最長使用期限 (3 天) 的範例：

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

指定 **UseMaxAge** 時會根據 **CacheControlMaxAge** 屬性中所指定的值將 Cache-Control: max-age=<nnn> 標頭加入回應。**cacheControlMaxAge** 屬性的 timespan 格式為 <days>.<hours>:<min>:<sec>。如需 **clientCache** 節點的詳細資訊，請參閱[用戶端快取<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。

針對從應用程式傳回的內容 (例如 .aspx 頁面)，設定 **HttpResponse.Cache** 屬性，即可透過程式設計方式設定 CDN 快取行為。如需 **HttpResponse.Cache** 屬性的詳細資訊，請參閱 [HttpResponse.Cache 屬性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和 [HttpCachePolicy 類別](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。

如果您想要以程式設計方式快取應用程式內容，請確定將 HttpCacheability 設為 *Public*，以將內容標示為可進行快取。也請確定已設定快取驗證程式。快取驗證程式可以是透過呼叫 SetLastModified 所設定的上次修改時間戳記，或透過呼叫 SetETag 所設定的 etag 值。您也可以選擇性地透過呼叫 SetExpires 來指定快取到期時間，或可以依賴本文件稍早所述的預設快取啟發學習法。

例如，若要快取一個小時的內容，請加入下列內容：

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##另請參閱

[如何管理 Azure 內容傳遞網路 (CDN) 中 Blob 內容的到期](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=62-->