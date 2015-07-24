<properties
   pageTitle="API 設計指引 | Microsoft Azure"
   description="說明如何建立設計完善之 API 的指引。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# API 設計指引

![](media/best-practices-api-design/pnp-logo.png)

本指引中的某些主題正在討論中，未來可能有所變更。歡迎您提供的意見反應！


## 概觀

許多現代化的 Web 型解決方案利用 Web 伺服器代管的 Web 服務來提供遠端用戶端應用程式所需的功能。Web 服務開放的作業構成 Web API。設計良好的 Web API 應以支援下列特性為目標：

- **平台獨立性**：用戶端應用程式應該要能夠利用 Web 服務提供的 API，而不受 API 公開之資料或作業的實際實作方法所限制。若要達到這個目標，API 必須遵守促使用戶端應用程式和 Web 服務就使用之資料格式，以及用戶端應用程式和 Web 服務兩者交換之資料格式等取得共識的通用標準。

- **服務演化**：Web 服務應該要能獨立於用戶端應用程式之外演化及新增 (或移除) 功能。當 Web 服務提供的功能變更時，現有用戶端應用程式應該要能不經修改即可繼續運作。所有功能也應該要可供探索，讓用戶端應用程式得以充分運用。

本指引的目的在於描述設計 Web API 時應考量的議題。

## 具象狀態傳輸 (REST) 簡介

在 2000 年，Roy Fielding 在他的論文中提出建構 Web 服務所公開之作業的替代架構方法：REST。REST 是建置以超媒體為基礎之分散式系統的架構樣式。REST 模型的主要優點是基於開放標準，因此能避免模型實作或存取模型的用戶端應用程式受到任何特定實作約束。比方說，您可以使用 Microsoft ASP.NET Web API 來實作 REST Web 服務，以及使用任何能產生 HTTP 要求及剖析 HTTP 回應的語言和工具組來開發用戶端應用程式。

> [AZURE.NOTE]：REST 實際上獨立於任何基礎通訊協定之外，因此不一定要與 HTTP 連結。不過在以 REST 為基礎的系統實作中，最常見的實作是將 HTTP 當做傳送及接收要求的應用程式通訊協定。本文件著重於將 REST 原則對應到為使用 HTTP 來運作而設計的系統。

REST 模型使用瀏覽配置來透過網路呈現物件和服務 (稱為_資源_)。一般來說，有許多實作 REST 系統會使用 HTTP 通訊協定來傳送這些資源的存取要求。在這些系統中，用戶端應用程式會以識別資源之 URI 的形式提交要求，以及指出要在該資源上執行作業的 HTTP 方法 (最常見的方法包括 GET、POST、PUT 或 DELETE)。HTTP 要求的本文包含執行作業所需的資料。您應了解的重點是 REST 能定義無狀態的要求模型。HTTP 要求應該是獨立的，而且可能會以任何順序發生，因此嘗試保留多個要求之間的暫時性狀態資訊不是恰當的做法。唯一可儲存資訊的場所是資源本身，而且每個要求都應該是不可部分完成的作業。實際上，REST 模型會實作有限狀態機器，讓要求將資源從定義完善的非暫時性狀態轉換成其他狀態。

> [AZURE.NOTE]REST 模型中個別要求的無狀態性質，讓遵循這些原則建構的系統具備高度擴充性。針對提出一系列要求的用戶端應用程式和處理這些要求的特定 Web 伺服器，您不需要保留兩者之間的同質性。

實作有效 REST 模型的另一個重點，是了解模型提供存取能力之各種資源間的關聯性。這些資源通常會組織成集合和關聯性。例如，假設電子商務系統的快速分析顯示有兩個用戶端應用程式可能會感興趣的集合：訂單和客戶。每張訂單和每位客戶都應該擁有自己的唯一索引鍵，以供識別之用。存取訂單集合的 URI 可能就只是像 _/orders_ 一樣簡單；同樣地，擷取所有客戶的 URI 可能是 _/customers_。向 _/orders_ URI 發出 HTTP GET 要求，應該會傳回以 HTTP 回應編碼且呈現集合中所有訂單的清單：

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

以下所示的回應會將訂單編碼成 XML 清單結構。這份清單包含 7 張訂單：

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
<OrderList xmlns:i="..." xmlns="..."><Order><OrderID>1</OrderID><OrderValue>99.90</OrderValue><ProductID>1</ProductID><Quantity>1</Quantity></Order><Order><OrderID>2</OrderID><OrderValue>10.00</OrderValue><ProductID>4</ProductID><Quantity>2</Quantity></Order><Order><OrderID>3</OrderID><OrderValue>16.60</OrderValue><ProductID>2</ProductID><Quantity>4</Quantity></Order><Order><OrderID>4</OrderID><OrderValue>25.90</OrderValue><ProductID>3</ProductID><Quantity>1</Quantity></Order><Order><OrderID>7</OrderID><OrderValue>99.90</OrderValue><ProductID>1</ProductID><Quantity>1</Quantity></Order></OrderList>
```
若要擷取個別訂單，您需要指定 _orders_ 資源中訂單的識別碼 (如 _/orders/2_)+：

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
<Order xmlns:i="..." xmlns="...">
<OrderID>2</OrderID><OrderValue>10.00</OrderValue><ProductID>4</ProductID><Quantity>2</Quantity></Order>
```

