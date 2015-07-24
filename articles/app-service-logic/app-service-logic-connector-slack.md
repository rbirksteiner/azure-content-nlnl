<properties 
	pageTitle="Slack 連接器"
	description="開始使用 Slack 連接器"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# 在邏輯應用程式中使用 Slack 連接器 #

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。

Slack 連接器可讓您將訊息張貼至 Slack 通道。

## 建立邏輯應用程式的 Slack 連接器 ##
若要使用 Slack 連接器，您必須先建立 Slack 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1.	使用 Azure 入口網站左下方的 "+ 新增" 選項，開啟 Azure Marketplace。
2.	瀏覽至 [Web 和行動] > [Azure Marketplace]，並搜尋 [Slack 連接器]。
3.	如下所示，設定 Slack 連接器：
 
	![][1]
	- **名稱** - 賦予 Slack 連接器的名稱
	- **App Service 方案** - 選取或建立 App Service 方案
	- **定價層** - 選擇連接器的定價層
	- **資源群組** - 選取或建立連接器應該位於的資源群組
	- **訂用帳戶** - 選擇您要在其中建立此連接器的訂用帳戶
	- **位置** - 選擇您要部署連接器的地理位置

4. 按一下 [建立]。將會建立新的 [Slack 連接器]。
5. 建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式以使用 Slack 連接器。

## 在邏輯應用程式中使用 Slack 連接器 ##
建立 API 應用程式後，您現在可以使用 Slack 連接器作為邏輯應用程式的動作。若要這麼做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Slack 連接器的相同資源群組。請依照指示[建立新的邏輯應用程式]。  	
	
2.	在建立的邏輯應用程式中開啟 [觸發程序和動作]，以開啟 Logic Apps Designer 並設定您的流程。
	
3.	Slack 連接器會出現在右手邊組件庫的 [此資源群組中的 API 應用程式] 一節。
 
	![][2]
4.	您可以按一下 [Slack 連接器]，將 Slack 連接器 API 應用程式放入編輯器中。按一下 [授權] 按鈕。提供您的 Microsoft 認證 (如果未自動登入的話)。遵循下列步驟，登入您的 Slack 帳戶。結束前，系統會要求您提供您的連接器權限，以存取您的 Slack 帳戶。請按一下 [授權]
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	您現在可以在流程中使用 Slack 連接器。目前，觸發程序無法使用於 Slack 連接器。可用的動作：張貼訊息
 
	![][7]

6.	我們一起逐步體驗「張貼訊息」。您可以使用這個動作，將訊息張貼至任何 Slack 通道。
 
	![][8]

	如下所示，設定「張貼訊息」動作的輸入屬性：

 - **文字** - 指定要張貼的訊息文字
 - **通道名稱** - 指定此訊息要上傳至的 Slack 通道。如果未提供此項目，則訊息會張於 #general

 	**進階屬性** - **Bot 使用者名稱** - 要用於此訊息的 Bot 名稱。如果未指定此屬性，訊息將會張貼為 "Bot"。- **圖示 URL** - 要作為此訊息圖示的影像 URL - **圖示 Emoji** - 要作為此訊息圖示的 Emoji。覆寫圖示 URL
 

7. 若要使用邏輯應用程式外部的連接器，可以運用連接器所公開的 REST API。您可以使用 [瀏覽] -> [API 應用程式]-> [Slack 連接器]，檢視此 API 定義 。現在按一下 [摘要] 區段下的 [API 定義] 鏡頭，以檢視此連接器所公開的所有 API。

	![][9]

9. 在 [Slack API 定義]也可以找到 API 的詳細資訊。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!-- Links -->
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[Slack API 定義]: https://msdn.microsoft.com/zh-tw/library/dn708020.aspx

<!----HONumber=62-->