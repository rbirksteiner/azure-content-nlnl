<properties 
   pageTitle="Twitter 連接器 API 應用程式" 
   description="如何使用 TwitterConnector" 
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
   ms.author="adgoda"/>


# 在邏輯應用程式中使用 Twitter 連接器 #

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。

Twitter 連接器可讓您張貼推文，並從您 Twitter 帳戶的時間表、好友和粉絲中取得推文。

- Twitter 連接器觸發程序將會擷取與給定關鍵字相關聯的新推文。擷取到新推文時，它會觸發流程的新執行個體，並將要求中所收到的資料傳遞到處理的流程中。 
- Twitter 連接器動作可讓您「推文」、「搜尋推文」、「回推」、「取得使用者時間表」等等。這些動作會取得回應，並可供流程中的動作取用。

## 建立邏輯應用程式的 Twitter 連接器 ##
若要使用 Twitter 連接器，您必須先建立 Twitter 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1. 使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
1. 瀏覽至 [API 應用程式]，並搜尋「Twitter 連接器」。
1. 設定 Twitter 連接器，如下所示：

	![][1]
4.	按一下 [確定] 以建立。
5.	建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 Twitter 連接器。 
	- 您也可以在邏輯應用程式中建立 Twitter 連接器 API 應用程式執行個體。 
	- 開啟邏輯應用程式編輯器，然後按一下右側組件庫中提供的 Twitter 連接器
	- 這會在與建立邏輯應用程式相同的資源群組中建立 Twitter 連接器 API 應用程式執行個體。


## 在邏輯應用程式中使用 Twitter 連接器 ##
建立 API 應用程式之後，您現在可以使用 Twitter 連接器做為邏輯應用程式的觸發程序/動作。若要這麼做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Twitter 連接器的相同資源群組。
 	
	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。 
 	
	![][3]
3.	Twitter 連接器就會出現在右側組件庫中的 [最近使用的] 區段。選取該項目。
 
	![][4]
4.	您可以將 Twitter 連接器 API 應用程式放入 [編輯器] 中，方法是按一下右側組件庫中 [最近使用的] 底下的 [Twitter 連接器]。按一下 [授權] 按鈕。提供您的 Twitter 認證。按一下 [授權應用程式]
 
	![][5]
6.	您現在便可以在流程中使用 Twitter 連接器。您現在可以在流程的其他動作中使用從 Twitter 觸發程序所擷取的推文。
 
	![][6]
7.	您可以採用類似方式在流程中使用 Twitter 動作。選取 Twitter 動作，並設定該個別動作的輸入。

	![][7]
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png
 

<!----HONumber=62-->