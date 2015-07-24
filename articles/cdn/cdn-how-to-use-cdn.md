<properties 
	pageTitle="如何使用 CDN - Azure 功能指南" 
	description="了解如何使用 Azure 內容傳遞網路 (CDN) 來快取 Blob 和靜態內容，以傳遞高頻寬內容。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>


# 使用 Azure 的 CDN

Azure 內容傳遞網路 (CDN) 是在 Azure 中調整任何 HTTP 應用程式的基礎建置組塊。它透過快取和傳遞接近使用者的內容，將全域方案提供給 Azure 客戶。因此，使用者要求會聰明地路由至最適合執行的 CDN 邊緣 POP，而不是每次都按原點。這會大幅增加效能和使用者經驗。如需目前的 CDN 節點位置的最新清單，請參閱 [Azure CDN 節點位置]。

使用 CDN 來快取 Azure 資料的優點包括：

-   讓離內容來源很遙遠、且所使用的應用程式需要在網際網路上歷經長途跋涉才能載入內容的使用者，享有更好的效能和使用者經驗
-   大型的分散式規模可更妥善處理瞬間大量負載 (例如產品上市等活動的開頭)
-   透過從全域邊緣 POP 分散使用者要求，以及服務內容，會將較少的流量傳送至原點，因而卸載原點。

現有的 CDN 客戶現在可以在 [Azure 管理入口網站] 中使用 Azure CDN。CDN 是一項可用於您訂閱的附加元件功能，且有自己的計費方案。

##步驟 1：在 Azure 中建立 CDN 原點

CDN 原點是 CDN 從中提取內容並快取於邊緣 POP 的位置。整合 Azure 原點包括 Azure 應用程式、雲端服務、儲存體和媒體服務。

## 步驟 2：建立指向您的 Azure 原點的 CDN 端點

原點在設定之後，會在[建立新的 CDN 端點](cdn-create-new-endpoint.md)時出現在原點清單中。

> [AZURE.NOTE]為端點建立的組態無法立即使用，最多需要 60 分鐘進行註冊，以透過 CDN 網路傳播。嘗試立即使用 CDN 網域名稱的使用者可能會收到狀態碼 400 (不正確的要求)，直到可透過 CDN 使用內容為止。

## 步驟 3：設定您的 CDN 組態 

您可以為您的 CDN 端點啟用一些功能 (例如，快取原則、查詢字串快取等)。

## 步驟 4：存取 CDN 內容

若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。例如，快取 Blob 的位址會與下面類似： http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>



## 另請參閱

[Azure 內容傳遞網路 (CDN) 概觀](cdn-overview.md)
 

<!---HONumber=62-->