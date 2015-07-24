<properties 
	pageTitle="Azure 搜尋服務的最新更新事項 | Microsoft Azure" 
	description="描述服務之最新更新內容的 Azure 搜尋服務版本資訊" 
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

#Azure 搜尋服務的最新更新事項#

Azure 搜尋服務目前已正式推出，並為支援的 [2015-02-28 版本 API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 組態提供 99.9% 可用性的服務等級協定 (SLA)。

##各版本的主要功能以及發行日期

我們會透過 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)、[.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) 或 [Azure 入口網站](https://portal.azure.com)的服務儀表板單獨或共同發佈各種功能。

.NET 程式庫和 REST API 都有多種版本。如果我們推出新的功能，舊的 API 仍會保持正常運作。若要瞭解版本政策的詳細資訊，請造訪[搜尋服務版本](https://msdn.microsoft.com/library/azure/dn864560.aspx)。


##.NET SDK 0.10.0 預覽版本

這是第二個重複推出的 .NET 用戶端程式庫，Microsoft.Azure.Search.dll。此版本新增透過 .NET 類別建立、管理和使用索引子的支援。此外，Azure SQL 索引子開始支援索引地理位置點。

- [索引子類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [資料來源類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6 預覽版本
**發行日期：2015 年 3 月 5日**

此為第一個 Azure 搜尋服務專用的 .NET SDK 公用版本。此版本包含用戶端程式庫 Microsoft.Azure.Search.dll，並且具有兩個命名空間：

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

不包含：

- [索引子](http://go.microsoft.com/fwlink/p/?LinkId=528173) (此功能不再被排除在 0.10.0-預覽版本外)
- [管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- [2015-02-28 預覽](search-api-2015-02-28-Preview.md)功能 (目前僅供預覽的功能包含 Microsoft 自然語言處理器和 `moreLikeThis`)。

請造訪[如何在.NET 中使用 Azure 搜尋服務](http://go.microsoft.com/fwlink/p/?LinkId=528088)以瞭解有關安裝和使用 SDK 的指引。

##Api 版本 2015-02-28 預覽
**發行日期：2015年 4 月 22 日**

- 索引子目前支援 fieldMapping 建構，當實際欄位名稱和外部資料庫及 Azure 搜尋服務索引不同時，會提供欄位指派。請參閱[索引子](search-api-indexers-2015-02-28-Preview.md)一文，以了解索引子文件的 `2015-02-28-preview` 版本。

- 此外，在預覽更新中，索引子現在也支援欄位類型轉換，當原始欄位呈現 JSON 陣列時，您可以在 SQL 資料表中將字串欄位對應到搜尋索引中的字串集合欄位。

**發行日期：2015 年 3 月 5日**

- [Microsoft 自然語言處理器](search-api-2015-02-28-Preview.md)支援更多語言，並為 Office 和 Bing 支援的所有語言提供廣泛的詞幹分析。

- [moreLikeThis =](search-api-2015-02-28-Preview.md) 搜尋參數與 `search=` 互斥，後者會觸發替代的查詢執行路徑。`moreLikeThis=` 會透過比較可搜尋的欄位，尋找與指定文件相似的文件，而不是根據輸入的搜尋詞彙對 `search=` 進行全文搜尋。

##Api 版本 2015-02-28
**發行日期：2015 年 3 月 5日**

- [索引子](http://go.microsoft.com/fwlink/p/?LinkID=528210) 這項新功能更大幅簡化從 Azure SQL Database、Azure DocumentDB 以及 Azure VM 的 SQL Server 上的資料來源編製索引的工作。

- [建議工具](https://msdn.microsoft.com/library/azure/dn798936.aspx)透過新增中置比對支援，取代了上一個實作 (僅比對字首) 中限制較多的預先輸入查詢支援。此實作可以在詞彙的任何地方找到符合項目，並且支援模糊比對。

- [標記提升](http://go.microsoft.com/fwlink/p/?LinkId=528212)啟用了新的評分設定檔案例。特別的是，此功能會運用保存的資料 (如購物喜好設定)，讓您可以依據個人化資訊提升個別使用者的搜尋結果品質。

請造訪 [Azure 搜尋服務現在已正式推出](http://go.microsoft.com/fwlink/p/?LinkId=528211)，以在專門討論所有這些功能的 Azure 部落格中查看服務公告。

##Api 版本 2014-10-20 預覽版本
**發行日期：2014 年 11 月，2015 年 1 月**

- 新增 [Lucene 語言分析器](search-api-2014-10-20-preview.md)，讓您能夠為 Lucene 發布的自訂語言分析器提供多語言支援。 

- 為了建置索引，目前已在 [Azure 管理入口網站](https://portal.azure.com)中導入工具支援，其中包括設定檔評分。

##Api 版本 2014-07-31 預覽版本
**發行日期：2014 年 8 月 21日**

此版本是 Azure 搜尋服務的公用預覽版本，提供以下核心功能：

- 索引及文件作業專用的 REST API。在 2015-02-28 版本中，仍保有此 API 版本的大部分內容。有關版本 `2014-07-31-Preview` 的文件可以在 [Azure 搜尋服務 REST API 2014-07-31](search-api-2014-07-31-preview.md) 中找到。

- 調整搜尋結果專用的評分設定檔。評分設定檔可新增用於運算搜尋分數的準則。此功能的文件可以在 [Azure 搜尋服務評分設定檔 REST API 版本 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md) 中找到。

- 地理空間支援一開始就已推出，透過 Azure 搜尋服務最早包含的 `Edm.GeographyPoint` 資料類型提供服務。

- 在 [Azure 管理入口網站](https://portal.azure.com)預覽版本中的佈建。Azure 搜尋服務是新版入口網站所提供的少數服務中的其中一項服務。

##管理 Api 版本 2015-02-28
**發行日期：2015 年 3 月 5日**

[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 為 API 管理的第一個版本，屬於 Azure 搜尋服務中正式運作的版本。這個版本與舊版預覽的功能沒有不同。

##管理 api-版本 2014-07-31-預覽
**發行日期：2014 年 10 月**

新增的[管理 REST API](search-management-api-2014-07-31-preview.md) 預覽版本支援以程式設計方式管理服務。REST API 服務單獨建自己的版本。


 

<!---HONumber=July15_HO2-->