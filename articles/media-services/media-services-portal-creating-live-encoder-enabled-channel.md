<properties 
	pageTitle="使用管理入口網站建立通道，以執行從單一位元速率到多位元速率串流的即時編碼" 
	description="本教學課程將逐步引導您建立通道，可接收單一位元速率即時串流，並將其編碼為多位元速率串流。" 
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
	ms.date="05/27/2015" 
	ms.author="juliako"/>


#使用管理入口網站建立通道，以執行從單一位元速率到多位元速率串流的即時編碼 (預覽)

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

本教學課程將逐步引導您建立**通道**，可接收單一位元速率即時串流，並將其編碼為多位元速率串流。

>[AZURE.NOTE]如需為即時編碼啟用之通道相關的詳細概念資訊，請參閱[使用通道執行從單一位元速率到多位元速率串流的即時編碼](media-services-manage-live-encoder-enabled-channels.md)。

##常見即時串流案例

下列是建立常見即時串流應用程式所含的一般步驟。

1. 將攝影機連接到電腦。啟動和設定可使用下列其中一種通訊協定輸出單一位元速率串流的內部部署即時編碼器：RTMP、Smooth Streaming 或 RTP (MPEG-TS)。如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
	
	此步驟也可以在您建立通道之後執行。

1. 建立並啟動通道。

1. 擷取通道內嵌 URL。

	內嵌 URL 可供即時編碼器用來傳送串流到通道。
1. 擷取通道預覽 URL。 

	使用此 URL 來確認您的通道會正確接收即時串流。

3. 建立程式，此程式也會建立資產。
1. 發佈程式，以建立相關聯資產的 OnDemand 定位器。  

	請確定在您想串流內容的串流端點上至少有一個串流保留單元。
1. 當您準備好開始串流和封存時，請啟動程式。
2. 即時編碼器會收到啟動公告的信號 (選擇性)。公告會插入輸出串流中。
1. 每當您想要停止串流處理和封存事件時，請停止程式。
1. 刪除程式 (並選擇性地刪除資產)。   

##本教學課程內容

在本教學課程中，Azure 管理入口網站用來完成下列工作：

2.  設定串流端點。
3.  建立啟用即可執行即時編碼的通道。
1.  取得內嵌 URL，以將其提供給即時編碼器。即時編碼器將使用此 URL 將串流內嵌到通道。
1.  建立程式 (和資產)
1.  發佈資產並取得串流 URL  
1.  播放您的內容 
2.  清除

##必要條件
需要有下列項目，才能完成教學課程。

- 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](azure.microsoft.com)。
- 媒體服務帳戶。若要建立媒體服務帳戶，請參閱[建立帳戶](media-services-create-account.md)。
- 網路攝影機以及可以傳送單一位元速率即時串流的編碼器。

##使用入口網站設定串流端點

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

使用即時串流時，內部部署即時編碼器 (在我們的案例中，是 Wirecast) 會將多位元速率即時串流內嵌到您的通道。使用者要求串流時，媒體服務會使用動態封裝，以將來源串流重新封裝為所要求的調適性位元速率串流 (HLS、DASH 或 Smooth)。

若要利用動態封裝，您需要為想要從該處傳遞內容的**串流端點**取得至少一個串流單元。

若要變更串流保留單元數目，請執行下列動作：

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [媒體服務]。接著，按一下媒體服務的名稱。

2. 選取 [串流端點] 頁面。然後，按一下您要修改的串流端點。

3. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [reserved capacity] 滑動軸。

	![Scale page](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	配置任何新的單元需要大約 20 分鐘的時間才能完成。

	 
	>[AZURE.NOTE]目前，如果串流單元從任何正值到無，可能會停用串流長達一小時。
	>
	> 計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱＜[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)＞。

 
##建立通道

1.	在[管理入口網站](http://manage.windowsazure.com/)中按一下 [媒體服務]，然後按一下媒體服務帳戶名稱。
2.	選取 [通道] 頁面。
3.	選取 [新增+] 以新增通道。

選擇 [**標準**] 編碼類型。此類型指定您想要建立通道，而啟用通道即可進行即時編碼。這表示內送單一位元速率串流會傳送至通道，並編碼為使用所指定即時編碼器設定的多位元速率串流。如需詳細資訊，請參閱[使用執行從單一位元速率到多位元速率串流之即時編碼的通道](media-services-manage-live-encoder-enabled-channels.md)。

![standard0][standard0]

針對 [**標準**] 編碼類型，有效的內嵌通訊協定選項如下：

- 單一位元速率分段的 MP4 (Smooth Streaming)
- 單一位元速率 RTMP
- RTP (MPEG-TS)：透過 RTP 的 MPEG-2 傳輸串流。

如需每種通訊協定的詳細說明，請參閱[使用執行從單一位元速率到多位元速率串流之即時編碼的通道](media-services-manage-live-encoder-enabled-channels.md)。

![standard1][standard1]

通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。

在 [**廣告組態**] 頁面上，您可以指定廣告標記信號的來源。使用入口網站時，您只能選取 API，其指出通道內的即時編碼器應該接聽非同步廣告標記 API。使用入口網站時，您只能選取 API。

如需詳細資訊，請參閱[使用執行從單一位元速率到多位元速率串流之即時編碼的通道](media-services-manage-live-encoder-enabled-channels.md)。

![standard2][standard2]

在 [**編碼預設**] 頁面上，您可以選取系統預設。目前，您唯一可選取的系統預設是 [**預設 720p**]。

![standard3][standard3]

在 [**通道建立**] 頁面上，您可以定義允許將視訊發佈到這個通道的 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

如果未指定 IP 位址，而且沒有任何規則定義，則不允許任何 IP 位址。若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。


![standard4][standard4]

>[AZURE.NOTE]目前，在預覽中，通道啟動可能需要最多 30 分鐘。重設通道可能需要最多 5 分鐘。

建立通道之後，可以選取 [**編碼器**] 索引標籤，您可以在其中檢視您的通道組態。您也可以管理廣告和平板電腦。

![standard5][standard5]

如需詳細資訊，請參閱[使用執行從單一位元速率到多位元速率串流之即時編碼的通道](media-services-manage-live-encoder-enabled-channels.md)。


##取得內嵌 URL

建立通道之後，即可取得您提供給即時編碼器的內嵌 URL。編碼器會使用這些 URL 來輸入即時串流。

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##建立和管理程式

###概觀

通道是與程式相關聯，而程式可讓您控制即時串流中區段的發佈和儲存。「通道」會管理「程式」。「通道」與「程式」的關係與傳統媒體十分類似，其中，通道具有固定的內容串流，而且程式的範圍是該通道上的某個計時事件。

設定 [**封存時間範圍**] 長度，即可指定您想要保留程式之錄製內容的時數。此值可以設為最少 5 分鐘到最多 25 個小時。封存時間範圍長度也會指出用戶端可以從目前即時位置及時往回搜尋的最大時間量。程式在超過指定的時間量後還是可以執行，但是會持續捨棄落後時間範圍長度的內容。此屬性的這個值也會決定用戶端資訊清單可以成長多長的時間。

每個程式都是與「資產」相關聯。若要發佈程式，您必須建立相關聯資產的 OnDemand 定位器。擁有此定位器，可讓您建置可提供給用戶端的串流 URL。

通道支援最多三個同時執行的程式，因此您可以建立相同內送串流的多個封存。這可讓您視需要發佈和封存事件的不同部分。例如，您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。為了達成此目的，您必須建立兩個同時執行的程式。其中一個程式設定為封存 6 小時的事件，但是未發行該程式。另一個程式則設定為封存 10 分鐘，並發行程式。

您不應該將現有程式重複用於新的事件。而是為每個事件建立並啟動新的程式 (如＜程式設計即時串流應用程式＞一節中所述)。

當您準備好開始串流和封存時，請啟動程式。每當您想要停止串流處理和封存事件時，請停止程式。

若要刪除封存的內容，請停止並刪除程式，然後刪除相關聯的資產。如果程式使用資產，則無法刪除資產；必須先刪除程式。

只要您未刪除資產，即使在停止並刪除程式之後，使用者還是可以視需求將封存的內容串流為視訊。

如果想要保留封存的內容，但不要讓它可進行串流處理，請刪除串流定位器。

###建立/啟動/停止程式

讓串流流入通道之後，您可以建立「資產」、「程式」和「串流定位器」來開始串流事件。這將封存串流，並透過「串流端點」將它提供給檢視器。

有兩種方式可以啟動事件：

1. 從 [**通道**] 頁面中，按 [**新增**] 新增程式。

	指定：程式名稱、資產名稱、封存時間範圍和加密選項。
	
	![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
	
	如果您保留 [**立即發佈此程式**] 的核取狀態，則程式會建立發佈 URL。
	
	只要準備好串流處理程式，即可按 [**開始**]。

	啟動程式之後，即可按 [播放] 開始播放內容。


	![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. 或者，您可以使用捷徑，並按 [**通道**] 頁面上的 [**開始串流處理**] 按鈕。這樣會建立「資產」、「程式」和「串流定位器」。

	程式的名稱為 DefaultProgram，而封存時間範圍設定為 1 個小時。

	您可以從 [通道] 頁面播放發佈的程式。

	![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


如果您按一下 [**通道**] 頁面上的 [**停止串流處理**]，則會停止並刪除預設程式。資產仍然位在該處，而您可以從 [**內容**] 頁面發佈或解除發佈資產。

如果您切換到 [**內容**] 頁面，則會看到針對程式所建立的資產。

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##播放內容

若要將可用來串流處理內容的 URL 提供給使用者，您必須建立定位器 (使用入口網站發佈資產時，即會建立定位器) 先 「發佈」資產 (如前一節所述)。定位器提供對於資產中包含之檔案的存取。

根據您要用來播放內容的串流通訊協定，可能需要修改從「通道\程式」的 [**發佈 URL**] 連結取得的 URL。

動態封裝會負責將即時串流封裝成指定的通訊協定。

串流 URL 預設具有下列格式，而且您可以用它來播放 Smooth Streaming 資產：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建置 HLS 串流 URL，請將 (格式=m3u8-aapl) 附加至 URL。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建置 MPEG DASH 串流 URL，請將 (格式=mpd-time-csf) 附加至 URL。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

如需傳遞內容的詳細資訊，請參閱[傳遞內容](media-services-deliver-content-overview.md)。

您可以使用 [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 播放 Smooth Stream，或使用 iOS 和 Android 裝置播放 HLS 第 3 版。

##清除

如果您完成串流處理事件，而且想要清除先前佈建的資源，請遵循下列程序。

- 停止從編碼器發送串流。
- 停止通道。停止通道之後，就不會產生任何費用。當您需要重新啟動它時，它會具有相同的內嵌 URL，因此您不需要重新設定編碼器。
- 除非您想要繼續將即時事件封存為隨選串流，否則您可以停止「串流端點」。如果通道處於已停止狀態，就不會產生任何費用。
  


[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png

<!---HONumber=62-->