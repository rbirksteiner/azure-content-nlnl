<properties
	pageTitle="如何使用服務匯流排主題 (Java) - Azure"
	description="了解如何在 Azure 使用服務匯流排主題及訂用帳戶。程式碼範例專為 Java 應用程式撰寫。"
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="sethm"/>

# 如何使用服務匯流排主題/訂用帳戶

本指南說明如何使用服務匯流排主題和訂用帳戶。相關範例是以 Java 撰寫並使用 [Azure SDK for Java][]。所涵蓋的案例包括**建立主題和訂用帳戶**、**建立訂用帳戶篩選器**、**傳送訊息至主題**、**接收訂用帳戶的訊息**，及**刪除主題和訂用帳戶**。

[AZURE.INCLUDE [service-bus-java-how-to-create-topic](../../includes/service-bus-java-how-to-create-topic.md)]

## 設定應用程式以使用服務匯流排
先確定已安裝 [Azure SDK for Java][] 再建置此範例。如果使用 Eclipse，您可以安裝包含 Azure SDK for Java 的 [Azure Toolkit for Eclipse][]。然後您可以將 **Microsoft Azure Libraries for Java** 新增至您的專案：![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

在 Java 檔案頂端新增下列 import 陳述式：

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    import com.microsoft.windowsazure.core.*;
    import javax.xml.datatype.*;

將 Azure Libraries for Java 新增至您的建置路徑，並將其納入專案部署組件中。

## 如何建立主題

服務匯流排主題的管理作業可透過 **ServiceBusContract** 類別來執行。**ServiceBusContract** 物件可使用封裝 SAS 權限權加以管理的適當組態來建構，而對於 Azure，**ServiceBusContract** 類別是唯一的通訊點。

**ServiceBusService** 類別會提供建立、列舉及刪除主題的方法。下列範例將說明如何使用 **ServiceBusService** 物件建立名為 "TestTopic"、且命名空間的名稱為 "HowToSample" 的主題：

    Configuration config =
    	ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

**TopicInfo** 有相關方法可讓您調整主題的屬性 (例如，針對要在傳送至主題的訊息所套用的 [存留時間] 設定預設值)。下列範例將說明如何建立名為 "TestTopic"、且大小上限為 5GB 的主題：

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

請留意到，您可以在 **ServiceBusContract** 物件上使用 **listTopics** 方法，來檢查服務命名空間內是否已有指定名稱的主題存在。

## 如何建立訂用帳戶

**ServiceBusService** 類別也能用來建立主題訂用帳戶。為訂用帳戶命名，且能包含選擇性篩選器，以用來限制傳遞至訂用帳戶的虛擬佇列的訊息集合。

### 使用預設 (MatchAll) 篩選器建立訂用帳戶

**MatchAll** 篩選器是預設篩選器，如果在建立新的訂用帳戶時沒有指定篩選器，便會使用此篩選器。使用 **MatchAll** 篩選器時，所有發佈至主題的訊息都會被置於訂用帳戶的虛擬佇列中。下列範例將建立名為 "AllMessages" 的訂用帳戶，並使用預設的 **MatchAll** 篩選器。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### 使用篩選器建立訂用帳戶

您也可以設定篩選器，讓您界定傳送至主題的哪些訊息應出現在特定主題訂用帳戶中。

訂用帳戶所支援的最具彈性篩選器類型是實作 SQL92 子集的 **SqlFilter**。SQL 篩選器會對發佈至主題之訊息的屬性運作。如需可與 SQL 篩選器搭配使用之運算式的詳細資訊，請檢閱 SqlFilter.SqlExpression 語法。

以下範例將建立名為 "HighMessages" 並帶有只選取自訂 **MessageNumber** 屬性大於 3 的訊息之 **SqlFilter** 的訂用帳戶：

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result =
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult =
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

同樣地，下列範例將建立名為 "LowMessages"、且其 SqlFilter 只選取 MessageNumber 屬性小於或等於 3 之訊息的訂用帳戶：

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result =
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult =
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


現在，當訊息傳送到 "TestTopic" 時，一律會將該訊息傳遞到已訂閱 "AllMessages" 主題訂用帳戶的接收者，並選擇性地將它傳遞到已訂閱 "HighMessages" 和 "LowMessages" 主題訂用帳戶的接收者 (視訊息內容而定)。

## 如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式會取得 **ServiceBusContract** 物件。下列程式碼將說明如何針對我們先前在 "HowToSample" 服務命名空間內建立的 "TestTopic" 主題傳送訊息：

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

傳送至服務匯流排主題的訊息是 **BrokeredMessage** 類別的執行個體。**BrokeredMessage** 物件具有一組標準方法 (例如 **setLabel** 和 **TimeToLive**)、一個用來保存自訂應用程式特定屬性的目錄，以及一組任意的應用程式資料。應用程式可設定訊息內文，方法是將任何可序列化物件傳遞到 **BrokeredMessage** 的建構函式，接著系統便會使用適當的 **DataContractSerializer** 來序列化物件。或者，也可以提供 **java.io.InputStream**。

下列範例將說明如何傳送五則測試訊息至我們在上述程式碼片段中取得的 "TestTopic" **MessageSender**。請注意迴圈反覆運算上每個訊息的 **MessageNumber** 屬性值的變化 (這可判斷接收訊息的訂用帳戶為何)：

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。此主題大小會在建立時定義，上限是 5 GB。

## 如何自訂用帳戶接收訊息

從訂用帳戶接收訊息的主要方式，是使用 **ServiceBusContract** 物件。接收的訊息可在兩種不同的模式下運作：**ReceiveAndDelete** 和 **PeekLock**。

使用 **ReceiveAndDelete** 模式時，接收是一次性作業；也就是說，當服務匯流排收到訊息的讀取要求時，它會將此訊息標示為已使用，並將它傳回應用程式。**ReceiveAndDelete** 模式是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它會在已接收的訊息上呼叫 **Delete**，以完成接收程序的第二個階段。當服務匯流排發現 **Delete** 呼叫時，它會將訊息標示為已取用，並將它從主題中移除。

下列範例將說明如何使用 **PeekLock** 模式 (這不是預設模式) 來接收與處理訊息。下列範例將執行迴圈，並處理 "HighMessages" 訂用帳戶中的訊息，然後在沒有任何訊息時結束 (您也可以將其設為等待新訊息)。

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

		while(true)  {
		    ReceiveSubscriptionMessageResult  resultSubMsg =
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());
			    // Display the topic message.
			    System.out.print("From topic: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " +
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
		    }  
		    else  
		    {
		        System.out.println("Finishing up - no more messages.");
		        break;
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，它可以在已接收的訊息上呼叫 **unlockMessage** 方法 (而不是 **deleteMessage** 方法)。這將導致服務匯流排將主題中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在主題內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未發出 **deleteMessage** 要求時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **getMessageId** 方法來達到此目的，該方法將在各個傳遞嘗試中保持不變。

## 如何刪除主題和訂用帳戶

刪除主題和訂用帳戶的主要方式，是使用 **ServiceBusContract** 物件。刪除主題也將會刪除對主題註冊的任何訂用帳戶。您也可以個別刪除訂用帳戶。

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## 後續步驟

現在您已了解服務匯流排佇列的基本概念，請參閱 MSDN 主題[服務匯流排佇列、主題和訂用帳戶][]，以取得詳細資訊。

  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit for Eclipse]: https://msdn.microsoft.com/zh-tw/library/azure/hh694271.aspx
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Configure Your Application to Use Service Bus]: #bkmk_ConfigYourApp
  [How to: Create a Topic]: #bkmk_HowToCreateTopic
  [How to: Create Subscriptions]: #bkmk_HowToCreateSubscrip
  [How to: Send Messages to a Topic]: #bkmk_HowToSendMsgs
  [How to: Receive Messages from a Subscription]: #bkmk_HowToReceiveMsgs
  [How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrash
  [How to: Delete Topics and Subscriptions]: #bkmk_HowToDeleteTopics
  [Next Steps]: #bkmk_NextSteps
  [Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure Management Portal]: http://manage.windowsazure.com/
  [Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Create a New Namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [服務匯流排佇列、主題和訂用帳戶]: http://msdn.microsoft.com/library/hh367516.aspx
 

<!---HONumber=July15_HO2-->