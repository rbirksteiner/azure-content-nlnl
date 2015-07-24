<properties 
	pageTitle="如何使用拼音的佇列儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。範例以 Ruby 撰寫。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# 如何使用 Ruby 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南示範如何使用 Microsoft Azure 佇列儲存體服務執行常見案例。這些範例是以 Ruby Azure API 撰寫的。所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/) (英文)。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

	require "azure"

## 設定 Azure 儲存體連接

azure 模組會讀取環境變數 **AZURE_STORAGE_ACCOUNT** 及 **AZURE_STORAGE_ACCESS_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。若未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::QueueService** 之前指定帳戶資訊：

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

若要取得這些值，請執行下列動作：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2. 瀏覽到您要使用的儲存體帳戶
3. 按一下導覽窗格底部的 [管理金鑰]。
4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可以選取主要存取金鑰或次要存取金鑰。

## 作法：建立佇列

下列程式碼會建立一個 **Azure::QueueService** 物件，讓您能夠使用佇列。

	azure_queue_service = Azure::QueueService.new

使用 **create_queue()** 方法，建立具有指定名稱的佇列。

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## 作法：將訊息插入佇列中

若要將訊息插入佇列中，請使用 **create_message()** 方法建立新訊息，並將其新增至佇列。

	azure_queue_service.create_message("test-queue", "test message")

## 作法：預覽下一個訊息

您可以呼叫 **peek_messages()** 方法，以在佇列前面查看訊息，而無需將它從佇列中移除。根據預設，**peek_messages()** 會查看單一訊息。您也可以指定所要查看的訊息數。

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## 作法：清除下一個佇列訊息

您可以使用兩個步驟將訊息從佇列中移除。

1. 根據預設，在呼叫 **list_messages()** 時，您會取得佇列中的下一個訊息。您也可以指定您要取得的訊息數。從 **list_messages()** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。您可以傳入以秒為單位的可見性逾時，作為參數。

2. 若要完成從佇列中移除訊息的作業，您還必須呼叫 **delete_message()**。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **delete_message()**。

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## 作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。以下程式碼會使用 **update_message()** 方法來更新訊息。此方法會傳回一個 Tuple (其中包含佇列訊息的 pop receipt) 和一個 UTC 日期時間值 (代表訊息將會顯示在佇列上的時間)。

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## 作法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。

1. 您可以取得一批訊息。

2. 您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例將使用 **list_messages()** 方法，在一次呼叫中取得 15 個訊息。接著，它會列出每個訊息，並加以刪除。它也會將可見度逾時設定為每個訊息五分鐘。

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## 作法：取得佇列長度

您可以取得佇列中的估計訊息數目。**get_queue_metadata()** 方法會要求佇列服務傳回約略的訊息計數和佇列的相關中繼資料。

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## 作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫佇列物件的 **delete_queue()** 方法。

	azure_queue_service.delete_queue("test-queue")

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- 請參閱 MSDN 參考資料：[Azure 儲存體](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- 造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
- 請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。

若要比較本文所討論的 Azure 佇列服務與[如何使用服務匯流排佇列](/develop/ruby/how-to-guides/service-bus-queues/)一文中討論的 Azure 服務匯流排佇列，請參閱 [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照](http://msdn.microsoft.com/library/azure/hh767287.aspx)。
 

<!---HONumber=July15_HO2-->