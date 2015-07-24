<properties
	pageTitle="使用管理入口網站建立通道，以從內部部署編碼器接收多位元速率即時串流"
	description="本教學課程將逐步引導您實作基本媒體服務即時串流應用程式，其中，「通道」會從內部部署即時編碼器接收多位元速率即時串流。"
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
	ms.topic="get-started-article" 
	ms.date="04/16/2015"
	ms.author="juliako"/>


#使用管理入口網站建立通道，以從內部部署編碼器接收多位元速率即時串流

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]


本教學課程將逐步引導您實作基本媒體服務即時串流應用程式，其中，「**通道**」會從內部部署即時編碼器接收多位元速率即時串流。如需使用通道和相關元件的詳細概觀，請參閱[使用從內部部署編碼器接收多位元速率即時串流的通道](../media-services-channels-overview.md)。

在本教學課程中，Azure 管理入口網站用來完成下列工作：

2.  設定串流端點
3.  建立通道
1.  設定即時編碼器並將即時串流內嵌到通道 (在此步驟中，使用 Wirecast)
1.  建立程式 (和資產)
1.  發佈資產並取得串流 URL  
1.  播放您的內容
2.  清除

##必要條件
需要有下列項目，才能完成教學課程。

- 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](azure.microsoft.com)。
- 媒體服務帳戶。若要建立媒體服務帳戶，請參閱[建立帳戶](media-services-create-account.md)。
- 網路攝影機以及可以傳送多位元速率即時串流的編碼器。


##使用入口網站設定串流端點

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

使用即時串流時，內部部署即時編碼器 (在我們的案例中，是 Wirecast) 會將多位元速率即時串流內嵌到您的通道。使用者要求串流時，媒體服務會使用動態封裝，以將來源串流重新封裝為所要求的調適性位元速率串流 (HLS、DASH 或 Smooth)。

若要利用動態封裝，您需要為想要從該處傳遞內容的**串流端點**取得至少一個串流單元。

若要變更串流保留單元數目，請執行下列動作：

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [媒體服務]。接著，按一下媒體服務的名稱。

2. 選取 [串流端點] 頁面。然後，按一下您要修改的串流端點。

3. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [reserved capacity] 滑動軸。

	![Scale page](./media/media-services-portal-get-started-with-live/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	配置任何新的單元需要大約 20 分鐘的時間才能完成。


	>[AZURE.NOTE]目前，如果串流單元從任何正值到無，可能會停用串流長達一小時。
	>
	> 計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱＜[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)＞。


##建立通道

在 Azure 管理入口網站中，選取 [**通道**] 頁面。然後，按一下 [**新增**]。在 [**建立新的即時通道**] 對話方塊中，輸入您通道的名稱。

![createchannel](./media/media-services-portal-get-started-with-live/media-services-create-channel.png)

按下 [**確定**]。

在幾分鐘後，會建立並啟動通道。

##取得內嵌 URL

建立通道之後，即可取得您提供給即時編碼器的內嵌 URL。編碼器會使用這些 URL 來輸入即時串流。

![readychannel](./media/media-services-portal-get-started-with-live/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-get-started-with-live/media-services-ingest-urls.png)

如需內嵌 URL 的詳細資訊，請參閱[使用內部部署編碼器將多位元速率即時串流傳送至通道](../media-services-channels-overview.md)。

##設定即時編碼器並內嵌即時串流

>[AZURE.NOTE]此步驟需要上一個步驟中所述的通道內嵌 URL。

如需如何設定 Wirecast 以及開始內嵌串流的詳細資訊，請參閱 [Wirecast 組態](http://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)。

>[AZURE.NOTE]如果您因任何原因停止編碼器，而需要重新啟動，則應該按一下 Azure 管理入口網站中的 [**重設**] 命令，先重設通道。


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

	![createprogram](./media/media-services-portal-get-started-with-live/media-services-create-program.png)

	如果您保留 [**立即發佈此程式**] 的核取狀態，則程式會建立發佈 URL。

	只要準備好串流處理程式，即可按 [**開始**]。

	啟動程式之後，即可按 [播放] 開始播放內容。


	![createdprogram](./media/media-services-portal-get-started-with-live/media-services-created-program.png)

2. 或者，您可以使用捷徑，並按 [**通道**] 頁面上的 [**開始串流處理**] 按鈕。這樣會建立「資產」、「程式」和「串流定位器」。

	程式的名稱為 DefaultProgram，而封存時間範圍設定為 1 個小時。

	您可以從 [通道] 頁面播放發佈的程式。

	![channelpublish](./media/media-services-portal-get-started-with-live/media-services-channel-play.png)


如果您按一下 [**通道**] 頁面上的 [**停止串流處理**]，則會停止並刪除預設程式。資產仍然位在該處，而您可以從 [**內容**] 頁面發佈或解除發佈資產。

如果您切換到 [**內容**] 頁面，則會看到針對程式所建立的資產。

![contentasset](./media/media-services-portal-get-started-with-live/media-services-content-assets.png)


##播放內容

若要將可用來串流處理內容的 URL 提供給使用者，您必須建立定位器 (使用入口網站發佈資產時，即會建立定位器) 先 「發佈」資產 (如前一節所述)。定位器提供對於資產中包含之檔案的存取。

根據您要用來播放內容的串流通訊協定，可能需要修改從「通道\\程式」的 [**發佈 URL**] 連結取得的 URL。

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



##後續步驟

深入了解建置即時串流應用程式[建置即時串流應用程式](media-services-live-streaming-workflow.md)

###其他資源
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>


<!-- Anchors. -->


<!-- URLs. -->
[Management Portal]: http://manage.windowsazure.com/

<!-- Images -->

<!---HONumber=July15_HO2-->