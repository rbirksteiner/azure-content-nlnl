<properties 
    pageTitle="使用索引子連接 DocumentDB 與 Azure 搜尋 | Azure" 
    description="本文將說明如何在 DocumentDB 中使用 Azure 搜尋索引子做為資料來源。"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA" 
    ms.workload="data-services" 
    ms.date="03/19/2015" 
    ms.author="andrl"/>

#使用索引子連接 DocumentDB 與 Azure 搜尋

如果您想要實作 DocumentDB 資料上的絕佳搜尋經驗，請在 DocumentDB 中使用 Azure 搜尋索引子！在本文中，我們將說明如何整合 Azure DocumentDB 與 Azure 搜尋，而不需要撰寫任何程式碼來維護索引的基礎結構！

若要設定此功能，您必須 [設定 Azure 搜尋帳戶](../search-get-started.md#start-with-the-free-service) (您不需要升級至標準搜尋)，然後呼叫 [Azure 搜尋 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 以建立 DocumentDB **資料來源**和該資料來源的**索引子**。

##<a id="Concepts"></a>Azure 搜尋索引子概念

Azure 搜尋支援建立與管理資料來源 (包括 DocumentDB) 和操作這些資料來源的索引子。

「**資料來源**」指定哪些資料需要編製索引、存取資料的認證，以及啟用 Azure 搜尋的原則，以便有效地識別資料中的變更 (例如修改或刪除集合內的文件)。資料來源會被定義為獨立的資源，因此可供多個索引子使用。

「**索引子**」說明資料如何從資料來源流動到目標搜尋索引。您應該規劃為每個目標索引和資料來源組合建立一個索引子。雖然您可以擁有寫入相同索引的多個索引子，但一個索引子只能寫入單一索引。索引子可用來： 

- 執行資料的一次性複製以填入索引。
- 依照排程將索引與資料來源中的變更同步。排程是索引子定義的一部分。
- 視需要叫用索引的隨選更新。 

##<a id="CreateDataSource"></a>步驟 1：建立資料來源

發出 HTTP POST 要求，以便在您的 Azure 搜尋服務中建立新的資料來源，包括下列要求標頭。
    
    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

需要 `api-version`。有效值包括 `2015-02-28` 或更新版本。

要求的主體包含資料來源定義，其中應包含下列欄位：

- **name**：資料來源的名稱。

- **type**：使用 `documentdb`。

- **credentials**：

    - **connectionString**：必要。以下列格式指定 Azure DocumentDB 資料庫的連接資訊： `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **container**：

    - **name**：必要。指定要編製索引的 DocumentDB 集合。 

    - **query**：選用。您可以指定查詢將任意 JSON 文件簡維成 Azure 搜尋可以編製索引的一般結構描述。

- **dataChangeDetectionPolicy**：選用。請參閱以下的 [資料變更偵測原則](#DataChangeDetectionPolicy)。

- **dataDeletionDetectionPolicy**：選用。請參閱以下的 [資料刪除偵測原則](#DataDeletionDetectionPolicy)。

###<a id="DataChangeDetectionPolicy"></a>擷取已變更的文件

資料變更偵測原則旨在有效率地識別已變更的資料項目。目前，唯一支援的原則是使用 `_ts` DocumentDB 所提供之上次修改時間戳記屬性的 `High Water Mark`原則，指定方式如下：

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts" 
    } 

您還必須為查詢在投射中加入 `_ts` 和  `WHERE` 子句。例如：

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>擷取已刪除的文件

當從來源資料表中刪除資料列時，您也應該在搜尋索引中刪除這些資料列。資料刪除偵測原則旨在有效率地識別已刪除的資料項目。目前，唯一支援的原則是「 `Soft Delete`」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a document as deleted" 
    }

> [AZURE.NOTE] 如果您打算使用自訂投射，則必須在 SELECT 子句中包含該屬性。

###<a id="CreateDataSourceExample"></a>要求內文範例

下列範例會建立包含自訂查詢和原則提示的資料來源：

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark" 
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###Response

如果已成功建立該資料來源，您將會收到一則 HTTP 201 已建立的回應。

##<a id="CreateIndex"></a>步驟 2：建立索引

建立目標 Azure 搜尋索引 (如果您尚未建立)。您可以從 [Azure 入口網站 UI](../search-get-started.md#test-service-operations) 或使用 [建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 來執行此作業。

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

###圖 A：JSON 資料類型與 Azure 搜尋資料類型之間的對應

<table style="font-size:12">
    <tr>
        <th>JSON 資料類型</th>
        <th>相容的目標索引欄位類型</th>
    </tr>
    <tr>
        <td>Bool</td>
        <td>Edm.Boolean、Edm.String</td>
    </tr>
    <tr>
        <td>看起來像是整數的數字</td>
        <td>Edm.Int32、Edm.Int64、Edm.String</td>
    </tr>
    <tr>
        <td>看起來像是浮點的數字</td>
        <td>Edm.Double、Edm.String</td>
    </tr>
    <tr>
        <td>字串</td>
        <td>Edm.String</td>
    </tr>
    <tr>
        <td>基本類型的陣列，例如 ["a"、"b"、"c"]</td>
        <td>Collection(Edm.String)</td>
    </tr>
    <tr>
        <td>看起來像是日期的字串</td>
        <td>Edm.DateTimeOffset、Edm.String</td>
    </tr>
    <tr>
        <td>JSON 物件</td>
        <td>N/A</td>
    </tr>
</table>

###<a id="CreateIndexExample"></a>要求內文範例

下列範例會建立包含識別碼和描述欄位的索引：

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###Response

如果已成功建立該索引，您將會收到一則 [HTTP 201 已建立] 的回應。

##<a id="CreateIndexer"></a>步驟 3：建立索引子

您可以使用包含下列標頭的 HTTP POST 要求，在 Azure 搜尋服務內建立新的索引子。
    
    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

要求的本文包含索引子定義，其中應包含下列欄位：

- **name**：必要。索引子的名稱。

- **dataSourceName**：必要。現有資料來源的名稱。

- **targetIndexName**：必要。現有索引的名稱。

- **schedule**：選用。請參閱以下的[索引排程](#IndexingSchedule)。

###<a id="IndexingSchedule"></a>依照排程執行索引子

索引子可以選擇性地指定排程。如果排程存在的話，則索引子會依照排程定期執行。排程具有下列屬性：

- **interval**：必要。可用來指定索引子執行間隔或期間的持續時間值。允許的最小間隔為 5 分鐘；最長間隔為一天。它必須格式化為 XSD "dayTimeDuration" 值 ([ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。其模式為： `P[nD][T[nH][nM]]`. Examples: `PT15M` 每隔 15 分鐘， `PT2H` 每隔 2 小時。 

- **startTime**：必要。指定索引子應該開始執行的 UTC 日期時間。 

###<a id="CreateIndexerExample"></a>要求內文範例

下列範例會建立索引子，可將資料從 `myDocDbDataSource` 資料來源所參考的集合，依照排程 (從 2015 年 1 月 1 日 UTC 開始，每小時執行一次) 複製到 `mySearchIndex`。

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Response

如果已成功建立該索引子，您將會收到一則 [HTTP 201 已建立] 的回應。

##<a id="RunIndexer"></a>步驟 4：執行索引子

為了依照排程定期執行，您也可以發出下列 HTTP POST 要求，視需要叫用索引子： 

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Response

如果已成功叫用該索引子，您將會收到一則 [HTTP 202 已接受] 的回應。

##<a name="GetIndexerStatus"></a>步驟 5：取得索引子狀態

您可以發出 HTTP GET 要求來擷取索引子的目前狀態和執行記錄： 

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Response

您將會看到隨著回應本文傳回的一則 [HTTP 200 確定] 的回應，內容包括整體索引子健全狀況狀態、最後索引子引動過程，以及最新索引子引動過程 (如果有的話) 的歷程記錄等相關資訊。 

回應看起來應該如下所示：

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行儲存 (因此最新的執行會排在回應中的第一位)。

##<a name="NextSteps"></a>後續步驟

恭喜！您剛剛了解如何使用 DocumentDB 的索引子來整合 Azure DocumentDB 與 Azure 搜尋 。

 - 若要深入了解 Azure DocumentDB，請按一下 [這裡](/services/documentdb/)。

 - 若要深入了解 Azure 搜尋，請按一下 [這裡](/services/search/)。

<!--HONumber=49--> 