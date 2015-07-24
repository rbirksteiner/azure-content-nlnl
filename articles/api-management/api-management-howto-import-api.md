<properties 
	pageTitle="API 管理主要概念" 
	description="了解 API、產品、角色、群組及其他 API 管理的主要概念。" 
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

# 如何在 Azure API 管理中連同操作一起匯入 API 的定義

在 API 管理中，可手動建立新的 API 並新增作業，或在一個步驟中連同作業一起匯入 API。

您可以使用下列格式來匯入 API 及其操作。

-	WADL
-	Swagger

本指南示範如何在一個步驟中建立新的 API 並匯入其操作。如需有關手動建立 API 和加入操作的資訊，請參閱[如何建立 API][] 和[如何將操作加入至 API][]。

## <a name="import-api"> </a>匯入 API

API 是在發行者入口網站中建立和設定。若要存取發行者入口網站，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

![發行者入口網站][api-management-management-console]

從左邊的 [API 管理] 功能表中按一下 [API]，然後按一下 [匯入 API]。

![Import API][api-management-import-apis]

[匯入 API] 視窗有三個索引標籤，分別對應至三種提供 API 規格的方式。

-	[From clipboard] 可讓您將 API 規格貼到指定的文字方塊中。
-	[從檔案] 可讓您瀏覽並選取含有 API 規格的檔案。
-	[From URL] 可讓您提供指向 API 規格的 URL。

![Import API format][api-management-import-api-clipboard]

提供 API 規格之後，請使用右邊的選項按鈕來指出規格格式。支援下列格式。

-	WADL
-	Swagger

接下來，輸入 [Web API URL 尾碼]。這會附加至 API 管理服務的基礎 URL 後面。基礎 URL 是 API 管理服務的每一個執行個體上主控的所有 API 所共有。API 管理依尾碼來區分 API，因此，特定 API 管理服務執行個體中的每一個 API 必須有唯一的尾碼。

輸入所有的值之後，按一下 [**儲存**]，以建立 API 和相關聯的操作。

>[AZURE.NOTE]如需以 Swagger 格式匯入基本計算機 API 的教學課程，請參閱 [在 Azure API 管理中管理您的第一個 API](api-management-get-started.md)。

## <a name="export-api"> </a> 匯出 API

除了匯入新的 API 之外，您還可以從發行者入口網站匯出 API 的定義。若要這樣做，請從 **API** 的 [摘要] 索引標籤中按一下 [匯出 API]。

![Export API][api-management-export-api]

您可以使用 WADL 或 Swagger 來匯出 API。選取所需的格式，按一下 [儲存]，並選擇用來儲存檔案的位置。

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>後續步驟

建立 API 並匯入操作之後，您就可以檢閱和設定其他任何設定、將 API 加入至產品，以及發佈它供開發人員使用。如需詳細資訊，請參閱下列指南。

-	[如何設定 API 設定][]
-	[如何建立和發佈產品][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[建立 API 管理服務執行個體]: api-management-get-started.md
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance

[如何將操作加入至 API]: api-management-howto-add-operations.md
[如何建立和發佈產品]: api-management-howto-add-products.md
[如何建立 API]: api-management-howto-create-apis.md
[如何設定 API 設定]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=62-->