> [AZURE.NOTE]為了簡單起見，這些範例顯示以 XML 文字資料形式傳回之回應中的資訊。然而，限制資源不能包含其他 HTTP 支援的資料類型 (如二進位或加密資訊) 並不合理；HTTP 回應中的 content-type 應該要指定類型。此外，REST 模型也可以傳回不同格式 (如 XML 或 JSON) 的相同資料。在此情況下，Web 服務應該要能夠與提出要求的用戶端交涉內容。此要求包含 _Accept_ 標頭，其指定用戶端慣用的接收格式，而 Web 服務應盡可能嘗試遵守該格式。

請注意，來自 REST 要求的回應會使用標準 HTTP 狀態碼。例如，傳回有效資料的要求應包含 HTTP 回應碼 200 (良好)，而找不到或無法刪除指定資源的要求應傳回包含 HTTP 狀態碼 404 (找不到) 的回應。

## 符合 REST 限制之 Web API 的設計和結構

設計成功 Web API 的關鍵是簡單和一致性。具備這兩項要素的 Web API 可讓您輕鬆地建置需要取用 API 的用戶端應用程式。

符合 REST 限制的 Web API 著重於公開一組連接的資源，並提供讓應用程式操作這些資源及輕易地在之間瀏覽的核心作業。基於這個理由，構成典型符合 REST 限制之 Web API 的 URI 應該以 Web API 公開的資料為導向，並使用 HTTP 所提供的機制來操作這些資料。這個方法需要的心態與設計一組物件導向 API 之類別時普遍抱持的心態不同，因為後者較傾向受物件和類別的行為所驅策。此外，符合 REST 限制的 Web API 應該沒有狀態，因此不需要仰賴特定序列叫用的作業。以下各節摘要說明在設計符合 REST 限制之 Web API 時應考量的重點。

### 將 Web API 排列在資源周圍

> [AZURE.TIP]REST Web 服務所公開的 URI 應基於名詞 (Web API 提供存取能力的資料)，而不是動詞 (應用程式可以如何使用資料)。

將焦點放在 Web API 公開商業實體。例如，對於前述專為支援電子商務系統而設計的 Web API，主要實體是客戶和訂單。諸如下訂單這類的程序，您可以提供能提取訂單資訊，並將其加入客戶之訂單清單的 HTTP POST 作業來達成。就內部而言，這項 POST 作業可以執行如檢查存貨量及向客戶收費等工作。HTTP 回應可以指出訂購成功與否。另請注意，資源不一定要依據一個實體資料項目。舉例來說，藉由使用自分散在關聯式資料庫多個資料表中許多資料列彙總而來的資訊，您可以將訂單資源實作在內部，但以單一實體的形式呈現給用戶端。

> [AZURE.TIP]請避免設計會鏡像處理或需仰賴自己公開之資料內部結構的 REST 介面。REST 不僅僅是針對關聯式資料庫中個別資料表執行簡單的 CRUD (建立、擷取、更新、刪除) 作業。REST 的目的是將商業實體和應用程式可針對商業實體執行的作業對應到這些實體的實際實作，但避免向用戶端公開這些實際詳細資料。

個別的商業實體很少以隔離狀態存在 (雖然可能有某些單一物件存在)，反之，它們傾向於集結成集合。就 REST來說，每個實體和每個集合都是資源。在符合 REST 限制的 Web API 中，每個集合在 Web 服務中都有自己的 URI，而透過 URI 針對集合執行 HTTP GET 能擷取該集合中的項目清單。每個個別的項目也有自己的 URI，應用程式可以使用該 URI 來提交另一個 HTTP GET 要求，以便擷取該項目的詳細資料。您應該以階層方式組織集合和項目的 URI。在電子商務系統中，URI _/customers_ 代表客戶的集合，而 _/customers/5_ 能擷取該集合中的識別碼為 5 之單一客戶的詳細資料。這個方法有助於維持 Web API 的直覺性。

> [AZURE.TIP]在 URI 中採用一致的命名慣例；一般而言，在參考集合的 URI 中使用複數名詞比較有效益。

