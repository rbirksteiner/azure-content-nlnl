<properties 
	pageTitle="Microsoft Azure 應用程式服務中的 BizTalk 整合 API 應用程式 | Azure" 
	description="了解如何建立和設定 BizTalk 整合 API 應用程式；微服務架構" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Microsoft Azure 應用程式服務中的 BizTalk 整合 API 應用程式
Microsoft Azure 應用程式服務 (或簡稱應用程式服務) 包含許多對整合環境很重要的 BizTalk API 應用程式。這些 API 應用程式會以 BizTalk Server 內所使用的概念和工具為基礎，但現在可做為 Azure 應用程式服務的一部分提供。

這些 API 應用程式的其中一個類別是 BizTalk 整合 API 應用程式。使用這些 BizTalk API 應用程式，您可以輕鬆地新增商務規則、轉換和驗證 XML 訊息、編碼一般檔案和 JSON 資料等等；就如同在 BizTalk Server 的內部部署一樣。

這些整合 API 應用程式提供「動作」功能。動作為結果，就像是接收 XML 訊息之後，根據 XML 結構描述驗證 XML 訊息。


## 什麼是整合 API 應用程式？
BizTalk 整合 API 應用程式是指現有、預先建立的 API 應用程式，可進行資料處理和產生輸出。比方說，部分 API 應用程式可以同時使用不同的檔案格式，部分適用於商務邏輯或應用程式邏輯。整合 API 應用程式包括：

API 應用程式 | 說明
--- | ---
<ul><li>X12 API 應用程式</li><li>AS2 連接器</li><li>EDIFACT API 應用程式</li><li>交易夥伴管理 API 應用程式</li> | 這些 API 應用程式提供企業對企業的能力。[企業對企業連接器](app-service-logic-b2b-connectors.md)提供這些 API 應用程式的其他詳細資料。
BizTalk 一般檔案編碼器 | **動作** API 應用程式。可同時使用一般檔案資料 (例如 Excel、csv) 與 XML 資料 (互通性)。它可以將一般檔案執行個體轉換成 XML，反之亦然。
BizTalk JSON 編碼器 | **動作** API 應用程式。可同時使用 JSON 和 XML 資料 (互通性)。它可以將 JSON 執行個體轉換成 XML，反之亦然。
BizTalk 規則 | **動作** API 應用程式。您可以實作並套用可控制商務作業的商務邏輯或「規則」。規則是動態的，而且可以隨時變更。BizTalk 規則可讓使用者在無需撰寫任何程式碼的情況下啟用商務邏輯，並在不影響應用程式的情況下輕鬆更新。
BizTalk 轉換 | **動作** API 應用程式。將資料從一種格式轉換成另一種格式。您可以使用不同的內建函數來管理字串、完成算術運算式、格式化日期和時間等等。 
BizTalk XML 驗證器 | **動作** API 應用程式。根據預先定義的 XML 結構描述驗證 XML 資料。您可以根據一般檔案執行個體、JSON 執行個體或現有連接器來使用結構描述。 
BIzTalk XPath 擷取程式 | **動作** API 應用程式。根據特定的 XPath 來查詢和擷取來自 XML 內容的資料。
等候 | 在您輸入的這段期間延遲執行，或延遲執行直到某個特定時間。新增至邏輯應用程式時，它可以用來延遲執行整個應用程式。


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

使用這些 API 應用程式，您可以完成不同的傳訊或資料工作。比方說，使用 BizTalk 規則 API 應用程式，您可以接收訂單，並在訂購特定的數量時套用折扣。或者，您可以根據郵遞區號收取特定的稅率。

您可以輕鬆地建立任意數目的 API 應用程式。您也可以在多個案例或工作流程內重複使用 API 應用程式。

例如，假設您有一個商務原則，當客戶訂購 100 個以上的項目時可套用 10% 的折扣。在您的應用程式中，您可以新增可檢查訂購數量的 BizTalk 規則 API 應用程式，並在超過 100 個時套用 10% 的折扣。

您也可以展開該商務原則。假設您的目標是增加北卡羅來那州的銷售。除了對 100 個或以上的訂單套用 10% 的折扣以外，如果訂單是來自北卡羅來那州，您還提供免運費的優惠。您可以輕鬆地將這個北卡羅來那州條件新增至現有的 BizTalk 規則。

您可以使用這些 API 應用程式來達到此目標，而且不需要撰寫任何程式碼。現在就開始吧。


## 建立 API 應用程式
使用 Azure 入口網站或 REST API，您可以建立整合 API 應用程式。


