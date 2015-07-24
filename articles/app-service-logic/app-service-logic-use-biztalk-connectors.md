<properties
   pageTitle="使用連接器"
   description="使用連接器"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#連接器#
連接器是 API 應用程式，可讓您連接到執行於雲端或內部部署的資料和服務。連接器可讓您透過各種可在邏輯應用程式和其他應用程式中輕鬆存取的內建觸發程序和動作，來取得所需資料。

Azure 應用程式服務提供許多現成連接器：

##標準連接器##
* [Azure 服務匯流排連接器]
* [Azure 儲存體 Blob 連接器]
* Azure Webjobs 連接器
* [Box 連接器]
* [Chatter 連接器]
* [DropBox 連接器]
* [Facebook 連接器]
* [檔案連接器]
* [FTP 連接器]
* [HDInsight 連接器]
* [HTTP 連接器]
* [Office 365 連接器]
* [OneDrive 連接器]
* [POP3 連接器]
* [QuickBooks 連接器]
* [Salesforce 連接器]
* [SFTP 連接器]
* [Sharepoint 連接器]
* [Slack 連接器]
* [SMTP 連接器]
* [SQL 連接器]
* [SugarCRM 連接器]
* [Twilio 連接器]
* [Twitter 連接器]
* Wait 連接器
* [Yammer 連接器]

##高階連接器和 API 應用程式##
* [AS2 連接器]
* [BizTalk EDIFACT]
* [BizTalk 一般檔案編碼器]
* BizTalk 轉換服務
* [BizTalk 規則]
* [BizTalk X12]
* [BizTalk XPath 擷取程式]
* [BizTalk XML 驗證器]
* DB2 連接器
* Informix 連接器
* [Oracle 連接器]
* MQ 連接器
* [SAP 連接器]

如需其他詳細資訊，請參閱連接器 API 參考，網址為 [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference)。

##連接器和邏輯應用程式##
邏輯應用程式由觸發程序和動作所組成。有些連接器可以做為觸發程序，根據事件或某些資料的可用性進行工作流程的執行個體化。連接器也可以做為動作，來讀取和寫入資料，或執行連接器所支援的其他動作。

###連接器做為觸發程序###
有數個連接器提供邏輯應用程式的觸發程序。這些觸發程序分成兩種類型：

1. 輪詢觸發程序：這些觸發程序會以指定的頻率輪詢相關服務，以檢查是否有新資料。有新資料可用時，邏輯應用程式的新執行個體將會以該項資料做為輸入而執行。觸發程序可能會執行其他工作 (例如清除已讀取並傳遞到邏輯應用程式的資料)，以防止相同的資料被取用多次。例如，檔案、SQL 和 Azure 儲存體都屬於此類連接器。
2. 推送觸發程序：這些觸發程序會接聽端點上的資料或發生的事件，並觸發新的邏輯應用程式執行個體。例如，HTTP 接聽程式和 Twitter 都屬於此類連接器。

###連接器做為動作###
連接器也可做為邏輯應用程式中的動作。這些動作有助於查閱邏輯應用程式中要在執行中使用的資料，例如，您可能需要在處理訂單時查閱來自 SQL DB 的資料，以取得客戶的相關資訊。或者，您可能需要寫入、更新或刪除目的地中的資料，為此，您可以使用連接器所提供的動作。動作會對應至 API 應用程式中的作業 (如其 Swagger 中繼資料所定義一般)。


<!-- Links -->

[Box 連接器]: app-service-logic-connector-box.md
[Facebook 連接器]: app-service-logic-connector-facebook.md
[Salesforce 連接器]: app-service-logic-connector-salesforce.md
[Twitter 連接器]: app-service-logic-connector-twitter.md
[SAP 連接器]: app-service-logic-connector-sap.md
[FTP 連接器]: app-service-logic-connector-ftp.md
[HTTP 連接器]: app-service-logic-connector-http.md
[Azure 儲存體 Blob 連接器]: app-service-logic-connector-azurestorageblob.md
[Office 365 連接器]: app-service-logic-connector-office365.md
[Sharepoint 連接器]: app-service-logic-connector-sharepoint.md
[SugarCRM 連接器]: app-service-logic-connector-sugarcrm.md
[QuickBooks 連接器]: app-service-logic-connector-quickbooks.md
[Yammer 連接器]: app-service-logic-connector-yammer.md
[Twilio 連接器]: app-service-logic-connector-twilio.md
[SMTP 連接器]: app-service-logic-connector-smtp.md
[SFTP 連接器]: app-service-logic-connector-sftp.md
[POP3 連接器]: app-service-logic-connector-pop3.md
[DropBox 連接器]: app-service-logic-connector-dropbox.md
[Chatter 連接器]: app-service-logic-connector-chatter.md
[HDInsight 連接器]: app-service-logic-connector-hdinsight.md
[Azure 服務匯流排連接器]: app-service-logic-connector-azureservicebus.md
[Oracle 連接器]: app-service-logic-connector-oracle.md
[SQL 連接器]: app-service-logic-connector-sql.md
[OneDrive 連接器]: app-service-logic-connector-onedrive.md
[檔案連接器]: app-service-logic-connector-file.md
[Slack 連接器]: app-service-logic-connector-slack.md
[BizTalk 一般檔案編碼器]: app-service-logic-flatfile-encoder.md
[BizTalk XPath 擷取程式]: app-service-logic-xpath-extract.md
[BizTalk XML 驗證器]: app-service-logic-xml-validator.md
[BizTalk 規則]: app-service-logic-use-biztalk-rules.md
[AS2 連接器]: app-service-logic-connector-as2.md
[BizTalk EDIFACT]: app-service-logic-connector-edifact
[BizTalk X12]: app-service-logic-connector-x12.md

<!---HONumber=62-->