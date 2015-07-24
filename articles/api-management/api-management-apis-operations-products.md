<properties 
	pageTitle="如何在 Azure API 管理中建立 API、作業和產品" 
	description="了解如何在 API 管理中建立 API、作業和產品。" 
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
	ms.date="05/19/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中建立 API、作業和產品

在 Azure API 管理中，會將 API 和其作業加入至產品中，開發人員可在產品中用其建置使用 API 的應用程式。本節中的指南將示範如何建立 API、加入作業至 API，以及將 API 與產品建立關聯並發佈以供開發人員使用。

## <a name="create-apis"> </a>如何建立 API

API 管理中的 API 代表可供用戶端應用程式叫用的一組作業。新 API 是在 API 管理入口網站中建立。

本指南示範如何在 API 管理中建立和設定新 API。

-   [如何建立 API][]

## <a name="add-operations"> </a>如何將作業新增至 API

您必須先加入作業，才能夠使用 API 管理中的 API。本指南示範如何在 API 管理中對 API 加入和設定不同類型的作業。

-   [如何將作業新增至 API][]

您也可以使用 WADL 或 Swagger 格式，在一個步驟中匯入 API 及其作業。

-	[如何匯入具有作業之 API 的定義][]

## <a name="add-product"> </a>如何建立和發佈產品

在 API 管理中，產品包含一或多個 API 以及使用量配額與使用規定。發佈產品之後，開發人員便可訂閱產品，並開始使用產品的 API。這些主題提供建立產品、加入 API 和發佈 API 供開發人員使用的指引。

-   [如何加入和發佈產品][]
-	[如何建立和設定進階產品設定][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[如何建立 API]: api-management-howto-create-apis.md
[如何將作業新增至 API]: api-management-howto-add-operations.md
[如何加入和發佈產品]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[如何匯入具有作業之 API 的定義]: api-management-howto-import-api.md
[如何建立和設定進階產品設定]: api-management-howto-product-with-rules.md

<!---HONumber=62-->