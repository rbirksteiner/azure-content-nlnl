<properties 
	pageTitle="將內容傳遞給客戶概觀" 
	description="本主題簡介當您利用 Azure 媒體服務傳遞內容時會涉及各個方面。" 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#將內容傳遞給客戶概觀

##概觀

當您將內容傳遞給客戶時 (串流即時事件或點播視訊)，您的目標是在不同的網路條件下將高品質的視訊傳遞到各種裝置。

若要達成此目標：

- 將您的資料流編碼成多位元速率 (彈性位元速率) 視訊串流 (這會處理品質與網路條件)，並 
- 使用媒體服務[動態封裝](media-services-dynamic-packaging-overview.md)將資料流動態地重新封裝至不同的通訊協定 (這會處理不同裝置上的資料流)。媒體服務支援傳遞下列可調位元速率資料流技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access licensees)。

本主題概述[內容傳遞概念](media-services-deliver-content-overview.md#concepts)，以及顯示如何執行內容傳遞相關[工作](media-services-deliver-content-overview.md#tasks)的主題連結。

##<a id="concepts"></a>概念

下列清單描述當您傳遞媒體時，可以參考的實用詞彙和概念。

###動態封裝

建議您使用動態封裝來傳遞您的內容。如需詳細資訊，請參閱[動態封裝](media-services-dynamic-packaging-overview.md)。

若要利用動態封裝，您必須先取得至少一個串流端點的隨選串流單位，您打算從中傳遞您的內容。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

###篩選器與動態資訊清單

媒體服務可讓您為資產定義篩選器。這些篩選器是伺服器端規則，可讓您的客戶選擇執行如下的動作：只播放一段視訊 (而非播放完整視訊)，或只指定您客戶裝置可以處理的一部分音訊和視訊轉譯 (而非與該資產相關的所有轉譯)。透過在您客戶要求下建立的**動態資訊清單**可達成對資訊進行這樣的篩選，藉此根據指定的篩選器來串流視訊。

如需詳細資訊，請參閱[篩選器與動態資訊清單](media-services-dynamic-manifest-overview.md)。

###定位器

如要想提供 URL 給使用者，讓使用者可以利用這個 URL 來傳送或下載內容，請您先建立定位器來發行您的資產。定位器提供一個登入點，讓使用者可以存取資產包含的檔案。媒體服務支援兩種類型的定位器：

- **OnDemandOrigin** 定位器可用來傳送媒體 (例如，MPEG DASH、 HLS 或 Smooth Streaming) 或漸進式下載檔案。
-  **SAS** (存取簽章) URL 定位器可用來將媒體檔案下載到本機電腦。 

**存取原則**可用來定義權限 (例如讀取、寫入和清單) 以及用戶端可以使用特定資產多久的時間。請注意，建立 OrDemandOrigin 定位器時，不應使用清單權限 (AccessPermissions.List)。

定位器有到期日。使用入口網站來發佈您的資產時，建立的定位器會在 100 年後失效。

>[AZURE.NOTE]如果您在 2015 年 3 月前使用入口網站來建立定位器，則建立的定位器 2 年後便告失效。

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。
 
定位器並不是用來控制每個使用者的權限。若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。如需詳細資訊，請參閱[保護媒體](http://msdn.microsoft.com/library/azure/dn282272.aspx)。

請注意，建立定位器時，由於 Azure 儲存體需要執行一些儲存體和傳播程序，因此有時候會延遲 30 秒的時間。


###調適性串流處理 

調適性位元速率技術可讓視訊播放器應用程式判斷網路狀況，並由數種位元速率中進行選擇。當網路通訊降級時，用戶端會選擇較低的位元速率，讓播放器能夠持續以較低的視訊品質來播放視訊。當網路狀況改善時，用戶端會切換至較高的位元速率，以提高視訊品質。Azure 媒體服務支援下列調適性位元速率技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS。

若要提供漸進式下載 URL 給使用者，您必須先建立 OnDemandOrigin 定位器。建立定位器可針對其中包含您要串流之內容的資產，提供其基底路徑。不過，若要能夠串流此內容，您還需要進一步修改此路徑。若要建構串流資訊清單檔案的完整 URL，您必須串連定位器的 Path 值和資訊清單 (filename.ism) 的檔案名稱。接著，在定位器路徑後面附加 /Manifest 和適當的格式 (如果需要)。

您也可以透過 SSL 連線串流您的內容。若要這樣做，請確定您的串流 URL 以 HTTPS 開頭。

請注意，只有在您從中傳遞內容的串流端點在 2014 年 9 月 10 日之後建立時，才能透過 SSL 串流。如果您的串流 URL 是根據 9 月 10 日之後建立的串流端點，則 URL 會包含 "streaming.mediaservices.windows.net" (新格式)。包含 "origin.mediaservices.windows.net" (舊格式) 的串流 URL 不支援 SSL。如果您的 URL 是舊格式，而且您希望能夠透過 SSL 串流，請建立新的串流端點。使用根據新的串流端點建立的 URL，透過 SSL 串流處理內容。


####Streaming URL 格式：

**MPEG DASH 格式**

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest(format=mpd-time-csf)

範例

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Apple HTTP Live Streaming (HLS) V4 格式**

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HTTP Live Streaming (HLS) V3 格式**

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)


**Smooth Streaming 格式**

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest

範例：

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

**Smooth Streaming 2.0 資訊清單 (舊版資訊清單)**

根據預設，Smooth Streaming 資訊清單格式會包含重複的標記 (r-tag)。不過，有些播放程式不支援 r-tag。此類用戶端可以使用停用 r-tag 的格式：

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

**HDS (僅適用於 Adobe PrimeTime/Access 使用人)**

{串流端點名稱-媒體服務帳戶名稱}.streaming.mediaservices.windows.net/{定位器識別碼}/{檔案名稱}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###動態封裝

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼成一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案。
- 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整隨選串流保留單元](media-services-manage-origins.md#scale_streaming_endpoints/)。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

請注意，除了能夠使用動態封裝功能，隨選串流保留單元提供您專用傳出功能，此功能可以以 200 Mbps 為增量來購買。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買隨選串流保留單元。

###漸進式下載 

漸進式下載可以在下載完整檔案前就開始播放媒體。您無法漸進式下載 .ism* (ismv、isma、ismt、ismc) 檔案。

若要漸進式下載內容，請使用 OnDemandOrigin 類型的定位器。下列範例顯示的 URL 採用的是 OnDemandOrigin 類型的定位器：

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

您必須考量下列事項：

- 您必須解密任何您想要從原始服務傳送的儲存體加密資產，這樣才能漸進式下載。


###下載

若要將您的內容下載到用戶端裝置，您必須建立一個 SAS 定位器。您可以利用 SAS 定位器存取檔案所在的 Azure 儲存體容器。若要建立下載 URL，您必須將檔案名稱內嵌在主機與 SAS 簽章之間。

下列範例顯示的 URL 是採用 SAS 定位器：

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

您必須考量下列事項：

- 您必須解密任何您想要從原始服務傳送的儲存體加密資產，這樣才能漸進式下載。
- 未在 12 小時內完成下載，最後一定會失敗。



###串流端點

**串流端點**代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。從串流端點服務的輸出串流可以是即時資料流，或媒體服務帳戶中的隨選視訊資產。此外，您可以藉由調整串流保放留單位，控制串流端點服務如何應付不斷增加的頻寬需求。您應該為生產環境中的應用程式，至少配置一個保留單元。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

##<a id="tasks"></a>與傳遞資產有關的作業


###設定串流端點

如需串流端點的概觀以及如何管理它們的詳細資訊，請參閱 [如何在媒體服務帳戶中管理串流端點](media-services-manage-origins.md)。

###上傳媒體 

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 上傳檔案。

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###編碼資產

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

###設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [媒體-服務-選取器-資產-傳遞-原則](../../includes/media-services-selector-asset-delivery-policy.md)]

###發行資產

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器) 發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##相關主題

[啟動儲存體金鑰之後更新媒體服務定位器](media-services-roll-storage-access-keys.md)
 

<!---HONumber=July15_HO2-->