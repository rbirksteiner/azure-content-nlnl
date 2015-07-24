<properties 
	pageTitle="如何使用服務匯流排主題 (Node.js) - Azure" 
	description="了解如何從 Node.js 應用程式，在 Azure 中使用服務匯流排主題和訂用帳戶。" 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="mwasson"/>






# 如何使用服務匯流排主題和訂用帳戶

本指南說明如何從 Node.js 應用程式使用服務匯流排主題和訂用帳戶。所涵蓋的案例包括**建立主題和訂用帳戶、建立訂用帳戶篩選器、傳送訊息**至主題、**接收訂用帳戶的訊息**，及**刪除主題和訂用帳戶**。如需主題和訂用帳戶的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## 建立 Node.js 應用程式

建立空白的 Node.js 應用程式。如需建立 Node.js 應用程式的相關指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站]、[Node.js 雲端服務][Node.js Cloud Service] (使用 Windows PowerShell) 或使用 WebMatrix 的網站。

## 設定應用程式以使用服務匯流排

若要使用服務匯流排，請下載 Node.js azure 封裝。此封裝含有一組能與服務匯流排 REST 服務通訊的便利程式庫。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、[終端機] (Mac) 或 **Bash** (Unix)，瀏覽到您建立範例應用程式的資料夾。

2.  在命令視窗中輸入 **npm install azure**，這應該會導致下列輸出：

         azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  您可以手動執行 **ls** 命令，確認已建立 **node_modules** 資料夾。在該資料夾內找出 **azure** 套件，其中含有存取服務匯流排主題所需的程式庫。

### 匯入模組

使用記事本或其他文字編輯器將以下內容新增至應用程式 **server.js** 檔案的頂端：

    var azure = require('azure');

### 設定服務匯流排連接

Azure 模組會讀取環境變數 AZURE_SERVICEBUS_NAMESPACE 和 AZURE_SERVICEBUS_ACCESS_KEY，找出連接 Azure 服務匯流排所需的資訊。如果您未設定這些環境變數，必須在呼叫 **createServiceBusService** 時指定帳戶資訊。

如需在 Azure 雲端服務組態檔中設定環境變數的範例，請參閱[使用儲存體的 Node.js 雲端服務]。

如需在 Azure 網站管理入口網站中設定環境變數的範例，請參閱[使用儲存體的 Node.js Web 應用程式]。

## 如何建立主題

**ServiceBusService** 物件可讓您操作主題。下列程式碼將建立 **ServiceBusService** 物件。請將程式碼新增至 **server.js** 檔案的頂端附近，放置在匯入 azure 模型的陳述式後方：

    var serviceBusService = azure.createServiceBusService();

藉由在 **ServiceBusService** 物件上呼叫 **createTopicIfNotExists**，系統將傳回指定的主題 (若有的話) 或建立具有指定名稱的新主題。以下程式碼使用 **createTopicIfNotExists** 來建立或連接名為 'MyTopic' 的主題：

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** 亦支援其他選項，如此可讓您覆寫訊息存留時間或主題大小上限等預設主題設定。以下範例示範將主題大小上限設定為 5GB，將存留時間設定為 1 分鐘：

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

### 篩選器

您可以將選用的篩選作業套用至使用 **ServiceBusService** 執行的作業。篩選作業可包括記錄、自動重試等等。篩選器是以簽章實作方法的物件：

		function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

		function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。下列程式碼將建立使用 **ExponentialRetryPolicyFilter** 的 **ServiceBusService** 物件：

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## 如何建立訂閱

**ServiceBusService** 物件也能用來建立主題訂閱。訂閱是具名的，它們能擁有選用的篩選器，以限制傳遞至訂閱之虛擬佇列的訊息集合。

> [AZURE.NOTE]訂閱是持續性的，它們會持續存在，直到本身或相關的主題遭到刪除為止。如果應用程式含有建立訂閱的邏輯，它應該會先使用 **getSubscription** 方法檢查訂閱是否存在。

### 使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll** 篩選器是預設篩選器，如果在建立新的訂閱時沒有指定篩選器，便會使用此篩選器。使用 **MatchAll** 篩選器時，所有發佈至主題的訊息都會被置於訂閱的虛擬佇列中。下列範例將建立名為 'AllMessages' 的訂閱，並使用預設的 **MatchAll** 篩選器。

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### 使用篩選器建立訂閱

您也可以建立篩選器，讓您界定傳送至主題的哪些訊息應出現在特定主題訂閱中。

