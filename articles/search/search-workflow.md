<properties
	pageTitle="Azure 搜尋開發的一般工作流程 | Microsoft Azure"
	description="一個工作流程或藍圖，用於建置原型以及與 Azure 搜尋整合的生產應用程式"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="04/23/2015"
	ms.author="heidist"/>

# Azure 搜尋開發的一般工作流程

本文是用於將 Azure 搜尋包括為元件的藍圖，而元件提供自訂應用程式中的搜尋經驗。根據剛開始進行還是已準備就緒，您會想要有如何將 Azure 搜尋整合到您自訂開發專案的一些初步指引。

在下列各節中，我們細分初始原型的一般工作流程，以協助您評估 Azure 搜尋符合您應用程式的搜尋需求的程度。本文的第二個部分涵蓋歸入更嚴重應用程式開發工作的重要設計決策。

開始建立原型之前，建議您使用其中一個「快速入門」教學課程或這個[一小時深入探討簡報影片](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/) (英文)。「快速入門」教學課程具有下列語言版本：[.NET](../search-get-started-dotnet/)、[Java](../search-get-started-java/)、[Node.JS](../search-get-started-nodejs/)。

##原型開發

建立成功原型的最快路徑通常會包括本節中的步驟。步驟包括佈建服務、定義您索引的結構描述、載入具有文件的索引，以及查詢索引。

對於具有變動資料 (例如，常見案例包括庫存或內容的快速變更) 的應用程式，您的原型也應該包括用於更新文件的元件。

   ![][1]

###步驟 1：佈建服務

