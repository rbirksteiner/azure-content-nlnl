<properties 
	pageTitle="如何在 Azure 搜尋服務中實作多面向導覽" 
	description="Azure 搜尋服務中的多面向導覽" 
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
	ms.date="07/07/2015" 
	ms.author="heidist"/>

#如何在 Azure 搜尋服務中實作多面向導覽

多面向導覽是一個篩選機制，它在搜尋應用程式中提供自動導向的向下鑽研導覽。雖然「多面向導覽」一詞對您而言可能不熟悉，但您先前應該很可能使用過此功能。如下列範例所示，多面向導覽其實就是用來篩選結果的類別。

## 它看起來像什麼

 ![][1]
  
多面向可協助您找到要找的項目，同時確保您不會得到沒有項目的結果。身為開發人員，面向可讓您將最有用的搜尋準則公開，以導覽您的搜尋主體。在線上零售應用程式中，通常會根據品牌、部門 (童鞋)、尺寸、價格、熱門程度和評分來建置多面向導覽。

實作多面向導覽會因各搜尋技術而不同，而且可能非常複雜。在 Azure 搜尋服務中，多面向導覽是使用先前在結構描述中指定之屬性欄位，在查詢時間建置。在應用程式建置的查詢中，查詢必須傳送「面向查詢參數」，才能接收該文件結果集之可用的面向篩選值。若要實際修剪文件結果集，應用程式必須套用 `$filter` 運算式。

根據應用程式開發，撰寫建構查詢的程式碼，而這些查詢構成整體工作的一大部分。此服務提供大部分您希望從多面向導覽獲得的應用程式行為，包括設定範圍和取得面向結果計數的內建支援。服務也包含合理的預設值，能協助您避免難處理的導覽結構。

