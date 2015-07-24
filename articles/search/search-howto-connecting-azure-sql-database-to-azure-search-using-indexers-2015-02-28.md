<properties 
	pageTitle="使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務。" 
	description="了解如何使用索引子將資料從 Azure SQL Database 提取至 Azure 搜尋服務索引。" 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="eugenesh"/>

#使用索引子將 Azure SQL Database 連接至 Azure 搜尋服務。#

Azure 搜尋服務可以輕鬆地提供強大的搜尋體驗，但您必須先在 Azure 搜尋服務索引中填入您的資料，您才能夠搜尋。如果 Azure SQL Database 中有您的資料，您可以在 Azure 搜尋服務中，使用全新 **適用於 Azure SQL Database 的 Azure 搜尋服務索引子** (或簡稱 **Azure SQL 索引子**) 功能，自動化編製索引的程序。這表示您可以減少編寫程式碼的工作，並且減少需要維護的基礎結構。

目前索引子僅適用於 Azure SQL Database、Azure VM 上的 SQL Server，以及 Azure DocumentDB。在本文中，我們將重點放在如何針對 Azure SQL Database 使用索引子。如果您想參閱其他資料來源的支援，請您在 [Azure 搜尋服務意見反應論壇](http://feedback.azure.com/forums/263029-azure-search)上提供寶貴意見。

本文將介紹使用索引子的機制，但我們也會深入了解僅在 SQL 資料庫上出現的功能與行為 (例如，整合變更追蹤)。

## 索引子與資料來源 ##

若要設定 Azure SQL 索引子，您可以呼叫 [Azure 搜尋服務 REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173) 以建立和管理**索引子**及**資料來源**。此功能日後也能在 Azure 管理入口網站和 Azure 搜尋服務 .NET SDK 中使用。

**資料來源**能指定哪項資料要編製索引、存取資料需要哪些認證，以及哪些政策能讓 Azure 搜尋服務有效識別資料變更 (新增、修改或刪除的資料列)。資料來源會被定義為獨立的資源，因此可供多個索引子使用。

**索引子**是一種用來連接資料來源與目標搜尋索引的資源。索引子的使用方式如下：
 
- 執行資料的一次性複製以填入索引。
- 依照排程使用資料來源中的變更來更新索引。
- 視需要執行隨選作業更新索引。 

## 使用 Azure SQL 索引子的時機 ##

使用 Azure SQL 索引子適當與否，取決於一些資料相關因素。如果您的資料符合下列要求，您就可以使用 Azure SQL 索引子：

- 所有資料皆來自單一資料表或檢視
	- 如果資料散布在多個資料表中，您可以建立一個檢視，並且將該檢視與索引子搭配使用。但是請注意，若您使用檢視，您將不能使用 SQL Server 的整合變更偵測。請參閱此節以瞭解更多詳細資訊。 
- 索引子支援資料來源中所使用的資料類型。支援大部分但並非所有的 SQL 類型。如需詳細資料，請參閱[對應 Azure 搜尋服務內的資料來源](http://go.microsoft.com/fwlink/p/?LinkID=528105)。 
- 當資料列變更時，您不需要幾近即時索引更新。 
	- 索引子可以最多每隔 5 分鐘重新索引您的資料表。若您經常變更資料，且變更需要在幾秒或幾分鐘內反映到索引中，我們建議您直接使用 [Azure 搜尋服務索引 API](https://msdn.microsoft.com/library/azure/dn798930.aspx)。 
- 若您擁有大量資料集且計畫按照排程執行索引子，您的結構描述讓我們能夠有效識別變更 (若適用刪除) 的資料列。如需更多詳細資料，請參閱下列的「擷取變更及刪除的資料列」。 
- 資料列中的索引欄位大小不得超過 Azure 搜尋服務索引要求的大小上限 (16 MB)。 

## 建立和使用 Azure SQL 索引子 ##

首先，建立資料來源：

	POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
	    "container" : { "name" : "name of the table or view that you want to index" }
	}


您可以從 [Azure 入口網站](https://portal.azure.com)取得連接字串；使用 `ADO.NET connection string` 選項。

然後，建立目標 Azure 搜尋服務索引 (如果您尚未建立)。您可以從 [Azure 入口網站 UI](https://portal.azure.com) 或使用[建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 來執行此作業。確保您的目標索引結構敘述與來源資料表結構敘述相容。請參閱下列資料表，對應 SQL 及 Azure 搜尋服務間的資料類型。

**在 SQL 資料類型及 Azure 搜尋服務資料類型間比對** <table style="font-size:12"> <tr> <td>SQL 資料類型</td> <td>允許的目標索引欄位類型</td> <td>注意</td> </tr> <tr> <td>bit</td> <td>Edm.Boolean、Edm.String</td> <td></td> </tr> <tr> <td>int、smallint、tinyint</td> <td>Edm.Int32、Edm.Int64、 Edm.String</td> <td></td> </tr> <tr> <td>bigint</td> <td>Edm.Int64、 Edm.String</td> <td></td> </tr> <tr> <td>real、 float</td> <td>Edm.Double、 Edm.String</td> <td></td> </tr> <tr> <td>smallmoney、 money<br/>decimal<br/>numeric </td> <td>Edm.String</td> <td>Azure 搜尋服務不支援將 decimal 類型轉換為 Edm.Double，因為這可能會失去精確度</td> </tr> <tr> <td>char、 nchar、varchar、 nvarchar</td> <td>Edm.String<br/>Collection(Edm.String)</td> <td>將字串資料行轉換為 Collection (Edm.String) 需要使用預覽 API 版本 2015-02-28-預覽。請參閱 [本文](search-api-indexers-2015-02-28-Preview.md#create-indexer) 了解詳細資訊</td> </tr> <tr> <td>smalldatetime、datetime、datetime2、date、datetimeoffset</td> <td> Edm.DateTimeOffset、Edm.String</td> <td></td> </tr> <tr> <td> uniqueidentifer</td> <td>Edm.String</td> <td></td> </tr> <tr> <td>geography</td> <td>Edm.GeographyPoint</td> <td>僅支援使用 SRID 4326 (預設) 之 POINT 類型的 geography執行個體</td> </tr> <tr> <td>rowversion</td> <td>N/A</td> <td> 資料列版本的資料行無法儲存在搜尋索引中，但可用於變更追蹤</td> </tr> <tr> <td>time、 timespan<br>binary、varbinary、image、<br>xml、 geometry、CLR 類型</td> <td>N/A</td> <td>不支援</td> </tr> </table>

最後，利用命名及參考資料來源和目標索引建立索引子。

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

以這種方式建立索引子不需依照排程。一旦索引子建立完成，會自動執行一次。您可以在任何時候使用 **執行索引子** 要求再執行一次：

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key
 
在您可能需要允許 Azure 服務以連線您的資料庫的時候。如需瞭解如何執行連線的指示，請參閱 [從 Azure連線](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 。

若要監視索引子狀態及執行歷程紀錄 (項目索引編製數量、失敗等)，請使用 **索引子狀態** 要求：

	GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
	api-key: admin-key

回應看起來應該如下所示：

	{
		"@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2015-02-21T00:23:24.957Z",
			"endTime":"2015-02-21T00:36:47.752Z",
			"errors":[],
			"itemsProcessed":1599501,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null 
        },
		"executionHistory":
		[
			{
				"status":"success",
				"errorMessage":null,
				"startTime":"2015-02-21T00:23:24.957Z",
				"endTime":"2015-02-21T00:36:47.752Z",
				"errors":[],
				"itemsProcessed":1599501,
				"itemsFailed":0,
				"initialTrackingState":null,
				"finalTrackingState":null 
			},
			... earlier history items 
		]
	}

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行排序 (因此最新的執行會排在回應中的第一位)。有關回應的的其他資訊可在 [取得索引子狀態](http://go.microsoft.com/fwlink/p/?LinkId=528198) 找到。

## 依照排程執行索引子 ##

您也可以排列索引子，依照排程定期執行。若要執行此工作，您只需在建立或更新索引子時加入 **排程** 屬性。下方範例顯示以 PUT 要求更新索引子：

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

**間隔** 參數是必需的。間隔指兩個連續索引子開始執行的時間。允許的最小間隔為 5 分鐘；最長間隔為一天。其必須格式化為 XSD "dayTimeDuration" 值 ([ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集)。間隔的模式為：`P(nD)(T(nH)(nM))`。範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

選用的 **startTime** 表示排定執行應開始的時間 ；如果省略選用時間，則會使用您目前的 UTC 時間 。開始時間可以設定為過去的時間 (在這種情況下，可以假設索引子從 startTime 已持續執行一段時間，以排定第一次執行的時間)。

給定的索引子一次僅能執行一個。若下個索引子應開始執行時此索引此仍在執行，該執行將會被跳過並且在下個間隔時間重新開始執行 (假設已無其他索引子工作正在執行)。

讓我們參考一個範例，讓您更具體了解詳情。我們假設下列排程已設定為每小時執行：

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

排程狀況如下：

1. 第一次索引子執行開始時間約於 2015年 3 月 1 日 上午 12:00UTC。
1. 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
1. 第二次執行開始時間約於 2015 年 3 月 1 日 上午 1:00。 
1. 現在假設此執行需要一個多小時 (此狀況實際發生於可能需要執行大量文件時，但是個相當實用的示範) ：例如，70 分鐘，因此結束時間約於上午 2:10。
1. 目前為上午 2:00，為第三個執行的開始時間。但是，因為從上午 1 點開始的第二次執行仍在運作，第三次執行就會被跳過。第三次執行時間改於上午 3 點開始。

您可以使用 **PUT 索引子** 要求，加入、 變更或刪除現有之索引子的排程。

## 擷取新增、變更和刪除的資料列 ##

若您正在使用排程且資料表包含非一般數量的資料列，應使用資料變更偵測原則，如此索引子才能有效地只擷取新增或變更的資料列，而不需再重新編製整個資料列的索引。

### SQL 整合變更追蹤原則 ###

如果您的 SQL 資料庫支援 [變更追蹤](https://msdn.microsoft.com/library/bb933875.aspx)，則建議您使用 **SQL 整合式變更追蹤原則**。這項原則可保障最高效的變更追蹤，並讓 Azure 搜尋服務識別出已刪除的資料列，而不需在資料表中加入明確的「虛刪除」資料行。

下列的 SQL Server 資料庫版本支援整合變更追蹤：
 
- SQL Server 2008 R2 和更高版本 (若您使用 Azure VM 上 SQL Server )。 
- Azure SQL Database V12 (若您使用 Azure SQL Database )。

使用 SQL 整合式變更追蹤原則時，請不要指定個別的資料刪除偵測原則，因為本原則已內建支援刪除資料列的識別。

這項原則僅可用於資料表，無法用於檢視表。您必須先啟用正在使用之資料表的變更追蹤，才能使用這項原則。如需指示，請參閱[啟用和停用變更追蹤](https://msdn.microsoft.com/library/bb964713.aspx)。

若要使用此原則，請以下列方式建立或更新您的資料來源：
 
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	  }
	}

### 上限標準變更偵測原則 ###

雖然我們推薦您使用 SQL 整合變更追蹤原則，但如果檢視表中有您的資料，或您使用的是舊版的 Azure SQL Database ，則無法使用此原則。若是這種情況，請考慮使用上限標準原則。您可以使用此原則，若您的資料表包含之資料行符合下列準則：

- 所有插入都有指定資料行的值。 
- 所有項目更新變更資料行的值。 
- 每次變更都會增加此資料行的值。
- 使用與 `WHERE [High Water Mark Column] > [Current High Water Mark Value]` 相同的 `WHERE` 子句的查詢能有效地執行。

例如，已編製索引的 **rowversion** 資料行就適合使用上限標準資料行。若要使用此原則，請以下列方式建立或更新您的資料來源：

	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
	       "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	  }
	}

### 虛刪除資料行刪除偵測原則 ###

當從來源資料表中刪除資料列時，您應該也想刪除在搜尋索引內的那些資料列。若您使用 SQL 整合變更追蹤原則，就能幫您處理這件工作。但是，上限標準變更追蹤原則無法幫助您刪除資料列。怎麼辦？

若資料列已實際從資料表內移除，那麼很不幸地，您無法推斷出不復存在的記錄。但是，您可以使用「虛刪除」技術標記資料列，如此可以以邏輯方式刪除資料列，使用資料行內的標記值加入已刪除的資料行及標記已刪除的資料行，您就不會移除它們。

當您使用虛刪除技術時，可以在建立或升級資料來源時，按照下列方式指定虛刪除原則：

	{ 
	    …, 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

請注意， **softDeleteMarkerValue** 必須為一個字串 (使用代表實際值的字串)。例如，若您擁有一個整數的資料行，且刪除的資料列標記為 1 值，請使用 `"1"`；若您擁有一個 BIT 資料行，且刪除的資料列標記為布林真值，請使用 `"True"`。

## 自訂 Azure SQL 索引子 ##
 
您可以自訂某些方面的索引子行為 (例如，批次處理大小、在索引子執行失敗前跳過多少文件等)。如需詳細資訊，請參閱 [索引子 API 文件說明](http://go.microsoft.com/fwlink/p/?LinkId=528173)。

## 常見問題集 ##

**問：** 在 Azure 服務的 IaaS 模擬器上執行 SQL 資料庫時，能夠使用 Azure SQL 索引子嗎？

答：可以。只要您允許 Azure 服務開啟適當的連接埠連線至您的資料庫。

**問：** 在內部部署執行 SQL 資料庫時，能夠使用 Azure SQL 索引子嗎？

答：我們不建議或不支援此功能，因為使用此功能時，需要您的網際網路流量開啟資料庫。

**問：** 若不使用 Azure 服務的 IaaS 模擬器執行 SQL Server ，能夠使用其他資料庫的 Azure SQL 索引子嗎？

答：我們並不支援這樣的案例，因為我們尚未測試 SQL Server 以外之資料庫的索引子。

**問：** 可以建立多個依照排程執行的索引子嗎？

答： 可以。但是一次只能在一個節點上執行一個索引子。如果您需要多個同時執行的索引子，請考慮將搜尋服務調整大於一個搜尋單位。

**問：** 執行一個索引子會影響我的查詢工作負載嗎？

答： 會。索引子會在您搜尋服務中的其中一個節點執行，且節點上的資源會在索引及服務查詢流量和其他 API 要求之間共用。如果您密集執行索引及查詢工作負載，且經常遇到 503 錯誤或回應次數增加，請考慮調整您的搜尋服務。



 

<!---HONumber=July15_HO2-->