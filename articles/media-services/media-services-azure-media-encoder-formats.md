<properties 
	pageTitle="Azure Media Encoder 格式和轉碼器" 
	description="本主題提供 Azure 媒體編碼器格式和轉碼器的概觀。" 
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

#Azure Media Encoder 格式和轉碼器

編碼器會使用轉碼器壓縮數位媒體。編碼器通常會有各種設定，可讓您指定產生的媒體屬性，例如使用的轉碼器、檔案格式、解析度和位元速率。檔案格式是容器，可用來保存已壓縮視訊以及使用哪些轉碼器來壓縮視訊等相關資訊。

轉碼器有兩個元件：一個用來壓縮數位媒體檔案以利傳輸，另一個元件則是解壓縮數位媒體檔案以利播放。音訊轉碼器用於壓縮和解壓縮音訊，視訊轉碼器則用於壓縮和解壓縮視訊。轉碼器可以運用不失真或失真壓縮。不失真轉碼器在進行壓縮時會保留所有資訊。解壓縮檔案時，產生的檔案與輸入媒體完全相同，因此不失真轉碼器相當適合用於封存和儲存。失真轉碼器在進行編碼時會遺失部分資訊並產生較小的檔案 (與原始檔案相比)，代價是減損視訊品質，適用於透過網際網路串流處理。Azure Media Encoder 用來編碼的兩種主要轉碼器是 H.264 和 VC-1。其他轉碼器可能適用於我們合作夥伴的編碼器生態系統。

請務必了解轉碼器和檔案格式之間的差異。轉碼器是實作壓縮/解壓縮演算法的軟體，而檔案格式是保存已壓縮視訊的容器。如需詳細資訊，請參閱[編碼和封裝](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/)。

本文件包含您可以在 Azure Media Encoder 中使用的常見匯入和匯出檔案格式清單。


