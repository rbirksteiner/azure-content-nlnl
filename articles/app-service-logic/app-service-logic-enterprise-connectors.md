<properties 
	pageTitle="Microsoft Azure 應用程式服務中的企業連接器 | Azure" 
	description="了解如何建立和設定企業連接器；微服務架構" 
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

# Microsoft Azure 應用程式服務中的企業連接器
Microsoft Azure 應用程式服務 (或簡稱應用程式服務) 包括數個適用於企業系統 (例如 SAP 和 Marketo) 的連接器 。使用這些連接器，您可以輕鬆地連接到企業系統，並完成不同的工作。

這些企業連接器提供「觸發程序」或「動作」功能。觸發程序會根據特定事件 (例如更新 MongoDb 中的項目) 啟動新的執行個體。動作為結果，像是更新 MongoDB 中的項目之後，再從 MongoDB 取得項目。


## 什麼是企業連接器
企業連接器是指可連線至內部部署與雲端架構企業系統的現有「API 應用程式」，包括：

連接器 | 說明
--- | ---
Marketo | **動作**連接器。連接至 Marketo 並執行不同動作，例如建立/更新潛在客戶、取得潛在客戶變更、排定促銷活動、要求促銷活動、取得潛在客戶、取得促銷活動/清單資訊，將潛在客戶加入清單，以及將潛在客戶從清單中移除。
MongoDB | **觸發程序**和**動作**連接器。連接至 Mongo 伺服器資料庫或內部部署 Mongo 伺服器。在 MongoDB 集合上執行不同的動作，例如建立、更新、取得和刪除項目。 
QuickBooks | **動作**連接器。連接至 QuickBooks，並使用您的應用程式來建立、更新、讀取、刪除和查詢 Intuit QuickBooks 的不同實體，例如客戶、項目、發票等等。
Salesforce |**動作**連接器。連接至 SalesForce 連接器以管理 Salesforce 帳戶中的不同實體，例如帳戶、潛在客戶、商機、案例等等。您可以在不同的實體上執行不同的動作，例如建立、更新、更新插入、查詢和刪除。 
SugarCRM | **動作**連接器。連接至 SugarCRM Online，並使用您的應用程式來建立、更新、讀取和刪除不同類型的模組，例如帳戶、連絡人、產品等等。
SAP | **動作**連接器。連接至內部部署 SAP 伺服器。叫用 RFC、BAPI 和 tRFC 並傳送 IDOC。


使用這些連接器，您可以在企業系統上完成不同的工作。例如，使用 QuickBooks 連接器，您可以在 QuickBooks 中執行一般工作，例如新增客戶、更新發票，或刪除項目。

您可以輕鬆地建立任意數目的連接器。您也可以在多個案例或工作流程內重複使用單一連接器。

例如，假設您在會議上遇到一位想要購買 10 個產品項目的客戶。在您的行動裝置上，您可以將使用您所建立之 QuickBooks 連接器的行動應用程式開啟、新增客戶詳細資料，並建立發票。

在會議上，您辦公室地點的某位客戶想要支付現有發票。員工可以將使用您所建立之相同 QuickBooks 連接器的應用程式開啟、搜尋客戶資訊，並更新發票以反映新的餘額。

這些屬於不同的流程，但它們會使用相同的 QuickBooks 連接器。您可以在無需撰寫任何程式碼的情況下建立和使用這些連接器。現在就開始吧。


## 開始使用的需求
建立使用企業的連接器時會有一些必要資源。這些必須是由您所建立的項目才能在連接器上使用。這些需求包括：