Azure 搜尋是可透過 Azure 訂用帳戶取得的完全管理線上服務。[註冊 Azure 之後](http://azure.microsoft.com/pricing/free-trial/)，新增搜尋服務極為快速。如需如何將搜尋服務新增至訂用帳戶的指示，請瀏覽[在入口網站中建立搜尋服務](../search-create-service-portal/)。

有 2 種定價層可供選擇。建議您使用用於建立原型的共用 (免費) 服務，但請注意，您需要使用您資料的其中一小部分。共用服務可以供現有訂閱者自由使用 (透過試用或定期成員資格) 並且快速進行設定，但會將可使用的索引和文件數目限制為 3 個索引 (一個索引最多 10,000 份文件) 或儲存體總計 50 MB (看何者先達到)。

###步驟 2：建立索引

建立服務之後，就可以從其結構描述定義開始來建立索引。

建立索引的最快速且最簡單方式是透過入口網站。每份文件最少必須具有唯一索引鍵，以及至少一個包含可搜尋資料的欄位。若要開始使用，請參閱[在入口網站中建立索引](../search-create-index-portal/)。

> [AZURE.NOTE]**在 Azure 搜尋索引內**
>
> *「索引」*是組織化的持續性資料，做為所有後續搜尋作業的*「搜尋主體」*。搜尋主體儲存在雲端做為搜尋服務訂用帳戶的一部分，以快速且一致地執行搜尋作業。在搜尋術語中，搜尋主體中的項目稱為*「文件」*，而所有文件的總和是*「文件集」*。
>
>*「索引結構描述」*會依名稱、資料類型以及指定欄位是可搜尋、可篩選、可多面向等的屬性，來定義文件內的所有欄位。
>
> 除了文件結構之外，索引結構描述也會指定評分設定檔，以提供提升搜尋分數的準則，以及啟用自動完成查詢 (建議工具) 和跨網域查詢要求之 CORS 的組態設定。**對於原型，建議您只在文件中指定欄位**，然後以累加方式新增其他功能 (如需要在稍後新增的其他功能清單，請參閱步驟 5)。
>
> 套用至真實世界範例之後，請考慮使用電子商務應用程式。搜尋索引將包含可在應用程式中搜尋的所有產品或服務 (搜尋結果中出現的任何項目)。一個 SKU 會有一份文件。每份文件都會包括產品名稱、品牌、大小、價格、色彩，甚至是影像或其他您想要在搜尋結果內傳回之資源檔的參考。

###步驟 3： 載入文件

在 Azure 搜尋中儲存索引之後，下一步是填入具有文件的索引。在此步驟中，會在針對搜尋工作負載所設計的資料結構 (例如反向索引) 中上傳、分析、語彙基元化和儲存資料。

您上傳至索引的資料必須符合在上一個步驟中所定義的結構描述。文件資料代表每個欄位的一組索引鍵值組 (JSON 格式)。如果您的結構描述指定識別碼 (索引鍵) 欄位、名稱欄位、數字欄位和 URL 欄位 (如果外部影像是搜尋結果的一部分，則可能會這麼做)，則送入索引的所有文件都必須有每個欄位的值 (或 null)。

有幾種方式可以載入文件，但現在全部都需要 API。對於大部分的原型，這個步驟因需要撰寫程式碼而可能最為耗時。選項如下所述。

> [AZURE.NOTE]請記住，共用服務的限制是一個索引有 10,000 份文件。請務必減少您的資料集，讓它低於限制。如需詳細資訊，請參閱[限制條件](https://msdn.microsoft.com/library/dn798934.aspx)。

####如何將資料載入索引

其中一種方法是使用索引子。對於 Azure 中的 Azure DocumentDB 或 SQL Server 關聯式資料來源 (尤其是 Azure VM 中的 Azure SQL Database 或 SQL Server)，您可以使用[索引子](https://msdn.microsoft.com/library/dn946891.aspx)以從支援的資料來源擷取文件。在下列任何「快速入門」教學課程中，都可以找到使用用於載入文件之索引子的程式碼範例：[.NET](../search-get-started-dotnet/)、[Java](../search-get-started-java/)、[Node.JS](../search-get-started-nodejs/)。

第二個選項是使用 REST API 或載入文件的 .NET 程式庫來撰寫一個簡單的程式：

- [新增、更新或刪除文件 (REST API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [DocumentOperationsExtensions 類別](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

適用於極小資料庫的第三個選項是使用 [Fiddler](../search-fiddler/) 或 [Chrome Postman](../search-chrome-postman/) 上傳文件。

第四個選項 (可能是最簡單的選項) 是借用下列範例中的程式碼：[Adventure Works C# REST API 範例](https://azuresearchadventureworksdemo.codeplex.com/) (從解決方案的內嵌資料庫 (.mdf) 中載入文件) 或[評分設定檔 C# REST API 範例](https://azuresearchscoringprofiles.codeplex.com/) (從解決方案所包括的 JSON 資料檔中載入資料)。

> [AZURE.TIP]您可以修改並執行[評分設定檔範例](https://azuresearchscoringprofiles.codeplex.com/)，並將資料 JSON 檔案和 schema.json 檔案取代為您應用程式適用的資料。

###步驟 4：查詢文件

將文件載入索引之後，即可撰寫第一個查詢。

從搜尋服務取回初始搜尋結果的最快速方式是使用 [Fiddler](../search-fiddler/) 或 [Chrome Postman](../search-chrome-postman/) 檢視回應，但實際上，您會想要撰寫一些簡單的 UI 程式碼，以可讀取的格式檢視結果。

搜尋作業的 API 包括：

- [搜尋文件作業](https://msdn.microsoft.com/library/dn798927.aspx)
- [SearchIndexClient 類別](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Azure 搜尋中的查詢可以十分簡單。在 URI 上包括 `search=*` 將會傳回搜尋主體中的前 50 個項目；指定 `search=<some phrase>` 將執行片語的全文檢索搜尋 (最多傳回 50 份文件)，這假設至少有 50 份文件包含術語輸入的相符項。

50 份文件是預設值。您可以變更使用 `$Count` 查詢參數所傳回的項目數。此參數記載在[搜尋文件](https://msdn.microsoft.com/library/dn798927.aspx)中。

> [AZURE.TIP]最完整的查詢範例清單位於[搜尋文件](https://msdn.microsoft.com/library/dn798927.aspx)中，但您也可能想要檢閱[語法參考](https://msdn.microsoft.com/library/dn798920.aspx)來檢閱所支援的運算子清單。

###步驟 5：探索更多功能

現在您已有服務和索引，可以試驗功能進一步發展搜尋體驗。接下來會列出要調查的簡短功能清單。

**搜尋頁面**通常會在結果集中包括文件計數，或使用分頁將結果細分成更容易管理的數字。如需詳細資料，請參閱[分頁](../search-pagination-page-layout/)。

**searchMode=all** 是變更 Azure 搜尋評估 NOT 運算子方式的查詢參數。預設會展開包括 NOT (-) 的查詢，而非縮小結果範圍。您可以設定此參數，以變更運算子評估方式。它記載於[搜尋文件](https://msdn.microsoft.com/library/dn798927.aspx)或 [SearchMode 列舉](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx)中。

**評分設定檔**用來提升搜尋分數，讓符合預先定義準則的項目出現在搜尋結果中的較高位置。請參閱[開始使用評分設定檔](../search-get-started-scoring-profiles/)來逐步執行這項功能。

**篩選**用來透過提供選取項目的額外條件來縮小搜尋結果範圍。篩選運算式放在查詢內。如需詳細資料，請參閱[搜尋文件](https://msdn.microsoft.com/library/dn798927.aspx)。

**多面向導覽**用於自我引導篩選。Azure 搜尋會建置並傳回結構，而您的程式碼會將多面向導覽結構呈現在搜尋結果頁面中。如需詳細資料，請參閱[多面向導覽](../search-faceted-navigation/)。

**建議工具**指的是自動提示或自動完成查詢，以在使用者輸入搜尋片語的第一個字元時傳回建議的搜尋術語。如需詳細資訊，請參閱 [Suggestions 作業](https://msdn.microsoft.com/library/dn798936.aspx)或 [Suggesters 類別](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx)。

**語言分析器**提供文字分析期間所使用的語言規則。Azure 搜尋的預設語言分析器是 Lucene 英文，但是您可以使用不同甚至多個分析器，方法是在索引中指定它們。Lucene 分析器可用於所有 API 中。只有 [2015-02-28-Preview REST API](../search-api-2015-02-28-preview/) 才提供 Microsoft 自然語言處理器。如需詳細資訊，請參閱[語言支援](https://msdn.microsoft.com/library/dn879793.aspx)。

###步驟 6：更新索引和文件

您想要評估的一些功能可能需要更新您的索引，這通常會有需要更新文件的下游效果。

如果您需要更新索引或文件 (例如，新增建議工具，或在針對該目的新增的欄位上指定語言分析器)，請參閱下列連結以取得指示：

- [更新索引作業 (REST API)](https://msdn.microsoft.com/library/dn800964.aspx)
- [更新索引子作業 (REST API)](https://msdn.microsoft.com/library/dn946892.aspx)
- [新增、更新或刪除文件作業 (REST API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Index 類別 (.NET 程式庫)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Documents 類別 (.NET 程式庫)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

建置可建立概念證明的原型之後，即可將所學的知識用到下一個層級，方法是設計可支援生產工作負載的開發專案。

##XBOX Video Application Development

進到下一個階段現在需要決定使用哪些 API、如何管理文件和上傳頻率，以及是否要在搜尋結果中包括外部資源。

您的解決方案設計仍然需要包括針對原型所述的所有步驟，但是，您會想要考慮哪些方式最符合整體解決方案，而不是設定最有利路徑的優先順序。

###選擇 API

Azure 搜尋提供兩種程式設計模型：Managed 程式碼的 .NET 程式庫，以及程式設計語言 (如 Java、JavaScript 或另一種未指向 Microsoft .NET Framework 的語言) 的 REST API。

目前，.NET 程式庫中還未提供一小部分的功能，因此，即使您偏好撰寫 Managed 程式碼，還是可能需要使用 REST API 來取得您要的功能。REST API 中才有的功能包括：

- [Microsoft 自然語言處理器 - 僅限預覽](../search-api-2015-02-28-preview/)
- [moreLikeThis 功能 - 僅限預覽](../search-api-2015-02-28-preview/)
- [管理 API](https://msdn.microsoft.com/library/dn832684.aspx)

您可以定期檢查[新增功能](../search-latest-updates/)文章，以監視功能狀態變更。

###決定資料同步處理方法：發送或提取

發送和提取模型指的是如何更新索引中的文件。通常，案例會指定最適合您的模型。

如果您的業務是線上零售，則最可能需要發送模型，以將庫存中的任何變更發送或重複寫入至 OLTP 資料庫和 Azure 搜尋索引。如果特定 SKU 賣完，或是大小或色彩變成無法使用，則您會想要盡快更新索引，以避免造成客戶挫折。只有發送模型才能提供您搜尋索引的幾近即時更新。

在實作推送模型的 Azure 搜尋中，沒有任何特定機制。您在資料層的應用程式程式碼必須使用 [REST API](https://msdn.microsoft.com/library/dn798935.aspx) 或 [.NET 程式庫](https://msdn.microsoft.com/library/dn951165.aspx) 處理文件更新作業，來更新集合中的文件。做為實作詳細資料，使用文件索引鍵的產品 SKU 可以協助進行這項工作。

提取模型通常是從外部資料來源擷取資料的已排定作業。在 Azure 搜尋中，提取模型是透過[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx)取得，接著可用於特定資料來源：Azure DocumentDB 或 Azure SQL Database (以及 Azure VM 上的 SQL Server)。

###以批次方式載入文件

我們建議您批次新增文件以提高輸送量。假設文件平均大小為 1-2 KB，您可以批次處理最多 1,000 份文件。

針對 POST 要求，會有一個整體狀態碼。如果有組合成功和失敗的文件，則狀態碼為 HTTP 200 (成功) 或 HTTP 207 (多狀態)。除了針對 POST 要求的的狀態碼，Azure 搜尋會維護每個文件的狀態欄位。針對批次上傳，您需要一個方法來取得指出每個文件插入成功或失敗的文件專屬狀態。狀態欄會提供這些資訊。如果文件載入失敗，將設定為「false」。

負載量大時，出現一些上傳錯誤是正常的。如果出現此現象，整體狀態碼為 207，代表部分成功，而建立索引失敗之文件的「狀態」屬性會設定為「false」。

> [AZURE.NOTE]當服務接收文件時，文件會排入佇列以等待建立索引，而且可能不會立即納入搜尋結果中。如果不是在負載量大的情況下，一般來說，文件會在幾秒鐘內完成索引建立。

更新索引時，您可以將多個動作 (插入、合併、刪除) 結合在同個批次中，以排除反覆存取的時間。Azure 搜尋目前不支援部份更新 (HTTP PATCH)，因此如果要更新索引，您必須重新傳送索引定義。

###將外部資料整合至搜尋結果

Azure Search 會將內部儲存體使用於搜尋作業中所用的索引和文件。文字分析和索引剖析的執行取決於是否具有隨手可得的所有可搜尋欄位和相關聯屬性。

不過，並非文件中的所有欄位都是可搜尋的。例如，如果您的應用程式是音樂和影片的線上目錄，我們建議您將二進位檔儲存在 Azure BLOB 服務或一些其他儲存體格式中。二進位檔本身並不可搜尋，因此並不需要將其存在 Azure 搜尋儲存體中。雖然您應該將影像、視訊和音訊檔案存放在其他服務或位置，但您應該包含可參考檔案位置 URL 的欄位。如此一來，您即可傳回外部資料作為搜尋結果的一部分。

若要使用外部資料，您應該在索引中定義將 URL 指標儲存至外部資料檔的欄位。如果您發出[查閱文件](https://msdn.microsoft.com/library/dn798929.aspx)要求，或將欄位包括在搜尋結果中，則二進位檔案會出現在文件的內容中。

###容量規劃

Azure 搜尋中其中一個較吸引人的功能是可以輕鬆地放大或縮小回應需求的資源。雖然這項功能不會去除容量規劃，但是會將大部分的風險降至最低。您不需要使用額外的硬體 (或錯誤的硬體) 來執行搜尋工作負載。

最後一個步驟是檢閱複本和資料分割的現有資源層級，並判斷是否需要進行調整。調整容量的最簡單方式是在 [Azure 管理入口網站](https://ms.portal.azure.com/)中進行。

請記住，只能放大或縮小標準定價層。此外，根據調整程度，可能需要數分鐘到數小時的時間才能部署服務的其他叢集。

> [AZURE.NOTE]使用管理 REST API，以程式設計方式調整容量。如需詳細資訊，請參閱[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)。


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png
 

<!---HONumber=July15_HO1-->