您也需要將不同類型之資源間的關聯性和公開這些關聯的方式納入考量。例如，客戶可能不會下訂單或下了多張訂單。若要以自然的方式來呈現此關聯性，便是透過如 _/customers/5/orders_ 之類的 URI 來尋找客戶 5 的所有訂單。您也可以考慮透過 _/orders/99/customer_ 之類的 URI 來尋找訂單 99 的客戶，呈現從訂單追溯回特定客戶的關聯，不過過度擴充該模型可能因為太過繁瑣而難以實作。比較好的解決方案，是在查詢訂單時於傳回的 HTTP 回應訊息本文中提供相關資源 (如客戶) 的可瀏覽連結。本指引稍後的「使用 HATEOAS 方法來啟用相關資源導覽」一節將有這項機制的詳細描述。

在較複雜的系統中可能有許多不同類型的實體，而提供讓用戶端應用程式瀏覽多層級關聯性的 URI 將會是很誘人的做法。例如，瀏覽 _/customers/1/orders/99/products_ 可取得客戶 1 之訂單 99 中的產品清單。不過，如果資源之間的關聯性在未來發生變更，這種程度的複雜度不僅難以維持，也缺乏彈性。相反地，您應該尋求將 URL 保持在相對簡單的情況下。請記住，一旦應用程式擁有資源的參考，它就應該可以使用這個參考來尋找與該資源相關的項目。您可以將前述查詢取代為 URI _/customers/1/orders_ 來尋找客戶 1 的所有訂單，然後查詢 URI _/orders/99/products_ 來尋找此訂單中的產品 (假設訂單 99 是由客戶 1 下的)。

> [AZURE.TIP]請避免要求比 _collection/item/collection_ 更複雜的資源 URI。

另一個要考量的重點是所有 Web 要求都會造成 Web 伺服器的負載，而要求數目越大，負載也就越沈重。您應該嘗試定義資源，以避免「多話」的 Web API 公開大量小型資源。這類 API 可能會要求用戶端應用程式提交多個要求來尋找所需的所有資料。將資料反正規化並把相關資訊結合在一起，變成發出單一要求即可予以擷取的大型資源將能提高效益。不過，您需要平衡這個方法在擷取用戶端非經常性需要之資料時的額外負荷。擷取大型物件可能會增加要求的延遲時間，而且如果額外資料並非經常使用的資料，引發的額外頻寬成本並不會產生太高的效益。

請避免在 Web API 與基礎資料來源之結構、類型或位置之間導入相依性。比方說，如果資料位於關聯式資料庫中，Web API 就不需要將每個資料表公開為資源集合。您可以將 Web API 視為資料庫的抽象，如有必要，可以在資料庫和 Web API 之間導入對應層。如此一來，當資料庫的設計或實作改變時 (例如，從含有正規化資料表之集合的關聯式資料庫轉變成文件資料庫等反正規化 NoSQL 儲存系統)，用戶端應用程式將能隔絕這些變更。
> [AZURE.TIP]支持 Web API 的資料來源不一定非得是資料存放區，它可以是另一個服務或企業營運系統應用程式，或甚至是在組織內部執行的內部部署應用程式。

最後，您不一定能將 Web API 實作的每個作業對應到特定資源。您可以透過能叫用某項功能，並以 HTTP 回應訊息形式傳回結果的 HTTP GET 要求來處理這類_非資源_案例。實作簡單計算機樣式作業 (如加法和減法) 的 Web API 可以提供將這些作業公開為虛擬資源，並利用查詢字串來指定所需參數的 URI。例如，URI _/add?operand1=99&operand2=1_ 的 GET 要求可以傳回本文包含值 100 的回應訊息，而 URI _/subtract?operand1=50&operand2=20_ 的 GET 要求則可以傳回本文包含值 30 的回應訊息。儘管如此，請謹慎使用這些形式的 URI。

### 以 HTTP 方法定義作業

HTTP 通訊協定能定義數個將語意意義指派給要求的方法。大多數符合 REST 限制之 Web API 使用的常見 HTTP 方法包括︰

- **GET**：在指定的 URI 擷取一份資源。回應訊息的本文包含要求之資源的詳細資料。

- **POST**：在指定的 URI 建立新資源。要求訊息的本文提供新資源的詳細資料。請注意，POST 也能用來觸發實際上不會建立資源的作業。

- **PUT**：取代或更新指定之 URI 的資源。要求訊息的本文能會指定要修改的資源及要套用的值。

- **DELETE**：移除指定之 URI 的資源。

> [AZURE.NOTE]HTTP 通訊協定也能定義其他較不常用的方法。例如用來向資源要求選擇性更新的 PATCH、用來要求資源描述的 HEAD、讓用戶端資訊取得伺服器支援之通訊選項相關資訊的 OPTIONS，以及讓用戶端要求可供測試和診斷之用之資訊的 TRACE。

特定要求的效果應取決於要套用的資源是集合或個別項目。下表摘要說明的常見慣例是大部分使用電子商務範例之符合 REST 限制實作所採用的慣例。請注意，這些要求中並非所有要求都可以實作，須視特定案例的情況而定。

