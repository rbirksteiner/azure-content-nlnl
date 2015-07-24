<properties 
	pageTitle="Microsoft Azure App Service 中的企業對企業連接器和 API 應用程式 | Azure" 
	description="了解如何建立及設定 EDI、EDIFACT、AS2 和 TPM 連接器；微服務架構" 
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

# Microsoft Azure App Service 中的企業對企業連接器和 API 應用程式
Microsoft Azure App Service (或簡稱 App Service) 包含許多對整合環境很重要的 BizTalk API 應用程式。這些 API 應用程式會以 BizTalk Server 內所使用的概念和工具為基礎，但現在可做為 Azure App Service 的一部分提供。

這些 API 應用程式的其中一個類別是企業對企業 (B2B) API 應用程式。使用這些 B2B API 應用程式，您可以輕鬆地加入夥伴、建立協議，以及使用 EDI、AS2 和 EDIFACT 對內部部署進行您所要的作業。

這些 B2B API 應用程式提供「觸發程序」和「動作」功能。觸發程序會根據特定的事件啟動新的執行個體，例如來自夥伴的 X12 訊息送達。動作即為結果，就像在接收 X12 訊息之後，然後使用 AS2 傳送訊息。


## 什麼是企業對企業連接器或 API 應用程式
企業對企業 (B2B) 功能包含現有、預先建置的 API 應用程式，可讓不同的公司、部門、應用程式等等使用 AS2、EDI 和 EDIFACT 進行通訊。

B2B API 應用程式包括：

連接器或 API 應用程式 | 說明
--- | ---
BizTalk 交易夥伴管理 | 使用夥伴和協議建立企業對企業 (B2B) 關係的 API 應用程式。這些關係會使用 AS2、EDIFACT 和 X12 通訊協定。<br/><br/>TPM API 應用程式是 AS2 連接器以及 X12 或 EDIFACT API 應用程式的基本需求。 
AS2 連接器 | 使用 AS2 傳輸接收和傳送訊息的連接器。連接器會透過網際網路安全可靠地傳輸資料。
BizTalk EDIFACT | 使用 EDIFACT 接收和傳送訊息的 API 應用程式。EDIFACT 通常也稱為 UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport)，而且廣泛用於各產業。
BizTalk X12 | 使用 X12 通訊協定接收和傳送訊息的 API 應用程式。X12 通常也稱為 ASC X12 (Accredited Standards Committee X12)，而且廣泛用於各產業。 


您可以使用這些 API 應用程式來完成不同的 EDI 傳訊工作。例如，使用 AS2 連接器，您可以安全地對客戶、貴公司的部門 (如人力資源)，或使用 AS2 的任何人接收和傳送不同類型的訊息 (EDI、XML、一般檔案等等)。

您可以輕鬆地建立任意數目的 API 應用程式。您也可以在多個案例或工作流程中重複使用單一 API 應用程式。

不需撰寫任何程式碼，即可執行這項操作。現在就開始吧。


## 開始使用的需求
當您建立 B2B API 應用程式時，有一些必要的資源。您必須先建立這些項目，才可使用於其他 API 應用程式中。這些需求包括：

