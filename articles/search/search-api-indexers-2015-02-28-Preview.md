<properties 
pageTitle="索引子作業 (Azure 搜尋服務 REST API：2015-02-28-Preview)" 
description="索引子作業 (Azure 搜尋服務 REST API：2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="HeidiSteen" 
manager="mblythe" 
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="04/23/2015" 
ms.author="heidist" />

#索引子作業 (Azure 搜尋服務 REST API：2015-02-28-Preview)

> [AZURE.NOTE]本文說明 [2015-02-28-Preview](../search-api-2015-02-28-preview.md) 的索引子。目前， [MSDN](http://go.mirosoft.com/fwlink/p/?LinkID=528173) 上記載的 `2015-02-28` 版本與此處所述的 `2015-02-28-Preview` 版本唯一差異是預覽版可提供 *fieldMappings* (如[建立索引子](#CreateIndexer)當中所述)。

## 概觀

Azure 搜尋服務可以直接整合一些常用的資料來源，因此您不需要撰寫程式碼來編製資料的索引。若要進行此項設定，您可呼叫 Azure 搜尋服務 API 以建立並管理**索引子**和**資料來源**。

**索引子**是一種用來連接資料來源與目標搜尋索引的資源。索引子的使用方式如下：

- 執行資料的一次性複製以填入索引。
- 依照排程將索引與資料來源中的變更同步。排程是索引子定義的一部分。
- 視需要叫用以便更新索引。 

如果您想進行索引的一般更新， **索引子**就非常實用。您可在索引子定義中設定內嵌排程，或視需要使用[執行索引子](#RunIndexer)加以執行。

**資料來源**指出要編製索引的資料、資料存取認證以及可讓 Azure 搜尋服務更有效識別資料變更 (例如資料庫資料表中經修改或刪除的資料列) 的原則。資料來源會被定義為獨立的資源，因此可供多個索引子使用。

目前支援下列資料來源：

- Azure SQL Database 和 Azure VM 中的 SQL Server
- Azure DocumentDB 

我們考慮在未來增加支援其他資料來源。為了協助我們確定決策的優先順序，請您在 [Azure 搜尋服務意見反應論壇](http://feedback.azure.com/forums/263029-azure-search)上提供寶貴意見。

如需索引子和資料來源資源相關的最大限制，請參閱[限制和條件約束](https://msdn.microsoft.com/library/azure/dn798934.aspx)。

## 一般使用流程

您可針對特定的 `data source` 或 `indexer`，使用簡單的 HTTP 要求 (POST、GET、PUT、DELETE) 以建立與管理索引子和資料來源。

一般來說，設定自動編製索引的程序有下列四個步驟：

1. 識別包含須編製索引之資料的資料來源。請注意，Azure 搜尋服務不一定支援您的資料來源中包含的所有資料類型。如需清單，請參閱[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。

2. 建立結構描述與您的資料來源相容的 Azure 搜尋服務索引。
  
3. 如[建立資料來源](#CreateDataSource)所述，建立 Azure 搜尋服務的資料來源。
  
4. 根據[建立索引子](#CreateIndexer)所述，建立 Azure 搜尋服務的索引子。

您應該規劃為每個目標索引和資料來源組合建立一個索引子。您可將多個索引子寫入相同的索引中，並針對多個索引子重複使用相同的資料來源。不過，索引子一次只能取用一個資料來源，也只能寫入一個索引中。

建立索引子之後，您可以使用[取得索引子狀態](#GetIndexerStatus)作業來擷取它的執行狀態。除了定期排程執行以外，您還可以使用[執行索引子](#RunIndexer)作業，隨時執行索引子。

<!-- MSDN has 2 art files plus a API topic link list -->


## 建立資料來源

您可以使用 HTTP POST 要求，在 Azure 搜尋服務中建立新的資料來源。
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用 PUT，在 URI 中指定資料來源的名稱。如果索引不存在，系統就會加以建立。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**注意**：允許的資料來源數目上限依定價層而有所不同。免費服務允許最多 3 個資料來源。標準服務允許 50 個資料來源。如需詳細資訊，請參閱[限制條件](https://msdn.microsoft.com/library/azure/dn798934.aspx)。

**要求**

所有服務要求都需使用 HTTPS。**建立資料來源**要求可使用 POST 或 PUT 方法來建構。使用 POST 時，您必須在要求本文中提供資料來源名稱以及資料來源定義。使用 PUT 時，名稱為 URL 的一部分。如果資料來源不存在，系統將會加以建立。如果已有資料來源，則將其更新到最新定義。

資料來源名稱必須小寫，開頭為字母或數字，不可有斜線或點，且上限為 128 個字元。將資料來源名稱的開頭設為字母或數字後，其他部分即可包含任意字母、數字及破折號 (但不可為連續破折號)。如需詳細資料，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)。

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `Content-Type`：必要。請設為 `application/json`
- `api-key`：必要。`api-key` 可用來驗證搜尋服務的要求。其為服務的唯一字串值。**建立資料來源**要求必須包含管理員金鑰 (相對於查詢金鑰) 上所設的 `api-key` 標頭。 
 
您也必須提供服務名稱才能建構要求 URL。您可透過 [Azure 管理入口網站](https://portal.azure.com/)的服務儀表板取得服務名稱與 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立搜尋服務](search-create-service-portal.md)。

<a name="CreateDataSourceRequestSyntax"></a> **要求本文的語法**

要求的本文包含資料來源定義，該定義包括資料來源類型、資料讀取認證，以及選用的資料變更偵測和資料刪除偵測原則，以在搭配定期排程的索引子時，可有效識別資料來源中經變更或刪除的資料 。


下列為建構要求承載的語法。本主題稍後會提供範例要求。

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be 'azuresql' or 'documentdb'",
    	"credentials" : { "connectionString" : "Required. Connection string for your data source" },
    	"container" : { "name" : "Required. The name of the table or collection you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

要求可包含下列屬性：

- `name`：必要。資料來源的名稱。資料來源名稱僅可包含小寫字母、數字或破折號，開頭或結尾不可為破折號，且上限為 128 個字元。
- `description`：選用說明。 
- `type`：必要。必須是下列其中一種支援的資料來源類型：
	- `azuresql` - Azure SQL Database 或 Azure VM 中的 SQL Server
	- `documentdb` - Azure DocumentDB
- `credentials`：
	- 必要的 `connectionString` 屬性可指定資料來源的連接字串。連接字串的格式依據資料來源類型而定： 
		- 若是 Azure SQL，則通常為 SQL Server 連接字串。如果您是使用 Azure 管理入口網站來擷取連接字串，請使用 `ADO.NET connection string` 選項。
		- 若是 DocumentDB，則連接字串必須為下列格式：`"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`。所有值都是必要的。您可在 [Azure 管理入口網站](https://portal.azure.com/)中找到所有值。   
		
- `container`：
	- 必要的 `name` 屬性可指定要編製索引的資料表、檢視表 (若為 Azure SQL 資料來源) 或集合 (若為 DocumentDB 資料來源)。 
	- DocumentDB 資料來源亦支援選用的 `query` 屬性，以讓您指定查詢，將任意 JSON 文件版面配置壓平合併為 Azure 搜尋服務可編製索引的一般結構描述。   
- 以下說明選用的 `dataChangeDetectionPolicy` 與 `dataDeletionDetectionPolicy`。

<a name="DataChangeDetectionPolicies"></a> **資料變更偵測原則**

資料變更偵測原則是用來有效識別已變更的資料項目。支援的原則根據資料來源類型而有所不同。下節會對每項原則進行說明。

**注意：**建立好索引子之後，您可使用[重設索引子](#ResetIndexer) API 來切換資料偵測原則。

***高水位線變更偵測原則***

如果資料來源包含的資料行或屬性符合下列準則，請使用此原則：
 
- 所有插入都指定資料行的值。 
- 所有項目更新變更資料行的值。 
- 每次變更都會增加此資料行的值。
- 當查詢使用類似下方 `WHERE [High Water Mark Column] > [Current High Water Mark Value]` 的篩選子句時，可更有效執行。

例如，使用 Azure SQL 資料來源時，已編製索引的 `rowversion` 資料行就非常適合搭配高水位原則。

若是使用 DocumentDB 資料來源，則必須使用 DocumentDB 所提供的 `_ts` 屬性。
 
您可按照下列方式來指定這項原則：

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

**SQL 整合式變更偵測原則**

如果您的 SQL 資料庫支援[變更追蹤](https://msdn.microsoft.com/library/bb933875.aspx)，則建議您使用 SQL 整合式變更追蹤原則。這項原則可保障最高效的變更追蹤，並讓 Azure 搜尋服務識別出已刪除的資料列，而不需在結構描述中具備明確的「虛刪除」資料行。

從下列 SQL Server 資料庫版本開始，即支援整合式變更追蹤：SQL Server 2008 R2 (若您在 Azure VM 上使用 SQL Server) 及 Azure SQL Database V12 (若您使用 Azure SQL Database)。

使用 SQL 整合式變更追蹤原則時，請不要指定個別的資料刪除偵測原則，因為本原則已內建支援刪除資料列的識別。

此原則僅可用於資料表，無法用於檢視表。您必須先啟用正在使用之資料表的變更追蹤，才能使用這項原則。如需指示，請參閱[啟用和停用變更追蹤](https://msdn.microsoft.com/library/bb964713.aspx)。
 
建構**建立資料來源**要求時，可用下列方式指定 SQL 整合式變更追蹤原則：

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **資料刪除偵測原則**

資料刪除偵測原則可用來有效識別刪除的資料項目。目前唯一支援的原則是 `Soft Delete` 原則，其可依據資料來源的 `soft delete` 資料行值或屬性來識別已刪除的項目。您可按照下列方式來指定這項原則：

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

**注意：**僅支援具有字串、整數或布林值的資料行。即使對應的資料行具有整數或布林值，做為 `softDeleteMarkerValue` 的值仍必須是字串。例如，若資料來源中顯示的值為 1，請使用 `"1"` 做為 `softDeleteMarkerValue`。

<a name="CreateDataSourceRequestExamples"></a> **要求本文範例**

如果您想使用按排程執行之索引子的資料來源，本範例說明如何指定原則提示：

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}

如果您只想使用資料來源進行一次性的資料複製，則可省略原則：

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**回應**

要求成功：「201 已建立」。

<a name="UpdateDataSource"></a>
## 更新資料來源

您可以使用 HTTP PUT 要求來更新現有的資料來源。您可在要求 URI 上指定要更新的資料來源名稱：

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**要求**：要求本文的語法和[建立資料來源要求](#CreateDataSourceRequestSyntax)的語法相同。

**回應**要求成功：如果已建立新的資料來源，會顯示「201 已建立」；如果現有資料來源已更新，則會顯示「204 沒有內容」。

**注意：**現有的資料來源上有些屬性無法更新。例如，您無法變更現有資料來源的類型。

<a name="ListDataSource"></a>
## 列出資料來源

**列出資料來源**作業可傳回 Azure 搜尋服務中的資料來源清單。

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

要求成功：「200 確定」。

下列為回應本文的範例：

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

請注意，您可以僅針對所需的屬性來篩選回應。例如，如果您只需要資料來源名稱的清單，可使用 OData `$select` 查詢選項：

    GET /datasources?api-version=205-02-28&$select=name

在這種情況下，上述範例的回應將顯示為：

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

如果您的搜尋服務中有很多資料來源，此方法可以節省頻寬。

<a name="GetDataSource"></a>
## 取得資料來源

**取得資料來源**作業可讓您透過 Azure搜尋服務取得資料來源定義。

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「200 確定」。

回應與[建立資料來源範例要求](#CreateDataSourceRequestExamples)中的範例類似：

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : "true" }
	}

**注意**：呼叫此 API 時，請不要將 `Accept` 要求標頭設為 `application/json;odata.metadata=none`，以免導致回應的 `@odata.type` 屬性被省略，而無法區分不同類型的資料變更與資料刪除偵測原則。

<a name="DeleteDataSource"></a>
## 刪除資料來源

**刪除資料來源**作業可將資料來源從 Azure 搜尋服務中移除。

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**注意**：即使您要刪除的資料來源有受到任何索引子的參考，刪除作業仍會繼續。但是，這些索引子在下一次執行時將轉換為錯誤狀態。

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「204 沒有內容」。

<a name="CreateIndexer"></a>
## 建立索引子

您可以使用簡單的 HTTP POST 要求，在 Azure 搜尋服務中建立新的索引子。
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用 PUT，在 URI 中指定資料來源的名稱。如果索引不存在，系統就會加以建立。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**注意**：允許的索引子數目上限依定價層而有所不同。免費服務允許最多 3 個索引子。標準服務允許 50 個索引子。如需詳細資訊，請參閱[限制條件](https://msdn.microsoft.com/library/azure/dn798934.aspx)。

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。


<a name="CreateIndexerRequestSyntax"></a> **要求本文的語法**

要求的本文包含索引子定義，以指定要編製索引的資料來源與目標索引，以及選用的索引排程和參數。


下列為建構要求承載的語法。本主題稍後會提供範例要求。

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. }
	}

**索引子排程**

索引子可以選擇性地指定排程。如有排程，索引子將會依照排程定期執行。排程具有下列屬性：

- `interval`：必要。可用以指定索引子執行間隔或期間的持續時間值。允許的最小間隔為 5 分鐘；最長間隔為一天。其必須格式化為 XSD "dayTimeDuration" 值 ([ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集)。間隔的模式為：`P(nD)(T(nH)(nM))`。範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 小時。 

- `startTime`：必要。索引子應該開始執行的 UTC 日期時間。

**索引子參數**

您可選擇性地為索引子指定數個參數來影響其行為。所有參數都是選用的。

- `maxFailedItems`：在索引子執行發生錯誤前，可能無法編製索引的項目數。預設值為 0。[取得索引子狀態](#GetIndexerStatus)作業可傳回失敗項目的相關資訊。 

- `maxFailedItemsPerBatch`：在索引子執行發生錯誤前，每個批次中可能無法編製索引的項目數。預設值為 0。

- `base64EncodeKeys`：指定文件金鑰是否要進行 base-64 編碼。針對文件金鑰中可使用的字元，Azure 搜尋服務有加諸限制。但在您的來源資料中的值，可能包含無效字元。如果您必須將這些值編製索引為文件金鑰，可將此旗標設為 true。預設值為 `false`。

**欄位對應**

您可使用欄位對應，將資料來源中的欄位名稱對應至目標索引的不同欄位名稱。例如，假設來源資料表包含 `_id` 欄位。Azure 搜尋服務不允許欄位名稱的開頭是底線，因此必須重新命名欄位。您可使用索引子的 `fieldMappings` 屬性來進行這項作業，如下所示：
	
	"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

您可以指定多個欄位對應：

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

來源和目標欄位名稱皆有區分大小寫。

<a name="FieldMappingFunctions"></a> ***欄位對應函數***

您也可以使用「對應函數」，將欄位對應用來轉換來源欄位值。

這類函數目前僅支援一種：`jsonArrayToStringCollection`。它會將格式化為 JSON 陣列的字串剖析為目標索引中的 Collection(Edm.String) 欄位。該函數主要用來搭配 Azure SQL 索引子，因為 SQL 不具備原生的集合資料類型。其用法如下：

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

例如，若來源欄位中包含 `["red", "white", "blue"]` 字串，則 `Collection(Edm.String)` 類型的目標欄位會填入下列三個值：`"red"`、`"white"` 與 `"blue"`。

注意：`targetFieldName` 屬性為選用；如果省略，則會使用 `sourceFieldName` 值。

<a name="CreateIndexerRequestExamples"></a> **要求本文範例**

下列範例會建立索引子，以將資料從 `ordersds` 資料來源所參考的資料表，依照排程 (從 2015 年 1 月 1 日 UTC 開始，每小時執行一次) 複製到 `orders` 索引。如果每個批次中無法編製索引的項目不超過 5 個，且無法編製索引的項目總數不超過 10 個的話，每個索引子的引動都將成功。

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**回應**

「201 已建立」表示要求成功。


<a name="UpdateIndexer"></a>
## 更新索引子

您可以使用 HTTP PUT 要求來更新現有的索引子。您可在要求 URI 上指定要更新的索引子名稱：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` 為必要項目。目前版本為 `2015-02-28`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**要求**

要求本文的語法和[建立索引子要求](#CreateIndexerRequestSyntax)的語法相同。

**回應**

要求成功：如果已建立新的索引子，會顯示「201 已建立」；如果現有索引子已更新，則會顯示「204 沒有內容」。


<a name="ListIndexers"></a>
## 列出索引子

**列出索引子**作業可傳回 Azure 搜尋服務中的索引子清單。

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

要求成功：「200 確定」。

下列為回應本文的範例：

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

請注意，您可以僅針對所需的屬性來篩選回應。例如，如果您只需要索引子名稱的清單，可使用 OData `$select` 查詢選項：

    GET /indexers?api-version=2014-10-20-Preview&$select=name

在這種情況下，上述範例的回應將顯示為：

    {
      "value" : [ { "name": "myindexer" } ]
    }

如果您的搜尋服務中有很多索引子，這個方法可以節省頻寬。


<a name="GetIndexer"></a>
## 取得索引子

**取得索引子**作業可取得 Azure 搜尋服務的索引子定義。

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「200 確定」。

回應與[建立索引子範例要求](#CreateIndexerRequestExamples)中的範例類似：

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}


<a name="DeleteIndexer"></a>
## 刪除索引子

**刪除索引子**作業可移除 Azure 搜尋服務中的索引子。

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

如果索引子在執行當中受到刪除，該索引子仍會完成執行，但之後將不會再排程任何執行。如果您嘗試使用不存在的索引子，就會顯示 HTTP 狀態碼「404 找不到」。
 
`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「204 沒有內容」。

<a name="RunIndexer"></a>
## 執行索引子

除了依照排程定期執行以外，您也可以透過**執行索引子**作業，視需要叫用索引子：

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「202 已接受」。

<a name="GetIndexerStatus"></a>
## 取得索引子狀態

**取得索引子狀態**作業可擷取索引子目前的狀態與執行記錄：

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「200 確定」。

回應本文包含整體索引子健全狀態、最後索引子引動過程，以及最新索引子引動過程的歷程記錄 (如果有) 等相關資訊。

範例回應本文應如下所示：

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

**索引子狀態**

索引子狀態可為下列值之一：

- `running` 表示索引子正常執行。請注意，有些索引子的執行仍可能會失敗，因此建議一併檢查 `lastResult` 屬性。 

- `error` 表示索引子發生錯誤，必須使用者介入才能修正。例如，資料來源認證可能已過期，或資料來源/目標索引的結構描述已受到中斷式的變更。

**索引子執行結果**

索引子執行結果包含單一索引子執行的相關資訊。索引子狀態屬性 `lastResult` 表示最新的結果。索引子狀態屬性 `executionHistory` 會傳回其他最新的結果 (如果有)。

索引子執行結果包含下列屬性：

- `status`：執行的狀態。如需詳細資料，請參閱下方的[索引子執行狀態](#IndexerExecutionStatus)。 

- `errorMessage`：執行失敗的錯誤訊息。

- `startTime`：這項執行開始的 UTC 時間。

- `endTime`：這項執行結束的 UTC 時間。如果執行仍在進行中，則不會設定此值。

- `errors`：項目層級的錯誤清單 (如果有)。

- `itemsProcessed`：在這項執行期間，索引子嘗試編製索引的資料來源項目數 (例如資料表的資料列)。

- `itemsFailed`:此項執行期間失敗的項目數。
 
- `initialTrackingState`：第一次執行索引子，或當所使用的資料來源未啟用資料變更追蹤原則時，一律為 `null`。如果啟用該原則，則在後續執行中，這個值就表示此執行處理的第一個 (最低) 變更追蹤值。

- `finalTrackingState`：當所使用的資料來源未啟用資料變更追蹤原則時，一律為 `null`。否則，表示此執行成功處理的最新 (最高) 變更追蹤值。

<a name="IndexerExecutionStatus"></a> **索引子執行狀態**

索引子執行狀態可擷取單一索引子執行的狀態。它具有下列值：

- `success` 表示已成功完成索引子的執行。

- `inProgress` 表示正在進行索引子的執行。

- `transientFailure` 表示正在進行索引子的執行。如需詳細資料，請參閱 `errorMessage`。有些失敗可能需要使用者介入，有些則否 - 例如，修正
- 資料來源與目標索引之間的結構描述不相容性，就需要使用者介入；暫時性的資料來源停止運作則不需要。索引子引動將繼續按照排程進行 (若有排程的話)。 

- `persistentFailure` 表示索引子失敗，需要使用者介入。排程的索引子執行會停止。解決問題之後，您可使用重設索引子 API 來重新啟動排程的執行。

- `reset` 表示索引子已被重設索引子 API 呼叫重設 (請參閱下方)。

<a name="ResetIndexer"></a>
## 重設索引子

**重設索引子**作業可重設與索引子相關聯的變更追蹤狀態。這可讓您觸發從頭開始重新編製索引 (例如，當您的資料來源結構描述已變更時)，或變更與索引子相關聯之資料來源的資料變更偵測原則。

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version` 為必要項目。目前的預覽版本為 `2015-02-28-Preview`。[Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)中有提供替代版本的詳細資訊。

`api-key` 必須為管理員金鑰 (相對於查詢金鑰)。如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的驗證一節。[在入口網站中建立搜尋服務](search-create-service-portal.md)說明如何取得要求所需的服務 URL 與金鑰屬性。

**回應**

狀態碼：回應成功時會傳回「204 沒有內容」。

## SQL 資料類型與 Azure 搜尋服務資料類型之間的對應

<table style="font-size:12">
<tr>
<td>SQL 資料類型</td>	
<td>允許的目標索引欄位類型</td>
<td>注意事項</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>int、smallint、tinyint</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>real、float</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney、money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Azure 搜尋服務不支援將十進位類型轉換為 Edm.Double，因為這麼做會降低準確度。
</td>
</tr>
<tr>
<td>char、nchar、varchar、nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>如需如何將字串資料行轉換為 Collection(Edm.String) 的詳細資料，請參閱本文件中的 [欄位對應函數](#FieldMappingFunctions)。</td>
</tr>
<tr>
<td>smalldatetime、datetime、datetime2、date、datetimeoffset</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>僅支援使用 SRID 4326 (預設) 之 POINT 類型的 geography 執行個體。</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>資料列版本的資料行無法儲存在搜尋索引中，但可用於追蹤變更。</td>
</tr>
<tr>
<td>time、timespan<br>binary、varbinary、image<br>xml、geometry、CLR 類型</td>
<td>N/A</td>
<td>不支援</td>
</tr>
</table>

## JSON 資料類型與 Azure 搜尋服務資料類型之間的對應

<table style="font-size:12">
<tr>
<td>JSON 資料類型</td>	
<td>允許的目標索引欄位類型</td>
<td>注意事項</td>
</tr>
<tr>
<td>布林</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>整數</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>浮點數</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>字串</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>基本類型的陣列，例如 [ "a"、"b"、"c" ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>看起來像是日期的字串</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON 點物件</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON 點為 JSON 物件，其格式為：{ "type" : "Point", "coordinates" : [long, lat] } </td>
</tr>
<tr>
<td>其他 JSON 物件</td>
<td>N/A</td>
<td>不支援；Azure 搜尋服務目前僅支援基本類型與字串集合</td>
</tr>
</table>

<!---HONumber=62-->