[媒體編碼器匯入格式](#import_formats)

[媒體編碼器匯出格式](#export_formats)


##<a id="import_formats"></a>媒體編碼器匯入格式 

下節會列出支援匯入的轉碼器和檔案格式清單。

###視訊轉碼器

- H.264 (基準、主要和高設定檔)
- MPEG-1 (包括 MPEG-PS)
- MPEG-2 (簡易和主要設定檔及 4:2:2 設定檔)
- MPEG-4 v2 (簡易視覺設定檔和進階簡易設定檔)
- VC-1 (簡易、主要和進階設定檔)
- Windows Media 視訊 (簡易、主要和進階設定檔)
- DV (DVC、DVHD、DVSD、DVSL)
- Grass Valley HQ/HQX
 
###音訊轉碼器

- AC-3 (Dolby Digital 音訊)
- AAC (AAC-LC、搭配 AAC-LC 核心的 HE-AAC v1，和搭配 AAC-LC 核心的 HE-AAC v2)
- MP3
- Windows Media 音訊 9 (Windows Media Audio Standard、Windows Media Audio Professional 和 Windows Media Audio Lossless)

###視訊檔案格式
 
<table border="1">
<tr><th>檔案格式</th><th>檔案副檔名</th></tr>
<tr><td>3GPP、3GPP2</td><td>.3gp、.3g2、.3gp2</td></tr>
<tr><td>進階系統格式 (ASF)</td><td>.asf</td></tr>
<tr><td>進階視訊編碼高畫質 (AVCHD) [MPEG-2 傳輸資料流]</td><td>.mts、.m2ts</td></tr>
<tr><td>音訊視訊交錯格式 (AVI)</td><td>.avi</td></tr>
<tr><td>數位攝影機 MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>DVD 傳輸串流 (TS) 檔案</td><td>.ts</td></tr>
<tr><td>DVD 視訊物件 (VOB) 檔案</td><td>.vob</td></tr>
<tr><td>Expression Encoder 螢幕擷取轉碼器檔案</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>MPEG-1 系統資料流</td><td>.mpeg、.mpg</td></tr>
<tr><td>MPEG-2 視訊檔案</td><td>.m2v</td></tr>
<tr><td>Smooth Streaming 檔案格式 (PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>Windows Media 視訊 (WMV)</td><td>.wmv</td></tr>
</table>

支援部分未壓縮格式。如需詳細資訊，請參閱[支援的未壓縮視訊格式](#uncompressed)

###音訊檔案格式

<table border="1">
<tr><th>檔案格式</th><th>檔案副檔名</th></tr>
<tr><td>AC-3 (Dolby Digital) 音訊</td><td>.ac3</td></tr>
<tr><td>音訊交換檔案格式 (AIFF)</td><td>.aiff</td></tr>
<tr><td>廣播聲波格式</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 音訊層 3)</td><td>.mp3</td></tr>
<tr><td>MP4 音訊</td><td>.m4A</td></tr>
<tr><td>MPEG-4 有聲書</td><td>.m4b</td></tr>
<tr><td>WAVE 檔案</td><td>.wav</td></tr>
<tr><td>Windows Media 音訊</td><td>.wma</td></tr>   
</table>

###影像檔案格式

<table border="1">
<tr><th>檔案格式</th><th>檔案副檔名</th></tr>
<tr><td>Bitmap</td><td>.bmp</td></tr>
<tr><td>GIF、動畫 GIF</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg、.jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF 畫布 XAML</td><td>.xaml</td></tr>
</table>


##<a id="export_formats"></a>媒體編碼器匯出格式

下表會列出支援匯出的轉碼器和檔案格式清單。


<table border="1">
<tr><th>檔案格式</th><th>視訊轉碼器</th><th>音訊轉碼器</th></tr>
<tr><td>Windows Media (*.wmv、*.wma)</td><td>VC-1 (進階、主要和簡易設定檔)</td><td>Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Voice、Windows Media Audio Lossless</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (高、主要和基準設定檔)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2、Dolby Digital Plus</td></tr>
<tr><td>Smooth Streaming 檔案格式 (PIFF 1.1) (*.ismv、*.isma)</td><td>VC-1 (進階設定檔)<br/><br/>
H.264 (高、主要和基準設定檔)</td><td>Windows Media Audio Standard、Windows Media Audio Professional<br/><br/>
AAC-LC、HE-AAC v1、HE-AAC v2</td></tr>
</table>

如需媒體服務中其他支援的轉碼器和篩選器，請參閱 [Windows DirectShow 篩選](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx)。

##<a id="uncompressed"></a>支援的未壓縮視訊格式 

Azure 媒體服務提供匯入未壓縮視訊資料的支援。

以下是支援的未壓縮格式部分清單。

<table border="1">
<tr><th>未壓縮的視訊格式</th><th>說明</th></tr>
<tr><td>標準 YVU9 格式未壓縮資料</td><td>平面 YUV 格式。每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線上的像素都是 Y 樣本，每第四條垂直線上的像素是 U 和 V 樣本。每像素 9 位元。</td></tr>
<tr><td>YUV 411 格式資料</td><td>每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 12 位元組的區塊有 8 個影像像素。</td></tr>
<tr><td>Y41P 格式資料</td><td>每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 12 位元組的區塊有 8 個影像像素。</td></tr>
<tr><td>YUY2 格式資料</td><td>與 UYVY 相同，但有不同的像素順序。位元組順序 (從低到高) 會是 Y0、U0、Y1、V0、Y2、U2、Y3、V2、Y4、U4、Y5、V4，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。</td></tr>
<tr><td>YVYU 格式資料</td><td>壓縮 YUV 格式。與 UYVY 相同，但有不同的像素順序。位元組順序 (從低到高) 會是 Y0、V0、Y1、U0、Y2、V2、Y3、U2、Y4、V4、Y5、U4，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。</td></tr>
<tr><td>UYVY 格式資料</td><td>壓縮 YUV 格式。每行的每個水平像素都是 Y 樣本，每第二個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。最常用的各種 YUV 4:2:2 格式。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U2、Y2、V2、Y3、U4、Y4、V4、Y5，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。</td></tr>
<tr><td>YUV 211 格式資料</td><td>壓縮 YUV 格式。每行的每第二個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 4 個影像像素。</td></tr>
<tr><td>Cirrus Logic Jr YUV 411 格式</td><td>Cirrus Logic Jr YUV 411 格式， 每個 Y、U 和 V 樣本小於 8 位元。每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。</td></tr>
<tr><td>Indeo-produced YVU9 格式</td><td>Indeo-produced YVU9 格式，包含與最後一個影格的區別額外資訊。每個像素 9.5 位元，但回報為 9 位元。</td></tr>
</table>

<!---HONumber=July15_HO2-->