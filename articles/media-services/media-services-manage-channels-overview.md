<properties 
	pageTitle="使用通道，從內部部署編碼器接收多位元速率即時串流" 
	description="本主題描述如何讓通道接收內部部署編碼器的多位元速率即時資料流。串流可以隨即透過一或多個串流端點傳遞給用戶端播放應用程式，使用下列其中一個自動調整串流通訊協定：HLS、Smooth Streaming、MPEG DASH、HDS。" 
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
	ms.date="05/23/2015" 
	ms.author="juliako"/>

#使用通道，從內部部署編碼器接收多位元速率即時串流

##概觀

在 Azure 媒體服務中，**通道**代表處理即時串流內容的管線。**通道**會以下列方式接收即時輸入串流：

- 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到通道。您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。下列即時編碼器會輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉錄器。內嵌的串流會通過**通道**，而不需任何進一步處理。您的即時編碼器也會傳送單一位元速率串流，但是不建議。接到要求時，媒體服務會傳遞串流給客戶。


下圖顯示一個即時資料流工作流程，這個流程利用即時編碼器輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。

![即時工作流程][live-overview]

本主題包含下列項目：

- [常見即時串流案例](media-services-manage-channels-overview.md#scenario)
- [通道和其相關元件的說明](media-services-manage-channels-overview.md#channel)
- [考量](media-services-manage-channels-overview.md#considerations)
- [與即時串流相關的工作](media-services-manage-channels-overview.md#tasks)

##<a id="scenario"></a>常見即時串流案例
下列步驟描述當我們建立一般即時資料流應用程式時，會涉及到的各種工作。

1. 將攝影機連接到電腦。啟動並設定內部部署即時編碼器，讓它輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
	
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

##<a id="channel"></a>通道和其相關元件的說明

###<a id="channel_input"></a>頻道輸入 (內嵌) 組態

####<a id="ingest_protocols"></a>嵌入串流通訊協定

媒體服務支援各種採用下列資料流通訊協定的內嵌即時訊息來源：

- **多位元速率分散 MP4**
 
- **多位元速率 RTMP**

	選取 **RTMP** 內嵌串流通訊協定時，會為通道建立兩個 ingest(input) 端點：
	
	**主要 URL**：指定通道主要 RTMP 內嵌端點的完整 URL。

	**次要 URL** (選用)：指定通道次要 RTMP 內嵌端點的完整 URL。


	如果您想要改善內嵌串流的持久性、容錯以及編碼器容錯移轉和容錯，特別是針對下列案例，請使用次要 URL。

	- 單一編碼器雙重推送至主要和次要 URL：
	
		這個做法的主要目的是為網路變動起伏和快速變換提供恢復功能。部分 RTMP 編碼器無法妥善處理網路中斷連線。當發生網路中斷連線時，編碼器可能會停止編碼，而且在重新連線時不會傳送緩衝處理過的資料，這會導致不連續和資料遺失。網路中斷連線會因為網路不佳或 Azure 端正在進行維護而發生。主要/次要 URL 會減少網路問題，同時提供受控制的升級程序。每次已排程的網路發生中斷連線時，媒體服務會管理主要和次要中斷連線，並提供兩者之間的延遲中斷連線，給予編碼器時間可以持續傳送資料，以及再次重新連線。中斷連線的順序可以是隨機的，但是主要/次要或次要/主要之間一定會有延遲。在這種情況下，編碼器仍是單一失敗點。
	 
	- 多個編碼器的情況下，每個編碼器會推送到專用點：
		
		這種情況下會提供兩個編碼器和擷取備援。在這個案例中，encoder1 會推送至主要 URL，而 encoder2 會推送至次要 URL。當有編碼器失敗時，其他編碼器仍然可以持續傳送資料。仍然可以維持資料備援，因為媒體服務不會同時中斷主要和次要的連線。此案例假設編碼器是時間同步處理，並提供完全相同的資料。
 
	- 多個編碼器雙重推送至主要和次要 URL：
	
		在此案例中，兩個編碼器會將資料推送至主要和次要 URL。這提供最佳的可靠性及容錯功能，以及資料備援。它可容許兩個編碼器皆失敗並且中斷連線，即使一個編碼器停止運作。此案例假設編碼器是時間同步處理，並提供完全相同的資料。

如需 RTMP 即時編碼器的詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。

您必須考量下列事項：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 使用次要內嵌 URL 時會佔用額外的頻寬。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (也稱為圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 您可以內嵌單一位元速率至您的通道，不過由於通道尚未處理串流，所以用戶端應用程式也會收到單一位元速率串流 (不建議這個選項)。

####內嵌 URL (端點) 

通道提供一個輸入端點 (內嵌 URL)，您可以在即時編碼器中指定這個端點，這樣編碼器就可以將資料流推播到您的通道。

當您建立通道時，您可以取得內嵌 URL。若要取得這些 URL，通道不一定要在**執行**狀態。當您準備好開始將資料推入通道，通道必須處於**執行**狀態。一旦通道開始內嵌資料後，您可以透過預覽 URL 來預覽資料流。

您可以選擇透過 SSL 連線來內嵌 Fragmented MP4 (Smooth Streaming) 即時資料流。若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。目前，您無法內嵌 RTMP over SSL。

####<a id="keyframe_interval"></a>主要畫面格間隔

當使用內部部署即時編碼器來產生多位元速率資料流時，主要畫面格間隔會指定 GOP 持續期間 (供該外部編碼器使用)。一旦通道收到此內送串流時，然後您可以再將即時串流傳遞至下列任一形式的用戶端播放應用程式：Smooth Streaming、DASH 及 HLS。在執行即時資料流時，會一律動態封裝 HLS。依預設，媒體服務會根據從即時編碼器收到的主要畫面格間隔 (也稱為圖片群組 – GOP)，自動計算 HLS 區段封裝比例 (每一個區段的片段)。

下表顯示如何計算區段持續時間：

<table border="1">
<tr><th>主要畫面格間隔</th><th>HLS 區段封裝比例 (FragmentsPerSegment)</th><th>範例</th></tr>
<tr><td>小於或等於 3 秒</td><td>3:1</td><td>如果 KeyFrameInterval (或 GOP) 為 2 秒鐘，則預設 HLS 區段封裝比例就會是 3 比 1，這將會建立 6 秒鐘的 HLS 區段。</td></tr>
<tr><td>3 到 5 秒</td><td>2:1</td><td>如果 KeyFrameInterval (或 GOP) 為 4 秒鐘，則預設 HLS 區段封裝比例就會是 2 比 1，這將會建立 8 秒鐘的 HLS 區段。</td></tr>
<tr><td>大於 5 秒</td><td>1:1</td><td>如果 KeyFrameInterval (或 GOP) 為 6 秒鐘，則預設 HLS 區段封裝比例就會是 1 比 1，這將會建立 6 秒鐘的 HLS 區段。</td></tr>
</table>

您可以設定通道的輸出並在 ChannelOutputHls 上設定 FragmentsPerSegment，即可變更每個區段比例的片段。

您也可以在 ChanneInput 上設定 KeyFrameInterval 屬性，即可變更主要畫面格間隔值。

如果您明確設定 KeyFrameInterval，就會使用上述的規則計算 FragmentsPerSegment HLS 區段封裝比率。

如果您明確設定 KeyFrameInterval 和 FragmentsPerSegment，媒體服務會使用您所設定的值。


####允許的 IP 位址

您可以定義允許將視訊發行到這個通道的 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。

###通道預覽 

####預覽 URL

通道會在進一步處理和傳遞之前，提供您用來預覽及驗證串流的預覽端點 (預覽 URL)。

當您建立通道時，您可以取得預覽 URL。若要取得此 URL，通道不一定要在**執行**狀態。

通道開始內嵌資料後，您就可以預覽您的資料流。

請注意，無論指定的輸入類型為何，預覽資料流目前只能利用 Fragmented MP4 (Smooth Streaming) 格式傳遞。您可以使用 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) 播放器測試 Smooth Stream。您也可以使用 Azure 管理入口網站中架設的播放程式來查看您的資料流。


####允許的 IP 位址

您可以定義允許連接到預覽端點的 IP 位址。如果沒有指定 IP 位址，將會允許任何 IP 位址。允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

###通道輸出

如需詳細資訊，請參閱[設定主要畫面格間隔](#keyframe_interval)章節。


###通道的程式

通道與程式相關聯，而程式可讓您控制即時串流中區段的發行和儲存。「通道」會管理「程式」。「通道」與「程式」的關係與傳統媒體十分類似，其中，通道具有固定的內容串流，而且程式的範圍是該通道上的某個計時事件。

設定 [**封存時間範圍**] 長度，即可指定您想要保留程式之錄製內容的時數。此值可以設為最少 5 分鐘到最多 25 個小時。封存時間範圍長度也會指出用戶端可以從目前即時位置及時往回搜尋的最大時間量。程式在超過指定的時間量後還是可以執行，但是會持續捨棄落後時間範圍長度的內容。此屬性的這個值也會決定用戶端資訊清單可以成長為多長的時間。

每個程式都與儲存串流內容的資產相關聯。資產會對應到 Azure 儲存體帳戶中的 blob 容器，且資產中的檔案會儲存為該容器中的 blob。若要發行程式讓您的客戶檢視串流，您必須建立相關聯資產的隨選定位器。擁有此定位器，可讓您建立可以提供給用戶端的串流 URL。

通道支援最多三個同時執行的程式，因此您可以建立相同內送串流的多個封存。這可讓您視需要發佈和封存事件的不同部分。例如，您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。為了達成此目的，您必須建立兩個同時執行的程式。其中一個程式設定為封存 6 小時的事件，但是未發行該程式。另一個程式則設定為封存 10 分鐘，並發行程式。

您不應該將現有程式重複用於新的事件。而是為每個事件建立並啟動新的程式 (如＜程式設計即時串流應用程式＞一節中所述)。

當您準備好開始串流和封存時，請啟動程式。每當您想要停止串流處理和封存事件時，請停止程式。

若要刪除封存的內容，請停止並刪除程式，然後刪除相關聯的資產。如果程式使用資產，則無法刪除資產；必須先刪除程式。

只要您未刪除資產，即使在停止並刪除程式之後，使用者還是可以視需求將封存的內容串流為視訊。

如果想要保留封存的內容，但不要讓它可進行串流處理，請刪除串流定位器。

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

###隱藏式字幕和廣告插入 

下表示範支援的隱藏式字幕和廣告插入標準。

<table border="1">
<tr><th>標準</th><th>注意事項</th></tr>
<tr><td>CEA-708 和 EIA-608 (708/608)</td><td>CEA-708 和 EIA-608 是美國和加拿大的隱藏式字幕標準。<br/>目前只有編碼的輸入資料流附帶字幕時，才能播放字幕。您使用的即時媒體編碼器，必須可以將 608 或 708 字幕插入至已傳送至媒體服務的已編碼資料流。媒體服務會將內含字幕的內容傳遞給您的檢視器。</td></tr>
<tr><td>.ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)</td><td>媒體服務動態封裝功能可讓您的用戶端傳送以下任何格式的內容：MPEG DASH、HLS 或 Smooth Streaming。不過，如果您內嵌 Fragmented MP4 (Smooth Streaming) 而且在 .ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)，您就只能將資料流傳遞至 Smooth Streaming 用戶端。</td></tr>
<tr><td>SCTE-35</td><td>數位訊號系統，用來提示廣告插入。下游接收端會使用信號並根據分配的時間，將廣告切割成資料流。SCTE 35 必須以鬆散播放軌的形式傳送至輸入資料流中。<br/>請注意，目前唯一支援附帶廣告訊號的輸入資料流格式是 Fragmented MP4 (Smooth Streaming)。唯一支援的輸出格式也是 Smooth Streaming。</td></tr>
</table>

##<a id="Considerations"></a>考量

使用內部部署即時編碼器並將多位元速率資料流傳送到通道時，請注意以下限制：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (10 個圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 


以下是其他與通道和相關元件應用有關的注意事項：

- 每當您重新設定即時編碼器，請呼叫通道上的**重設**方法。重設通道之前，您必須停止程式。重設通道之後，請重新啟動程式。 
- 只有當通道處於執行中的狀態，且通道上的所有程式皆已停止時，才能停止通道。
- 依預設，您最多可以在媒體服務帳戶中新增 5 個通道。如需詳細資訊，請參閱[配額和限制](media-services-quotas-and-limitations.md)。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 只有當您的通道處於**執行中**狀態時，才會向您計費。若需詳細資訊，請參閱[這個](media-services-manage-channels-overview.md#states)章節。

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

###建立通道，從內部部署編碼器接收多位元速率即時串流

如需內部部署即時編碼器的詳細資訊，請參閱[搭配使用協力廠商即時編碼器與 Azure 媒體服務](https://msdn.microsoft.com/library/azure/dn783464.aspx)。

選擇**入口網站**、**.NET**、**REST API** 以了解如何建立及管理通道和程式。

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

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

- [動態封裝概觀](media-services-dynamic-packaging-overview.md)

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

[Azure 媒體服務的分散 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)

[使用 Azure 媒體服務傳遞即時串流事件](media-services-live-streaming-workflow.md)

[媒體服務概念](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
 

<!---HONumber=July15_HO2-->