需求 | 說明
--- | ---
Azure SQL Database | 儲存 B2B 項目，包括夥伴、結構描述、憑證及協議。每一個 B2B API 應用程式需要專屬 Azure SQL Database。<br/><br/>**注意** 將連接字串複製至這個資料庫。<br/><br/>[建立 Azure SQL Database](../sql-database-create-configure.md)
Azure Blob 儲存體容器 | 啟用 AS2 封存功能時，可儲存訊息屬性。如果您不需要 AS2 訊息封存，則儲存體容器不是必要的容器。<br/><br/>**注意** 如果您是啟用封存，請將連接字串複製至這個 Blob 儲存體。<br/><br/>[關於 Azure 儲存體帳戶](../storage-create-storage-account.md)
服務匯流排命名空間及其金鑰值 | 會儲存 X12 和 EDIFACT 批次資料。如果您不需要批次處理，則服務匯流排命名空間不是必要的命名空間。<br/><br/>**注意** 如果您是啟用批次處理，請複製這些值。<br/><br/>[建立服務匯流排命名空間](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM 執行個體 | 建立 AS2 連接器和 X12 或 EDIFACT API 應用程式時，需要 BizTalk 交易夥伴管理 (TPM) 執行個體。當您建立 TPM API 應用程式時，表示您正在建立 TPM 執行個體。<br/><br/>**注意** 知道您的 TPM API 應用程式名稱。 


## 建立 API 應用程式
可以使用 Azure 入口網站或 REST API 建立 B2B API 應用程式。


### 使用 REST API 建立 API 應用程式
[請參閱如何使用 REST API 的相關文件。](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### 在 Azure 入口網站中建立 B2B API 應用程式
在 Azure 入口網站中，您可以在建立邏輯應用程式、Web 應用程式或行動應用程式時建立 B2B API 應用程式。或者，您可以使用它自己的分頁建立一個整合 API 應用程式。這兩種方式都十分簡單，視您的需求或喜好設定而定。有些使用者偏好先使用其特定屬性建立所有 B2B API 應用程式。然後，建立邏輯應用程式/Web 應用程式/行動應用程式，並加入您建立的 B2B API 應用程式。

下列步驟會使用 API 應用程式分頁建立 B2B API 應用程式。


#### 建立 BizTalk 交易夥伴管理 (TPM) API 應用程式

> [AZURE.NOTE]建立 AS2 連接器和 X12 或 EDIFACT API 應用程式時，需要 BizTalk 交易夥伴管理 (TPM) 執行個體。當您建立 TPM API 應用程式時，表示您正在建立 TPM 執行個體。

下列步驟會建立 TPM 執行個體：

1. 在 Azure 入口網站開始面板 (首頁) 中，選取 [**Marketplace**]。**API 應用程式**會列出所有現有的 API 應用程式和連接器。您也可以**搜尋**特定 B2B API 應用程式。
2. 選取 [**BizTalk 交易夥伴管理**]。在新分頁中，選取 [**建立**]。 
3. 輸入屬性： 

	屬性 | 說明
--- | ---
名稱 | 輸入 TPM 執行個體的任何名稱。例如，您可以將它命名為 *AccountsPayableTPM*。
Package Settings | 將 ADO.NET **資料庫連接字串**輸入到您所建立的 Azure SQL Database。<br/><br/>當您複製連接字串時，密碼不會加入至連接字串。請務必在連接字串中輸入密碼。
App Service 方案 | 列出您的付款方案。如果需要增減資源，則可以變更它。
定價層 | 一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。 
資源群組 | 建立新群組或使用現有的群組。邏輯應用程式、Web 應用程式和行動應用程式的所有 API 應用程式和連接器都必須位於相同的資源群組。<br/><br/>[使用資源群組](../resource-group-overview.md)說明此屬性 
訂閱 | 可列出目前訂閱的唯讀屬性
位置 | 裝載您 Azure 服務的地理位置 
新增至儀表板 | 選取此選項可將 B2B API 應用程式新增至您的開始面板 (首頁)。

4. 選取 [**建立**]。

建立 TPM API 應用程式 (TPM 執行個體) 之後，您可以接著建立 AS2 連接器和/或 X12 或 EDIFACT API 應用程式。


#### 建立 AS2 連接器

1. 在 Azure 入口網站開始面板 (首頁) 中，選取 [**Marketplace**]。**API 應用程式**會列出所有現有的 API 應用程式和連接器。您也可以**搜尋**特定 B2B API 應用程式。
2. 選取 [**AS2 連接器**]。在新分頁中，選取 [**建立**]。 
3. 輸入屬性： 

	屬性 | 說明
--- | ---
名稱 | 輸入 AS2 連接器的任何名稱。例如，您可以將它命名為 *AS2Connector*。
Package Settings | 輸入該 API 應用程式特定的設定，像是 TPM 執行個體名稱。<br/><br/>請參閱本主題中的[新增 AS2 封裝設定](#AddAS2Conn)以取得特定屬性。 
App Service 方案 | 列出您的付款方案。如果需要增減資源，則可以變更它。
定價層 | 一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。 
資源群組 | 建立新群組或使用現有的群組。[使用資源群組](../resource-group-overview.md)說明此屬性。 
訂閱 | 可列出目前訂閱的唯讀屬性
位置 | 裝載您 Azure 服務的地理位置 
新增至儀表板 | 選取此選項可將 B2B API 應用程式新增至您的開始面板 (首頁)。

	**<a name="AddAS2Conn"></a>AS2 連接器封裝設定**

	屬性 | 說明
--- | --- 
資料庫連接字串 | 將 ADO.NET 連接字串輸入到您所建立的 Azure SQL Database。當您複製連接字串時，密碼不會加入至連接字串。請務必在貼上之前，在連接字串中輸入密碼。
啟用內送訊息的封存 | 選用。啟用這個屬性以儲存從合作夥伴接收之內送 AS2 訊息的訊息屬性。 
Azure Blob 儲存體連接字串 | 將連接字串輸入到您所建立的 Azure Blob 儲存體容器。啟用封存後，已編碼和解碼的訊息會儲存在此儲存體容器中。
TPM 執行個體名稱 | 輸入您先前建立的 **BizTalk 交易夥伴管理** API 應用程式的名稱。當您建立 AS2 連接器時，這個連接器只會執行此特定 TPM 執行個體中的 AS2 協議。

4. 選取 [**建立**]。


#### 建立 X12 或 EDIFACT API 應用程式

1. 在 Azure 入口網站開始面板 (首頁) 中，選取 [**Marketplace**]。**API 應用程式**會列出所有現有的 API 應用程式和連接器。您也可以**搜尋**特定 B2B API 應用程式。
2. 選取 **BizTalk X12** 或 **BizTalk EDIFACT**。在新分頁中，選取 [**建立**]。 
3. 輸入屬性： 

	屬性 | 說明
--- | ---
名稱 | 輸入 B2B API 應用程式的任何名稱。例如，您可以將它命名為 *EDI850APIApp*。
Package Settings | 輸入該 API 應用程式特定的設定，像是 TPM 執行個體名稱。<br/><br/>請參閱本主題中的 [X12 或 EDIFACT 封裝設定](#AddX12)以取得特定屬性。 
App Service 方案 | 列出您的付款方案。如果需要增減資源，則可以變更它。
定價層 | 一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。 
資源群組 | 建立新群組或使用現有的群組。[使用資源群組](../resource-group-overview.md)說明此屬性。 
訂閱 | 可列出目前訂閱的唯讀屬性
位置 | 裝載您 Azure 服務的地理位置 
新增至儀表板 | 選取此選項可將 B2B API 應用程式新增至您的開始面板 (首頁)。

	**<a name="AddX12"></a>X12 和 EDIFACT API 應用程式封裝設定**

	屬性 | 說明
--- | --- 
資料庫連接字串 | 將 ADO.NET 連接字串輸入到您所建立的 Azure SQL Database。當您複製連接字串時，密碼不會加入至連接字串。請務必在貼上之前，在連接字串中輸入密碼。
服務匯流排 命名空間 | 輸入您所建立的服務匯流排命名空間。只有在啟用批次時才需要。 
服務匯流排命名空間共用存取金鑰名稱 | 輸入您所建立的服務匯流排命名空間存取金鑰。只有在啟用批次時才需要。 
服務匯流排命名空間共用存取金鑰值 | 輸入您所建立的服務匯流排命名空間存取金鑰值。只有在啟用批次時才需要。 
TPM 執行個體名稱 | 輸入您先前建立的 **BizTalk 交易夥伴管理** API 應用程式的名稱。當您建立 X12 或 EDIFACT API 應用程式時，此 API 應用程式只會執行此特定 TPM 執行個體中的 X12/EDFIACT 協議。

4. 選取 [**建立**]。


## 新增夥伴、協議、憑證和結構描述 
在 Azure 管理入口網站中，開啟您的 TPM API 應用程式。在 [**元件**] 區段中，新增夥伴、協議、憑證及結構描述。

您也可以將協議加入至 AS2 連接器、X12 API 應用程式及 EDIFACT API 應用程式。


## 監視 API 應用程式
在 Azure 管理入口網站中，開啟您的 TPM API 應用程式。在 [**作業**] 區段中，您可以檢視不同的管理作業。例如，您可以：

- 檢視資訊和錯誤事件
- 檢視工作處理序 (w3wp) 的記憶體使用量和執行緒計數
- 檢視應用程式和 Web 伺服器記錄檔

另請參閱[監視邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)，以取得詳細資訊。


## 將 API 應用程式加入至您的應用程式 
Microsoft Azure App Service 會公開可使用這些 B2B API 應用程式的不同應用程式類型。您可以建立新的應用程式或將現有的 B2B API 應用程式加入至邏輯應用程式、行動應用程式或 Web 應用程式。

在您的應用程式中，只要從組件庫選取 B2B API 應用程式，即可自動將它加入至您的應用程式。

> [AZURE.IMPORTANT]若要新增您先前建立的連接器和 API 應用程式，請在相同的資源群組中建立邏輯應用程式、行動應用程式或 Web 應用程式。

下列步驟會將 B2B API 應用程式加入至邏輯應用程式、行動應用程式或 Web 應用程式：

1. 在 Azure 入口網站開始面板 (首頁) 中，移至 [**Marketplace**]，然後搜尋邏輯、行動、或 Web 應用程式。 

	如果您是建立新的應用程式，請搜尋邏輯應用程式、行動應用程式或 Web 應用程式。選取應用程式，然後在新分頁中選取 [**建立**]。[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)會列出步驟。

2. 開啟您的應用程式，然後選取 [**觸發程序和動作**]。

3. 從 [**組件庫**] 選取 B2B API 應用程式，即可自動將它加入至您的應用程式。您也可以建立新的 B2B API 應用程式。

	> [AZURE.IMPORTANT]AS2 連接器和 X12、EDIFACT API 應用程式都需要 TPM 執行個體。所以如果您要建立新的 B2B API 應用程式，請先建立 TPM API 應用程式，然後建立 AS2 連接器、X12 API 的應用程式或 EDIFACT API 應用程式。

4. 選取 [**確定**] 以儲存變更。


## 其他 B2B 資源

[建立 B2B 程序](app-service-logic-create-a-b2b-process.md)<br/> [建立交易夥伴協議](app-service-logic-create-a-trading-partner-agreement.md)<br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)


## 閱讀關於邏輯應用程式和 Web 應用程式的資訊
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)<br/> [Azure 應用程式服務中的網站和 Web 應用程式](../app-service-web/app-service-web-overview.md)


## 其他連接器
[BizTalk 整合連接器](app-service-logic-integration-connectors.md)<br/> [企業連接器](app-service-logic-enterprise-connectors.md)<br/> [社交連接器](app-service-logic-social-connectors.md)<br/> [通訊協定連接器](app-service-logic-protocol-connectors.md)<br/> [應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)<br/> [連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->