### 使用 REST API 建立 API 應用程式
請參閱 [REST API](http://go.microsoft.com/fwlink/p/?LinkId=529766)。


### 在 Azure 入口網站中建立整合 API 應用程式
在 Azure 入口網站中，您可以在建立邏輯應用程式、Web 應用程式或行動應用程式時，建立 BizTalk 整合 API 應用程式。或者，您可以使用它自己的分頁建立一個整合 API 應用程式。這兩種方式都十分簡單，視您的需求或喜好設定而定。部分使用者喜歡先建立包含其特定屬性的所有整合 API 應用程式。然後，建立邏輯、Web 或行動應用程式，並加入您所建立的整合 API 應用程式。

下列步驟會使用 API 應用程式分頁來建立 BizTalk 整合 API 應用程式：

1. 在 Azure 入口網站開始面板 (首頁) 中，選取 [**Marketplace**]。**API 應用程式**會列出所有現有的 API 應用程式和連接器。您也可以**搜尋**某個特定的 BizTalk API 應用程式。
2. 選取 API 應用程式。在新分頁中，選取 [**建立**]。 
3. 輸入屬性： 

	屬性 | 說明
--- | ---
名稱 | 輸入 API 應用程式的名稱。例如，您可以將其命名為 *RulesDiscountTaxCode* 或 *APIAppValidateXML*。
應用程式服務方案 | 列出您的付款方案。如果需要增減資源，則可以變更它。
定價層 | 一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。 
資源群組 | 建立新群組或使用現有的群組。邏輯應用程式、Web 應用程式和行動應用程式的所有 API 應用程式和連接器都必須位於相同的資源群組。<br/><br/>[使用資源群組](../resource-group-overview.md)說明此屬性 
訂閱 | 可列出目前訂閱的唯讀屬性
位置 | 裝載您 Azure 服務的地理位置 
新增至開始面板 | 選取此選項將整合 API 應用程式新增至您的開始面板 (首頁)。


## 設定 BizTalk API 應用程式
在 Azure 管理入口網站中，開啟您的 BizTalk API 應用程式。在 [**元件**] 區段中，您可以新增完成 API 應用程式所需的其他元件：

	API App | Tasks
--- | ---
BizTalk 一般檔案編碼器 | 輸入您要轉換成 XML 的一般檔案，例如 Excel 或 csv 檔案。或者，輸入您要轉換成一般檔案的 XML 檔案。
BizTalk JSON 編碼器 | 輸入您要轉換成 XML 的 JSON 檔案。或者，輸入您要轉換成 JSON 的 XML 檔案。 
BizTalk 規則 | 新增您的詞彙，並建立您的 IF THEN 規則。請參閱[使用 BizTalk 規則](app-service-logic-use-biztalk-rules.md)。 
BizTalk 轉換 | 新增對應，然後輸入輸入 XML 結構描述和輸出 XML 結構描述。您可以使用內建函數來管理內送訊息或資料以符合您的輸出 XML 結構描述。請參閱[轉換 XML 文件](app-service-logic-transform-xml-documents.md)。 
BizTalk XML 驗證器 | 輸入要根據預先定義的 XML 結構描述驗證的 XML。您可以根據一般檔案執行個體、JSON 執行個體或現有連接器來使用結構描述。 
BIzTalk XPath 擷取程式 | 根據特定的 XPath 來查詢和擷取來自 XML 內容的資料。
等候 | 輸入要執行 Web 應用程式、行動應用程式或邏輯應用程式的期間或特定時間。


## 監視 API 應用程式
在 Azure 管理入口網站中，開啟您的 BizTalk API 應用程式。在 [**作業**] 區段中，您可以檢視不同的管理作業。例如，您可以：

- 檢視資訊和錯誤事件
- 檢視工作處理序 (w3wp) 的記憶體使用量和執行緒計數
- 檢視應用程式和 Web 伺服器記錄檔\

另請參閱[監視邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)以取得更多資訊。


## 將 BizTalk API 應用程式新增至您的應用程式 
Microsoft Azure 應用程式服務會公開可以使用這些整合 API 應用程式的不同應用程式類型。您可以建立新的或將現有的 BizTalk API 應用程式新增至邏輯應用程式、行動應用程式或 Web 應用程式。

在您的應用程式中，您只需從組件庫中選取 BizTalk API 應用程式，系統便會自動將它加入您的應用程式。

下列步驟會將 BizTalk API 應用程式新增至邏輯應用程式、行動應用程式或 Web 應用程式：

1. 在 Azure 入口網站開始面板 (首頁) 中，移至 [**Marketplace**]，然後搜尋邏輯、行動、或 Web 應用程式。 

	如果您是建立新的應用程式，請搜尋邏輯應用程式、行動應用程式或 Web 應用程式。選取應用程式，然後在新分頁中選取 [**建立**]。[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)會列出步驟。

2. 開啟您的應用程式，然後選取 [**觸發程序和動作**]。

3. 從 [**組件庫**] 中，選取 BizTalk API 應用程式，系統便會自動將它新增至您的應用程式。

4. 選取 [**確定**] 以儲存變更。


## 其他整合 API 應用程式資源
[使用 VETR 建立 EAI 邏輯應用程式](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [轉換 XML 文件](app-service-logic-transform-xml-documents.md)<br/> [使用 BizTalk 規則](app-service-logic-use-biztalk-rules.md)<br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)


## 閱讀關於邏輯應用程式和 Web 應用程式的資訊
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)<br/> [Azure 應用程式服務中的網站和 Web 應用程式](../app-service-web/app-service-web-overview.md)


## 其他連接器
[企業連接器](app-service-logic-enterprise-connectors.md)<br/> [企業對企業連接器](app-service-logic-b2b-connectors.md)<br/> [社交連接器](app-service-logic-social-connectors.md)<br/> [通訊協定連接器](app-service-logic-protocol-connectors.md)<br/> [應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)<br/> [連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->