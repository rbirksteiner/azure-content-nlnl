<properties 
   pageTitle="AS2 連接器" 
   description="AS2 連接器" 
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

#AS2 連接器
Microsoft Azure AS2 連接器可以讓您在企業對企業通訊中，透過 AS2 傳輸通訊協定接收和傳送訊息。AS2 代表 Applicability Statement 2。資料是透過網際網路安全、可靠地傳輸。並使用數位簽章和加密來達成安全性。

##必要條件
- TPM API 應用程式：在建立 AS2 連接器之前，您必須先建立 [BizTalk 交易夥伴管理連接器][1]
- SQL Azure 資料庫：每個 B2B API 應用程式都需要自己的 Azure SQL Database。
- Azure Blob 儲存體容器：啟用 AS2 封存時，用於存放訊息屬性。如果您不需要 AS2 訊息封存功能，就不需要儲存體容器。 

##使用 AS2 連接器
若要使用 AS2 連接器，您必須先建立 AS2 連接器 API 應用程式的執行個體。這可以在建立邏輯應用程式時以內嵌方式完成，或是透過從 Azure Marketplace 選取 AS2 連接器 API 應用程式。

##設定 AS2 連接器
交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。當兩個夥伴建立關係時，這稱為協議。定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。

建立交易夥伴協議所涉及的步驟在[這裡][2]說明

##在邏輯應用程式設計工具介面中使用 AS2 連接器
AS2 連接器可用做觸發程序或動作。

###觸發程序
- 啟動 Azure邏輯應用程式流程設計工具
- 從右窗格中按一下 AS2 連接器

	![觸發程序設定][3]
- 按一下 ->

	![觸發程序選項][4]
- AS2 連接器會公開單一觸發程序。選取 [接收和解碼]

	![接收和解碼輸入][5]
- 這個觸發程序沒有輸入。按一下 ->

	![接收和解碼已設定][6]
- 做為輸出的一部分，連接器會傳回 AS2 承載以及 AS2 特定的中繼資料。

###動作
- 從右窗格中按一下 AS2 連接器

	![動作設定][7]
- 按一下 ->

	![動作清單][8]
- AS2 連接器只支援一個動作。選取 [編碼和傳送]

	![編碼和傳送輸入][9]
- 提供動作的輸入，並進行設定

	![編碼和傳送已設定][10]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>Payload</td>
		<td>物件</td>
		<td>Payload</td>
	</tr>
	<tr>
		<td>AS2 來源</td>
		<td>字串</td>
		<td>AS2 來源</td>
	</tr>
	<tr>
		<td>AS2 目標</td>
		<td>字串</td>
		<td>AS2 目標</td>
	</tr>
	<tr>
		<td>交易夥伴 URL</td>
		<td>字串</td>
		<td>交易夥伴 URL</td>
	</tr>
	<tr>
		<td>啟用封存</td>
		<td>布林值</td>
		<td>啟用封存</td>
	</tr>
</table>

此動作會在成功完成時傳回 HTTP 200 回應碼。

## 進一步運用您的連接器
現在已建立連接器，您可以使用邏輯應用程式將它加入商務流程。請參閱 [邏輯應用程式是什麼？](app-service-logic-what-are-logic-apps.md)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視 API 應用程式和連接器](../app-service-api/app-service-api-manage-in-portal.md)。

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->