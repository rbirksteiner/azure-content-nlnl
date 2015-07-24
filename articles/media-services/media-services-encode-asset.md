<properties 
	pageTitle="透過 Azure Media Services 編碼的隨選內容" 
	description="本主題簡介如何使用 Media Services 編碼隨選內容。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#透過 Azure Media Services 編碼的隨選內容

本主題是[媒體服務隨選視訊工作流程](media-services-video-on-demand-workflow.md)的一部分。

##概觀

媒體服務支援下列編碼器：

- [Azure Media Encoder](#azure_media_encoder)
- [Media Encoder Premium Workflow](#media_encoder_premium_workflow) (公用預覽版)

[下列章節](#compare_encoders)會比較兩個編碼器的功能。

依預設，每個媒體服務帳戶一次可以有一個進行中的編碼工作。您可以保留編碼單位，這樣就可以同時執行多個編碼工作，其中一個用於您購買的每一個編碼保留單位。如需瞭解如何調整編碼單位，請參閱下列**入口網站**和 **.NET** 主題。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Azure 媒體編碼器

[媒體服務編碼器支援的格式](media-services-azure-media-encoder-formats.md) – 討論 **Azure 媒體編碼器**支援的檔案和串流格式。

**Azure 媒體編碼器**是使用其中一種編碼器預設字串設定的，相關資訊請參閱[這裡](https://msdn.microsoft.com/library/azure/dn619392.aspx)。您也可以從[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)取得實際的 Azure 媒體編碼器預設檔案。

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####其他相關主題

[動態封裝](https://msdn.microsoft.com/library/azure/jj889436.aspx) – 描述如何編碼為調適性位元速率 MP4，並動態提供 Smooth Streaming、 Apple HLS 或 MPEG-DASH。

[控制媒體服務編碼器輸出檔案名稱](https://msdn.microsoft.com/library/azure/dn303341.aspx) – 描述 Azure Media Encoder 使用的檔案名稱慣例以及如何修改輸出檔名稱。

[使用 Dolby Digital Plus 將您的媒體編碼](media-services-encode-with-dolby-digital-plus.md) – 描述如何使用 Dolby Digital Plus 編碼技術進行曲目的編碼。


##<a id="media_encoder_premium_wokrflow"></a>Media Encoder Premium Workflow (公用預覽版)

**注意** 本主題中討論的 Media Encoder Premium Workflow 媒體編碼器不適用於中國。

[Media Encoder Premium Workflow 支援的格式](media-services-premium-workflow-encoder-formats.md) – 討論 **Media Encoder Premium Workflow** 支援的檔案格式和轉碼器。

**Media Encoder Premium Workflow** 使用複雜的工作流程設定。您可以使用[工作流程設計工具](media-services-workflow-designer.md)建立工作流程檔案。

您可在[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)取得預設的工作流程檔案。資料夾也包含這些檔案的說明。

利用 **Media Encoder Premium Workflow** 並搭配 **.NET** 進行編碼。如需詳細資訊，請參閱[使用 Media Encoder Premium Workflow 進行進階編碼](media-services-encode-with-premium-workflow.md)。
 

##<a id="compare_encoders"></a>比較編碼器

本節比較 **Azure Media Encoder** 和 **Media Encoder Premium Workflow** 的編碼功能。

###輸入格式

輸入容器/檔案格式

<table border="1">
<tr><th>輸入容器/檔案格式</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>是</td><td>否</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>是</td><td>限制</td></tr>
<tr><td>GXF</td><td>是</td><td>否</td></tr>
<tr><td>MPEG-2 傳輸資料流</td><td>是</td><td>是</td></tr>
<tr><td>MPEG-2 程式資料流</td><td>是</td><td>是</td></tr>
<tr><td>MPEG-4/MP4</td><td>是</td><td>是</td></tr>
<tr><td>Windows Media/ASF</td><td>是</td><td>是</td></tr>
<tr><td>AVI (未壓縮 8 位元/10 位元)</td><td>是</td><td>是</td></tr>
<tr><td>3GPP/3GPP2</td><td>否</td><td>是</td></tr>
<tr><td>Smooth Streaming 檔案格式 (PIFF 1.3)</td><td>否</td><td>是</td></tr>
</table>

輸入視訊轉碼器

<table border="1">
<tr><th>輸入視訊轉碼器</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra</td><td>是</td><td>唯一的 8 位元 4:2:0</td></tr>
<tr><td>Avid DNxHD (使用 MXF)</td><td>是</td><td>否</td></tr>
<tr><td>DVCPro/DVCProHD (使用 MXF)</td><td>是</td><td>否</td></tr>
<tr><td>JPEG2000</td><td>是</td><td>否</td></tr>
<tr><td>MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)</td><td>是</td><td>最高 422 設定檔</td></tr>
<tr><td>MPEG-1</td><td>是</td><td>是</td></tr>
<tr><td>Windows Media 視訊/VC-1</td><td>是</td><td>是</td></tr>
<tr><td>Canopus HQ/HQX</td><td>否</td><td>是</td></tr>
</table>

輸入音訊轉碼器

<table border="1">
<tr><th>輸入音訊轉碼器</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M 和 302M，AES3-2003)</td><td>是</td><td>否</td></tr>
<tr><td>Dolby® E</td><td>是</td><td>否</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>是</td><td>是</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>是</td><td>否</td></tr>
<tr><td>AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)</td><td>是</td><td>是</td></tr>
<tr><td>MPEG Layer 2</td><td>是</td><td>是</td></tr>
<tr><td>MP3 (MPEG-1 音訊層 3)</td><td>是</td><td>是</td></tr>
<tr><td>Windows Media 音訊</td><td>是</td><td>是</td></tr>
<tr><td>WAV/PCM</td><td>是</td><td>是</td></tr>
</table>

###輸出格式

輸出容器/檔案格式

<table border="1">
<tr><th>輸出容器/檔案格式</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>是</td><td>否</td></tr>
<tr><td>MXF (OP1a、XDCAM 和 AS02)</td><td>是</td><td>否</td></tr>
<tr><td>DPP (包括 AS11)</td><td>是</td><td>否</td></tr>
<tr><td>GXF</td><td>是</td><td>否</td></tr>
<tr><td>MPEG-4/MP4</td><td>是</td><td>是</td></tr>
<tr><td>Windows Media/ASF</td><td>是</td><td>是</td></tr>
<tr><td>AVI (未壓縮 8 位元/10 位元)</td><td>是</td><td>否</td></tr>
<tr><td>Smooth Streaming 檔案格式 (PIFF 1.3)</td><td>是</td><td>是</td></tr>
</table>

輸出視訊轉碼器

<table border="1">
<tr><th>輸出視訊轉碼器</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264；8 位元；高達 High Profile、Level 5.2；4K Ultra HD；AVC Intra)</td><td>是</td><td>唯一的 8 位元 4:2:0 最高 1080p</td></tr>
<tr><td>Avid DNxHD (使用 MXF)</td><td>是</td><td>否</td></tr>
<tr><td>DVCPro/DVCProHD (使用 MXF)</td><td>是</td><td>否</td></tr>
<tr><td>MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)</td><td>是</td><td>否</td></tr>
<tr><td>MPEG-1</td><td>是</td><td>否</td></tr>
<tr><td>Windows Media 視訊/VC-1</td><td>是</td><td>是</td></tr>
<tr><td>JPEG 縮圖建立</td><td>是</td><td>是</td></tr>
</table>

輸出音訊轉碼器

<table border="1">
<tr><th>輸出音訊轉碼器</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M 和 302M，AES3-2003)</td><td>是</td><td>否</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>是</td><td>是</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) 高達 7.1</td><td>是</td><td>最高 5.1</td></tr>
<tr><td>AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)</td><td>是</td><td>是</td></tr>
<tr><td>MPEG Layer 2</td><td>是</td><td>否</td></tr>
<tr><td>MP3 (MPEG-1 音訊層 3)</td><td>是</td><td>否</td></tr>
<tr><td>Windows Media 音訊</td><td>是</td><td>是</td></tr>
</table>
##相關文章

- [介紹 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [如何使用 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [配額和限制](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->