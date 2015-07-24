<properties 
	pageTitle="播放您的內容" 
	description="本主題列出現有的播放程式，您可以使用來播放您的內容。" 
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


#使用現有播放器來播放您的內容

Azure 媒體服務支援許多熱門的串流格式，例如 Smooth Streaming、HTTP 即時資料流和 MPEG-Dash。本主題會指引您可用來測試串流的現有播放程式。

>[AZURE.NOTE]若要播放動態封裝或動態加密的內容，請務必從您打算傳遞內容的串流端點取得至少一個串流單元。如需調整串流單位的相關資訊，請參閱： [如何調整串流單位](media-services-manage-origins.md#scale_streaming_endpoints)。

###Azure 管理入口網站媒體服務內容播放程式

**Azure 管理入口網站**提供內容播放程式，您可用來測試您的視訊。

按一下想用的視訊 (請確定它[已發行](media-services-manage-content.md#publish))，按一下入口網站底部的 [**播放**] 按鈕。
 
適用一些考量事項：

- **媒體服務內容播放程式**會從預設串流端點播放。如果您想要從非預設串流端點播放，請使用其他播放程式。例如，[Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
 

![AMSPlayer][AMSPlayer]

###Azure 媒體服務播放器

使用 [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)播放以下任一格式的內容 (清除或受保護)：

- Smooth Streaming
- MPEG DASH
- HLS
- Progressive MP4


###Flash Player

####AES 加密與權杖 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

###Silverlight 播放程式

####監視

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####PlayReady 與權杖

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### DASH 播放程式

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###其他

若要測試 HLS URL，您也可以使用：

- iOS 裝置上的 **Safari** 或
- Windows 上的 **3ivx HLS 播放器**。

##開發視訊播放器

如需瞭解如何自行開發播放程式，請參閱[開發視訊播放器](media-services-develop-video-players.md)
 
[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=July15_HO2-->