| **Resource** | **POST** | **GET** | **PUT** | **DELETE** |
|--------------|----------|---------|---------|------------|
| /customers | 建立新客戶 | 擷取所有客戶 | 大量更新客戶 (_若實作的話_) | 移除所有客戶 |
| /customers/1 | 錯誤 | 擷取客戶 1 的詳細資料 | 更新客戶 1 的詳細資料 (若有的話)，否則傳回錯誤 | 移除客戶 1 |
| /customers/1/orders | 為客戶 1 建立新訂單 | 擷取客戶 1 的所有訂單 | 大量更新客戶 1 的訂單 (_若實作的話_) | 移除客戶 1 的所有訂單 (_若實作的話_) |

GET 和 DELETE 要求的用途相對地較清楚明瞭，但是 POST 和 PUT 要求用途和效果則有造成混淆的範圍。

POST 要求應利用要求本文提供的資料建立新資源。在 REST 模型中，您經常會將 POST 要求套用至集合形式的資源；新資源會加入集合中。

> [AZURE.NOTE]您也可以定義觸發某些功能 (不一定是傳回資料) 的 POST 要求，而這些類型的要求可以套用至集合。比方說，您可以使用 POST 要求將時程表傳遞給薪資處理服務，然後將計算出來稅金當做回應。

PUT 要求的目的在於修改現有資源。如果指定的資源不存在，PUT 要求可能會傳回錯誤 (在某些情況下，它可能會實際建立資源)。PUT 要求最常套用至個別項目形式的資源 (如特定客戶或訂單)，雖然您可以將它們套用至集合，不過這是比較不常實作的案例。請注意，PUT 要求具有等冪性，POST 要求則否。如果應用程式提交相同的 PUT 要求多次，結果應該會保持不變 (使用相同的值修改相同的資源)，但如果應用程式重複相同的 POST 要求，結果會是建立多個資源。

> [AZURE.NOTE]嚴格來說，HTTP PUT 要求會以在要求本文中指定的資源取代現有資源。如果您的用意是要修改資源中選取範圍內的屬性，但保留其他屬性不變，這時應使用 HTTP PATCH 要求來實作。不過，許多符合 REST 限制的實作放寬這項規則，均使用 PUT 來滿足這兩種情況的需求。

### 處理 HTTP 要求
用戶端應用程式加入許多 HTTP 要求中的資料，以及來自 Web 伺服器的對應回應訊息，都能以各種不同的格式 (或媒體類型) 來呈現。例如，指定客戶或訂單詳細資料的資料可能是 XML、JSON 或其他編碼及壓縮格式。符合 REST 限制的 Web API 應支援提交要求之用戶端應用程式所要求的各種媒體類型。

當用戶端應用程式傳送會在訊息本文中傳回資料的要求時，它能在要求的 Accept 標頭中指定可以處理的媒體類型。下列程式碼說明可擷取 customer 1 之詳細資料，並要求以 JSON 傳回結果的 HTTP GET 要求 (用戶端仍應該檢查回應中的資料媒體類型，以確認傳回的資料格式)：

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

如果 Web 伺服器支援此媒體類型，它可以回覆含 Content-Type 標頭的回應，並在其中指定訊息本文中的資料格式：

> [AZURE.NOTE]為了取得最大互通性，Accept 和 Content-Type 標頭中所參考媒體類型應辨識為 MIME 類型，而非某些自訂媒體類型。

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"OrderID":2,"ProductID":4,"Quantity":2,"OrderValue":10.00}
```

如果 Web 伺服器不支援要求的媒體類型，它可以利用不同的格式來傳送資料。在所有情況下，它必須在 Content-Type 標頭中指定媒體類型 (如 _text/xml_)。用戶端應用程式必須負責剖析回應訊息，並適當地解譯訊息本文中的結果。

請注意，在此範例中 Web 伺服器成功擷取要求的資料，並藉由在回應標頭中傳回狀態碼 200 來表示成功。如果找不到相符的資料，它應改為傳回狀態碼 404 (找不到)，而回應訊息的本文可以包含其他資訊。這些資訊的格式是由 Content-Type 標頭指定，如下列範例所示：

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

訂單 222 不存在，所以回應訊息看起來像：

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"Message":"No such order"}
```

當應用程式傳送 HTTP PUT 要求來更新資源時，它會指定資源的 URI，並在要求訊息的本文中提供要修改的資料。它也可以使用 Content-Type 標頭來指定這些資料的格式。以文字為基礎的資訊常會使用 _application/x-www-form-urlencoded_ 格式，其中包括一組以 & 字元分隔的名稱/值對。下一個範例顯示修改訂單 1 之資訊的 HTTP PUT 要求：

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