需求 | 說明
--- | ---
服務匯流排命名空間及其金鑰值 | 使用內部部署 SAP 或 MongoDB 時，需有服務匯流排命名空間及其金鑰值。如果您尚未連接至內部部署系統，就不需要服務匯流排命名空間。<br/><br/>[建立服務匯流排命名空間](http://msdn.microsoft.com/library/azure/hh690931.aspx)
內部部署混合式連線管理員 | 連接至內部部署系統時，您會在內部部署系統上安裝混合式連線管理員。建立內部部署連接器時，連接器屬性或設定值會提供下載。 
系統特定屬性 | 使用企業系統時，您會需要一些系統特定屬性 (例如使用者名稱和密碼)。 


## 建立連接器
可以使用 Azure 入口網站建立連接器。

### 在 Azure 入口網站中建立連接器
在 Azure 入口網站中，您可以在建立邏輯應用程式、Web 應用程式或行動應用程式時，建立企業連接器。或者，您可以使用它自己的分頁建立一個整合 API 應用程式。這兩種方式都十分簡單，視您的需求或喜好設定而定。部分使用者喜歡先建立包含其特定屬性的所有連接器。然後，建立邏輯、Web 或行動應用程式，並加入您所建立的連接器。

下列步驟會建立使用 [連接器] 分頁的企業連接器：

1. 在 Azure 入口網站開始面板 (首頁) 中，選取 [**Marketplace**]。**API 應用程式**會列出所有現有的連接器。您也可以**搜尋**特定連接器。
2. 選取連接器。在新分頁中，選取 [**建立**]。 
3. 輸入屬性： 

	屬性 | 說明
--- | ---
名稱 | 為您的連接器輸入任何名稱。例如，您可將它命名為 *SAPConnector*、*SalesForceGetAccounts* 或 *QuickBooksGetItems*。
Package Settings | 輸入企業系統設定，如 *SAP 使用者名稱*或 *SugarCRM 伺服器 URL*。請參閱本主題中的[企業系統特有屬性](#AddProperties)。 
應用程式服務計劃 | 列出您的付款方案。如果需要增減資源，則可以變更它。
定價層 | 一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。 
資源群組 | 建立新群組或使用現有的群組。邏輯應用程式、Web 應用程式和行動應用程式的所有 API 應用程式和連接器都必須位於相同的資源群組。<br/><br/>[使用資源群組](../resource-group-overview.md)說明此屬性 
訂閱 | 可列出目前訂閱的唯讀屬性
位置 | 裝載您 Azure 服務的地理位置 
新增至儀表板 | 選取此選項將連接器新增至您的開始面板 (首頁)


	**<a name="AddProperties"></a>企業系統特有屬性**

> [AZURE.IMPORTANT]每個連接器都會有該企業系統特有的屬性。連接至 SAP 時，您必須輸入 SAP 特有的屬性。連接至 Salesforce 時，您必須輸入 Salesforce 特有的屬性，以此類推。下表列出必要的企業系統屬性。
	
	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>端點</li><li>提供者名稱</li></ul>
MongoDB| <ul><li>連接字串</li><li>主機</li><li>埠</li><li>使用者名稱</li><li>密碼</li><li>資料庫</li><li>使用 SSL 加密</li><li>內部部署：如果是雲端架構，請輸入 False。如果 MongoDb 是內部部署系統，請輸入 True，並輸入下列屬性：<ul><li>共用的存取金鑰名稱</li><li>服務匯流排命名空間</li><li>轉送路徑</li><li>傳送金鑰</li></ul></li></ul>
QuickBooks | <ul><li>公司識別碼</li><li>提供者名稱</li></ul>
SAP | <ul><li>主機名稱</li><li>語言</li><li>使用者名稱</li><li>密碼</li><li>系統編號</li><li>服務匯流排連接字串</li><li>RFC 名稱</li><li>TRFC 名稱</li><li>BAPI 名稱</li><li>IDOC 名稱</li></ul>
Salesforce | <ul><li>提供者名稱</li><li>執行個體</li><li>版本</li><li>實體 (以逗號分隔值)</li></ul>
SugarCRM | <ul><li>伺服器 URL</li><li>提供者名稱</li><li>模組名稱</li></ul>

4. 選取 [**建立**]。


## 安裝內部部署混合式連線管理員
建立連接至內部部署系統 (例如 SAP) 的企業連接器之後，請安裝混合式連線管理員：

1. 在內部部署企業系統上，開啟 Azure 管理入口網站，然後選取您的企業連接器。 
2. 在分頁中，選取 [**混合式連線**]。 
3. 複製 [**主要閘道設定字串**] 值。 
4. 選取 [**下載及設定**]。安裝過程中，請貼上您所複製的閘道設定字串，並繼續進行安裝。 
5. 若要確認連線能力，請開啟您的企業連接器分頁。狀態應會列出 [**已連線**]。 

[與內部部署 SAP 伺服器整合](app-service-logic-integrate-with-an-on-premise-SAP-server.md)提供一個範例。


## 監視 API 應用程式
在 Azure 管理入口網站中，開啟您的企業 API 應用程式。在 [**作業**] 區段中，您可以檢視不同的管理作業。例如，您可以：

- 檢視資訊和錯誤事件
- 檢視工作處理序 (w3wp) 的記憶體使用量和執行緒計數
- 檢視應用程式和 Web 伺服器記錄檔\

另請參閱[監視邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)以取得更多資訊。


## 將連接器新增至您的應用程式 
Microsoft Azure 應用程式服務會公開可以使用這些連接器的不同應用程式類型。您可以建立新的或將現有的連接器新增至邏輯應用程式、行動應用程式或 Web 應用程式。

在您的應用程式中，您只需從組件庫中選取您的連接器，系統便會自動將它加入您的應用程式。

下列步驟會將企業連接器新增至邏輯應用程式、行動應用程式或 Web 應用程式：

1. 在 Azure 入口網站開始面板 (首頁) 中，移至 [**Marketplace**]，然後搜尋邏輯、行動、或 Web 應用程式。

	如果您是建立新的應用程式，請搜尋邏輯應用程式、行動應用程式或 Web 應用程式。選取應用程式，然後在新分頁中選取 [**建立**]。[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)會列出步驟。

2. 開啟您的應用程式，然後選取 [**觸發程序和動作**]。

3. 從 [**組件庫**] 中，新增您所建立的連接器，系統便會自動將它新增至您的應用程式。
4. 選取 [**確定**] 以儲存變更。 


## Security
部分的企業連接器會使用 OAuth 安全性。將連接器新增至您的應用程式時，您可以藉由使用您的登入帳戶連接至企業系統並同意接受這些條款，來**授權**連接器。當您這麼做時，您的應用程式和連接器會使用登入帳戶來向系統進行驗證。


### 使用 REST API 存取連接器
[請參閱如何使用 REST API 的相關文件。](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## 其他企業連接器資源
[與內部部署 SAP 伺服器整合](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)


## 閱讀關於邏輯應用程式和 Web 應用程式的資訊
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)<br/> [Azure 應用程式服務中的網站和 Web 應用程式](../app-service-web/app-service-web-overview.md)



## 其他連接器
[BizTalk 整合連接器](app-service-logic-integration-connectors.md)<br/> [企業對企業連接器](app-service-logic-b2b-connectors.md)<br/> [社交連接器](app-service-logic-social-connectors.md)<br/> [通訊協定連接器](app-service-logic-protocol-connectors.md)<br/> [應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)<br/> [連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->