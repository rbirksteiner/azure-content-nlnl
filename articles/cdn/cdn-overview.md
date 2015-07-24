<properties 
	pageTitle="Azure CDN 概觀" 
	description="了解何謂 Azure 內容傳遞網路 (CDN)，以及如何使用它透過快取 Blob 和靜態內容來傳遞高頻寬內容。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="mazha"/>

#Azure 內容傳遞網路 (CDN) 概觀

Azure 內容傳遞網路 (CDN) 會在策略性放置的位置上快取雲端服務所使用的 Azure Blob 和靜態內容，以提供將內容傳遞給使用者的最大頻寬。

現有的 CDN 客戶現在可以透過 [Microsoft Azure 管理入口網站](https://manage.windowsazure.com)管理您的 CDN 端點。


>[AZURE.NOTE]Azure CDN 有專屬的 Azure 儲存體或 Azure 雲端服務[計費方案](http://www.microsoft.com/windowsazure/pricing/)。
 

CDN 為開發人員提供一套全球解決方案，以在全球實體節點上快取內容來傳遞高頻寬內容。如需 CDN 節點位置的最新清單，請參閱「[Azure 內容傳遞網路 (CDN) POP 位置](http://msdn.microsoft.com/library/azure/gg680302.aspx)」。

使用 CDN 來快取 Azure 資料的優點包括：

- 讓離內容來源很遙遠、且所使用的應用程式需要在網際網路上歷經長途跋涉才能載入內容的使用者，享有更好的效能和使用者經驗
- 大型的分散式規模可更妥善處理瞬間大量負載 (例如產品上市事件的開頭)。 


>[AZURE.IMPORTANT]建立或啟用 CDN 端點時，可能需要 60 分鐘的時間才能傳播到全球。
 
第一次對 CDN 提出物件要求時，會直接從 Blob 服務或雲端服務擷取物件。使用 CDN 語法提出要求時，會將要求重新導向至 CDN 端點，而此 CDN 端點最接近從中提出要求以存取物件的位置。如果在該端點上找不到物件，則會從服務中擷取該物件，並在端點上進行快取，而且會維護所快取物件的存留時間 (TTL) 設定。
 
##從 Azure 儲存體快取內容

在 Azure 儲存體帳戶上啟用 CDN 之後，將會透過 CDN 快取 Blob，而這些 Blob 位於公用容器中且可供匿名存取。使用 Azure CDN 只能快取公開使用的 Blob。若要讓 Blob 設為公開可用以供匿名存取，則必須將其容器表示為公用。這樣做之後，該容器內的所有 Blob 都可供進行匿名讀取存取。您也可以選擇將容器資料設為公開，或只限存取其內的 Blob。如需有關管理容器和 Blob 之存取控制的相關資訊，請參閱「[限制對容器和 Blob 的存取](http://msdn.microsoft.com/library/azure/dd179354.aspx)」。

為了達到最佳效能，請使用 CDN 邊緣快取來傳遞大小小於 10 GB 的 Blob。

啟用儲存體帳戶的 CDN 存取時，管理入口網站會提供下列格式的 CDN 網域名稱：http://<identifier>.vo.msecnd.net/。此網域名稱可用來從網站擷取物件。例如，如果提供名稱為 cdn 的公用容器和稱為 music.png 的影像檔，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：

- **Azure Blob 服務 URL**：`http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/music/` 

##從 Azure 網站快取內容

您可以從網站啟用 CDN 來快取您的 Web 內容 (例如影像、指令碼和樣式表)。請參閱「[整合 Azure 網站與 Azure CDN](../cdn-websites-with-cdn.md)」。

啟用網站的 CDN 存取時，管理入口網站會提供下列格式的 CDN 網域名稱：http://<identifier>.vo.msecnd.net/。此網域名稱可用來從網站擷取物件。例如，如果提供名稱為 cdn 的公用容器和稱為 music.png 的影像檔，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：

- **Azure 網站 URL**：`http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/cdn/music.png`
 
##從 Azure 雲端服務快取內容

您可以將 Azure 雲端服務所提供的物件快取至 CDN。

雲端服務的快取具有下列限制：


- CDN 只應該用來快取靜態內容。

	>[AZURE.WARNING]
- 您的雲端服務必須部署至生產部署中。
- 您的雲端服務必須使用 HTTP 在連接埠 80 上提供物件。
- 雲端服務必須在雲端服務的 /cdn 資料夾中放置要快取的內容，或從中傳遞內容。

啟用雲端服務的 CDN 存取時，管理入口網站會提供下列格式的 CDN 網域名稱：http://<identifier>.vo.msecnd.net/。此網域名稱可用來從雲端服務擷取物件。例如，如果提供名稱為 myHostedService 的雲端服務以及稱為 music.aspx 並傳遞內容的 ASP.NET 網頁，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：


- **Azure 雲端服務 URL**：`http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **Azure CDN URL**：`http://<identifier>.vo.msecnd.net/music.aspx` 


###使用查詢字串快取特定內容

您可以使用查詢字串，區分擷取自雲端服務的物件。例如，如果雲端服務所顯示的圖表可能不同，則可以傳遞查詢字串來擷取所需的特定圖表。例如：

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

查詢字串會傳遞為字串常值。如果您有一項使用兩個參數 (例如 `?area=2&item=1`) 的服務，並使用 `?item=1&area=2` 進行服務的後續呼叫，則會快取兩個相同的物件。
 

##透過 HTTPS 存取所快取的內容


Azure 可讓您使用 HTTPS 呼叫從 CDN 擷取內容。這可讓您將 CDN 中所快取的內容納入安全的網頁，而不會收到混合安全性內容類型的警告。

使用 HTTPS 存取 CDN 內容具有下列限制：


- 您必須使用 CDN 所提供的憑證。不支援協力廠商憑證。
- 您必須使用 CDN 網域來存取內容。因為 CDN 目前不支援自訂憑證，所以自訂網域名稱 (CNAME) 不提供 HTTPS 支援。



即使啟用 HTTPS，還是可以使用 HTTP 和 HTTPS 擷取 CDN 中的內容。

如需有關為 CDN 內容啟用 HTTPS 的詳細資訊，請參閱「[如何啟用 Azure 內容傳遞網路 (CDN)](http://msdn.microsoft.com/library/azure/gg680301.aspx)」。


##使用自訂網域存取所快取的內容

您可以將 CDN HTTP 端點對應至自訂網域名稱，並使用該名稱從 CDN 要求物件。

如需有關對應自訂網域的詳細資訊，請參閱「[如何將內容傳遞網路 (CDN) 內容對應至自訂網域](http://msdn.microsoft.com/library/azure/gg680307.aspx)」。

 

<!---HONumber=62-->