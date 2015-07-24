<properties 
	pageTitle="使用啟用的通道來以 Azure 媒體服務執行即時編碼" 
	description="本主題描述如何設定通道，從內部部署編碼器接收單一位元速率即時串流，再利用媒體服務將此串流即時編碼為自動調整位元速率串流。串流可以隨即透過一或多個串流端點傳遞給用戶端播放應用程式，使用下列其中一個自動調整串流通訊協定：HLS、Smooth Streaming、MPEG DASH、HDS。" 
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

#使用啟用的通道來以 Azure 媒體服務執行即時編碼 (預覽)

##概觀

在 Azure 媒體服務中，**通道**代表處理即時串流內容的管線。**通道** 會以兩種方式之一收到即時輸入串流：

- 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到通道。您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。下列即時編碼器會輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉錄器。內嵌的串流會通過**通道**，而不需任何進一步處理。接到要求時，媒體服務會傳遞串流給客戶。
- 單一位元速率串流 (下列格式之一：**RTP** (MPEG-TS))、**RTMP** 或 **Smooth Streaming** (分散的 MP4)) 會傳送至**通道**來執行啟用的通道來以媒體服務執行即時編碼。**通道**接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。接到要求時，媒體服務會傳遞串流給客戶。 

	利用媒體服務編碼即時串流目前處於**預覽**狀態。

自媒體服務 2.10 版起，當您建立通道時，您可以指定您希望通道接收輸入串流的方式，以及您是否想要通道執行串流的即時編碼。您有兩個選擇：

- **無** – 如果您打算使用會輸出多位元速率串流的內部部署即時編碼器，請指定這個值。在此情況下，連入的串流會傳遞至輸出，無須任何編碼。這是在 2.10 版以前的通道行為。如需使用此類型通道的詳細資訊，請參閱[使用會從內部部署編碼器接收多位元速率即時串流的通道](media-services-manage-channels-overview.md)。

- **標準** (預覽) – 如果您打算使用媒體服務將單一位元速率即時串流編碼成多位元速率串流，請選擇這個值。

	利用媒體服務編碼即時串流目前處於預覽狀態。

>[AZURE.NOTE]本主題討論通道的屬性，這些通道是啟用來執行即時編碼的 (**標準**編碼類型)。如需使用未啟用來執行即時編碼的通道之相關資訊，請參閱[使用會從內部部署編碼器接收多位元速率即時串流的通道](media-services-manage-channels-overview.md)。

下圖代表即時串流工作流程，其中通道可使用下列其中一種通訊協定接收單一位元速率串流：RTMP、Smooth Streaming 或 RTP (MPEG-TS)；然後它會將串流編碼為多位元速率串流。

![即時工作流程][live-overview]

>[AZURE.NOTE]並非所有資料中心都支援使用 Azure 媒體服務進行即時編碼。
>
>如果您使用 Azure 管理入口網站來建立通道，您將會有兩個通道編碼類型選項可用：[**無**] 和 [**標準**]。如果您只看到 [**無**] 選項時，表示您的資料中心不支援使用 AMS 的即時編碼。
>
>如果您使用 .NET SDK 或 REST API，請執行下列動作來檢查：
>
>1. 嘗試建立通道，將編碼類型設為 [標準]。 
>2. 如果傳回結果 HTTP 錯誤碼 412 (預先指定的條件失敗) 且具有下列訊息：「*即時編碼在此區域中不支援；EncodingType 必須設定為 [無]。*」，則您的資料中心不支援即時編碼。


##本主題內容

