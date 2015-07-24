<properties 
   pageTitle="使用 VETR 建立 EAI 邏輯應用程式" 
   description="本主題涵蓋 BizTalk XML 服務的驗證、編碼和轉換功能。" 
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
   ms.date="06/24/2015"
   ms.author="rajram"/>


# 使用 VETR 建立 EAI 邏輯應用程式

大部分的企業應用程式整合 (EAI) 案例可在來源與目的地之間傳達資料。此種案例通常會有一組常見的需求：

- 確定來自不同系統的資料都會使用正確的格式
- 在內送資料上執行 [查閱] 以便制定決策
- 將資料從一種格式轉換到另一種格式 (例如，從 CRM 系統的資料格式轉換成 ERP 系統的資料格式)
- 將資料路由到所需的應用程式或系統

在本文中，我們會探討常見的整合模式：「單向訊息傳達」或 VETR (驗證、擴充、轉換、路由)。 VETR 模式會在來源實體與目的地實體之間傳達資料。通常來源和目的地為資料來源。

想像一個接受訂單的網站。使用者會使用 HTTP 將訂單張貼至系統。系統會在幕後驗證內送資料的正確性、將其正規化，並將它保存在服務匯流排佇列中以進行後續處理。系統會將訂單從佇列中移除，預期它會採用特定的格式。因此：端對端流程會是：

HTTP -> 驗證 -> 轉換 -> 服務匯流排

![基本 VETR 流程][1]

下列 BizTalk API 應用程式可協助建置這種模式：

*HTTP 觸發程序* - 觸發訊息事件的來源 *驗證* - 驗證傳入的資料是否正確 *轉換* - 將資料從傳入格式轉換為下游系統所需的格式 *服務匯流排連接器* - 傳送資料的目的地實體


## 建構基本的 VETR 模式
### 基本概念

在 Azure 管理入口網站中，按一下畫面左下角的 [**+新增**]按鈕，然後按一下 [邏輯應用程式]。選擇名稱、位置、訂閱、資源群組以及運作位置。資源群組可做為應用程式的容器使用，且應用程式的所有資源都屬於相同的資源群組。

接下來，讓我們新增觸發程序和動作。


## 新增 HTTP 觸發程序

1. 從組件庫中選取 [**HTTP 接聽程式**] 以建立新的接聽程式。稱它為 **HTTP1**。
2. 將 [**是否自動傳送回應？**] 設定保留為 false。透過將 _HTTP 方法_設定為 _POST_，並將_相對 URL_ 設定為 _/OneWayPipeline_，來設定觸發程序動作。

![HTTP 觸發程序][2]


## 加入驗證動作

現在，讓我們輸入每當引發觸發程序時 (亦即，每當 HTTP 端點上收到呼叫時) 所要執行的動作。

1. 從組件庫中新增 **BizTalk XML 驗證器**，並將它命名為 _(Validate1)_ 以建立執行個體。
2. 設定 XSD 結構描述來驗證內送的 XML 訊息。選取 _Validate_ 動作，然後選取 _triggers(‘httplistener’).outputs.Content_ 做為 _inputXml_ 參數的值。

目前，驗證動作是 HTTP 接聽程式之後的第一個動作。讓我們以類似的方式加入其餘動作。

![BizTalk XML 驗證器][3]


## 新增轉換動作
我們將設定可標準化內送資料的轉換。

1. 從組件庫新增**轉換**。 
2. 若要設定一個轉換，來轉換傳入的 XML 訊息，請選取 [**轉換**] 動作，做為呼叫此 API 時所要執行的動作，然後選取 ```triggers(‘httplistener’).outputs.Content``` 做為 _inputXml_ 的值。因為內送資料符合所有已設定的轉換，因此對應會是選擇性參數，而且僅適用於符合結構描述的對應。
3. 最後，只有當驗證成功時才會執行轉換。若要設定這種情況，請選取右上方的齒輪圖示以「_新增要符合的條件_」 。將條件設為 ```equals(actions('xmlvalidator').status,'Succeeded')```


![BizTalk 轉換][4]


## 新增服務匯流排連接器
接下來，我們將新增可寫入資料的目的地 (服務匯流排佇列)。

1. 從組件庫新增 [**服務匯流排連接器**]。將 _Name_ 設為 _Servicebus1_、將 _Connection String_ 設為服務匯流排執行個體的連接字串、將 _Entity Name_ 設為 _Queue_，然後略過 _Subscription name_。 
2. 選取 [**傳送訊息**] 動作，並將動作的 _Message_ 欄位設為 _actions('transformservice').outputs.OutputXml_

![服務匯流排][5]


## 傳送 HTTP 回應
完成管線處理之後，我們會使用下列步驟傳回成功和失敗的 HTTP 回應：

1. 從組件庫新增 [**HTTP 接聽程式**]，然後選取 [**傳送 HTTP 回應**] 動作。
2. 將 _Response Content_ 設為 “Pipeline processing completed”、將 _Response Status Code_ 設為 “200” 以表示 HTTP 200 確定，然後將條件設為 ```@equals(actions('servicebusconnector').status,'Succeeded')``` 運算式
	
您也可以重複上述步驟來傳送失敗的 HTTP 回應。將條件變更為 ```@not(equals(actions('servicebusconnector').status,'Succeeded')).```


## 完成
每當有人傳送訊息至 HTTP 端點時，它便會觸發應用程式，並執行我們剛剛建立的動作。若要管理任何您所建立的此類邏輯應用程式，請在 Azure 管理入口網站中按一下 [**瀏覽**]，再按一下 [**邏輯應用程式**]。按一下您的應用程式，以查看更多資訊。


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

 

<!---HONumber=62-->