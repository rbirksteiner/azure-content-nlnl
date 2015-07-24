<properties 
	pageTitle="如何將操作加入至 Azure API 管理中的 API" 
	description="了解如何將操作加入 Azure API 管理中的 API。" 
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

# 如何將操作加入至 Azure API 管理中的 API

您必須先加入作業，才能夠使用 API 管理中的 API。本指南示範如何在 API 管理中對 API 加入和設定不同類型的作業。

## <a name="add-operation"> </a>新增作業

請在發行者入口網站新增和設定 API 的作業。若要存取發行者入口網站，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

在 API 管理入口網站中選取所需的 API，然後選取 [操作] 索引標籤。

![Operations][api-management-operations]

按一下 [加入作業] 以加入新的作業。將顯示 [新增作業]，並且預設會選取 [簽章] 索引標籤。

![Add operation][api-management-add-operation]

從下拉式清單中選擇，指定 [HTTP 指令動詞]。

![HTTP method][api-management-http-method]

輸入 URL 片段 (由一或多個 URL 路徑區段及零個以上的查詢字串參數組成)，以定義 URL 範本。URL 範本 (附加至 API 的基礎 URL) 可識別單一 HTTP 操作。可能包含一或多個以大括弧識別的具名變數部分。這些變數部分稱為範本參數，當 API 管理平台處理要求時，就會動態地指派從要求的 URL 中擷取的值給這些變數。

![URL template][api-management-url-template]

若需要，請指定 [Rewrite URL template]。這樣可讓您在前端使用標準 URL 範本來處理傳入的要求，同時根據重寫範本利用轉換過的 URL 來呼叫後端。重寫範本中應該使用 URL 範本中的範本參數。下列範例顯示如何利用 URL 範本，將前一個範例的 Web 服務中以路徑區段編碼的內容類型，提供給透過 API 管理平台所發佈的 API 中作為查詢參數。

![URL template rewrite][api-management-url-template-rewrite]

作業的呼叫端將使用格式 `/customers?customerid=ALFKI`，而當叫用後端服務時，這會對應至 `/Customers('ALFKI')`。


[顯示名稱] 和 [描述] 提供操作的描述，用來提供文件給在開發人員入口網站中使用此 API 的開發人員。

![說明][api-management-description]

在 [描述] 文字方塊中，可以用純文字或 HTML 來指定操作描述。

## <a name="operation-caching"> </a>作業快取

回應快取可縮短 API 取用者所感受的延遲、降低頻寬耗用量，以及減少實作 API 的 HTTP Web 服務的負載。

若要輕鬆又快速地啟用操作的快取，請選取 [快取] 索引標籤並核取 [啟用] 核取方塊。

![快取][api-management-caching-tab]

[持續期間] 指定操作回應保留在快取中的時間週期。預設值為 3600 秒或 1 小時。

快取索引鍵用來區分回應，以便讓對應至每一個不同快取索引鍵的回應取得各自的快取值。選擇性地，在 [依查詢字串參數改變] 和 [Vary by headers] 文字方塊中，分別輸入用來計算快取索引鍵值的特定查詢字串參數及/或 HTTP 標頭。未指定時，則會使用完整要求 URL 和下列 HTTP 標頭值來產生快取索引鍵：**Accept** 和 **Accept-Charset**。

>如需快取和快取原則的詳細資訊，請參閱[如何在 Azure API 管理中快取操作結果][]。


## <a name="request-parameters"> </a>要求參數

操作參數是在 [參數] 索引標籤上管理。[簽章] 索引標籤的 [URL 範本] 中指定的參數是自動加入，只能透過編輯 URL 範本才變更。可手動輸入其他參數。

若要加入新的查詢參數，請按一下 [加入查詢參數] 並輸入下列資訊：

-	**名稱** - 參數名稱。
-	**描述** - 參數的簡短描述 (選擇性)。
-	**類型** - 參數類型，從下拉式清單中選取。
-	**值** - 可指派給此參數的值。其中一個值可標示為預設值 (選擇性)。
-	**必要** - 選取此核取方塊將參數標示為必要。 

![Request parameters][api-management-request-parameters]

## <a name="request-body"> </a>要求本文

如果操作允許 (例如 PUT、POST) 且需要本文，您可以使用所有支援的表示法格式 (例如 json、XML) 來提供其範例。

>要求本文只作為文件說明用途，不需要驗證。

若要輸入要求本文，請切換至 [本文] 索引標籤。

按一下 [Add Representation]，開始輸入想要的內容類型名稱 (例如 application/json)，在下拉式清單中選取它，然後將所需的要求本文範例以選取的格式貼到文字方塊中。

![Request body][api-management-request-body]

除了表示法，您也可以在 [描述] 文字方塊中指定選用的文字描述。

## <a name="responses"> </a>回應

為作業可能產生的所有狀態碼提供回應範例是良好作法。每個狀態碼可能有不只一個回應本文範例，每個支援的內容類型則會有一個範例。

若要加入回應，請按一下 [加入]，開始輸入所需的狀態碼。在此範例中，狀態碼為 **200 OK**。當代碼出現在下拉式清單時，請選取它，回應碼就會建立並加入至您的操作。

![Response code][api-management-response-code]

按一下 [Add Representation]，開始輸入想要的內容類型名稱 (例如 application/json)，然後在下拉式清單中選取它。

![Body content type][api-management-response-body-content-type]

將回應本文範例以選取的格式貼到文字方塊中。

![Response body][api-management-response-body]

若需要，將選擇性描述加入至 [描述] 文字方塊。

設定操作之後，按一下 [儲存]。


## <a name="next-steps"> </a>後續步驟

當操作加入至 API 之後，下一步是將 API 與產品建立關聯並發佈，讓開發人員可呼叫其操作。

-	[如何建立和發佈產品][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[建立 API 管理服務執行個體]: api-management-get-started.md
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[如何建立和發佈產品]: api-management-howto-add-products.md
[如何在 Azure API 管理中快取操作結果]: api-management-howto-cache.md

<!---HONumber=62-->