如果修改成功，在理想的情況下它會回應 HTTP 204 狀態碼，表示已成功處理程序，但回應本文不會包含進一步的資訊。回應中的 Location 標頭包含新更新之資源的 URI：

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [AZURE.TIP]如果 HTTP PUT 要求訊息中的資料包含日期和時間資訊，請確認 Web 服務接受遵循 ISO 8601 標準格式化的日期和時間。

如果要更新的資源不存在，Web 伺服器可以回覆「找不到」回應，如先前所述。或者，如果伺服器確實自行建立物件，它會傳回狀態碼 HTTP 200 (良好) 或 HTTP 201 (已建立)，而回應本文可以包含新資源的資料。如果要求的 Content-Type 標頭指定了 Web 伺服器無法處理的資料格式，它應該會回應 HTTP 狀態碼為 415 (不支援的媒體類型)。

> [AZURE.TIP]請考慮實作可批次更新集合中多個資源的大量 HTTP PUT 作業。PUT 要求應指定集合的 URI，而要求本文應指定要修改之資源的詳細資料。這個方法有助於減少多對話的情況及提升效能。

建立新資源之 HTTP POST 要求的格式與 PUT 要求的格式相似，其訊息本文包含要加入之新資源的詳細資料。不過，URI 通常會指定應加入資源的集合。下列範例會建立新訂單並加入訂單集合：

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=5&Quantity=15&OrderValue=400
```

如果要求成功，Web 伺服器應該會回應含 HTTP 狀態碼 201 (已建立) 的訊息代碼。Location 標頭應包含新建立之資源的 URI，且回應本文應包含一份新資源。Content-type 標頭能指定這些資料的格式：

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"OrderID":99,"ProductID":5,"Quantity":15,"OrderValue":400}
```

> [AZURE.TIP]如果 PUT 或 POST 要求所提供的資料無效，Web 伺服器應回應含 HTTP 狀態碼 400 (不正確的要求) 的訊息。此訊息的本文可以包含有關要求之問題的其他資訊和預期的格式，也可以包含提供更多詳細資料的 URL 連結。

若要移除資源，HTTP DELETE 要求恰恰能提供要刪除之資源的 URI。下列範例會嘗試移除訂單 99：

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

如果刪除作業成功，Web 伺服器應回應 HTTP 狀態碼 204，指出已成功處理程序，但回應本文不包含進一步的資訊 (這個回應與成功 PUT 作業傳回的回應相同，只不過沒有 Location 標頭，因為資源已不存在)。 如果以非同步方式執行刪除作業，DELETE 要求也可能會傳回 HTTP 狀態碼 200 (良好) 或 202 (已接受)。

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

如果找不到資源，Web 伺服器應改為傳回 404 (找不到) 訊息。

> [AZURE.TIP]如果您需要刪除集合中的所有資源，可以針對集合的 URI 指定 HTTP DELETE 要求，避免強迫應用程式輪流移除集合中的每個資源。

### 篩選及為資料分頁

您應該力圖保持 URI 簡單且具直覺性。透過單一 URI 公開資源集合能帶來這方面的優點，不過可能會導致應用程式在只需要資訊的子集時擷取大量資料。產生大量流量不僅會影響 Web 伺服器的效能和延展性，還會對要求資料之用戶端應用程式回應能力造成不良影響。

例如，如果訂單包含針對訂單支付的價格，需要擷取所有含價格 (非特定價值) 之訂單的用戶端應用程式可能需要從 _/orders_ URI 擷取所有訂單，然後在本機篩選這些訂單。顯然地，這個程序的效率不佳，它浪費裝載 Web API 之伺服器的網路頻寬和處理能力。

提供 _/orders/ordervalue_greater_than_n_ (其中 _n_ 代表訂單價格) 之類的 URI 配置可能是其中一個解決方案，但除了有限數目的價格以外，這個方法並不實用。此外，如果您需要依據其他準則查詢訂單，可能會面臨提供一長串的 URI 和可能非直覺式名稱的窘境。

較好的資料篩選策略是以查詢字串形式提供要傳遞到 Web API 的篩選準則，如 _/orders?ordervaluethreshold=n_。在此範例中，Web API 中對應的作業會負責剖析和處理 `ordervaluethreshold` 查詢字串中的參數，並在 HTTP 回應中傳回篩選結果。

