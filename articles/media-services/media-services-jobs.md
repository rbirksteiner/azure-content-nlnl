<properties 
	pageTitle="使用 Azure 媒體服務工作" 
	description="本主題提供如何管理「管理 Azure 媒體服務工作」的概觀。" 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#使用 Azure 媒體服務工作

**工作** (Job) 包含要進行之處理的相關中繼資料。每個**工作** (Job) 包含一或多個**工作** (Task)，指定不可部分完成的處理工作、其輸入資產、輸出資產、媒體處理器以及其關聯的設定。如需編碼器設定的詳細資訊，請參閱＜編碼器指南和編碼器結構描述＞。

編碼工作 (Job) 通常會與其他處理程序結合，例如封裝或加密資產或由編碼器產生的資產。工作 (Job) 中的工作 (Task) 可以鏈結在一起，其中一項工作 (Task) 的輸出資產指定為下一個工作 (Task) 的輸入資產。透過這種方式，一項工作 (Job) 可以包含一個媒體簡報所需的所有處理。

本節提供使用 Job\Task 時要執行之常見工作 (Task) 的連結。

>[AZURE.NOTE]目前有每個工作裡 30 個工作的限制。如果您需要鏈結超過 30 個工作，請建立多個工作來包含這些工作。


##取得媒體處理器

使用 **.NET** 或 **REST API** 取得媒體處理器。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##建立工作 (Job) 

工作 (Job) 是一個實體，其中包含一組工作 (Task，例如編碼或編製索引) 的相關中繼資料。每一項工作 (Task) 會對輸入資產執行不可部分完成的作業。如需如何建立編碼工作 (Job) 的範例，請參閱：

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##編製索引

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##編碼 

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##監視工作進度

使用 **Azure 管理入口網站**、**.NET** 或 **REST API** 監視工作進度。

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##相關連結

[配額和限制](media-services-quotas-and-limitations.md) – 說明使用的配額和媒體服務編碼器的限制
 

<!---HONumber=62-->