<properties 
	pageTitle="如何調整媒體服務" 
	description="了解如何指定要佈建給帳戶的隨選串流保留單元和編碼保留單元，以調整媒體服務。" 
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
	ms.date="04/24/2015" 
	ms.author="juliako"/>


#如何調整媒體服務  

##概觀

您可以指定要佈建給帳戶的**串流保留單元**和**編碼保留單元**，藉以調整**媒體服務**。

您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。每個儲存體帳戶的限制為 500 TB。若想將儲存空間擴大為超過預設限制，您可以選擇將多個儲存體帳戶附加至單一媒體服務帳戶。

本主題會連結相關主題。

##<a id="streaming_endpoins"></a>串流保留單位

如需詳細資訊，請參閱[調整串流單位](media-services-manage-origins.md#scale_streaming_endpoints)。

##<a id="encoding_reserved_units"></a>編碼保留單元

如需瞭解如何調整編碼單位，請參閱下列**入口網站**和 **.NET** 主題。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

請注意，編碼與編製索引工作的保留單位都相同。

##<a id="storage"></a>調整儲存體

如需詳細資訊，請參閱[管理多個儲存體帳戶的媒體服務資產](https://msdn.microsoft.com/library/azure/dn271889.aspx)和[使用 Azure 儲存體](https://msdn.microsoft.com/library/azure/dn767951.aspx)。



 

<!---HONumber=62-->