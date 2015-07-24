<properties 
   pageTitle="BizTalk X12 連接器" 
   description="BizTalk X12 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk X12 連接器
Microsoft Azure X12 服務可讓您在企業對企業通訊中，透過 X12 通訊協定接收和傳送訊息。X12 通常也稱為 ASC X12 (Accredited Standards Committee X12)，廣泛用於各產業。

##必要條件
- TPM API 應用程式：在建立 X12 連接器之前，您必須先建立 [BizTalk 交易夥伴管理連接器][1]
- SQL Azure 資料庫：每個 B2B API 應用程式都需要自己的 Azure SQL Database。
- Azure 服務匯流排：這是選用的，且僅用於批次處理。

##使用 BizTalk X12 連接器
若要使用 BizTalk X12 連接器，您必須先建立 BizTalk X12 連接器 API 應用程式的執行個體。這可以在建立邏輯應用程式時同時建立，或從 Azure Marketplace 選取 BizTalk X12 連接器 API 應用程式來建立。

##設定 BizTalk X12 連接器
交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。當兩個夥伴建立關係時，這稱為協議。定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。

建立交易夥伴協議所涉及的步驟在[這裡][2]說明

##在邏輯應用程式設計工具介面中使用 X12 連接器
X12 連接器可用做觸發程序或動作。

###觸發程序
- 啟動 Azure邏輯應用程式流程設計工具
- 從右窗格中按一下 X12 連接器

	![觸發程序設定][3]
- 按一下 ->

	![觸發程序選項][4]
- BizTalk X12 連接器會公開單一觸發程序。選取 [發行批次]

	![發行批次輸入][5]
- 這個觸發程序沒有輸入。按一下 ->

	![已設定發行批次][6]
- 連接器會在輸出中傳回 X12 裝載、協議識別碼，以及訊息是否為批次的資訊。

###動作
- 從右窗格中按一下 X12 連接器

	![動作設定][7]
- 按一下 ->

	![動作清單][8]
- X12 連接器支援許多動作。選取 [編碼]

	![編碼輸入][9]
- 提供動作的輸入，並進行設定

	![已設定編碼][10]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>內容</td>
		<td>字串</td>
		<td>XML 訊息</td>
	</tr>
	<tr>
		<td>協議識別碼</td>
		<td>字串</td>
		<td>協議識別碼</td>
	</tr>
	<tr>
		<td>是批次訊息</td>
		<td>字串</td>
		<td>是批次訊息</td>
	</tr>
</table>

動作傳回物件，其中包含 X12 裝載。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱 [邏輯應用程式是什麼？](app-service-logic-what-are-logic-apps.md)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=62-->