訂閱所支援的最具彈性篩選器類型是實作 SQL92 子集的 **SqlFilter**。SQL 篩選器會對發佈至主題之訊息的屬性運作。如需可與 SQL 篩選器搭配使用的運算式詳細資料，請檢閱 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 語法。

您可以使用 **ServiceBusService** 物件的 **createRule** 方法將篩選器新增至訂閱。此方法可讓您將篩選器新增至現有的訂閱中。

> [AZURE.NOTE]

> 由於預設篩選器會自動套用至所有新訂用帳戶，因此您必須先移除預設篩選器，否則 <strong>MatchAll</strong> 會覆寫您指定的其他任何篩選器。您可以使用 <strong>ServiceBusService</strong> 物件的 <strong>deleteRule</strong> 方法移除預設規則。

以下範例將建立名為 'HighMessages' 並帶有只選取自訂 **messagenumber** 屬性大於 3 的訊息之 **SqlFilter** 的訂閱：

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

同樣地，下列範例將建立名為 'LowMessages' 並帶有只選取 **messagenumber** 屬性小於或等於 3 的訊息之 **SqlFilter** 的訂閱：

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

當訊息傳送到 "TestTopic" 時，一律會將該訊息傳遞到已訂閱 "AllMessages" 主題訂閱的接收者，並選擇性地將它傳遞到已訂閱 "HighMessages" 和 "LowMessages" 主題訂閱的接收者 (視訊息內容而定)。

## 如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式必須使用 **ServiceBusService** 物件的 **sendTopicMessage** 方法。傳送至服務匯流排主題的訊息是 **BrokeredMessage** 物件。**BrokeredMessage** 物件具有一組標準屬性 (例如 **Label** 和 **TimeToLive**)、一個用來保存自訂應用程式特定屬性的目錄，以及一堆字串資料。應用程式能將字串值傳遞至 **sendTopicMessage** 以設定訊息本文，系統會將預設值填入任何需要的標準屬性中。

下列範例示範如何將五個測試訊息傳送至 'MyTopic'。請注意迴圈反覆運算上每個訊息的 **messagenumber** 屬性值的變化 (這可判斷接收訊息的訂閱為何)：

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。此主題大小會在建立時定義，上限是 5 GB。

## 如何自訂閱接收訊息

您可以使用 **ServiceBusService** 物件的 **receiveSubscriptionMessage** 方法接收來自訂閱的訊息。依預設，當您讀取訊息後，系統便會從訂閱刪除訊息，不過您可以將選用參數 **isPeekLock** 設定為 **true**，藉此讀取 (查看) 並鎖定訊息，避免系統從訂閱將訊息刪除。

隨著接收作業讀取及刪除訊息之預設行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果您將 **isPeekLock** 參數設定為 **true**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可透過呼叫 **deleteMessage** 方法和以參數形式提供要刪除的訊息，完成接收程序的第二個階段。**deleteMessage** 方法會將訊息標示為已取用，並將其自訂閱移除。

以下範例將示範如何使用 **receiveSubscriptionMessage** 來接收與處理訊息。此範例會先接收來自 'LowMessages' 訂閱的訊息並加以刪除，然後再使用設定為 true 的 **isPeekLock** 接收來自 'HighMessages' 訂閱的訊息。接著再使用 **deleteMessage** 刪除訊息：

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，它可以呼叫 **ServiceBusService** 物件上的 **unlockMessage** 方法。這將導致服務匯流排將訂閱中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在訂閱內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **deleteMessage** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 如何刪除主題和訂閱

主題和訂閱是持續性的，您必須透過 Azure 管理入口網站或程式設計方法明確地加以刪除。下列範例示範如何刪除名為 'MyTopic' 的主題：

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

刪除主題也將會刪除對主題註冊的任何訂閱。您也可以個別刪除訂閱。下列程式碼將示範如何將名為 'HighMessages' 的訂閱從 'MyTopic' 主題中刪除：

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂用帳戶][]。
-   [SqlFilter][] 的 API 參考資料。
-   造訪 GitHub 上的 [Azure SDK for Node] (英文) 儲存機制。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Azure Management Portal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [佇列、主題和訂用帳戶]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [建立 Node.js 應用程式並將其部署到 Azure 網站]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [使用儲存體的 Node.js 雲端服務]: /develop/nodejs/tutorials/web-app-with-storage/
  [使用儲存體的 Node.js Web 應用程式]: /develop/nodejs/tutorials/web-site-with-storage/
 

<!---HONumber=July15_HO2-->