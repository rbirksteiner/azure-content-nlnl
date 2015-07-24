<properties pageTitle="Azure 搜尋服務 REST API 版本 2014-07-31-Preview" description="Azure 搜尋服務 REST API：版本 2014-07-31-Preview" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="05/21/2015" ms.author="heidist" />

# Azure 搜尋服務 REST API 版本：2014-07-31-Preview

本文說明 **2014-07-31-Preview** 舊版本的 Azure 搜尋服務 REST API (2014 年 8 月針對 Azure Search Public Preview 發行)。您可以在 MSDN 上找到適用於目前公開上市版本之 Azure 搜尋 REST API 的文件。如需詳細資訊，請參閱 [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)。

如果您在應用程式程式碼中使用 API 的預覽版本，強烈鼓勵您移轉至公開上市版本。如需指引，請參閱[從預覽轉換為公開上市 API 版本](search-transition-from-preview.md)。

與 **2014-07-31-Preview** 相關的其他 API 內容包含下列項目：

- [評分設定檔 (Azure 搜尋服務 REST API：2014-07-31-Preview)](../search-api-scoring-profiles-2014-07-31-preview.md)


##關於服務 REST API

Azure 搜尋服務是以雲端為基礎的服務，可用來建置自訂搜尋應用程式。Azure 搜尋服務具有「搜尋服務」和「索引」的概念，而搜尋服務會包含一或多個索引。您的搜尋服務可透過完整網域名稱來進行唯一識別 (例如：`mysearchservice.search.windows.net`)。佈建服務時會產生 API 金鑰，用來驗證對 Azure 搜尋服務的要求。

可對 Azure 搜尋服務執行的動作有兩種：

- **索引管理**：這包括可對搜尋服務或搜尋索引執行的系統管理工作。

- **文件動作**：這些動作會查詢和管理指定索引的主體。

本節中記載的 API 提供搜尋資料相關操作的存取，例如，索引建立與填入、文件上傳以及查詢。呼叫 API 時，請記住下列幾點：

- 所有的 API 都是依據 HTTP 要求和回應，使用 OData JSON 格式來定義。

- 所有的 API 都必須在標頭或查詢字串中伴隨 `api-key`，如下列注意事項中所述。

- 所有的 API 都必須透過 HTTPS 來發出 (在預設連接埠 443 上)。

- 所有的 API 要求都必須包含 `api-version` 查詢字串參數。它的值必須設定為目前服務的版本，如下列範例所示：

GET /indexes?api-version=2014-07-31-Preview

- 所有的 API 要求都可選擇性地設定 `Accept` HTTP 標頭。若未設定，即會假設預設值為 `application/json`。

已針對服務管理提供個別的 API。服務管理操作的範例包含佈建服務或更改容量。如需此 API 的詳細資訊，請參閱＜Azure 搜尋管理 REST API＞。

### 端點

服務操作的端點是您佈建的 Azure 搜尋服務 URL：https://<yourService>.search.windows.net。


### 版本

有多個 API 版本適用於 Azure 搜尋服務。如果您正在評估將 Azure 搜尋服務與生產應用程式搭配使用，建議您檢閱針對每組 API 所發行的版本資訊。如需選擇特定版本的指引，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。


<a name="Authentication"></a>
### 驗證和存取控制

驗證 Azure 搜尋服務時需要兩項資訊：搜尋服務 URL 和 `api-key`。`api-keys` 會在服務建立時產生，並且可在佈建服務之後視需求重新產生。`api-key` 可以是授與所有操作存取權的系統管理金鑰，或是僅驗證查詢要求的查詢金鑰。針對每個服務，您擁有 2 個系統管理金鑰，且最多可有 50 個查詢金鑰。

存取控制僅限用於透過 Azure 預覽入口網站中提供的角色型取控制 (RBAC) 來執行的服務管理。角色可用來設定服務管理的存取層級。例如，檢視系統管理金鑰限用於擁有者和參與者角色，而檢視服務狀態則是所有角色的成員都可檢視。

對搜尋服務端點執行的資料操作 (包括索引管理、索引填入和查詢) 都是透過 `api-keys` 以獨佔方進行式存取。RBAC 不適用於索引或文件相關的操作。若要深入了解 Azure 搜尋服務中的 `api-keys` 或 RBAC，請參閱[在 Microsoft Azure 上管理您的搜尋服務](search-manage.md)。

**注意**：一般來說，在要求 URI 中傳遞敏感資料 (例如 `api-key`) 被視為是安全性較差的做法。基於此因素，Azure 搜尋服務在查詢字串中只會接受查詢金鑰做為 `api-key`，而且，除非您應該公開提供索引內容，否則應避免採用此做法。建議您改以要求標頭的形式來傳遞 `api-key`。

###API 的摘要

Azure 搜尋服務 API 支援兩種查閱實體的語法。下列清單顯示簡單和替代的 OData 語法。

