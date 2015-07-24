<properties 
	pageTitle="Azure 媒體服務概觀" 
	description="本主題提供 Azure 媒體服務的概觀" 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Azure 媒體服務概觀

Microsoft Azure 媒體服務是一個可延伸的雲端型平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。媒體服務是以 REST API 為基礎，可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時資料流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

您可以建置完全採用媒體服務的端對端工作流程。您也可以選擇在工作流程的某些部分採用協力廠商元件。例如，使用第三方編碼器來進行編碼；然後使用媒體服務上傳、保護、封裝、傳遞。

若要建置媒體服務解決方案，您可以使用：

- [媒體服務 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 其中一個可用的用戶端 SDK：[Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、[Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media)、[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 現有工具：[Azure 管理入口網站](http://manage.windowsazure.com/)或 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)。


下列海報說明從媒體建立到耗用量的 Azure 媒體服務工作流程。您可以從這裡下載海報：[Azure 媒體服務海報](http://www.microsoft.com/download/details.aspx?id=38195)

![概觀][overview]

**服務等級協定 (SLA)**：

- 對於媒體服務編碼，我們保證 REST API 交易可用性高達 99.9%。
- 對於串流，我們在至少購買一個串流單位時，針對現有的媒體內容，可保證以 99.9% 的可用性成功服務要求。
- 對於即時通道，我們保證執行中的通道至少 99.9% 的時間具有外部連線能力。
- 對於內容保護，我們保證至少 99.9% 的時間將會成功滿足金鑰要求。
- 對於索引子，我們將會在 99.9% 的時間成功服務編碼保留單元處理的索引子工作要求。

	如需詳細資訊，請參閱 [Microsoft Azure SLA](http://azure.microsoft.com/support/legal/sla/)。

##概念

如需詳細資訊，請參閱[概念](media-services-concepts.md)。


##利用 Azure 媒體服務提供點播媒體

下列主題說明一般媒體服務隨選視訊工作流程的步驟。這個主題會連結至其他主題，說明如何使用媒體服務支援的技術達成這些步驟。

[利用 Azure 媒體服務提供點播媒體](media-services-video-on-demand-workflow.md)。

##利用 Azure 媒體服務提供即時資料流

下列主題說明一般媒體服務即時串流工作流程的步驟。這個主題會連結至其他主題，說明如何使用媒體服務支援的技術達成這些步驟。

[利用 Azure 媒體服務提供即時串流](media-services-live-streaming-workflow.md)。

##使用內容

Azure 媒體服務提供一些工具，供您用來建立適用於大部分平台的豐富、動態用戶端播放器應用程式，此處所述的平台包括：iOS 裝置、Android 裝置、Windows、Windows Phone、Xbox 和機上盒。下列主題也會提供 SDK 和 Player Framework 連結，可讓您開發自己的用戶端應用程式，使用來自媒體服務的串流媒體。

[開發視訊播放器應用程式](media-services-develop-video-players.md)

##模式與實例指南

[模式與實例指南](https://wamsg.codeplex.com/) [線上文件](https://msdn.microsoft.com/library/dn735912.aspx) [可下載的電子書](https://www.microsoft.com/download/details.aspx?id=42629)

##支援

[Azure 支援](http://azure.microsoft.com/support/options/)提供 Azure 的支援選項，包括媒體服務。

##後續步驟

[利用 Azure 媒體服務提供即時資料流](media-services-live-streaming-workflow.md)

[開發視訊播放器應用程式](media-services-develop-video-players.md)
 
[開發視訊播放器應用程式](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO2-->