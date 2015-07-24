<properties
	pageTitle="如何使用服務匯流排佇列 (Ruby) - Azure"
	description="了解如何使用 Azure 中的服務匯流排佇列。程式碼範例以 Ruby 撰寫。"
	services="service-bus"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="tomfitz"/>




# 如何使用服務匯流排佇列

本指南將說明如何使用服務匯流排佇列。這些範例均以 Ruby 撰寫，並使用 Azure gem。所涵蓋的案例包括**建立佇列、傳送並接收訊息**，以及**刪除佇列**。如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

## 什麼是服務匯流排佇列？

服務匯流排佇列支援**代理訊息通訊**模型。使用佇列時，分散式應用程式的元件彼此不直接通訊，而是透過扮演中繼角色的佇列來交換訊息。訊息產生者 (傳送者) 會將訊息遞交給佇列，然後繼續其處理工作。訊息取用者 (接收者) 非同步地從佇列中提取訊息並處理。產生者不必等待取用者的回覆，即可繼續處理及傳送其他訊息。如果有一或多個競爭取用者，佇列會採取**先進先出 (FIFO)** 訊息傳遞機制。亦即，通常由接收者依訊息加入佇列的順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。

![佇列概念](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

服務匯流排佇列為適用於各種情況的通用技術：

-   多層式 Azure 應用程式中 Web 角色和背景工作角色之間的通訊
-   混合式解決方案中的內部部署應用程式和 Azure 代管應用程式之間的通訊
-   在不同組織或同一組織的不同部門中，在內部部署執行之分散式應用程式的各元件之間的通訊

使用佇列可讓應用程式進一步向外延展，提高架構的備援能力。

## 建立服務命名空間
若要開始在 Azure 中使用服務匯流排佇列，首先必須建立服務命名空間。服務命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。由於入口網站不會使用 ACS 連線建立服務匯流排，因此您必須透過命令列介面建立命名空間。

建立服務命名空間：

1. 開啟 Azure Powershell 主控台。

2. 輸入命令以建立 Azure 服務匯流排命名空間，如下所示。提供您自己的命名空間值，並指定與您的應用程式相同的區域。

    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![建立命名空間](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)

## 取得命名空間的管理認證
若要在新的命名空間上執行管理作業，例如建立佇列，您必須取得命名空間的管理認證。

您執行以建立 Azure 服務匯流排命名空間的 PowerShell Cmdlet 會顯示您可以用來管理命名空間的金鑰。複製 **DefaultKey** 值。您將會在本教學課程稍後的程式碼中使用此值。

![複製金鑰](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE]如果您登入 [Azure 管理入口網站](http://manage.windowsazure.com/) 並瀏覽至您的服務匯流排命名空間的連接資訊，也可以找到此金鑰。

## 建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/) (英文)。

## 設定應用程式以使用服務匯流排

若要使用 Azure 服務匯流排，您需要下載並使用 Ruby azure 封裝；其中包含一組能與儲存體 REST 服務通訊的便利程式庫。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

    require "azure"

## 設定 Azure 服務匯流排連接

azure 模組會讀取環境變數 **AZURE_SERVICEBUS_NAMESPACE** 和 **AZURE_SERVICEBUS_ACCESS_KEY**，以取得連接到 Azure 服務匯流排命名空間所需的資訊。若未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::ServiceBusService** 之前指定命名空間資訊：

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

將服務匯流排命名空間值設為您建立的值而非整個 URL。例如，使用 **"yourexamplenamespace"** 而非 "yourexamplenamespace.servicebus.windows.net"。

## 如何建立佇列

**Azure::ServiceBusService** 物件可讓您處理佇列。若要建立佇列，請使用 **create_queue()** 方法。下列範例將建立佇列或列出錯誤 (若有的話)。

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

您也可以使用其他選項傳入 **Azure::ServiceBus::Queue** 物件，這可讓您覆寫訊息存留時間或佇列大小上限等預設佇列設定。下列範例說明將佇列大小上限設為 5GB，將存留時間設為 1 分鐘的設定：

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## 如何傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，您的應用程式必須在 **Azure::ServiceBusService** 物件上呼叫 **send_queue_message()** 方法。傳送至服務匯排流 (以及服務匯流排接收) 的佇列是 **Azure::ServiceBus::BrokeredMessage** 物件，此類物件具有一組標準屬性 (例如 **label** 和 **time_to_live**)、一個用來保存自訂應用程式特定屬性的字典，以及一堆任意的應用程式資料。應用程式可用訊息的形式傳遞字串值以設定訊息內文，任何必要的標準屬性都將以預設值填入。

下列範例示範如何使用 **send_queue_message()** 將測試訊息傳送至名為 "test-queue" 的佇列：

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。此佇列大小會在建立時定義，上限是 5 GB。

## 如何從佇列接收訊息

您可以在 **Azure::ServiceBusService** 物件上使用 **receive_queue_message()** 方法，以從佇列接收訊息。根據預設，在讀取及鎖定訊息後並不會將其從佇列中刪除。但您可以將 **:peek_lock** 選項設為 **false**，而在讀取訊息後將其從佇列中刪除。

預設行為會使讀取和刪除變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可呼叫 **delete_queue_message()** 方法，並將要刪除的訊息提供做為參數，以完成接收程序的第二個階段。**delete_queue_message()** 方法會將訊息標示為已取用，並將它從佇列中移除。

如果 **:peek_lock** 參數設為 **false**，讀取和刪除訊息將會變成最簡單的模型，且最適用於應用程式容許在發生失敗時不處理訊息的案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

以下範例將示範如何使用 **receive_queue_message()** 來接收與處理訊息。此範例會先使用設為 **false** 的 **:peek_lock** 來接收及刪除訊息，然後再接收另一個訊息，接著使用 **delete_queue_message()** 刪除訊息：

    message = azure_service_bus_service.receive_queue_message("test-queue",
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因故無法處理訊息，它可以呼叫 **Azure::ServiceBusService** 物件的 **unlock_queue_message()** 方法。這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete_queue_message()** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **message_id** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂用帳戶](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。

若要比較本文所討論的 Azure 服務匯流排佇列與[如何使用 Azure 佇列服務](/develop/ruby/how-to-guides/queue-service/)一文中討論的 Azure 佇列，請參閱 [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)。
 

<!---HONumber=July15_HO1-->