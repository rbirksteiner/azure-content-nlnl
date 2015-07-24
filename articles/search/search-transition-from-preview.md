<properties 
	pageTitle="從預覽 API 版本 = 2014* 轉換為 API 版本 = 2015*" 
	description="了解重大變更，以及要如何將寫在 2014-07-31-Preview 或 2014-10-20-Preview 上的程式碼移轉到 Azure 搜尋服務 (API 版本 = 2015-02-28)。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

#從預覽 API 版本 = 2014* 轉換為 API 版本 = 2015*#

下列指引供客戶在 Azure 搜尋服務的預覽版本建置自訂應用程式，及目前移轉到正式運作的版本所用 (2015-02-28)。

身為預覽客戶，您可能有這些舊的預覽版本之一：

- [2014-07-31-Preview](search-api-2014-07-31-preview.md)
- [2014-10-20-Preview](search-api-2014-10-20-preview.md)

既然 Azure 搜尋服務已正式推出，我們鼓勵轉換到較新的版本：2015-02-28 是 Azure 搜尋服務正式運作版本的官方 API 版本。這個版本記載於 [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx)。

我們還推出下一個預覽版本 [2015-02-28-Preview](search-api-2015-02-28-preview.md)，引入還在開發中的功能。您可以透過 [Azure 搜尋服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)或我們的[意見反應頁面](http://feedback.azure.com/forums/263029-azure-search)提供有關預覽 API 的意見反應。

###移轉的檢查清單###

- 檢查重大變更以判斷您的解決方案是否受到影響。
- 對於鎖定的版本，請直接將 API 版本改成 `2015-02-28`。此版本是在 SLA 下。如果您遇到問題，則可更快解決。
- 建置、部署、測試。就搜尋行為而言，您仍有 100% 同等的服務，除了重大變更之外。
- 推出產品。
- 請評估新的功能供日後功能採用。如果您想測試 Microsoft 自然語言處理器或 `morelikethis`，請直接改成 2015-02-28-Preview。

##API 版本 = 2015* 的重大變更##

此 API 的初始版本包含自動完成或預先輸入建議的功能。雖然很實用，但這受限制於只能比對前置詞，在搜尋詞彙中的第一個字元搜尋，而無法支援欄位中其他地方的比對。此實作曾為布林屬性，稱為 `suggestions`，如果您想在特定欄位上啟用前置詞比對，可將其設定為 `true`。

最初的實作現在已經棄用，改用新的 `Suggesters` 建構，定義於[索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)功能中，提供字間和模糊比對。顧名思義，字間和模糊比對提供更廣泛的比對能力。字間比對包含前置詞，它仍然會比對開頭的字元，但延伸比對到包含字串的剩餘部分。

我們選擇停用先前的實作 (布林屬性)，代表這在 2015 的任一版本中都完全無法使用，也沒有回溯相容性，以避免客戶建置較新解決方案時意外地採用它。如果您使用 `2015-02-28` 或 `2015-02-28-Preview`，您將需要使用新的 `Suggesters` 建構來啟用預先輸入的查詢。

##編譯現有程式碼為新版本##

建議屬性為僅有的重大變更。如果您未使用此屬性，則可將 `api-version` 從 `2014-07-31-Preview` 或 `2014-10-20-Preview` 直接改成 `2015-02-28`，然後再重新建置和重新部署。此應用程式會像之前一樣運作。

實作建議的自訂應用程式應執行下列動作：

1. 更新所有 Nuget 封裝。
1. 將 API 版本直接改成 `2015-02-28`。如果您正使用下列程式碼範例，此 API 版本就位在 **AzureSearchHelper** 類別中。
1. 從定義索引的 JSON 結構描述刪除 `Suggestions={true | false}` 屬性。
1. 在 `Suggesters` 的索引底部加入建構 (如[後續](#after)章節所述)。
1. 請確認您可以發佈到服務 (您可能需要重新命名索引以避免命名衝突)。
1. 重新建置此解決方案，然後部署到測試環境中。
1. 執行所有測試案例，確保解決方案如預期執行。
1. 推出產品。

來自[在 CodePlex 上的 Adventure Works 範例](https://azuresearchadventureworksdemo.codeplex.com/)的程式碼範例具有原始的 `Suggestions` 實作。您可能會想要使用此範例實際在範例程式碼上進行程式碼移轉。

在下節中，我們會示範[先前](#before)和[後續](#after)建議的實作。您可以替換 **CreateCatalogIndex()** 方法為[後續](#after)章節中的版本，然後建置和部署解決方案以嘗試新的功能。

<a name="before"></a>
###先前###

如您所見，`Suggestions` 為設定在每個欄位上的布林屬性。請刪除所有 `Suggestions` 屬性。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###在###

已移轉的結構描述定義會省略 `Suggestions` 屬性，並且在底部加入 `Suggesters` 建構。

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##評估新功能和方法##

當您已編譯解決方案為新的版本並確認如預期運作後，您可使用下列連結以知悉新的功能。

- [Azure 搜尋服務已正式推出 (部落格文章)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Azure 搜尋服務有什麼最新的更新？](search-latest-updates.md)
- [何謂 Azure 搜尋服務？](search-what-is-azure-search.md)

##取得說明##

API 版本 `2015-02-28` 是在 SLA 下。請使用在[此頁](../support/options/)的支援選項和連結來提出支援票證。

 

<!---HONumber=July15_HO2-->