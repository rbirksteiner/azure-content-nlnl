<properties 
   pageTitle="Azure 服務匯流排連接器 API 應用程式" 
   description="如何使用 AzureServiceBusConnector" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# 在邏輯應用程式中使用 Azure 服務匯流排連接器 #

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。

Azure 服務匯流排連接器讓您透過佇列/主題/訂閱接收和傳送訊息。

## 建立邏輯應用程式的 Azure 服務匯流排連接器 ##
若要使用 Azure 服務匯流排連接器，您必須先建立 Azure 服務匯流排連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1.	使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「Azure 服務匯流排連接器」。
3.	設定 Azure 服務匯流排連接器，如下所示：
 
	![][1] - **位置** - 選擇您要部署連接器的地理位置 - **訂用帳戶** - 選擇您要在其中建立此連接器的訂用帳戶 - **資源群組** - 選取或建立連接器所在的資源群組 - **Web 主控方案** - 選取或建立 Web 主控方案 - **定價層** - 選擇用於連接器的定價層 - **名稱** - 提供 Azure 服務匯流排連接器的名稱 - **封裝設定** - **連接字串** - Azure 服務匯流排的連接字串。範例：Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key] - **實體名稱** - 佇列或主題的名稱 - **訂用帳戶名稱** - 訂用帳戶接收訊息之來源的名稱。

4.	按一下 [建立]。將建立新的 Azure 服務匯流排連接器。
5.	建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 Azure 服務匯流排連接器。 

## 在邏輯應用程式中使用 Azure 服務匯流排連接器 ##
建立 API 應用程式之後，您現在可以使用 Azure 服務匯流排連接器做為邏輯應用程式的觸發程序/動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Azure 服務匯流排連接器的相同資源群組。
 
	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。 
 
	![][3]
3.	Azure 服務匯流排連接器就會出現在右側資源庫中的 [此資源群組中的 API 應用程式] 區段。
 
	![][4]
4. 您可以在 [Azure 服務匯流排連接器] 上按一下來將 Azure 服務匯流排連接器 API 應用程式置入編輯器。
 
5.	您現在便可以在流程中使用 Azure 服務匯流排連接器。您現在可以在流程的其他動作中使用從 Azure 服務匯流排觸發程序 ([可用訊息]) 擷取的訊息。
 
	![][5] ![][6] 
6.	同樣地，您可以使用 Azure 服務匯流排動作 [傳送訊息] 來傳送訊息。

	![][7] ![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=62-->