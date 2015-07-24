<properties
	pageTitle="如何使用服務匯流排 (Ruby) - Azure"
	description="了解如何在 Azure 使用服務匯流排主題及訂閱。程式碼範例專為 Ruby 應用程式撰寫。"
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





# 如何使用服務匯流排主題/訂閱

本指南將說明如何從 Ruby 應用程式使用服務匯流排主題和訂閱。所涵蓋的案例包括**建立主題和訂閱、建立訂閱篩選器、傳送訊息**至主題、**接收訂閱的訊息**，及**刪除主題和訂閱**。如需主題和訂閱的詳細資訊，請參閱[後續步驟](#NextSteps)一節。

## 什麼是服務匯流排主題和訂閱

服務匯流排主題和訂閱支援**發佈/訂閱訊息通訊**模型。使用主題和訂閱時，分散式應用程式的元件彼此不直接通訊，而是透過扮演中繼角色的主題來交換訊息。

![主題概念](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

相較於服務匯流排佇列，其中每個訊息只由單一取用者處理，主題和訂閱採用發佈/訂閱模式，提供**一對多**的通訊形式。一個主題可以登錄多個訂閱。當訊息傳送至主題時，每個訂閱都可取得訊息來個別處理。

主題訂閱類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。您可以選擇為個別訂閱來登錄主題的篩選規則，以篩選/限制主題的哪些訊息由哪些主題訂閱接收。

服務匯流排主題和訂閱可讓您擴大處理非常多使用者和應用程式上非常大量的訊息。

## 建立服務命名空間

若要開始在 Azure 中使用服務匯流排佇列，首先必須建立服務命名空間。服務命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。由於入口網站不會使用 ACS 連線建立服務匯流排，因此您必須透過命令列介面建立命名空間。

若要建立命名空間：

1. 開啟 Azure Powershell 主控台。

2. 輸入命令以建立 Azure 服務匯流排命名空間，如下所示。提供您自己的命名空間值，並指定與您的應用程式相同的區域。

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![建立命名空間](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## 取得命名空間的預設管理認證

若要在新的命名空間上執行管理作業，例如建立佇列，您必須取得命名空間的管理認證。

您執行以建立 Azure 服務匯流排命名空間的 PowerShell Cmdlet 會顯示您可以用來管理命名空間的金鑰。複製 **DefaultKey** 值。您將會在本教學課程稍後的程式碼中使用此值。

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]如果您登入 [Azure 管理入口網站](http://manage.windowsazure.com/) 並瀏覽至您的服務匯流排命名空間的連接資訊，也可以找到此金鑰。

## 建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/) (英文)。

## 設定應用程式使用服務匯流排

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

## 如何建立主題

**Azure::ServiceBusService** 物件可讓您處理主題。下列程式碼將建立 **Azure::ServiceBusService** 物件。若要建立主題，請使用 **create_topic()** 方法。下列範例將建立主題或列出錯誤 (若有的話)。

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

您也可以使用其他選項傳入 **Azure::ServiceBus::Topic** 物件，這可讓您覆寫訊息存留時間或佇列大小上限等預設主題設定。下列範例說明將佇列大小上限設為 5GB，將存留時間設為 1 分鐘的設定：

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## 如何建立訂閱

**Azure::ServiceBusService** 物件也能用來建立主題訂閱。訂閱是具名的，它們能擁有選用的篩選器，以限制傳遞至訂閱之虛擬佇列的訊息集合。

訂閱是持續性的，它們會持續存在，直到本身或相關的主題遭到刪除為止。如果應用程式含有建立訂閱的邏輯，它應該會先使用 getSubscription 方法檢查訂閱是否存在。

### 使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll** 篩選器是預設篩選器，如果在建立新的訂閱時沒有指定篩選器，便會使用此篩選器。使用 **MatchAll** 篩選器時，所有發佈至主題的訊息都會被置於訂閱的虛擬佇列中。下列範例將建立名為 "all-messages" 的訂閱，並使用預設的 **MatchAll** 篩選器。

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>使用篩選器建立訂用帳戶

您也可以設定篩選器，讓您界定傳送至主題的哪些訊息應出現在特定主題訂閱中。

在訂閱支援的篩選器中，**Azure::ServiceBus::SqlFilter** 是最具彈性的類型，可用來實作 SQL92 的子集。SQL 篩選器會對發佈至主題之訊息的屬性運作。如需可與 SQL 篩選器搭配使用的運算式詳細資料，請檢閱 [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 語法。

您可以使用 **Azure::ServiceBusService** 物件的 **create_rule()** 方法，將篩選器新增至訂閱。此方法可讓您將篩選器新增至現有的訂閱中。

由於預設篩選器會自動套用至所有新訂閱，因此您必須先移除預設篩選器，否則 **MatchAll** 會覆寫您指定的其他任何篩選器。您可以使用 **Azure::ServiceBusService** 物件的 **delete_rule()** 方法移除預設規則。

以下範例將建立名為 "high-messages"、且其 **Azure::ServiceBus::SqlFilter** 只選取自訂 **message_number** 屬性大於 3 之訊息的訂閱：

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

同樣地，下列範例將建立名為 "low-messages"、且其 **Azure::ServiceBus::SqlFilter** 只選取 **message_number** 屬性小於或等於 3 之訊息的訂閱：

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

現在，當訊息傳送至 "test-topic" 時，一律會將該訊息傳遞至已訂閱 "all-messages" 主題訂閱的接收者，並選擇性地傳遞至已訂閱 "high-messages" 和 "low-messages" 主題訂閱的接收者 (視訊息內容而定)。

## 如何傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式必須使用 **Azure::ServiceBusService** 物件的 **send_topic_message()** 方法。傳送至服務匯流排主題的訊息是 **Azure::ServiceBus::BrokeredMessage** 物件。**Azure::ServiceBus::BrokeredMessage** 物件具有一組標準屬性 (例如 **label** 和 **time_to_live**)、一個用來保存自訂應用程式特定屬性的字典，以及一堆字串資料。應用程式可將字串值傳遞至 **send_topic_message()** 方法以設定訊息本文，任何必要的標準屬性都將以預設值填入。

下列範例說明如何將五個測試訊息傳送至 "test-topic"。請注意迴圈反覆運算上每個訊息的 **message_number** 自訂屬性值的變化 (這可判斷接收訊息的訂閱為何)：

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

服務匯流排主題支援 256 Kb 的訊息大小上限 (包含標準和自訂應用程式屬性的標頭可以容納 64 Kb 的大小上限)。主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。此主題大小會在建立時定義，上限是 5 GB。

## 如何自訂閱接收訊息

您可以使用 **Azure::ServiceBusService** 物件的 **receive_subscription_message()** 方法接收來自訂閱的訊息。根據預設，在讀取 (查看) 及鎖定訊息後並不會將其從訂閱中刪除。您可以將 **peek_lock** 選項設為 **false**，而在讀取訊息後將其從訂閱中刪除。

預設行為會使讀取和刪除變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可呼叫 **delete_subscription_message()** 方法，並將要刪除的訊息提供做為參數，以完成接收程序的第二個階段。**delete_subscription_message()** 方法會將訊息標示為已取用，並將其自訂閱移除。

如果 **:peek_lock** 參數設為 **false**，讀取和刪除訊息將會變成最簡單的模型，且最適用於應用程式容許在發生失敗時不處理訊息的案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

以下範例將示範如何使用 **receive_subscription_message()** 來接收與處理訊息。此範例會先使用設為 **false** 的 **:peek_lock** 接收及刪除來自「low-messages」訂閱的訊息，然後接收另一個來自「high-messages」的訊息，接著使用 **delete_subscription_message()** 刪除該訊息：

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因故無法處理訊息，它可以呼叫 **Azure::ServiceBusService** 物件的 **unlock_subscription_message()** 方法。這將使服務匯流排將訂閱中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在訂閱內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete_subscription_message()** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **message_id** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 如何刪除主題和訂閱

主題和訂閱是持續性的，您必須透過 [Azure 管理入口網站](https://manage.windowsazure.com)或程式設計明確地加以刪除。下列範例說明如何刪除名為 "test-topic" 的主題：

	azure_service_bus_service.delete_topic("test-topic")

刪除主題也將會刪除對主題註冊的任何訂閱。您也可以個別刪除訂閱。下列程式碼將示範如何從 "test-topic" 主題中刪除名為 "high-messages" 的訂閱：

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂用帳戶](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx) 的 API 參考資料。
-	請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。
 

<!---HONumber=July15_HO1-->