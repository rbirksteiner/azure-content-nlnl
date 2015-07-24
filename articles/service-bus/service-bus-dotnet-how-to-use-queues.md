<properties
    pageTitle="如何使用服務匯流排佇列 (.NET) - Azure"
    description="了解如何使用 Azure 中的服務匯流排佇列。程式碼範例是以 C# 撰寫並使用 .NET API。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="sethm"/>

# 如何使用服務匯流排佇列

本指南將說明如何使用服務匯流排佇列。這些範例均以 C# 撰寫並使用 .NET API。本文說明的案例包括**建立佇列**和**傳送並接收訊息**。如需佇列的詳細資訊，請參閱[後續步驟](#Next-steps)一節。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## 設定應用程式以使用服務匯流排

當您建立使用服務匯流排的應用程式時，您必須新增服務匯流排組件的參照，並包含對應的命名空間。

## 新增服務匯流排 NuGet 封裝

要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 **NuGet** 封裝。NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [喜好設定]，然後按一下 [Manage NuGet Packages]。
2.  搜尋「服務匯流排」並選取 [**Microsoft Azure 服務匯流排**] 項目。按一下 [安裝] 完成安裝作業，然後關閉此對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

## 如何設定服務匯流排連接字串

服務匯流排使用連接字串來儲存端點和認證。您可以將連接字串置於設定檔中，而非硬式編碼它：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (***.csdef** 和 ***.cscfg** 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 設定系統 (例如 **Web.config** 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。例如，在您的服務定義 (***.csdef**) 檔案中新增 `Setting` 標籤，如下所示：

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

接著您可以在服務組態 (***.cscfg**) 檔案中指定值：

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

使用從管理入口網站擷取的共用存取簽章 (SAS) 金鑰名稱和金鑰值，如上一節所述。

### 在使用網站或虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 設定系統 (例如，**Web.config**)。您可以使用 `<appSettings>` 元素儲存連接字串：

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

使用從管理入口網站擷取的 SAS 名稱和金鑰值，如上一節所述。

## 如何建立佇列

您可以使用 [`NamespaceManager` 類別](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)來執行服務匯流排佇列的管理作業。此類別提供建立、列舉及刪除佇列的方法。

本範例會使用 Azure `CloudConfigurationManager` 類別，以及包含服務匯流排服務命名空間基底位址的連接字串和具備管理此連接字串權限的適當 SAS 認證，來建構 `NamespaceManager` 物件。此連接字串的格式是

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

例如，基於上一節中的組態設定：

    // Create the queue if it does not exist already
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

[`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) 方法的超載可讓您調整佇列的屬性 (例如，針對要在傳送至佇列的訊息套用的 [存留時間] 設定預設值)。您可以使用 [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) 類別來套用這些設定。下列範例將示範如何使用大小上限為 5 GB 和預設訊息存留時間為 1 分鐘的設定，來建立名為 "TestQueue" 的佇列：

    // Configure queue settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]您可以在 [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 物件上使用 [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) 方法，來檢查服務命名空間內是否已有指定名稱的佇列存在。

## 如何傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式會使用連接字串來建立 [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 物件。

以下的程式碼將示範如何使用 [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API 呼叫，為剛才建立的 "TestQueue" 佇列建立 [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 物件：

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

傳送至服務匯排流佇列 (以及從服務匯流排佇列接收) 的訊息是 [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 類別的執行個體。`BrokeredMessage` 物件具有一組標準屬性 (例如[`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))、一個用來保存自訂應用程式特定屬性的目錄，以及一堆任意的應用程式資料。應用程式可設定訊息內文，方法是將任何可序列化物件傳遞到 [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 物件的建構函式，接著系統便會使用適當的 **DataContractSerializer** 來序列化物件。也可以提供 **System.IO.Stream**。

下列範例將示範如何傳送五則測試訊息至上述程式碼片段中所取得的 "TestQueue" [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 物件：

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue
       Client.Send(message);
     }

服務匯流排佇列[最多可支援 256 KB 的訊息大小](service-bus-quotas.md) (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。此佇列大小會在建立時定義，上限是 5 GB。如果啟用分割，上限會更高。如需詳細資訊，請參閱[分割訊息實體](https://msdn.microsoft.com/library/azure/dn520246.aspx)。

## 如何從佇列接收訊息

從佇列接收訊息的建議方式是使用 [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 物件。`QueueClient` 物件可在兩種不同模式中運作： [`ReceiveAndDelete` 和 `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)。

使用 **ReceiveAndDelete** 模式時，接收是一次性作業；也就是說，當服務匯流排在佇列中收到訊息的讀取要求時，會將此訊息標示為已取用，並將它傳回應用程式。**ReceiveAndDelete** 是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式 (此為預設模式) 中，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它會在已接收的訊息上呼叫 [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)，以完成接收程序的第二個階段。當匯流排看到 `Complete` 呼叫時，它會將訊息標示為已取用，並將它從佇列中移除。

以下範例將示範如何使用預設的 **PeekLock** 模式來接收與處理訊息。若要指定不同 [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 值，您可以使用 [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) 的另一個多載。這個範例會使用 [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 回呼在訊息抵達 **TestQueue** 時處理訊息。

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

此範例會使用 [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 物件設定 [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 回呼。將 [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) 設為 **false** 時，可在接收的訊息上呼叫 [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 時用手動控制。將 [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) 設為 1 分鐘，會導致用戶端用最多一分鐘的時間等候訊息是否呼叫逾時，之後用戶端便會進行新的呼叫以檢查訊息。這個屬性值會減少用戶端無法擷取訊息所出現的收費呼叫次數。

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，它可以呼叫所接收訊息上的 [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 方法 (而不是 [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 方法)。這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未發出 [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 要求時當機，會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 屬性來達到此目的，該屬性將在各個傳遞嘗試中保持不變。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 概觀：[佇列、主題和訂用帳戶。][]
-   建立一個可行的應用程式，往返傳送或接收服務匯流排佇列的訊息：[服務匯流排代理傳訊 .NET 教學課程]。
-   服務匯流排範例： 從 [Azure 範例][]下載，或參閱 [MSDN][] 上的概觀。

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure Management Portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [佇列、主題和訂用帳戶。]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [服務匯流排代理傳訊 .NET 教學課程]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Azure 範例]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx
 

<!---HONumber=July15_HO2-->