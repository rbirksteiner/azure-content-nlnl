<properties 
	pageTitle="連接器和 API 應用程式清單 | Azure" 
	description="閱讀有關在 Azure 應用程式服務中的連接器和 API 應用程式；微服務架構" 
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
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service 中的連接器和 API 應用程式清單
本主題列出所有由 Microsoft 建立的可用連接器和 API 應用程式。

如需定價資訊，以及每個服務層隨附項目的清單，請參閱 [Azure App Service 定價](http://azure.microsoft.com/pricing/details/app-service/)。


## 標準連接器
下表列出所有由 Microsoft 建立且可與標準連接器搭配使用的可用連接器和 API 應用程式：

名稱 | 說明
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | 使用這個連接器在 Azure 上建立 Hadoop 叢集，並提交不同的 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce。您也可以使用此連接器微調叢集、提交工作，以及等待工作完成。
[Azure 服務匯流排](app-service-logic-connector-azureservicebus.md) | 使用此連接器，您可以從服務匯流排實體 (如佇列和主題) 傳送訊息，並接收來自服務匯流排實體 (例如佇列和訂閱) 的訊息。
[Azure 儲存體 Blob](app-service-logic-connector-azurestorageblob.md) | 連接至 Blob 儲存體，還可以上傳 Blob、取得 Blob、刪除 Blob、列出容器中的 Blob、快照 Blob、複製 Blob，以及使用觸發程序來擷取 Blob。
Azure WebJobs | 連接到 WebJobs。
[Box](app-service-logic-connector-box.md) | 連接至 Box，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。
[Chatter](app-service-logic-connector-chatter.md) | 連接至 Chatter，還可以張貼訊息、搜尋，以及使用觸發程序來擷取新的訊息。
[Dropbox](app-service-logic-connector-dropbox.md) | 連接至 Dropbox，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。
[Facebook](app-service-logic-connector-facebook.md) | 連接至 Facebook，還可以發佈訊息、視訊、相片、優惠等等。您也可以擷取訊息、註解、事件、使用者摘要、使用者不同偏好 (包括書籍、遊戲、影片等等) 的相關資訊。此連接器還包括觸發程序，可用來擷取張貼在網頁上的新訊息。
[檔案](app-service-logic-connector-file.md) | 可讓客戶將檔案傳送或上傳至檔案系統或網路，並從檔案系統或網路接收或下載檔案。使用此連接器，您可以連接至內部部署檔案伺服器，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，也可以使用觸發程序來擷取檔案。
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | 連接至 FTP / FTPS 伺服器，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。
[HTTP](app-service-logic-connector-http.md) | HTTP 接聽程式可開啟當做 HTTP 伺服器的端點，並接聽內送 HTTP 要求。您可以張貼、取得、刪除和放置。HTTP 動作不需要 API 應用程式，Logic Apps 內原本就支援。可以使用 HTTP 或 HTTPS 通訊協定。
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365 連接器可以使用 Office 365 帳戶來傳送和接收電子郵件、管理您的行事曆及管理您的連絡人。您還可以傳送、接收和取得電子郵件、建立和刪除行事曆中的事件，以及建立、更新、取得和刪除您的連絡人。
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive 是項檔案裝載服務。OneDrive 連接器可以連接到您的個人 Microsoft OneDrive，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | 連接至內部部署 Microsoft SharePoint Server 或 SharePoint Online 來管理文件和清單項目。您還可以建立、更新、取得和刪除文件和清單項目。支援不同的驗證方法，例如預設認證、OAuth 2.0、Windows 驗證和表單架構驗證。
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Connects to on-premises SQL Server or an Azure SQL Database.您可以建立、更新、取得和刪除 SQL 資料庫資料表上的項目。
[Microsoft Yammer](app-service-logic-connector-yammer.md) | 連接到 Yammer。包含「張貼訊息」動作和可擷取新訊息的觸發程序。
[POP3](app-service-logic-connector-pop3.md) (郵局通訊協定)| 連接至 POP3 伺服器，且包含觸發程序來擷取帶有附件的電子郵件。
[QuickBooks](app-service-logic-connector-quickbooks.md) | 您可以使用此連接器建立、更新、讀取、刪除和查詢來自 Intuit QuickBooks (如客戶、項目、發票等等) 的不同實體。
[SFTP](app-service-logic-connector-sftp.md) (SSH 檔案傳輸通訊協定)| 連接至 SFTP，還可以上傳檔案、取得檔案、刪除檔案、列出檔案，以及使用觸發程序來擷取檔案。
[SMTP](app-service-logic-connector-smtp.md) (簡易郵件傳送通訊協定) | 連接至 SMTP 伺服器，還可以傳送帶有附件的電子郵件。
[Slack](app-service-logic-connector-slack.md) | 
[Salesforce](app-service-logic-connector-salesforce.md) | Salesforce 連接器可管理 Salesforce 帳戶中的不同實體，例如帳戶、潛在客戶、商機、案例等等。您也可以建立、更新、更新插入、查詢和刪除各種實體。
[SugarCRM](app-service-logic-connector-sugarcrm.md) | 連接至 SugarCRM Online，還可以建立、更新、讀取及刪除不同類型的模組 (如帳戶、連絡人、產品等等)。
[Twilio](app-service-logic-connector-twilio.md) | 連接至 Twilio，還可以傳送訊息、取得訊息、列出訊息、列出使用狀況、取得可用的免付費電話、取得可用的行動電話號碼、取得可用的市內電話號碼、列出來電號碼、取得來電號碼，以及新增來電號碼。
[Twitter](app-service-logic-connector-twitter.md) | 連接至 Twitter，還可以取得使用者時間軸、搜尋推文、取得粉絲、取得好友、搜尋使用者、取得首頁時間軸、取得資訊頁時間軸、張貼推文、張貼推文給使用者，以及傳送直接訊息。Twitter 連接器也可以使用觸發程序，例如依關鍵字取得推文、依使用者處理取得推文，以及依雜湊標記取得推文。
等候 | 您可以使用這個連接器來延遲執行應用程式。您可以將應用程式延遲一段特定期間，或延遲到特定時刻發生為止。


## 高階連接器
下表列出所有由 Microsoft 建立且可在高階連接器中的可用連接器和 API 應用程式：

名稱 | 說明
------------- | -------------
AS2 連接器 | 在企業對企業通訊中，AS2 連接器可以使用 AS2 傳輸通訊協定來接收和傳送訊息。系統會使用數位簽章和加密透過網際網路安全及可靠地傳輸資料。
BizTalk EDIFACT | 在企業對企業通訊中，EDIFACT API 應用程式會使用 EDIFACT 通訊協定來接收和傳送訊息。
BizTalk X12 | 在企業對企業通訊中，X12 API 應用程式會使用 X12 通訊協定來接收和傳送訊息。
BizTalk 交易夥伴管理 | 交易夥伴管理 API 應用程式可定義並保存企業對企業關係，方法是使用夥伴、協議，結構描述及協議中所使用的憑證。使用 AS2、EDIFACT，及 X12 API 應用程式強制執行這些關係。
BizTalk JSON 編碼器 | 協助您的應用程式在 JSON 和 XML 資料之間轉換的編碼器和解碼器。它可以將指定的 JSON 執行個體轉換成 XML，反之亦然。
[BizTalk 規則](app-service-logic-use-biztalk-rules.md) | BizTalk 規則可定義並控制組織的結構、作業和策略。您可以更新商務原則，而無需重新編譯和重新部署相關聯的應用程式。
DB2 連接器 | 連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM DB2 資料庫。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。動作包含資料表選取、插入、更新、刪除和自訂陳述式<br/><br/>此連接器也包含 Microsoft Client for DRDA，可透過 TCP/IP 網路連接至 Informix 伺服器。
Informix | 連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM Informix 資料庫。可將 Web API 和 OData API 作業對應至 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。動作包括資料表選取、插入、更新、刪除和自訂陳述式。<br/><br/>使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。此連接器也包括 Microsoft Client for DRDA，用來跨 TCP/IP 網路連接至 Informix 伺服器。
MQ | 連接至內部部署和執行 Windows 作業系統的 Azure 虛擬機器上的 IBM WebSphere MQ Server 第 8 版。使用內部部署時，可以使用 VPN 或 Azure ExpressRoute。此連接器也包含 Microsoft Client for MQ。<br/><br/>沒有觸發程序。沒有動作。<br/><br/><strong>注意</strong> 目前無法與 Logic Apps 搭配使用。
[Oracle 資料庫](app-service-logic-connector-oracle.md) | Oracle 資料庫可連接至內部部署 Oracle 資料庫，也可以建立、更新、取得和刪除資料庫資料表上的項目。
[SAP](app-service-logic-connector-sap.md) | SAP 連接器可連接至內部部署 SAP 伺服器，並叫用 RFC、BAPI 和 tRFC，以及傳送 IDOC。
[BizTalk 一般檔案編碼器](app-service-logic-flatfile-encoder.md) | 提供一般檔案資料 (例如 excel 和 csv) 和 XML 資料之間的互通性。此 API 應用程式可以將一般檔案執行個體轉換成 XML，反之亦然。
[BizTalk 轉換服務](app-service-logic-transform-xml-documents.md) | BizTalk 轉換 API 應用程式會將資料從一種格式轉換成另一種格式。您也可以上傳現有的對應 (.trfm 檔案)、檢視顯示來源和目標結構描述之間連結的對應圖形表示，並在輸入 XML 內容範例中使用 [測試] 功能。還有不同的內建功能可以使用，包括字串操作、條件式指派、算術運算式、日期時間格式器、迴圈等等。
[BizTalk XML 驗證器](app-service-logic-xml-validator.md) | 驗證器 API 應用程式可根據預先定義的 XML 結構描述驗證 XML 資料。您可以使用現有的結構描述，或根據一般檔案執行個體、JSON 執行個體，或現有的連接器產生結構描述。
[BizTalk XPath 擷取程式](app-service-logic-xpath-extract.md) | 擷取程式 API 應用程式可根據指定的 XPath 來查詢並擷取 XML 內容中的資料。

## 連接器做為觸發程序
有數個連接器提供邏輯應用程式的觸發程序。這些觸發程序分成兩種類型：

1. 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢您的服務，以檢查是否有新資料。有新資料可用時，邏輯應用程式的新執行個體會以該資料做為輸入而執行。為了防止重複取用相同的資料，觸發程序可能會清除已讀取並傳遞至邏輯應用程式的資料。例如，檔案、SQL 和 Azure 儲存體都屬於此類連接器。
2. 推送觸發程序：這些觸發程序會接聽端點上的資料，或接聽發生的事件。然後，觸發邏輯應用程式的新執行個體。例如，HTTP 接聽程式和 Twitter 都屬於此類連接器。

## 連接器做為動作
連接器也可做為邏輯應用程式內的動作。動作很適合在邏輯應用程式中查閱可供執行時使用的資料。例如，在處理訂單時，您可能需要查閱 SQL 資料庫中的資料，以取得客戶的其他資訊。或者，您可能需要寫入、更新或刪除目的地的資料。您可以使用連接器所提供的動作來這樣做。動作對應至 API Apps 中的作業 (由其 Swagger 中繼資料所定義)。

## 建立專屬的連接器和 API 應用程式
[連接器和 API Apps 參考](http://aka.ms/appservicesconnectorreference)<br/> [Azure App Service API 應用程式觸發程序](../app-service-api/app-service-api-dotnet-triggers.md)


## 多個連接器

[什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)<br/> [在 Azure App Service 中使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)

<!---HONumber=62-->