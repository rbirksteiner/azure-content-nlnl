<properties
	pageTitle="如何將 Azure API 管理服務執行個體部署到多個 Azure 區域"
	description="瞭解如何將 Azure API 管理服務執行個體部署到多個 Azure 區域。" 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="sdanie"/>

# 如何將 Azure API 管理服務執行個體部署到多個 Azure 區域

API 管理支援多區域部署，可讓 API 發行者跨所需的任意數量 Azure 區域發佈單一 API 管理服務。這有助於降低地理上分散的 API 取用者感受到的要求延遲，並且可以改善某個區域離線時服務的可用性。

最初建立 API 管理服務時，它只會包含一個[單位][]，並且位在指派做為主要區域的單一 Azure 區域中。可透過 Azure 入口網站輕鬆加入其他區域。API 管理 Proxy 伺服器會部署到每個區域，並且將呼叫流量遞送到最近的 Proxy。當區域離線時，流量會自動重新導向到下一個最近的 Proxy。

> [AZURE.IMPORTANT]多重區域部署僅供**[進階][]**層使用。

## <a name="add-region"> </a>部署 API 管理服務執行個體到新區域

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

導覽至 API 管理服務執行個體的 Azure 入口網站中的 [**調整**] 索引標籤。

![調整索引標籤][api-management-scale-service]

若要部署新區域，請按一下主要區域下的下拉式清單，然後從清單選擇一個區域。

![加入區域][api-management-add-region]

選取區域之後，從下拉式清單選擇新區域的單位數量。

![指定單位][api-management-select-units]

設定需要的區域和單位之後，請按一下 [**儲存**]。

## <a name="remove-region"> </a>從區域刪除 API 管理服務執行個體

若要從區域移除 API 管理服務執行個體，請導覽至 API 管理服務執行個體的 Azure 入口網站中的 [**調整**] 索引標籤。

![調整索引標籤][api-management-scale-service]

按一下所需區域右側的 **X** 來移除。

![移除區域][api-management-remove-region]

移除所需區域之後，按一下 [**儲存**]。


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance
[建立 API 管理服務執行個體]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[單位]: http://azure.microsoft.com/pricing/details/api-management/
[進階]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=62-->