某些針對集合資源的簡單 HTTP GET 要求可能會傳回大量的項目。為了對抗發生這種情況的可能性，您應該設計 Web API 以限制任何單一要求所傳回的資料量。藉由支援讓使用者指定要擷取之項目數目上限的查詢字串 (其本身可能需要受到上界限制，以防止阻絕服務攻擊)，以及插入集合的起始位移，您可以達到此目的。例如，URI _/orders?limit=25&offset=50_ 中的查詢字串應從訂單集合中找到的第 50 張訂單開始擷取 25 張訂單。如同篩選資料，在 Web API 中實作 GET 要求的作業需負責剖析和處理查詢字串中的 `limit` 和 `offset` 參數。為了協助用戶端應用程式，傳回已分頁資料的 GET 要求也應該包含某種形式的中繼資料，以便指出集合中的可用資源總數。您也可以考慮其他智慧型分頁策略。如需詳細資訊，請參閱 [API 設計注意事項：智慧型分頁](http://bizcoder.com/api-design-notes-smart-paging) (英文)

您可以遵循類似的策略在擷取資料時予以排序；您可以提供將欄位名稱當做值的排序參數 (如 _/orders?sort=ProductID_)。不過請注意，這種方法可能會對快取造成不良影響 (查詢字串參數會構成部分資源識別碼，而許多快取實作會將該識別碼當做索引鍵來快取資料)。

如果單一資源項目包含大量資料，您可以延伸這個方法來限制 (投射) 傳回的欄位。例如，您可以使用接受以逗號分隔清單之欄位的查詢字串參數 (如 _/orders?fields=ProductID,Quantity_)。

> [AZURE.TIP]為查詢字串中所有選擇性參數提供有意義的預設值。例如，如果您實作分頁，可以將 `limit` 參數設為 10，並將 `offset` 參數設為 0；如果您實作排序，可以將排序參數設定為資源的索引鍵；如果您支援投射，可以將 `fields` 參數設定為資源中的所有欄位。

### 處理大型二進位資源

單一資源可能包含大型的二進位欄位，如檔案或影像。若要克服傳輸問題造成的不可靠和間歇性連線，以及改善回應時間，請考慮提供讓用戶端應用程式以區塊形式擷取這類資源的作業。若要這樣做，Web API 應支援大型資源之 GET 要求的 Accept-Ranges 標頭，最理想的情況是針對這些資源實作 HTTP HEAD 要求。Accept-Ranges 標頭表示 GET 作業支援部分結果，而且用戶端應用程式可提交傳回以位元組範圍指定之資源子集的 GET 要求。HEAD 要求與 GET 要求相似，不過它只會傳回描述資源的標頭和空白的訊息本文。用戶端應用程式可以發出 HEAD 要求，以判斷是否要使用部分 GET 要求擷取資源。下列範例顯示取得產品影像之相關資訊的 HEAD 要求：

```HTTP
HEAD http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
...
```

回應訊息含有標頭，其中包括資源大小 (4580 個位元組)，以及對應 GET 作業支援部分結果的 Accept-Ranges 標頭：

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

用戶端應用程式可以使用這些資訊來建構一系列的 GET 作業，以便利用較小的區塊擷取影像。第一個要求會使用 Range 標頭擷取前 2500 個位元組：

```HTTP
GET http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
Range: bytes=0-2499
...
```

回應訊息會藉由傳回 HTTP 狀態碼 206 來指出這是部分回應。Content-Length 標頭能指出訊息本文傳回的實際位元組數目 (不是資源的大小)，而 Content-Range 標頭能指出這是資源的哪個部分 (4580 個位元組中的第 0-2499 個位元組)：

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

用戶端應用程式的後續要求可以使用適當的 Range 標頭來擷取資源的其餘部分：

```HTTP
GET http://adventure-works.com/products/10?fields=ProductImage HTTP/1.1
Range: bytes=2500-
...
```

對應的結果訊息看起來應該像這樣：

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## 使用 HATEOAS 方法來啟用相關資源導覽

REST 背後的其中一個主要動機，是它應該可以在不需要事先知道 URI 配置的情況下瀏覽整組資源。每個 HTTP GET 要求都應傳回讓您能透過回應包含之超連結尋找與要求物件直接相關之資源的資訊，您也應提供它描述每個資源上可供使用之作業的資訊。此原則就是所謂的 HATEOAS (Hypertext as the Engine of Application State)。系統實際上是有限的狀態機器，而每個要求的回應都包含從某個狀態轉換為另一個狀態所需的資訊。其他任何資訊都不是必要資訊。

> [AZURE.NOTE]目前還沒有定義該如何為 HATEOAS 原則建立模型的標準或規格。本節所示的範例將說明一個可行的解決方案。

舉例來說，若要處理客戶和訂單之間的關聯性，針對特定訂單傳回之回應中的資料應包含超連結格式的 URI，以便識別下訂單的客戶，以及可針對該客戶執行的作業。

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

回應訊息的本文包含 `Links` 陣列 (程式碼範例中反白顯示的內容)，其指定關聯性的本質 (_客戶_)、客戶的 URI (_http://adventure-works.com/customers/3_)、如何擷取此客戶的詳細資料 (_GET_)，以及 Web 伺服器支援擷取這些資訊的 MIME 型別 (_text/xml_ 和 _application/json_)。這是用戶端應用程式要能夠擷取客戶詳細資料所需的所有資訊。此外，連結陣列也包含其他可執行之作業的連結，如 PUT (修改客戶，以及 Web 伺服器預期用戶端提供的格式) 和 DELETE。

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"OrderID":3,"ProductID":2,"Quantity":4,"OrderValue":16.60,"Links":[(some links omitted){"Relationship":"customer","HRef":" http://adventure-works.com/customers/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":"
customer","HRef":" http://adventure-works.com /customers/3", "Action":"PUT","LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},{"Relationship":"customer","HRef":" http://adventure-works.com /customers/3","Action":"DELETE","LinkedResourceMIMETypes":[]}]}
```

基於完整性考量，連結陣列也應包含有關已擷取之資源的自我參考資訊。先前的範例省略了這些連結，不過我們在下列程式碼中反白顯示。請注意，在這些連結中，我們使用關聯性 _self_ 來指出這是作業所傳回之資源的參考：

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"OrderID":3,"ProductID":2,"Quantity":4,"OrderValue":16.60,"Links":[{"Relationship":"self","HRef":" http://adventure-works.com/orders/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":" self","HRef":" http://adventure-works.com /orders/3", "Action":"PUT","LinkedResourceMIMETypes":["application/x-www-form-urlencoded"]},{"Relationship":"self","HRef":" http://adventure-works.com /orders/3", "Action":"DELETE","LinkedResourceMIMETypes":[]},{"Relationship":"customer",
"HRef":" http://adventure-works.com /customers/3", "Action":"GET","LinkedResourceMIMETypes":["text/xml","application/json"]},{"Relationship":" customer" (customer links omitted)}]}
```

這個方法若要生效，您必須備妥用戶端應用程式，使其能擷取及剖析這些額外資訊。

## 符合 REST 限制的 Web API 版本控制

即使是在所有最簡單的情況下，Web API 依然不太可能維持不變。隨著商務需求變更，我們可能會加入新資源集合、資源之間的關聯性可能會改變，也會修改資源中的資料結構。雖然更新 Web API 來處理新的或不同的需求是相對簡單的程序，不過您必須將這類變更對取用 Web API 之用戶端應用程式所造成的效果納入考量。問題在於雖然設計和實作 Web API 的開發人員擁有該 API 完整的控制能力，不過對於從遠端作業之第三方組織所建置的用戶端應用程式，開發人員並沒有相同程度的控制能力。主要的要務是讓現有用戶端應用程式在未變更的情況下繼續運作，同時允許新用戶端應用程式充分運用新功能和資源。

版本控制可讓 Web API 指定其公開的功能和資源，而用戶端應用程式則可以提交導向特定版本之功能或資源的要求。下列小節描述幾個不同的方法，每個方法都有其優點和缺點。

### 無版本控制

這是最簡單的方法，而且也是某些內部 API 可接收的方法。重大變更可能會以新資源或新連結來呈現。將內容加入現有資源可能不會成為重大變更，因為未預期要查看此內容的用戶端應用程式會直接忽略。

例如，URI _http://adventure-works.com/customers/3_ 的要求應該會傳回包含 `Id`、`Name` 和 `Address` 欄位的單一客戶詳細資料，而這些欄位也是用戶端應用程式預期的欄位：

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

> [AZURE.NOTE]為了簡化及避免困擾，本節所示的範例回應不包含 HATEOAS 連結。

如果您將 `DateCreated` 欄位加入客戶資源的結構描述，回應看起來會像這樣：

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":"1 Microsoft Way Redmond WA 98053"}]
```

如果現有用戶端應用程式能略過無法辨認的欄位，它們可能會繼續正常運作，而新的用戶端應用程式可以在經過設計後處理這個新欄位。不過，如果資源的結構描述發生更巨大的變動 (如移除或重新命名欄位)，或資源之間的關聯性改變，這些變更可能會構成阻礙現有用戶端應用程式正常運作的重大變更。在這些情況下，您應該考慮下列其中一個方法。

### URI 版本控制

每次修改 Web API 或變更資源的結構描述時，您會在每個資源的 URI 加入版本號碼。早已存在的 URI 應維持先前的運作，傳回符合原始結構描述的資源。

延伸上述範例，如果將 `Address` 欄位重建為包含位址之每個構成組件的子欄位 (如 `StreetAddress`、`City`、`State` 及 `ZipCode`)，您可以透過包含版本號碼的 URI (如 http://adventure-works.com/v2/customers/3) 公開這個版本的資源：

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":{"StreetAddress":"1 Microsoft Way","City":"Redmond","State":"WA","ZipCode":98053}}]
```

這個版本控制機制非常簡單，但需仰賴伺服器將要求路由傳送到適當端點。不過，在經過數個反覆項目後當 Web API 成熟時，它會變得難以揮灑，因此伺服器必須支援許多不同的版本。此外，從純化論者的觀點來看，在所有情況下用戶端應用程式都在擷取相同的資料 (客戶 3)，所以 URI 不應該因版本而有所不同。此配置也會讓 HATEOAS 的實作變得更複雜，因為所有連結都需要在它們的 URI 中包含版本號碼。

### 查詢字串版本控制

與其提供多個 URI，您可以在附加至 HTTP 要求的查詢字串中，藉由使用參數來指定資源的版本，如 _http://adventure-works.com/customers/3?version=2_。如果較舊的用戶端應用程式省略版本參數，它應該預設成有意義的值 (如 1)。

這個方法具有語意上的優點，因為您總是從相同的 URI 擷取相同的資源，不過這還是要取決於處理要求以剖析查詢字串，然後回傳適當 HTTP 回應的程式碼。這個方法也需要面臨與實作 HATEOAS 同等複雜的 URI 版本控制機制。

> [AZURE.NOTE]某些較舊的網頁瀏覽器和 Web Proxy 不會快取在 URL 中包含查詢字串的要求回應。對於使用 Web API 且從這類網頁瀏覽器內部執行的 Web 應用程式，這會對應用程式的效能造成負面影響。

### 標頭版本控制

與其以查詢字串參數的形式附加版本號碼，您可以實作指出資源版本的自訂標頭。這個方法需要用戶端應用程式將適當標頭加入任何要求中，不過如果省略版本標頭，處理用戶端要求的程式碼可以使用預設值 (第 1 版)。下列範例使用名為 _Custom-Header_ 的自訂標頭。此標頭的值指出 Web API 的版本。

第 1 版：

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

第 2 版：

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","DateCreated":"2014-09-04T12:11:38.0376089Z","Address":{"StreetAddress":"1 Microsoft Way","City":"Redmond","State":"WA","ZipCode":98053}}]
```

請注意，如同先前的兩個方法，實作 HATEOAS 需要將適當的自訂標頭加入任何連結中。

### 媒體類型版本控制

當用戶端應用程式將 HTTP GET 要求傳送至 Web 伺服器時，它應該會使用 Accept 標頭來規定可處理的內容格式 (如本指引稍早所述)。_Accept_ 標頭的目的經常是讓用戶端應用程式指定回應本文應該是 XML、JSON 或其他某些用戶端可剖析的通用格式。不過，您也可以定義自訂媒體類型，加入讓用戶端應用程式指定預期之資源版本的資訊。下列範例顯示將 _Accept_ 標頭指定為 _application/vnd.adventure-works.v1+json_ 值的要求。_Vnd.adventure works.v1_ 元素指示 Web 伺服器傳回第 1 版的資源，而 _json_ 元素指定回應本文的格式應該是 JSON：

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

處理要求的程式碼會負責處理 _Accept_ 標頭且盡可能遵循 (用戶端應用程式可能會在 _Accept_ 標頭中指定多個格式。不論如何，Web 伺服器可以選擇最合適的回應本文格式)。Web 伺服器會使用 Content-Type 標頭確認回應本文中的資料格式：

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
[{"Id":3,"Name":"Contoso LLC","Address":"1 Microsoft Way Redmond WA 98053"}]
```

如果 Accept 標頭未指定任何已知的媒體類型，Web 伺服器可能會產生 HTTP 406 (無法接受) 回應訊息，或以預設媒體類型傳回訊息。

這個方法可以說是最純粹的版本控制機制，自然也符合 HATEOAS 的本質，也就是在資源連結中包含 MIME 類型的相關資料。

> [AZURE.NOTE]在選擇版本控制策略時，您也應該將對效能的潛在影響 (特別是 Web 伺服器上的快取) 納入考量。有鑑於同一個 URI/查詢字串組合每次都會參考相同的資料，因此 URI 版本控制和查詢字串版本控制等配置具備易於快取的特性。

> 標頭版本控制和媒體類型版本控制等機制，通常需要額外的邏輯來檢查自訂標頭或 Accept 標頭中的值。在大規模的環境中，使用不同版本之 Web API 的眾多用戶端可能會導致伺服器端快取出現大量的重複資料。如果用戶端應用程式透過實作快取的 Proxy 與 Web 伺服器通訊，而且只有在快取中目前沒有要求之資料的副本時才會將要求轉送到 Web 伺服器，這個問題可能會變得更嚴重。

## 詳細資訊

- [RESTful Cookbook](http://restcookbook.com/) (英文) 含有建置符合 REST 限制之 Web API 的簡介。
- Web [API 檢查清單](https://mathieu.fenniak.net/the-api-checklist/) (英文) 含有在設計及實作 Web API 時應納入考量的實用項目清單。

<!---HONumber=62-->