- [常見即時串流案例](media-services-manage-live-encoder-enabled-channels.md#scenario)概觀
- [通道和其相關元件的說明](media-services-manage-live-encoder-enabled-channels.md#channel)
- [考量](media-services-manage-live-encoder-enabled-channels.md#considerations)
- [與即時串流相關的工作](media-services-manage-live-encoder-enabled-channels.md#tasks)

##<a id="scenario"></a>常見即時串流案例

下列是建立常見即時串流應用程式所包含的一般步驟。

1. 將攝影機連接到電腦。啟動和設定可使用下列其中一種通訊協定輸出**單一**位元速率串流的內部部署即時編碼器：RTMP、Smooth Streaming 或 RTP (MPEG-TS)。如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
	
	此步驟也可以在您建立通道之後執行。

1. 建立並啟動通道。

1. 擷取通道內嵌 URL。

	內嵌 URL 可供即時編碼器用來傳送串流到通道。
1. 擷取通道預覽 URL。 

	使用此 URL 來確認您的通道會正確接收即時串流。

3. 建立程式。

	使用 Azure 管理入口網站時，建立程式也會建立資產。

	使用 .NET SDK 或 REST 時，您必須建立資產並指定要在建立程式時使用此資產。 
1. 發行與程式相關聯的資產。   

	請確定在您想串流內容的串流端點上至少有一個串流保留單元。
1. 當您準備好開始串流和封存時，請啟動程式。
2. 即時編碼器會收到啟動公告的信號 (選擇性)。公告會插入輸出串流中。
1. 每當您想要停止串流處理和封存事件時，請停止程式。
1. 刪除程式 (並選擇性地刪除資產)。   

[即時串流工作](media-services-manage-channels-overview.md#tasks)一節會連結至示範如何達成上述工作的主題。


##<a id="channel"></a>通道的輸入 (嵌入) 組態

###<a id="Ingest_Protocols"></a>嵌入串流通訊協定

如果**編碼器類型**設為**標準**，有效的選項如下：

- **RTP** (MPEG-TS)：透過 RTP 的 MPEG-2 傳輸串流。  
- 單一位元速率 **RTMP**
- 單一位元速率**分散的 MP4** (Smooth Streaming)

如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。

####RTP (MPEG-TS) - 透過 RTP 的 MPEG-2 傳輸串流。  

典型的使用案例：

專業的廣播器通常使用供應商 (如 Elemental Technologies、Ericsson、Ateme、Imagine 或 Envivio) 提供的高階內部部署即時編碼器來傳送串流。通常會和 IT 部門和私人網路一起使用。

考量：

- 強烈建議使用單一程式傳輸串流 (SPTS) 輸入。不過也支援使用多種語言的曲目
- 視訊串流的平均位元速率應低於 15 Mbps
- 視訊串流的彙總平均位元速率應低於 1 Mbps
- 以下是支援的轉碼器：
	- Mpeg-2 / H.262 視訊 
		
		- 主要設定檔 (4:2:0)
		- 高設定檔 (4:2:0, 4:2:2)
		- 422 設定檔 (4:2:0, 4:2:2)

	- MPEG-4 AVC / H.264 視訊
	
		- 基準、主要、高設定檔 (8-bit 4:2:0)
		- 高 10 設定檔 (10 位元 4:2:0)
		- 高 422 設定檔 (10 位元 4:2:2)


	- Mpeg-2 AAC-LC 音訊
	
		- 單聲道、立體聲、環繞 (5.1、7.1)
		- MPEG-2 樣式 ADTS 封裝

	- Dolby Digital (AC-3) 音訊

		- 單聲道、立體聲、環繞 (5.1、7.1)

	- MPEG 音訊 (Layer II 和 III)
			
		- 單聲道、立體聲

- 建議的廣播編碼器包含：
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>單一位元速率 RTMP

考量：

- 連入串流不能包含多位元速率視訊
- 視訊串流的平均位元速率應低於 15 Mbps
- 視訊串流的平均位元速率應低於 1 Mbps
- 以下是支援的轉碼器：

	- MPEG-4 AVC / H.264 視訊  
	
		- 基準、主要、高設定檔 (8-bit 4:2:0)
		- 高 10 設定檔 (10 位元 4:2:0)
		- 高 422 設定檔 (10 位元 4:2:2)

	- Mpeg-2 AAC-LC 音訊

		- 單聲道、立體聲、環繞 (5.1、7.1)
		- 44.1 kHz 範例速率
		- MPEG-2 樣式 ADTS 封裝
	
- 建議的編碼器包括：

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####單一位元速率的分散 MP4 (Smooth Streaming)

典型的使用案例：

使用廠商 (如 Elemental Technologies、Ericsson、Ateme、Envivio) 提供的內部部屬即時編碼器，透過開放式網際網路將輸入串流傳送至附近的 Azure 資料中心。

考量：

對於[單一位元速率 RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP) 也一樣。

####其他考量

- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 連入的視訊串流最大解析度是 1920x1080，若為交錯式，最多 60 欄位/秒，若為漸進式，最多 30 框架/秒。


###內嵌 URL (端點) 

通道會提供您在即時編碼器中指定的輸入端點 (內嵌 URL)，因此編碼器可以將串流推入您的通道。

建立通道之後，您可以取得內嵌 URL。若要取得這些 URL，通道不一定要在**執行**狀態。當您準備好開始將資料推入通道，它必須處於**執行**狀態。一旦通道開始內嵌資料，您就可以透過預覽 URL 預覽您的串流。

您可以透過 SSL 連線選擇內嵌的分散 MP4 (Smooth Streaming) 即時資料流。若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。

###允許的 IP 位址

您可以定義允許將視訊發行到這個通道的 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

如果未指定 IP 位址，而且沒有任何規則定義，則不允許任何 IP 位址。若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。


##通道預覽 

###預覽 URL

通道會在進一步處理和傳遞之前，提供您用來預覽及驗證串流的預覽端點 (預覽 URL)。

當您建立通道時，您可以取得預覽 URL。若要取得此 URL，通道不一定要在**執行**狀態。

一旦通道開始內嵌資料，您就可以預覽您的串流。

**注意**不論指定的輸入類型為何，目前預覽串流都只能以分散 MP4 (Smooth Streaming) 的格式傳遞。您可以使用 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) 播放器測試 Smooth Stream。您也可以使用裝載於 Azure 管理入口網站中的播放器來檢視您的串流。

###允許的 IP 位址

您可以定義允許連接到預覽端點的 IP 位址。如果沒有指定 IP 位址，將會允許任何 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

##即時編碼設定

本節說明通道的**編碼類型**設為**標準**時，如何調整通道中即時編碼器的設定。

###Ad 標記來源

您可以指定 ad 標記信號的來源。預設值為 **Api**，其指出通道內的即時編碼器應該接聽非同步 **Ad 標記 API**。

另一個有效的選項是 **Scte35** (只有內嵌串流通訊協定設為 RTP (MPEG-TS) 時才允許。指定 Scte35 時，即時編碼器將會剖析來自輸入 RTP (MPEG-TS) 串流的 SCTE 35 信號。

###CEA 708 隱藏式輔助字幕

選擇性旗標會通知即時編碼器略過任何內嵌於連入視訊的 CEA 708 字幕資料。當旗標設為 false (預設值) 時，編碼器會偵測 CEA 708 資料並將其重新插入至輸出視訊串流。

###視訊串流

選用。描述輸入視訊串流。如果未指定此欄位，則會使用預設值。輸入串流通訊協定設為 RTP (MPEG-TS) 時，才允許這項設定。

####索引

以零起始的索引，會指定通道內的即時編碼器應處理哪一個輸入視訊串流。只有當內嵌串流通訊協定是 RTP (MPEG-TS) 時才適用此設定。

預設值為零。建議您傳送單一程式傳輸串流 (SPTS)。如果輸入串流包含多個程式，即時編碼器會剖析輸入中的程式對應資料表 (PMT)、識別具有串流類型名稱 MPEG-2 視訊或 H.264 的輸入，並以 PMT 中指定的順序加以排列。接著會使用以零起始的索引，在排列中挑選第 n 個項目。

###音訊串流

選用。描述輸入音訊串流。如果未指定此欄位，則會套用預設值。輸入串流通訊協定設為 RTP (MPEG-TS) 時，才允許這項設定。

####索引

建議您傳送單一程式傳輸串流 (SPTS)。如果輸入串流包含多個程式，通道內的即時編碼器會剖析輸入中的程式對應資料表 (PMT)、識別具有串流類型名稱 MPEG-2 AAC ADTS 或 AC-3 System-A 或 AC-3 System-B 或 MPEG-2 Private PES 或 MPEG-1 音訊或 MPEG-2 音訊的輸入，並以 PMT 中指定的順序加以排列。接著會使用以零起始的索引，在排列中挑選第 n 個項目。

####語言

音訊串流的語言識別碼，符合 ISO 639-2，例如 ENG。如果不存在，則預設為 UND (未定義)。

如果通道的輸入是透過 RTP 的 MPEG-2，則可指定高達 8 個音訊串流集。不過，不會有具備相同索引值的兩個項目。

###<a id="preset"></a>系統預設

指定由此通道內之即時編碼器所使用的預設內容。目前，唯一允許的值是 **Default720p** (預設值)。

**Default720p** 會將視訊編碼成下列 7 個層。


####輸出視訊串流

<table border="1">
<tr><th>位元速率</th><th>寬度</th><th>高度</th><th>MaxFPS</th><th>設定檔</th><th>輸出串流名稱</th></tr>
<tr><td>3500</td><td>1280</td><td>720</td><td>30</td><td>高</td><td>Video_1280x720_30fps_3500kbps</td></tr>
<tr><td>2200</td><td>960</td><td>540</td><td>30</td><td>主要區段</td><td>Video_960x540_30fps_2200kbps</td></tr>
<tr><td>1350</td><td>704</td><td>396</td><td>30</td><td>主要區段</td><td>Video_704x396_30fps_1350kbps</td></tr>
<tr><td>850</td><td>512</td><td>288</td><td>30</td><td>主要區段</td><td>Video_512x288_30fps_850kbps</td></tr>
<tr><td>550</td><td>384</td><td>216</td><td>30</td><td>主要區段</td><td>Video_384x216_30fps_550kbps</td></tr>
<tr><td>350</td><td>340</td><td>192</td><td>30</td><td>基準</td><td>Video_340x192_30fps_350kbps</td></tr>
<tr><td>200</td><td>340</td><td>192</td><td>30</td><td>基準</td><td>Video_340x192_30fps_200kbps</td></tr>
</table>

####輸出音訊串流

音訊編碼為 64 kbps 的立體聲 AAC-LC，取樣率為 44.1 kHz。

##發出信號的廣告

當您的通道啟用即時編碼時，您會在處理視訊的管線中具有元件，並可加以操作。您可以發出信號給通道以將 Slate 及/或廣告插入連出的自動調整位元速率串流。Slate 是靜止映像，您可以用來在某些情況下遮蓋輸入的即時摘要 (例如廣告插播期間)。廣告信號是您嵌入連外串流的時間同步處理信號，告知視訊播放器採取特殊動作 – 例如在適當時機切換到廣告。如需此用途的 SCTE 35 信號發送機制的概觀，請參閱此[部落格](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/)。以下是您可以在即時事件中實作的典型案例。

1. 讓您的檢視器在事件開始前取得 PRE-EVENT 映像。
1. 讓您的檢視器在事件開始前取得 POST-EVENT 映像。
1. 在事件期間發生問題時 (例如場地中的電源中斷)，請讓您的檢視器取得 ERROR-EVENT 映像。
1. 傳送 AD-BREAK 映像來隱藏廣告插播期間的即時事件摘要。

以下是發送信號給廣告時，您可以設定的屬性。

###持續時間

廣告插播的持續時間，以秒為單位。必須為非零的正整數值才能開始廣告插播。當廣告插播正在進行中，持續時間設為零，且 CueId 符合正在進行的廣告插播，則會取消插播。

###CueId

廣告插播的唯一識別碼，可供下游應用程式用來採取適當的動作。必須是正整數。您可以將此值設定為任何隨機正整數，或使用上游系統來追蹤 Cue Id。請務必在透過 API 送出任何識別碼前將其標準化為正整數。

###顯示 slate

選用。在廣告插播期間發送信號給即時編碼器以切換至[預設 slate](media-services-manage-live-encoder-enabled-channels.md#default_slate) 映像，並隱藏連入的視訊摘要。在 slate 期間也要使音訊靜音。預設值為 **false**。
 
在建立通道時會透過預設 slate 資產識別碼屬性指定要使用的映像。slate 將會延伸到符合顯示映像大小。


##插入 slate 映像

通道內的即時編碼器會收到切換至 slate 映像的信號。它也會收到結束進行中 slate 的信號。

即時編碼器可設定為切換至 slate 映像，並在某些情況下隱藏連入的視訊信號 – 例如在廣告插播期間。如果未設定此 slate，就不會在廣告插播期間遮罩處理輸入視訊。

###持續時間

slate 的持續時間，以秒為單位。必須為非零的正整數值才能開始 slate。如果沒有進行中的 slate，就會指定玲為持續時間，進行中的 slate 就會終止。

###插入 ad 標記上的 slate

設為 true 時，此設定會將即時編碼器設為在廣告插播期間插入 slate 映像。預設值為 true。

###<a id="default_slate"></a>預設 slate 資產識別碼

選用。指定包含 slate 映像之媒體服務資產的資產識別碼。預設值為 null。

**注意**：建立通道之前，具有下列限制的 slate 映像應上傳做為專用的資產 (此資產中應該沒有其他檔案)。

- 最多 1920 x 1080 的解析度。
- 最多 3 Mb 的大小。
- 檔案名稱必須有 *.jpg 副檔名。- 影像必須上傳到資產做為唯一的 AssetFile，資產和此 AssetFile 應該標示為主要檔案。此資產不可為加密的儲存體。

如果未指定**預設 slate 資產識別碼**，且 **ad 標記上的插入 slate** 設為 **true**，將使用預設 Azure 媒體服務來隱藏輸入視訊串流。在 slate 期間也要使音訊靜音。


##通道的程式

通道與程式相關聯，而程式可讓您控制即時串流中區段的發行和儲存。通道會管理程式。「通道」與「程式」之間的關係與傳統媒體十分類似，通道在其中具有固定的內容串流，而且程式的範圍會設定為該通道上的某個計時事件。

設定**封存時間範圍**長度，即可指定您想要保留程式之錄製內容的時數。此值可以設為最少 5 分鐘到最多 25 個小時。封存時間範圍長度也會指出用戶端可以從目前即時位置及時往回搜尋的最大時間量。程式在超過指定的時間量後還是可以執行，但是會持續捨棄落後時間範圍長度的內容。此屬性的這個值也會決定用戶端資訊清單可以成長為多長的時間。

每個程式都與儲存串流內容的資產相關聯。資產會對應到 Azure 儲存體帳戶中的 blob 容器，且資產中的檔案會儲存為該容器中的 blob。若要發行程式讓您的客戶檢視串流，您必須建立相關聯資產的隨選定位器。擁有此定位器，可讓您建置可提供給用戶端的串流 URL。

通道可支援最多三個同時執行的程式，因此您可以建立相同連入串流的多個封存。這可讓您視需要發行和封存事件的不同部分。例如，您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。為了達成此目的，您必須建立兩個同時執行的程式。其中一個程式設定為封存 6 小時的事件，但是未發行該程式。另一個程式則設定為封存 10 分鐘，並發行程式。

您不應該將現有程式重複用於新的事件。而是為每個事件建立並啟動新的程式 (如＜程式設計即時串流應用程式＞一節中所述)。

當您準備好開始串流和封存時，請啟動程式。每當您想要停止串流處理和封存事件時，請停止程式。

若要刪除封存的內容，請停止並刪除程式，然後刪除相關聯的資產。如果程式使用資產，則無法刪除資產；必須先刪除程式。

只要您未刪除資產，即使在停止並刪除程式之後，使用者還是可以視需求將封存的內容串流為視訊。

如果想要保留封存的內容，但不要讓它可進行串流，請刪除串流定位器。


##取得即時摘要的縮圖預覽

啟用即時編碼時，您現在可以在即時摘要抵達通道時取得其預覽。這是重要的工具，可檢查您的即時摘要是否真的抵達通道。

##<a id="states"></a>通道狀態和狀態如何對應至計費模式 

通道的目前狀態。可能的值包括：

- **已停止**。這是通道建立後的初始狀態。在此狀態下，通道屬性可以更新，但是不允許串流。
- **啟動中**。正在啟動通道。在此狀態期間允許任何更新或串流。如果發生錯誤，通道會回到已停止狀態。
- **執行中**。通道能夠處理即時串流。
- **停止中**。正在停止通道。在此狀態期間允許任何更新或串流。
- **刪除中**。正在刪除通道。在此狀態期間允許任何更新或串流。

下表顯示通道狀態如何對應至計費模式。
 
<table border="1">
<tr><th>通道狀態</th><th>入口網站 UI 指標</th><th>是否計費？</th></tr>
<tr><td>啟動中</td><td>啟動中</td><td>無 (暫時性狀態)</td></tr>
<tr><td>執行中</td><td>就緒 (沒有執行中的程式)<br/>或<br/>串流 (至少一個執行中的程式)</td><td>是</td></tr>
<tr><td>停止中</td><td>停止中</td><td>無 (暫時性狀態)</td></tr>
<tr><td>已停止</td><td>已停止</td><td>否</td></tr>
</table>


>[AZURE.NOTE]目前在預覽中，通道啟動可能需要 20 分鐘以上。重設通道可能需要最多 5 分鐘。


##<a id="Considerations"></a>考量

- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 每當您重新設定即時編碼器，請呼叫通道上的**重設**方法。重設通道之前，您必須停止程式。重設通道之後，請重新啟動程式。 
- 只有當通道處於執行中的狀態，且通道上的所有程式皆已停止時，才能停止通道。
- 根據預設，您只能加入最多 5 個通道到媒體服務帳戶。這是所有新帳戶的彈性配額。如需詳細資訊，請參閱[配額和限制](media-services-quotas-and-limitations.md)。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。
- 只有當您的通道處於**執行中**狀態時，才會向您計費。若需詳細資訊，請參閱[這個](media-services-manage-live-encoder-enabled-channels.md#states)章節。

##已知問題

- 通道啟動時可能需要 20 分鐘以上。
- 為專業的廣播者建立 RTP 支援。請先檢閱[這個](http://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/)部落格中的 RTP 注意事項。
- Slate 映像應該符合[這裡](media-services-manage-live-encoder-enabled-channels.md#default_slate)所述的限制。如果您嘗試建立預設 slate 大於 1920 x 1080 的通道，要求最後將會發生錯誤。


##<a id="tasks"></a>與即時串流相關的工作

###建立媒體服務帳戶

[建立 Azure 媒體服務帳戶](media-services-create-account.md)。

###設定串流端點

如需串流端點的概觀以及如何管理它們的詳細資訊，請參閱[如何在媒體服務帳戶中管理串流端點](media-services-manage-origins.md)。

###設定開發環境  

為開發環境選擇 **.NET** 或 **REST API**。

[AZURE.INCLUDE [媒體-服務-選取器-設定](../../includes/media-services-selector-setup.md)]

###以程式設計方式連接  

選擇 **.NET** 或 **REST API** 以程式設計方式連接到 Azure 媒體服務。

[AZURE.INCLUDE [媒體-服務-選取器-連接](../../includes/media-services-selector-connect.md)]

###建立通道以執行從單一位元速率到自動調整多位元速率的即時編碼 

選擇**入口網站**、**.NET**、**REST API** 以了解如何建立及管理通道和程式。

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

###保護資產

**概觀**：

[內容保護概觀](media-services-content-protection-overview.md)

如果您想要使用進階加密標準 (AES) (使用 128 位元加密金鑰) 或 PlayReady DRM 加密和程式相關聯的資產，您必須建立內容金鑰。

使用 **.NET** 或 **REST API** 來建立金鑰。

[AZURE.INCLUDE [媒體-服務-選取器-建立-contentkey](../../includes/media-services-selector-create-contentkey.md)]

一旦您建立內容金鑰之後，您就可以使用 **.NET** 或 **REST API** 設定金鑰授權原則。

[AZURE.INCLUDE [媒體-服務-選取器-內容-金鑰-auth-原則](../../includes/media-services-selector-content-key-auth-policy.md)]

####與夥伴整合

[使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)


###發行及傳遞資產

**概觀**：

- [動態封裝概觀](../media-services-dynamic-overview.md)


使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [媒體-服務-選取器-資產-傳遞-原則](../../includes/media-services-selector-asset-delivery-policy.md)]

使用 **Azure 管理入口網站**或 **.NET** (藉由建立定位器) 發行資產。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


傳遞內容

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

###啟用 Azure CDN

媒體服務支援與 Azure CDN 整合。如需如何啟用 Azure CDN 的資訊，請參閱[如何管理媒體服務帳戶中的串流端點](media-services-manage-origins.md#enable_cdn)。

###調整媒體服務帳戶

您可以藉由指定您要佈建給帳戶的**串流保留單元**數目來調整 **媒體服務**。

如需調整串流單位的相關資訊，請參閱： [如何調整串流單位](media-services-manage-origins.md#scale_streaming_endpoints.md)。

##相關主題

[使用 Azure 媒體服務傳遞即時串流事件](media-services-live-streaming-workflow.md)

[媒體服務概念](media-services-concepts.md)

[Azure 媒體服務的分散 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
 

<!---HONumber=July15_HO2-->