如需實作經驗，建議您參閱 CodePlex 上的此範例：[Azure Search AdventureWorks Catalog](https://azuresearchadventureworksdemo.codeplex.com/)

您也可以觀賞[深入了解 Aure 搜尋服務](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) 。在 45:25 時有示範如何實作多面向。

本文包含下列章節：

- [如何建置](#howtobuildit)
- [建置展示層](#presentationlayer)
- [建置索引](#buildindex)
- [檢查資料品質](#checkdata)
- [建置查詢](#buildquery)
- [控制多面向導覽的秘訣](#tips)
- [以範圍值為基礎的多面向導覽](#rangefacets)
- [以 GeoPoint 為基礎的多面項導覽](#geofacets)
- [試做](#tryitout)

##使用原因
最有效的搜尋應用程式除了搜尋方塊之外，還有多種互動模型。多面向導覽是搜尋的替代進入點，提供便利的替代方法，取代以手動輸入複雜搜尋運算式。

##了解基礎知識

如果您不了解搜尋開發，最好的方法就是把多面向導覽想成它會顯示自動導向搜尋的可能選項。它是向下鑽研搜尋體驗的一種，基於預先定義的篩選條件，可以藉由「指向並按一下」的動作快速縮減搜尋結果。

**互動模型**

多面向導覽的搜尋體驗是反覆進行，因此，讓我們先將它理解成一連串回應使用者動作而展開的查詢。

起點是一個提供多面向導覽 (通常位在側邊) 應用程式頁面。多面向導覽通常是樹狀結構，且每個值有核取方塊，或可點選的文字。

1.	傳送到 Azure 搜尋服務會透過一或多個多面向查詢參數指定多面向導覽結構。例如，查詢可能包括 `facet=Rating`，可能還有 `:values` 或 `:sort` 選項，以進一步精簡展示。
2.	展示層會使用要求上的面向來轉譯提供多面向導覽的頁面。
3.	舉包含「評分」的多面向導覽結構為例，使用者按一下 [4] 表示只會顯示評分為 4 或更高的產品。 
4.	應用程式會傳送包含 `$filter=Rating ge 4` 的查詢做為回應。 
5.	展示層會更新頁面，並顯示精簡過的結果集，其中只包含滿足新準則 (此案例中是評分為 4 或更高) 的產品。

面向是查詢參數，但不要將它與查詢輸入混淆了。它在查詢中不是當作選取準則。反之，將面向查詢參數想成是，返回的回應中所包含對導覽結構的輸入。對於提供的每個面向查詢參數，Azure 搜尋服務會評估每個面向值的部分結果中有多少文件。

請注意步驟 4 中的 `$filter`。這是多面向導覽中的一個重要層面。雖然面向和篩選條件在 API 中是獨立的，但您會需要兩者來提供您想要的體驗。

**設計模式**

在應用程式程式碼中，模式為使用面向查詢參數來傳回多面向導覽結構和面向結果，並外加一個處理點擊事件的 $filter 運算式。將 `$filter` 運算式想成修剪傳回展示層之結果的程式碼舉「色彩」的面向為例，按一下 [紅色] 這個色彩是透過只選取有紅色這個色彩的項目之 `$filter` 運算式來實作。

**Azure 搜尋服務中查詢的基礎知識**

在 Azure 搜尋服務中，要求是透過一或多個查詢參數 (如需個別的詳細描述，請參閱[搜尋文件](http://msdn.microsoft.com/library/azure/dn798927.aspx)) 指定。沒有任何一個查詢參數是必要的，但您至少要有一個才能讓查詢有效。

精確度 (通常視為篩選並排除不相關項目的能力) 是透過下列運算式而達到：

- **search=**<br/> 此參數的值構成搜尋運算式。它可能為單一的文字，或包括多個名詞和運算子的複雜搜尋運算式。在伺服器上，搜尋運算式用於進行全文檢索搜尋、針對符合的單字查詢索引中可搜尋的欄位、按順位順序傳回結果。如果您將 `search` 設為 null，查詢會在整個索引執行 (也就是 `search=*`)。在此情況下，查詢的其他元素 (例如 `$filter` 或評分設定檔) 會成為影響所傳回文件 `($filter`，以及順序 (`scoringProfile` 或 `$orderb`y) 的主要因素。

- **$filter=**<br/> 篩選器是一個強大的機制，可以根據指定的文件屬性限制搜尋結果的大小。`$filter` 會先被評估，接著是會產生每個值的可用值和對應計數的多面向邏輯。

複雜的搜尋運算式會降低查詢的效能。盡可能使用建構良好的篩選運算式，來提高精確度並改善查詢效能。

為了更明白篩選器如何提高精確度，請將一個複雜的搜尋運算式，與包含下列篩選運算式的搜尋預算式進行比較：

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

儘管兩個查詢都有效，但如果您是尋找「位在西雅圖且有停車位的非汽車旅館」，後者會是較佳的查詢。第一個查詢依賴字串欄位 (例如「名稱」、「描述」及其他包含可搜尋資料的欄位) 是否提及指定的文字。第二個查詢是尋找精確符合的結構化資料，且可能較為正確。

在包含多面向導覽的應用程式中，您會想要確保每個透過多面向導覽結構進行的使用者動作，都伴隨經篩選運算式縮減過的搜尋結果。

<a name="howtobuildit"></a>
##如何建置

Azure 搜尋服務中的多面向導覽實作在建置要求的應用程式程式碼中，但卻依賴結構描述中預先定義的元素。

`Facetable [true|false]` 索引屬性預先定義在您的搜尋索引中，且設定在所選取的欄位，以便在多面向導覽結構中啟用或停用他們。若沒有 `"Facetable" = true`，該欄位就不能在多面向導覽中使用。

查詢的時候，應用程式程式碼會建立包含 `facet=[string]` 要求參數的要求，它會提供要進行面向篩選的欄位。一個查詢可以有多個面向，例如 `&facet=color&facet=category&facet=rating`，每個面向都以 & 符號分隔。

應用程式程式碼也必須建構 `$filter` 運算式來處理多面向導覽中的點選事件。`$filter` 會使用面向值當作篩選準則將搜尋結果縮減。

使用者每輸入一個字詞，Azure 搜尋服務就會傳回搜尋結果，並更新多面向導覽結構。在 Azure 搜尋服務中，多面向導覽是單一層級的結構，其中包含面向值及其找到結果的計數。

您程式碼中的展示層提供使用者經驗。它應該列示多面向導覽的組成部份，例如標籤、值、核取方塊和計數。Azure 搜尋服務 REST API 不限於任何平台，因此您可以使用任何想要的語言和平台。重要的是要包含支援累加式重新整理的 UI 元素，當選取額外的面向便會更新 UI 狀態。

接下來的章節我們將從展示從開始，進一步了解如何建立各個部份。

<a name="presentationlayer"></a>
##建置展示層

從展示層反向做起可協助您發現從另一方向可能忽略的需求，並了解搜尋體驗的必要功能有哪些。

以多面向導覽而言，您的網頁或應用程式頁面會顯示多面向導覽結構，偵測頁面上的使用者輸入，並插入變更的元素。

對於 Web 應用程式，展示層通常是使用 AJAX，因為它可讓您重新整理累加的變更。您也可以使用 ASP.NET MVC，或任何其他可透過 HTTP 連線到 Azure 搜尋服務的虛擬平台。這整篇文章所參考的範例應用程式 (**AdventureWorks Catalog**)，恰好是一個 ASP.NET MVC 應用程式。

以下是來自範例應用程式的 **index.cshtml** 檔案，它會建立動態 HTML 結構以在搜尋結果頁面顯示多面向導覽。範例中的多面向導覽建置在搜尋頁面內，並於使用者上傳搜尋字詞後出現。

請注意每個面向都有一個標籤 (色彩、類別、價格)、一個與多面向欄位 (color、categoryName、listPrice) 的繫節，及一個用來傳回該面向結果找到之項目數目的 `.count` 參數。

  ![][2]
 

> [AZURE.TIP]設計搜尋結果頁面時，請記得加入清除面向的機制。如果您使用搜尋方塊，使用者輕鬆地就能憑直覺知道如何清除篩選條件。針對其他配置，您可能需要階層模式或其他具創意的方法。例如在 AdventureWorks Catalog 範例應用程式中，您可以按一下標題 (AdventureWorks Catalog) 來重設搜尋頁面。

<a name="buildindex"></a>
##建置索引

多面向的功能是透過索引中各欄位的 `"Facetable": true` 索引屬性來啟用。根據預設，所有可能用於多面向導覽的欄位類型為 `Facetable`。這類欄位類型包括 `Edm.String`、`Edm.DateTimeOffset`，以及所有數值欄位類型 (基本上所有欄位類型都可用於多面向導覽，除了 `Edm.GeographyPoint`)。

在建置索引時，多面向導覽的最佳作法是關閉不應做為面向之欄位的多面向功能。尤其是單一值的字串欄位 (例如識別碼或產品名稱)，應該將它們設為 `"Facetable": false` 以防止意外被用於多面向導覽。

以下是 AdventureWorks Catalog 範例應用程式的結構描述 (已修剪一些屬性來減少整體大小)：

 ![][3]
 
請注意到，已針對不應當做面向的字串欄位 (例如識別碼或名稱) 關閉 `Facetable`。關閉不需要的多面向功能可讓索引的大小比較小，並增進整體效能。

> [AZURE.TIP]根據最佳作法，建議每個欄位都包含整組完整的索引屬性。雖然幾乎所有欄位的 `Facetable` 預設都為開啟，但刻意設定每個屬性，可協助您仔細思考每個結構描述的決定。

<a name="checkdata"></a>
##檢查資料品質 

設計以資料為中心的應用程式時，準備資料通常也是工作的一大部份。搜尋應用程式也不例外。資料品質會直接影響多面向導覽結構是否能如預期般具體化，以及它是否能有效協助建構縮減結果集的篩選器。

在 Azure 搜尋服務中，搜尋主體是由填入索引的文件形成。文件提供用來運算面向的值。若您想要根據「品牌」或「價格」進行多面向導覽，每個文件就應包含 *BrandName* 和 *ProductPrice* 值，它們要像篩選選項般有效且一致，並能提昇效率。

以下列出一些需注意的提醒事項：

- 思考每個要用於多面向導覽的欄位，是否包含適合在自動導向搜尋中，當作篩選條件的值。該值應簡短、具描述性，且能在對照的選項之間，提供可有效分辨的明確選擇。
- 拼字錯誤或近似符合的值。如果您根據「色彩」進行多面向導覽，且欄位值包含 Orange 和 Ornage (拼字錯誤)，根據「色彩」欄位的面向應該兩者都採用。
- 混合大小寫的文字對多面向導覽也會遭成嚴重影響 (orange 和 Orange 為兩個不同的值)。 
- 同一個植的單數和附數版本會導致個別的面向。

正如您所想，準備資料的評鑑部份，是建置有效的多面向導覽之必要層面。

<a name="buildquery"></a>
##建置查詢

您所撰寫用於建置查詢的程式碼，應指定有效查詢的所有部份，包括搜尋運算式、面向、篩選條件、評分設定檔 (制訂要求使用的任何項目)。在本節中，我們會探索面向在查詢中的位置，及篩選條件如何配合面向使用以提供縮減的結果集。

從範例著手是不錯的開始。以下範例來自 **CatalogSearch.cs** 檔案，它建置的要求會建立根據「色彩」、「類別」和「價格」的多面向導覽。

請注意，面向在此範例應用程式中是必要的。AdventureWorks Catalog 中的搜尋體驗是根據多面向導覽和篩選條件而設計。這可由此頁面中多面向導覽顯眼的放置位置看出。範例應用程式將面向 (color、category、prices) 的 URI 參數，當成屬性包含於 Search 方法 (如範例應用程式中所建構)。

  ![][4]
 
面向查詢參數已設定至欄位，且根據資料類型能夠由包含 `count:<integer>`、`sort:<>`、`intervals:<integer>` 與 `values:<list>` 的逗號分隔清單進一步參數化。當設定範圍時，針對數值資料支援值清單。如需用量詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/azure/dn798927.aspx)。

由您的應用程式制訂的要求也應該與面向一起建置篩選條件，以根據選取的面向值縮小候選文件集的範圍。針對單車店，多面向導覽提供像是「可用的單車顏色、製造商與類型」問題的線索，而篩選條件則提供「在此價格範圍內，實際單車為紅色登山車」問題的回答。

當使用者按一下 [紅色] 來表示僅顯示紅色的商品，應用程式傳送的下一個查詢將包含 `$filter=Color eq ‘Red’`。

## 多面向導覽的最佳做法

下列清單是一些最佳做法的摘要。

- **精確**<br/> 使用篩選條件。如果您只仰賴搜尋運算式，詞幹分析可能會造成傳回的文件中的任何欄位都沒有精確面向值。 

- **目標欄位**<br/> 在多面向向下切入中，您通常希望僅包含在特定欄位中，而非在所有可搜尋欄位的任一處具有面向值的文件。新增篩選條件能透過指示服務僅在多面向欄位中搜尋相符值，加強目標欄位。

- **索引效率**<br/> 如果您的應用程式單獨使用多面向導覽 (也就是沒有搜尋方塊)，您可以將欄位標記為 `searchable=false`、`facetable=true` 以產生更多壓縮索引。此外，僅會在完整的面向值 (沒有文字分行或多重文字值元件部分的索引) 上編製索引。

- **效能**<br/> 篩選條件能縮小候選文件集的範圍，以從排名中搜尋與排除它們。如果您有很大的文件集，使用經過挑選的面向向下切入通常能讓您的效能顯著提升。


<a name="tips"></a>
##如何控制多面向導覽的提示

下面是特定問題指引的提示工作表

**針對多面向導覽中的各欄位新增標籤**

標籤通常以 HTML 或表單定義 (範例應用程式中為 **index.cshtml**)。Azure Search 中沒有適用於多面向導覽標籤或任何其他類型中繼資料的 API。

**定義可做為面向的欄位**

請記得索引結構描述會定義哪一個欄位可做為面向。假設欄位可面向化，查詢會指定要執行面向化的欄位。您執行面向化的欄位會提供顯示於標籤下方的值。

顯示於各標籤下方的值會從索引擷取。例如，如果欄位是*色彩*，其他篩選條件可用的值會是適用於該欄位的值 (紅色、黑色，依此類推)。

僅針對數值與日期時間值，您可以明確地在面向欄位上設定值 (例如，`facet=Rating,values:1|2|3|4|5`)。這些欄位類型允許值清單，以簡化將面向結果分離至連續範圍 (根據數值或時間間隔其中之一的範圍) 的動作。

**修剪面向結果**

面向結果是在符合面向字詞的搜尋結果中找到的文件。在下列範例中，*雲端運算*的搜尋結果中有 254 個項目也具有做為內容類型的*內部規格*。項目毋須互斥。如果項目同時符合兩個篩選條件的條件，則會個別計數。當在 `Collection(Edm.String)` 欄位上進行面向化時可能發生，因為這些欄位通常會實作文件標記。

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

一般來說，如果您發現面向結果持續擴大，建議您新增更多篩選條件 (如前面章節所述)，讓您的應用程式使用者有更多選項縮小搜尋範圍。

**限制多面向導覽中的項目**

針對瀏覽樹狀目錄中的每個多面向欄位，預設限制為 10 個值。對於導覽結構來說，此項預設值相當合理，因為它維持值清單在一個可管理的大小。您可以透過指派計數值來覆寫預設值。

- `&facet=city,count:5` 指定只有最高排序結果中的前 5 個城市會傳回為面向結果。提供搜尋字詞「機場」有 32 個符合項 (若查詢指定 `&facet=city,count:5`)，只有搜尋結果中包含最多文件的前五個特定城市會包含在面向結果中。

請注意面向結果與搜尋結果之間的區別。搜尋結果是符合查詢的所有文件。面向結果是各面向值的符合項。在此範例中，搜尋結果將包含不在面向分類清單中的「城市」名稱 (範例中為 5 個)。當使用者清除面向或選擇「城市」以外的其他面向時，會顯示透過多面向導覽篩選出的結果。

> [AZURE.NOTE]當有一種類型以上時討論 `count` 可能會讓人混淆。下列表格提供了如何於 Azure Search API、範例程式碼與文件中使用字詞的簡短摘要。

- `@colorFacet.count`<br/> 在簡報程式碼中，您應該會在面向上面看見用來顯示面向結果數目的計數參數。在面向結果中，計數表示符合面向字詞或範圍的文件數目。

- `&facet=City,count:12`<br/> 在面向查詢中，您可以設定值的計數。預設值為 10，但您可以將它設為更高或更低的值。設定 `count:12` 會取得面向結果中依照文件計數排序的前 12 個相符項。

- "`@odata.count`"<br/> 在查詢回應中，此值表示搜尋結果中相符項目的數目。平均起來，該值大於所有面向結果結合的加總 (因為符合搜尋字詞的項目存在)，但沒有符合的面向值。


**多面向導覽中的層級**

如附註所說明，階層中沒有直接支援巢狀面向。多面向導覽預設僅支援單一層級的篩選條件。不過有因應措施。您可以在 `Collection(Edm.String)` 中利用各階層各一個進入點來編碼階層面向。實作此因應措施已超過本文的範疇，但是您可以在[依範例顯示的 OData](http://msdn.microsoft.com/library/ff478141.aspx) 中閱讀集合的相關資訊。

**驗證欄位**

如果您根據未受信任的使用者輸入動態建置面向清單，您應該驗證多面向欄位名稱是否有效，或是在 .NET 中使用 `Uri.EscapeDataString()` 或是所選平台中的對等項建置 URL 時逸出這些名稱。

**面向結果中的計數**

當新增篩選條件至多面向查詢時，您可能希望保留面向陳述式 (例如，`facet=Rating&$filter=Rating ge 4`)。以技術層面來看，不需要 facet=Rating，但保留它會傳回分級 4 與更高層級的面向值計數。例如，如果使用者按一下 "4" 且查詢包含大於或等於 "4" 的篩選條件，則會針對等於或大於 4 的各分級傳回計數。

**面向計數上的分區隱含式**

在特定情況下，您可能會發現面向計數不符合結果集 (請參閱 [Azure Search 中的多面向導覽 (論壇文章)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch))。

面向計數可能會因為分區結構而不正確。每個搜尋索引都有多個分區，且每一個都會依照文件計數報告前 N 個面向，然後結合為單一結果。如果一些分區有許多相符值，而一些有較少相符值，您可能發現結果中有一些面向值遺失或短少。

雖然此行為可以隨時變更，但如果您今天碰到這個行為，您可以透過人工將計數:<number> 擴張到非常大的數目，以加強來自各分區的完整報告來暫時解決問題。如果計數: 的值大於或等於欄位中唯一值的數目，您就能保證結果正確。不過，當文件計數真的很高的時候，則會有效能的負面影響，因此請謹慎使用此選項。

<a name="rangefacets"></a>
##根據範圍值的多面向導覽

透過範圍進行面向化是常見的搜尋應用程式需求。範圍支援數值資料與日期時間值。您可以在[搜尋文件 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) 中閱讀各方法的相關資訊。

Azure Search 透過提供兩種方法進行範圍運算，來簡化範圍建構。針對這兩種方法，Azure Search 會依照您提供的輸入建立適當的範圍。例如，如果您指定 10|20|30 的值範圍，它會自動建立 0-10、10-20、20-30 的範圍。範例應用程式會移除任何空的間隔。

**方法 1：使用間隔參數**<br/> 若要以 10 美元遞增設定價格面向，您可以指定：`&facet=price,interval:10`


**方法 2：使用值清單**<br/> 針對數值資料，您可以使用值清單。針對ListPrice 考量面向範圍，如下所示：

  ![][5]

範圍已在使用值清單的 **CatalogSearch.cs** 檔案中指定：

    facet=listPrice,values:10|25|100|500|1000|2500

已使用 0 做為起始點，清單中的值做為結束點建置各範圍，並且修剪之前的範圍來建立離散間隔。Azure Search 會將此動作做為多面向導覽的一部分來執行。您不需要撰寫程式碼來建構各個間隔。

### 針對面向範圍建置篩選條件 ###

若要根據使用者選取的範圍篩選文件，您可以在定義範圍端點的兩段式運算式中使用 `"ge"` 與 `"lt"` 篩選運算子。例如，如果使用者選擇範圍 10-25，篩選條件會是 `$filter=listPrice ge 10 and listPrice lt 25`。

在範例應用程式中，篩選運算式使用 **priceFrom** 與 **priceTo** 參數來設定端點。**CatalogSearch.cs** 中的 **BuildFilter** 方法包含了能提供您範圍內文件的篩選運算式。

  ![][6]

<a name="geofacets"></a>
##以 GeoPoint 為基礎的篩選導覽

根據篩選條件與您目前位置的鄰近性，協助您選擇店家、餐廳或目的地等很常見。這類型的篩選條件看起來像是多面向導覽，但實際上只是篩選條件。對於那些特別要尋找特定設計問題之實作建議的使用者，我們會在此處說明。

Azure Search 中有兩種地理空間函式，**geo.distance** 與 **geo.intersects**。

- **geo.distance** 函式會回傳兩點之間的距離 (單位為公里)，一個為欄位，一個為做為篩選條件一部分傳送的常數。 

- **geo.intersects** 函式在給定點位於給定多邊形中時會傳回 true，其中該給定點會是欄位，且多邊形會指定為做為篩選條件一部分所傳送的座標常數清單。

您可以在 [OData 運算式語法 (Azure Search)](http://msdn.microsoft.com/library/azure/dn798921.aspx) 中找到篩選條件範例。若要深入了解地理空間搜尋，請參閱[在 Azure 搜尋服務中建立地理空間搜尋應用程式](search-create-geospatial.md)。

<a name="tryitout"></a>
##立即試用

於 Codeplex 上的 Azure Search Adventure Works Demo 包含了此文章中參照的範例。當您使用搜尋結果的同時，請造訪該 URL 查看查詢建構中的變更。當您選取各個結果時，此應用程式會將面向附加到 URI。

1.	設定範例應用程式 (請參閱[針對指示建立您的第一個應用程式](search-create-first-solution.md))。 

	請注意 CatalogIndexer 專案的 Program.cs 檔案中定義的結構描述。它會針對色彩、ListPrice、大小、重量、CategoryName 與 ModelName 指定可面向化的欄位。只有少部分 (色彩、ListPrice、CategoryName) 會實際在多面向導覽中實作。

3.	執行應用程式。

	一開始，只看得到 [搜尋] 方塊。您可以按一下 [搜尋] 按鈕立即取得所有結果，或輸入搜尋字詞。

	![][7]
 
4.	輸入搜尋字詞 (例如，單車)，並按一下 [搜尋]。查詢會快速執行。
 
	多面向導覽結構會隨搜尋結果一併傳回。在 URL 中，色彩、類別與價格的面向會是 Null。在搜尋結果頁面中，多面向導覽結構會包含各面向結果計數。

	 ![][8]
 
5.	按一下色彩、類別與價格範圍。初始搜尋時面向會是 Null，但是隨著它們取得值，搜尋結果會修剪不再符合的項目。請注意，URI 會整理您的查詢變更。

	![][9]
 
6.	若要清除多面向查詢好讓您能嘗試不同的查詢行為，請按一下頁面頂端的 **AdventureWorks 資料目錄**。

	![][10]
 
<a name="nextstep"></a>
##下一步

若要測試您的知識，可以新增 *modelName* 的面向欄位。針對此面向已經設定索引，因此索引不需要任何費用。但是，您需要修改 HTML 才能包含模型的新面向，並將面向欄位新增到查詢建構函式。

如需多面向導覽設計原則的深入見解，推薦您下列連結：

- [多面向搜尋的設計](http://www.uie.com/articles/faceted_search/)
- [設計模式：多面向導覽](http://alistapart.com/article/design-patterns-faceted-navigation)

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Create a geospatial search application in Azure Search]: search-create-geospatial.md
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 

<!---HONumber=July15_HO2-->