[建立索引](#CreateIndex)

    POST /indexes?api-version=2014-07-31-Preview

[更新索引](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-07-31-Preview

[取得索引](#GetIndex)

    GET /indexes/[index name]?api-version=2014-07-31-Preview

[列出索引](#ListIndexes)

    GET /indexes?api-version=2014-07-31-Preview

[取得索引統計資料](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-07-31-Preview

[删除索引](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-07-31-Preview

[新增、刪除及更新索引內的資料](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-07-31-Preview 

[搜尋文件](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[查閱文件](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[文件計數](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-07-31-Preview

[建議](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________
<a name="IndexOps"></a>
## 索引操作

您可以針對指定的索引資源，透過簡單的 HTTP 要求 (POST、GET、PUT、DELETE)，在 Azure 搜尋服務中建立與管理索引。若要建立索引，您必須先 POST 一份 JSON 文件來說明索引結構描述。結構描述可定義索引欄位、其資料類型，以及使用它們的方式 (例如，用於全文檢索搜尋、篩選器、排序、面向設定或建議)。它也會定義評分設定檔，以及用來設定索引行為的其他屬性。

下列範例所說明的結構描述可用來搜尋其 [說明] 欄位是以兩種語言所定義的旅館資訊。請注意，屬性如何控制欄位的使用方式。例如，`hotelId` 是用來做為文件索引鍵 (`"key": true`)，並會從全文檢索搜尋中加以排除 (`"searchable": false`)。

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true, analyzer="fr.lucene"},
      {"name": "hotelName", "type": "Edm.String", "suggestions": true},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ] 
    }
 
建立索引之後，您將會上傳文件以填入索引。請參閱[新增或更新文件](#AddOrUpdateDocuments)，以取得這個後續步驟。

如需在 Azure 搜尋服務中編製索引的影片介紹，請參閱[第 9 頻道：有關 Azure 搜尋服務的雲端報導影片](http://go.microsoft.com/fwlink/p/?LinkId=511509)。


<a name="CreateIndex"></a>
## 建立索引

您可以使用簡單的 HTTP POST 要求，在 Azure 搜尋服務中建立新的索引。要求的本文是一份 JSON 文件，可用來指定索引名稱、欄位、屬性以控制查詢行為、為結果進行評分，以及 CORS 選項。

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用 PUT，在 URI 中指定索引名稱。如果索引不存在，系統就會加以建立。

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**注意**：允許的索引數目上限會依定價層而有所不同。免費服務允許最多 3 個索引。標準服務允許每個服務有 50 個索引。如需詳細資訊，請參閱[限制條件](http://msdn.microsoft.com/library/azure/dn798934.aspx)。

**要求**

所有服務要求都需使用 HTTPS。**建立索引**要求可以使用 POST 或 PUT 方法來建構。使用 POST 時，您必須在要求本文中提供索引名稱以及索引結構描述定義。使用 PUT 時，索引名稱為 URL 的一部分。如果沒有索引，系統就會加以建立。如果已有索引，就會使用新的定義來更新。

索引名稱必須是小寫、開頭為字母或數字、不可有斜線或點，且上限為 128 個字元。將索引名稱的開頭設為字母或數字之後，名稱的其餘部分就能包含任意字母、數字及破折號 (但不可為連續破折號)。

`api-version` 為必要項目。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能，例如，支援透過分析器索引屬性來表達的語言分析器。如需 API 版本的詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。如需語言分析器的詳細資訊，請參閱[語言支援](#LanguageSupport)。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `Content-Type`：必要。請設為 `application/json`
- `api-key`：必要。`api-key` 可用來 
- 驗證搜尋服務的要求。它是服務專屬的唯一字串值。**建立索引**要求必須包含已設為您系統管理金鑰 (相對於查詢金鑰) 的 `api-key` 標頭。 
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

<a name="RequestData"></a> **要求本文的語法**

要求的本文包含描述結構定義，其中包括文件內要傳送給此索引的資料欄位清單、資料類型、屬性，以及在查詢期間用來對相符文件進行評分的評分設定檔選用清單。

請注意，如果是 POST 要求，您必須在要求本文中指定索引名稱。

索引中只能有一個索引鍵欄位。它必須是字串欄位。此欄位會針對索引內儲存的每份文件顯示唯一的識別碼。

建構要求承載的語法如下。本主題稍後會提供範例要求。

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

注意：從 API 版本 2014-10-20-Preview 開始支援資料類型 `Edm.Int64`。
    
**索引屬性**

建立索引時，可以設定下列屬性。如需評分和評分設定檔的詳細資訊，請參閱 [將評分設定檔新增至搜尋索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

`name` - 設定欄位名稱。

`type` - 設定欄位的資料類型。如需支援的類型清單，請參閱[支援的資料類型](#DataTypes)。

`searchable` - 將欄位標記為 full-text search-able。這表示它將在索引設定期間執行像是斷字的分析。如果您為 `searchable` 欄位設定像是「sunny day」的值，則系統會在內部將它分割為「sunny」和「day」這兩個個別的語彙基元。這樣就能針對這些字詞進行全文檢索搜尋。類型 `Edm.String` 或 `Collection(Edm.String)` 的欄位會預設為 `searchable`。其他類型的欄位不能是 `searchable`。

  - **注意**：`searchable` 欄位會在您的索引中佔用額外空間，因為 Azure 搜尋服務會針對欄位值儲存其他已經語彙基元化的版本，以便進行全文檢索搜尋。如果您想要節省索引中的空間且不需在搜尋中包含欄位，則可將 `searchable` 設為 `false`。

`filterable` - 允許在 `$filter` 查詢中參考欄位。`filterable` 處理字串的方式與 `searchable` 不同。類型 `Edm.String` 或 `Collection(Edm.String)` 的欄位 (它們是 `filterable`) 不會執行斷字功能，因此，只會針對完全相符的項目進行比較。例如，如果您將欄位 `f` 之類的欄位設為「sunny day」，`$filter=f eq 'sunny'` 就會找不到相符項目，但 `$filter=f eq 'sunny day'` 可以。所有欄位都會預設為 `filterable`。

`sortable` - 根據預設，系統會依照分數來排序結果，但在許多體驗中，使用者會想要依照文件中的欄位來排序。類型 `Collection(Edm.String)` 的欄位不能是 `sortable`。所有的其他欄位都會預設為 `sortable`。

`facetable` - 通常用於搜尋結果的呈現方式，其中包括依類別排序的點閱數 (例如，搜尋數位相機，然後依照品牌、百萬像素、價格等項目來查看點閱數)。此選項無法與類型 `Edm.GeographyPoint` 的欄位搭配使用。所有的其他欄位都會預設為 `facetable`。

  - **注意**：類型 `Edm.String` 的欄位 (它們是 `filterable`、`sortable` 或 `facetable`) 長度上限為 32 KB。這是因為這類欄位會被視為單一搜尋字詞，而在 Azure 搜尋服務中，字詞的長度上限為 32 KB。如果您需要在單一字串欄位中儲存比此長度上限更多的文字，就需要在索引定義中明確地將 `filterable`、`sortable` 及 `facetable` 設為 `false`。

`suggestions` - 設定欄位是否可用於自動完成自動提示的動作。這只能針對類型 `Edm.String` 或 `Collection(Edm.String)` 的欄位進行設定。`suggestions` 預設是 `false`，因為它在您的索引中需要額外的空間。

  - **注意**：如果某個欄位未將上述屬性設為 `true` (`searchable`、`filterable`、`sortable`、`facetable` 或 `suggestions`)，系統就會從反向索引中有效地排除該欄位。針對查詢中未使用，但需要在搜尋結果中使用的欄位來說，此選項非常實用。從索引中排除這類欄位有助於提高效能。

`key` - 將欄位標記為包含索引內之文件的唯一識別碼。您必須只選擇一個欄位做為 `key` 欄位，而它的類型必須是 `Edm.String`。索引鍵欄位可以用來透過[查閱 API](#LookupAPI) 直接查閱文件。

`retrievable` - 設定搜尋結果中是否會傳回該欄位。當您想要使用某個欄位 (例如邊距) 做為篩選、排序或評分機制，但不想讓使用者看見該欄位時，這非常有用。針對 `key` 欄位，此屬性必須是 `true`。

`scoringProfiles` - 定義自訂評分行為，讓您能夠影響搜尋結果中哪些項目的出現機率會比較高。評分設定檔是由加權欄位和函式所組成。如需評分設定檔中所使用的屬性詳細資訊，請參閱[將評分設定檔新增至搜尋索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

`analyzer` - 設定要針對此欄位使用的文字分析器名稱。如需允許的值組，請參閱[語言支援](#LanguageSupport)。此選項只能與 `searchable` 欄位搭配使用。選擇分析器之後，就無法針對此欄位進行變更。


<a name="LanguageSupport"></a> **語言支援**

可搜尋的欄位最常執行的分析是斷字、文字正規化，以及篩選出字詞。根據預設，Azure 搜尋服務中可搜尋的欄位是使用 [Apache Lucene 標準分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)進行分析，並依照[「Unicode 文字區段」](http://unicode.org/reports/tr29/)規則來將文字分隔為元素。此外，標準分析器會將所有字元轉換為它們的小寫形式。已編製索引的文件和搜尋字詞在編製索引和查詢處理期間都會執行分析。

Azure 搜尋服務允許以各種語言來編製欄位的索引。這些語言中的每一個都需要非標準的文字分析器，以負責指定語言的特性。例如，法文分析器會應用[輕度法文詞幹分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html)來減少其[字組詞幹](http://en.wikipedia.org/wiki/Stemming)的字組。此外，它會從分析的文字中移除[元音省略](http://en.wikipedia.org/wiki/Elision)和法文停用字詞。適用於英文的分析器可擴充標準分析器。它會從字組中移除所有格 (結尾的 's)、為每個 [Porter 詞幹演算法](http://tartarus.org/~martin/PorterStemmer/)套用詞幹，然後移除英文[停用字詞](http://en.wikipedia.org/wiki/Stop_words)。
 
您可以藉由設定 `analyzer` 屬性，為索引定義中的每個欄位個別設定分析器。例如，您可以有個別適用於英文、法文及西班牙文旅館說明的欄位，這些欄位會以相同的索引並列存在。該查詢會指定要在您的搜尋查詢中傳回哪一個語言特定的欄位。

以下是支援的分析器清單以及其功能特性的簡短說明：

<table style="font-size:12">
    <tr>
		<th>語言</th>
		<th>分析器名稱</th>
		<th>說明</th>
	</tr>
    <tr>
		<td>阿拉伯文</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>實作阿拉伯文拼字正確正規化</li>
			<li>應用輕度演算法詞幹分析</li>
			<li>篩選出阿拉伯文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>巴西文</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出巴西文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>簡體中文</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>使用機率知識模型來尋找最佳的字詞分割</li>
			<li>篩選出中文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>繁體中文</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>編製雙字母組合的索引 (重疊兩個相鄰中文字元的群組)</li>
			<li>將字元寬度差異正規化</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>捷克文</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出捷克文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>丹麥文</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出丹麥文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>荷蘭文</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出荷蘭文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>德文</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出德文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>希臘文</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出希臘文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>English</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出英文停用字詞</li>
			<li>移除所有格</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>芬蘭文</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出芬蘭文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>法文</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出法文停用字詞</li>
			<li>移除元音省略</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>北印度文</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出北印度文停用字詞</li>
			<li>移除拼字變體中的一些差異</li>
			<li>將印度語中文字的 Unicode 表示法正規化。</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>匈牙利文</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出匈牙利文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>印尼文 (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出印尼文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>義大利文</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出義大利文停用字詞</li>
			<li>移除元音省略</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>日文</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>使用形態分析</li>
			<li>將常見的片假名拼字變體正規化</li>
			<li>輕度移除停用字詞/停用標記</li>
			<li>字元寬度正規化</li>
			<li>詞形還原 - 使字尾有變化的形容詞和動詞變為它們的基本形式</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>韓文</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>編製雙字母組合的索引 (重疊兩個相鄰中文字元的群組)</li>
			<li>將字元寬度差異正規化</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>拉脫維亞文</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出拉脫維亞文停用字詞</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>挪威文</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出挪威文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>波蘭文</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>應用演算法詞幹分析 (Stempel)</li>
			<li>篩選出波蘭文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>葡萄牙文</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出葡萄牙文停用字詞</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>羅馬尼亞文</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出羅馬尼亞文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>俄文</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出俄文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>西班牙文</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出西班牙文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>瑞典文</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出瑞典文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>土耳其文</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>去除所有格符號 (包括所有格符號本身) 之後的所有字元</li>
			<li>應用輕度詞幹分析</li>
			<li>篩選出土耳其文停用字詞</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>泰文</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>應用輕度詞幹分析</li>
			<li>篩選出泰文停用字詞</li>
		</ul>
		</td>
	</tr>
</table>

所有名稱加上 <i>lucene</i> 註解的分析器都是由 [Apache Lucene 的語言分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)所提供。

**CORS 選項**

用戶端的 Javascript 預設無法呼叫任何 API，因為瀏覽器將阻止所有跨原始來源的要求。設定 `corsOptions` 屬性來啟用 CORS (跨原始資源共用)，以允許對您的索引進行跨原始來源查詢。請注意，基於安全緣故，只有查詢 API 支援 CORS。您可以為 CORS 設定下列選項：

- `allowedOrigins` (必要)：這是將授與您索引存取權限的原始來源清單。這表示，將允許從這些原始來源提供的所有 Javascript 程式碼查詢您的索引 (假設它提供正確的 API 金鑰)。每個原始來源的形式通常是 `protocol://fully-qualified-domain-name:port` (儘管經常會忽略連接埠)。如需詳細資訊，請參閱[這篇文章](http://go.microsoft.com/fwlink/?LinkId=330822)。
 - 如果您想要允許存取所有原始來源，可在 `allowedOrigins` 陣列中包含 `*` 做為單一項目。請注意，**不建議針對生產搜尋服務使用這個做法。** 但是，如果是基於開發或偵錯目的，它就非常實用。
- `maxAgeInSeconds` (選用)：瀏覽器會使用這個值，來判斷快取 CORS 預檢回應的持續期間 (以秒為單位)。這必須是非負數的整數。這個值越大，效能就越好，但是讓 CORS 原則變更生效的時間也就越長。若未設定，即會使用預設持續期間 5 分鐘。

<a name="CreateUpdateIndexExample"></a> **要求本文範例**
 
    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "suggestions": true},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ] 
    }

**回應**

要求成功：「201 已建立」。

根據預設，回應本文將包含適用於已建立之索引定義的 JSON。如果將 `Prefer` 要求標頭設為 `return=minimal`，回應本文將是空白，而成功狀態碼會是「204 沒有內容」，而不是「201 已建立」。不論索引是使用 PUT 或 POST 來建立，都會發生此情況。

<a name="UpdateIndex"></a>
## 更新索引

您可以在 Azure 搜尋服務中，使用 HTTP PUT 要求來更新現有的索引。在公開預覽版本中，更新可包括將新欄位新增至現有的結構描述、修改 CORS 選項，以及修改評分設定檔 (請參閱[將評分設定檔新增至搜尋索引](http://msdn.microsoft.com/library/azure/dn798928.aspx))。您可以在要求 URI 上指定要更新的索引名稱：

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**重要：**在 Azure 搜尋服務公開預覽版本中，支援有限的索引結構描述更新。目前不支援任何需要重新編製索引的結構描述更新 (例如，變更欄位類型)。儘管無法變更或刪除現有欄位，但可隨時新增欄位。

將新欄位新增至索引時，索引中的所有現有文件會自動在該欄位中設定 Null 值。在將新文件新增至索引之前，將不會佔用任何額外的儲存空間。

**要求**

所有服務要求都需使用 HTTPS。**更新索引**要求是使用 HTTP PUT 來建構。使用 PUT 時，索引名稱為 URL 的一部分。如果沒有索引，系統就會加以建立。如果已有索引，就會使用新的定義來更新。

索引名稱必須是小寫、開頭為字母或數字、不可有斜線或點，且上限為 128 個字元。將索引名稱的開頭設為字母或數字之後，名稱的其餘部分就能包含任意字母、數字及破折號 (但不可為連續破折號)。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `Content-Type`：必要。請設為 `application/json`
- `api-key`：必要。`api-key` 可用來驗證搜尋服務的要求。它是服務專屬的唯一字串值。**更新索引**要求必須包含已設為您系統管理金鑰 (相對於查詢金鑰) 的 `api-key` 標頭。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文的語法**

更新現有的索引時，本文必須包含原始的結構描述定義，加上您新增的欄位，以及修改過的評分設定檔和 CORS 選項 (如果有的話)。如果未修改評分設定檔和 CORS 選項，就必須包含建立索引時的原始項目。通常，用來更新的最佳模式是使用 GET 抓取索引定義、修改它，然後使用 PUT 加以更新。

為了方便起見，將在此處重現用來建立索引的結構描述語法。如需更多詳細資訊，請參閱[建立索引](#CreateIndex)。

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

注意：從 API 版本 2014-10-20-Preview 開始支援資料類型 `Edm.Int64`。

**回應**

要求成功：「204 沒有內容」。

根據預設，回應本文將是空白。但是，如果將 `Prefer` 要求標頭設為 `return=representation`，回應本文將包含適用於已更新之索引定義的 JSON。在此情況下，成功狀態碼將是「200 確定」。

<a name="ListIndexes"></a>
## 列出索引

**列出索引**操作可傳回目前在 Azure 搜尋服務中的索引清單。

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**要求**

所有服務要求都需使用 HTTPS。**列出索引**要求可以使用 GET 方法來建構。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。
 
- `api-key`：必要。`api-key` 可用來驗證搜尋服務的要求。它是服務專屬的唯一字串值。**列出索引**要求必須包含已設為系統管理金鑰 (相對於查詢金鑰) 的 `api-key`。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

下列為回應本文的範例：

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

請注意，您可以僅針對所需的屬性來縮小篩選回應的範圍。例如，如果只想要索引名稱的清單，可使用 OData `$select` 查詢選項：

    GET /indexes?api-version=2014-07-31-Preview&$select=name

在這種情況下，上述範例的回應就會顯示為：

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

如果您的搜尋服務中有很多索引，這個方法可以節省頻寬。

<a name="GetIndex"></a>
## 取得索引

**取得索引**操作可取得 Azure 搜尋服務的索引定義。

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**要求**

服務要求需要使用 HTTPS。**取得索引**要求可以使用 GET 方法來建構。
 
要求 URI 中的 [索引名稱] 會指定要從索引集合中傳回哪一個索引。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。
 
- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務專屬的唯一字串值。**取得索引**要求必須包含已設為系統管理金鑰 (相對於查詢金鑰) 的 `api-key`。

您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

如需回應承載的範例，請參閱[建立和更新索引](#CreateUpdateIndexExample)中的範例 JSON。

<a name="DeleteIndex"></a>
## 删除索引

**刪除索引**操作可移除 Azure 搜尋服務中的索引和相關聯的文件。您可以透過 Azure Preview 入口網站的服務儀表板或 API 取得索引名稱。如需詳細資訊，請參閱[列出索引](#ListIndexes)。

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]
    
**要求**

服務要求需要使用 HTTPS。**刪除索引**要求可以使用 DELETE 方法來建構。
 
要求 URI 中的 [索引名稱] 會指定要從索引集合中刪除哪一個索引。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。
 
- `api-key`：必要。`api-key` 可用來驗證搜尋服務的要求。它是服務 URL 專屬的唯一字串值。**刪除索引**要求必須包含已設為您系統管理金鑰 (相對於查詢金鑰) 的 `api-key` 標頭。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

狀態碼：回應成功時會傳回「204 沒有內容」。

<a name="GetIndexStats"></a>
## 取得索引統計資料

**取得索引統計資料**操作會從 Azure 搜尋服務傳回目前索引的文件計數，以及儲存體使用量。

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**要求**

所有服務要求都需要使用 HTTPS。**取得索引統計資料**要求可以使用 GET 方法來建構。

要求 URL 中的 [索引名稱] 會告知服務傳回特定索引的索引統計資料。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。
 
- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務專屬的唯一字串值。**取得索引統計資料**要求必須包含已設為系統管理金鑰 (相對於查詢金鑰) 的 `api-key`。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

回應本文的格式如下：

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## 文件操作

在 Azure 搜尋服務中，使用您上傳到服務的 JSON 文件來填入索引。您上傳的所有文件會包含您搜尋資料的主體。文件會包含欄位，其中一些欄位會在它們上傳時語彙基元化為搜尋字詞。Azure 搜尋服務 API 中的 `/docs` URL 區段表示索引中的文件集合。在集合中執行的所有操作 (例如，上傳、合併、刪除或查詢文件) 都會在單一索引的內容中執行，因此，這些操作的 URL 一律會以 `/indexes/[index name]/docs` 為開頭來做為指定的索引名稱。

您的應用程式碼可以使用來自關聯資料庫的結果集或任何其他結構化的資料來源，產生要上傳到 Azure 搜尋服務的 JSON 文件。Codeplex 上的 Azure 搜尋服務 Adventure Works 示範範例應用程式包含可使用來自 Adventure Works 範例資料庫的結果集來建置 JSON 文件的程式碼。您可以在[此處](search-create-first-solution.md)深入了解範例應用程式。

在大多數的應用程式開發案例中，搜尋資料是在您的應用程式資料層外部獨立進行的。如果您的應用程式使用內部部署資料庫來追蹤庫存狀態，則保存於 Azure 搜尋服務中的文件將包含類似產品名稱、價格及可用性的資料或完全一樣的資料，但是它們將以反向索引進行儲存，以便將搜尋最佳化。

您應該規劃針對每個想要搜尋的項目擁有一份文件。電影出租應用程式可能是每部電影有一份文件、店面應用程式可能是每個 SKU 有一份文件、線上課程應用程式可能是每個課程有一份文件、研究公司可能會在他們的存放庫中針對每份學術報告有一份文件，依此類推。

文件是由一或多個欄位所組成。欄位包含已語彙基元化為搜尋字詞的文字，以及非語彙基元化或非文字的值 (這類值可用於篩選器或評分設定檔)。針對每個欄位支援的名稱、資料類型及搜尋功能是由索引結構描述所決定。您必須將每個索引結構描述中的其中一個欄位指定為識別碼，而且每份文件必須有一個適用於識別碼欄位的值，以便在索引中唯一識別該文件。所有的其他文件欄位都是選用的，如果未指定，即會預設為 Null 值。請注意，Null 值不會佔用反向索引中的空間。

上傳文件之前，您必須已經在服務上建立索引。如需這第一個步驟的詳細資訊，請參閱[建立索引](#CreateIndex)。

**注意**：Azure 搜尋服務公開預覽版本僅支援英文版的全文檢索搜尋。

<a name="AddOrUpdateDocuments"></a>
## 新增、更新或刪除文件

您可以使用 HTTP POST，從指定的索引上傳、合併、合併或上傳，或者刪除文件。如果是大量更新，建議使用批次來處理文件 (每個批次最多 1000 份文件，或者每個批次大約 16 MB)。

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    
**要求**

所有服務要求都需使用 HTTPS。您可以使用 HTTP POST，從指定的索引上傳、合併、合併或上傳，或者刪除文件。

要求 URI 包含 [索引名稱]，可指定要使用哪一個索引來張貼文件。您一次只能將文件張貼到一個索引。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `Content-Type`：必要。請設為 `application/json`
- `api-key`：必要。`api-key` 可用來驗證搜尋服務的要求。它是服務專屬的唯一字串值。**新增文件**要求必須包含已設為您系統管理金鑰 (相對於查詢金鑰) 的 `api-key` 標頭。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

要求的本文包含一或多份要編製索引的文件。文件是透過唯一的索引鍵來識別。每份文件都會與下列某個動作相關聯：上傳、合併、合併或上傳，或是刪除。上傳要求必須包含文件資料做為一組索引鍵/值組。

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }
    
**文件動作**

- `upload`：上傳動作類似「upsert」，如果是新文件，就會插入該文件，如果文件已經存在，就會更新/取代它。請注意，在更新案例中，會取代所有欄位。
- `merge`：使用指定的欄位，將更新與現有的文件合併。如果文件不存在，合併就會失敗。您在合併中指定的任何欄位將取代文件中現有的欄位。這包括類型 `Collection(Edm.String)` 的欄位。例如，如果文件包含欄位 "tags" 且值為 `["budget"]`，而您使用值 `["economy", "pool"]` 針對 "tags" 執行合併，則 "tags" 欄位最後的值是 `["economy", "pool"]`。它**不會**是 `["budget", "economy", "pool"]`。
- `mergeOrUpload`：如果含有指定索引鍵的文件已經存在於索引中，則行為會類似 `merge`。如果文件不存在，則其行為會類似含有新文件的 `upload`。
- `delete`：刪除會從索引中移除指定的文件。請注意，您只能在 `delete` 操作中指定索引鍵欄位值。嘗試指定其他欄位將導致 HTTP 400 錯誤。如果您想要從文件中移除個別欄位，請改用 `merge`，而且只需明確地將該欄位設為 `null`。 

**回應**

成功回應時所傳回的「狀態碼：200 確定」，表示所有項目都已成功編製索引 (如同針對所有項目將 'status' 欄位設為 true 所表示的一樣)：

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

至少有一個項目編製索引失敗時，會傳回「狀態碼：207」(如同針對尚未編製索引的項目，將 'status' 欄位設為 true 所表示的一樣)：
 
    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

`errorMessage` 屬性將指出發生編製索引錯誤的原因 (如果可能)。

**注意**：如果您的用戶端經常遇到 207 回應，有一個可能的原因是系統負載過低。您可以檢查 `errorMessage` 屬性來確認這一點。如果是發生這種情況，建議您***為編製索引要求設定節流***。否則，如果編製索引流量並未減緩，系統可能會開始拒絕所有要求並產生 503 錯誤。

「狀態碼：429」表示每個索引的文件數量上已經超過您的配額。您必須建立新的索引，或進行升級以取得更高的容量限制。

**範例：**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville", 
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## 搜尋文件

**搜尋**操作是當成 GET 要求來發出，可指定查詢參數，在選取相符文件時提供準則。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**要求**

服務要求需要使用 HTTPS。**搜尋**要求可以使用 GET 方法來建構。

要求 URI 會針對所有符合查詢參數的文件，指定要查詢哪一個索引。

**查詢參數**

`search=[string]` (選用) - 要搜尋的文字。除非指定 `searchFields`，否則預設會搜尋所有的 `searchable` 欄位。搜尋 `searchable` 欄位時，搜尋文字本身已經語彙基元化，因此，可以使用空格來分隔多個字詞 (例如：`search=hello world`)。若要比對任何字詞，請使用 `*` (這對於布林篩選查詢非常有用)。忽略此參數的效果與將它設為 `*` 的效果一樣。如需搜尋語法規格的相關資訊，請參閱下方的＜簡單的查詢語法＞。

  - **注意**：透過 `searchable` 欄位進行查詢時，結果有時會出人意料。Tokenizer 包含處理英文文字常見案例的邏輯，例如，所有格符號、數字內的逗號等。例如，`search=123,456` 將比對單一字詞 123,456，而不是個別的字詞 123 和 456，因為分號在英文中是用來做為較大數字的千位數分隔符號。基於此緣故，我們建議使用空格，而不是標點符號，來分隔 `search` 參數中的字詞。

`searchMode=any|all` (選用，預設為 `any`) - 任何或所有的搜尋字詞是否都必須相符，才能將文件當成相符項目來進行計數。

`searchFields=[string]` (選用) - 逗號分隔的欄位名稱清單，可針對特定文字進行搜尋。目標欄位必須標記為 `searchable`。

`$skip=#` (選用) - 要略過搜尋結果的數目；不能大於 100,000。如果您需要循序掃描文件，但因此限制而無法使用 `$skip`，請考慮改為在完全排序的索引鍵上使用 `$orderby`，以及含有查詢範圍的 `$filter`。

`$top=#` (選用) - 要抓取的搜尋結果數目。此預設值為 50。如果指定的值大於 1000 且結果數目超過 1000 個，則只會傳回前 1000 個結果，以及下一頁結果的連結 (請參閱[下列範例](#SearchResponse)中的 `@odata.nextLink`)。

`$count=true|false` (選用，預設為 `false`) - 是否要擷取結果的總計數。將此值設為 `true`，可能會對效能產生影響。請注意，傳回的計數是一個近似值。

`$orderby=[string]` (選用) - 逗號分隔的運算式清單，系統會據以排序結果。每個運算式可以是一個欄位名稱，或是 `geo.distance()` 函式的呼叫。每個運算式後面若接著 `asc` 表示遞增排序，接著 `desc` 則表示遞減排序。預設值為遞增排序。繫結將透過文件的相符分數來中斷。若未指定 `$orderby`，預設的排序順序會依照文件相符分數遞減排序。針對 `$orderby`，有 32 個子句的限制。

`$select=[string]` (選用) - 要抓取的逗號分隔欄位清單。若未指定，即會包含結構描述中標記為可抓取的所有欄位。您也可以將此參數設為 `*`，明確地要求所有欄位。

`facet=[string]` (零或多個) - 要設定多面向的欄位。字串可以選擇性地包含參數，來自訂要表達為逗號分隔之 `name:value` 對組的多面向。有效參數包括：

- `count` (多面向字詞的最大數目；預設值為 10)。沒有最大值，但較高的值會導致相對應的效能損失，尤其是在多面向欄位包含大量的唯一字詞時。
  - 例如：`facet=category,count:5` 會取得多面向結果中的前五個類別。  
  - **注意**：如果 `count` 參數小於唯一字詞的數目，結果可能不正確。這是因為多面向查詢的方式會散佈於各個分區中。提高 `count` 通常會增加字詞計數的準確性，但需要付出效能代價。 
- `sort` (下列其中一個：依計數「遞減」排序的 `count`、依計數「遞增」排序的 `-count`、依值「遞增」排序的 `value`，或是依值「遞增」排序的 `-value`)
  - 例如，`facet=category,count:3,sort:count` 會在多面向結果中，依照含有每個城市名稱的文件數目的遞減排序方式，來取得前三個類別。例如，如果前三個類別是 Budget、Motel 及 Luxury，且 Budget 有 5 個點閱數、Motel 有 6 個點閱數，而 Luxury 有 4 個點閱數，則值區依序為 Motel、Budget、Luxury。
  - 例如：`facet=rating,sort:-value` 會以依值的遞減排序方式，針對所有可能的評等來產生值區。舉例來說，如果評等是從 1 到 5，則值區的排序方式可能是 5、4、3、2、1，而不考慮有多少份文件符合每一個評等。
- `values` (直立線符號分隔的數字或 `Edm.DateTimeOffset` 值，可指定一組動態的多面向項目值)
  - 例如：`facet=baseRate,values:10|20` 會產生三個值區：一個用於基底匯率 0 到 10 (不包括 10)、一個用於 10 到 20 (不包括 20)，而一個用於 20 以上。
  - 舉例來說：`facet=lastRenovationDate,values:2010-02-01T00:00:00Z` 會產生兩個值區：一個用於 2010 年 2 月之前重新整修的旅館，另一個則用於 2010 年 2 月 1 日以後重新整修的旅館。
- `interval` (如果是數字，整數間隔大於 0，如果是日期時間值，則為 `minute`、`hour`、`day`、`week`、`month`、`quarter` 或 `year`)
  - 例如：`facet=baseRate,interval:100` 會根據大小為 100 的基本匯率範圍來產生值區。舉例來說，如果基本匯率全都介於 60 美元到 600 美元之間，則會有下列值區：0-100、100-200、200-300、300-400、400-500 及 500-600。
  - 例如：`facet=lastRenovationDate,interval:year` 會在旅館重新整修期間，每一年產生一個值區。
- **注意**：`count` 和 `sort` 可以在相同面向規格中組合在一起，但它們無法與 `interval` 或 `values` 結合，而且 `interval` 和 `values` 無法組合在一起。

`$filter=[string]` (選用) - 使用標準 OData 語法的結構化搜尋運算式。如需 Azure 搜尋服務支援的 OData 運算式文法子集詳細資訊，請參閱 [OData 運算式語法](#ODataExpressionSyntax)。

`highlight=[string]` (選用) - 一組適用於點閱數醒目提示的逗號分隔欄位名稱。只有 `searchable` 欄位可用於點閱數醒目提示。

`scoringProfile=[string]` (選用) - 評分設定檔的名稱，可用來評估比對文件的相符分數以排序結果。

`scoringParameter=[string]` (零或多個) - 使用「名稱：值」格式來指出評分函式中定義的每個參數值 (例如 `referencePointParameter`)。舉例來說，如果評分設定檔使用名為 "mylocation" 的參數來定義函式，則查詢字串選項會是 &scoringParameter=mylocation:-122.2,44.8

`api-version=[string]` (必要)。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：針對此操作，會將 `api-version` 指定為查詢參數。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務 URL 專屬的唯一字串值。**搜尋**要求可以為 `api-key` 指定系統管理金鑰或查詢金鑰。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

<a name="SearchResponse"></a> **回應**

回應成功時會傳回「狀態碼：200 確定」。

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }
    
**範例：**

您可以在 [Azure 搜尋服務的 OData 運算式語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)頁面上找到其他範例。

1) 搜尋依日期遞減排序的索引。

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

注意：DateTime 欄位的精確度已限制為毫秒。如果您推送時戳來指定任何更小的值 (例如，查看此時戳中秒的部分：10:30:09.7552052)，則系統會將傳回值四捨五入 (或是 10:30:09.7550000，按照每個範例)。

2) 在多面向搜尋中搜尋索引，並抓取特定範圍中類別、評等、標記以及含有 baseRate 之項目的多面向：

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-07-31-Preview

3) 使用篩選器，在使用者按一下評等 3 和類別 "Motel" 時，縮小先前多面向查詢結果的範圍：

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-07-31-Preview

4) 在多面向搜尋中，為查詢中傳回的唯一字詞數目設定上限。預設值為 10，但您可以使用 `facet` 屬性上的 `count` 參數來增加或減少此值：

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-07-31-Preview

5) 搜尋特定欄位內的索引；例如，語言特定的欄位：

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-07-31-Preview

6) 跨多個欄位搜尋索引。例如，您可以使用多種語言來儲存和查詢可搜尋的欄位，全部都使用相同的索引。如果英文和法文說明同時存在於同一份文件中，您就可以在查詢結果中傳回任一個或兩者：

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-07-31-Preview
	
請注意，您一次只能查詢一個索引。除非您打算一次查詢一個，否則請勿為每個語言建立多個索引。

7) 分頁 - 取得第 1 頁的項目 (頁面大小為 10)：

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-07-31-Preview

8) 分頁 - 取得第 2 頁的項目 (頁面大小為 10)：

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-07-31-Preview

9) 抓取一組特定的欄位：

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-07-31-Preview

10) 抓取符合特定查詢運算式的文件

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-07-31-Preview

11) 搜尋索引，並傳回含有點閱數醒目提示的片段

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-07-31-Preview
    
12) 搜尋索引，並傳回順序是從最接近參考位置到最遠離參考位置的方式排序的文件

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-07-31-Preview

13) 搜尋索引，但前提假設有一個名為 "geo" 的評分設定檔並含有兩個完全相同的評分函式，一個會定義名為 "currentLocation" 的參數，另一個則會定義名為 "lastLocation" 的參數

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-07-31-Preview


<a name="LookupAPI"></a>
##查閱文件

**查閱文件**操作會從 Azure 搜尋服務抓取文件。當使用者按一下特定的搜尋結果，而您想要查閱有關該文件的特定詳細資料時，這非常實用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters] 
    api-key: [admin key]
    
**要求**

服務要求需要使用 HTTPS。**查閱文件**要求的建構方式如下。

    GET /indexes/[index name]/docs/key?[query parameters] 

或者，您可以使用傳統的 OData 語法來查閱索引鍵：

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

要求 URI 包含 [索引名稱] 和 [索引鍵]，可指定要從哪一個索引抓取哪一份文件。您一次只能取得一份文件。使用**搜尋**，在單一要求中取得多份文件。

**查詢參數**

`$select=[string]` (選用) - 要抓取的逗號分隔欄位清單。若未指定或設定 `*`，即會在預測中包含結構描述中標記為可抓取的所有欄位。

`api-version=[string]` (必要)。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：針對此操作，會將 `api-version` 指定為查詢參數。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務 URL 專屬的唯一字串值。**查閱文件**要求可以為 `api-key` 指定系統管理金鑰或查詢金鑰。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

    {
      field_name: field_value (fields matching the default or specified projection)
    }
    
**範例**

查閱索引鍵為 '2' 的文件

    GET /indexes/hotels/docs/2?api-version=2014-07-31-Preview

使用 OData 語法，查閱索引鍵為 '3' 的文件：

    GET /indexes('hotels')/docs('3')?api-version=2014-07-31-Preview

<a name="CountDocs"></a>
##文件計數

**文件計數**操作可在搜尋索引中抓取文件數目的計數。`$count` 語法是 OData 通訊協定的一部分。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]
    
**要求**

服務要求需要使用 HTTPS。**文件計數**要求可以使用 GET 方法來建構。

要求 URI 中的 [索引名稱] 會告知服務傳回特定索引文件集合中所有項目的計數。

`api-version` 是必要參數。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**要求標頭**

下列清單說明必要及選用的要求標頭。

- `Accept`：此值必須設為 `text/plain`。
- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務 URL 專屬的唯一字串值。**文件計數**要求可以為 `api-key` 指定系統管理金鑰或查詢金鑰。
 
您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

回應本文包含的計數值是已格式化為純文字的整數。

<a name="Suggestions"></a>
##建議

**建議**操作會根據部分搜尋輸入抓取建議。通常用於搜尋方塊中，可以在使用者輸入搜尋字詞時提供自動提示的建議。

如果有多個候選項目符合同一個搜尋輸入，建議的文字可能就會重複。您可以使用 `$select` 來抓取其他文件欄位 (包括文件索引鍵)，如此一來，您就能針對每個建議告知來源文件。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]

**要求**

服務要求需要使用 HTTPS。**建議**要求可以使用 GET 方法來建構。

要求 URI 會指定要查詢的索引名稱。它還包括查詢字串中部分輸入的搜尋字詞。

**查詢參數**

`search=[string]` - 要用來建議查詢的搜尋文字。必須至少 3 個字元，而且不能多於 25 個字元。

`fuzzy=[boolean]` (選用，預設值 = false) - 設為 true 時，這個 API 將會尋找建議，即使搜尋文字中有替代或遺漏的字元也一樣。儘管這在某些案例中可提供較佳的體驗，但它也需要付出效能成本代價，因為模糊建議搜尋的速度較慢且耗用更多資源。

`searchFields=[string]` (選用) - 逗號分隔欄位名稱的清單，可針對特定搜尋文字進行搜尋。您必須啟用目標欄位才能提供建議。

`$top=#` (選用，預設值 = 5) - 要抓取的建議數目。必須是 1 到 10 之間的數字。

`$filter=[string]` (選用) - 可篩選考慮用來提供建議之文件的運算式。

`$orderby=[string]` (選用) - 逗號分隔的運算式清單，系統會據以排序結果。每個運算式可以是一個欄位名稱，或是 `geo.distance()` 函式的呼叫。每個運算式後面若接著 `asc` 表示遞增排序，接著 `desc` 則表示遞減排序。預設值為遞增排序。針對 `$orderby`，有 32 個子句的限制。

`$select=[string]` (選用) - 要抓取的逗號分隔欄位清單。如未指定，就只會傳回文件索引鍵和建議文字。

`api-version=[string]` (必要)。有效值包括 `2014-07-31-Preview` 或 `2014-10-20-Preview`。您可以指定在每個要求上使用哪一個值來取得版本特定的行為，但最佳做法是在整個程式碼中使用同一個版本。針對一般用法，建議的版本為 `2014-07-31-Preview`。或者，使用 `2014-10-20-Preview` 來評估實驗性功能。如需詳細資訊，請參閱[搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：針對此操作，會將 `api-version` 指定為查詢參數。

**要求標頭**

下表說明必要及選用的要求標頭。

- `api-key`：`api-key` 可用來驗證搜尋服務的要求。它是服務 URL 專屬的唯一字串值。**建議**要求可以指定系統管理金鑰或查詢金鑰來做為 `api-key`。

您也必須提供服務名稱才能建構要求 URL。您可以透過 Azure Preview 入口網站的服務儀表板取得服務名稱和 `api-key`。如需頁面導覽說明，請參閱[在入口網站中建立 Azure 搜尋服務](../search-create-service.portal.md)。

**要求本文**

無。

**回應**

回應成功時會傳回「狀態碼：200 確定」。

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

如果使用預測選項來抓取欄位，它們就會包含於陣列的每個元素中：

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **範例**

抓取 5 個部分搜尋輸入為 'lux' 的建議

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&api-version=2014-07-31-Preview




 

<!---HONumber=July15_HO1-->