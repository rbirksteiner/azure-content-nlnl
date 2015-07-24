<properties 
	pageTitle="如何使用服務匯流排佇列 (Python) - Azure" 
	description="了解如何從 Python 使用 Azure 服務匯流排佇列。" 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="huvalo"/>


# 如何使用服務匯流排佇列

本指南將說明如何使用服務匯流排佇列。相關範例是以 Python 撰寫的，並使用 [Python Azure 封裝][]。所涵蓋的案例包括**建立佇列、傳送並接收訊息**，以及**刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

**附註：**若要安裝 Python 或 [Python Azure 封裝][]，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 如何建立佇列

**ServiceBusService** 物件可讓您使用佇列。將下列程式碼新增至您想要在其中以程式設計方式存取 Azure 服務匯流排之任何 Python 檔案內的頂端附近：

	from azure.servicebus import ServiceBusService, Message, Queue

下列程式碼將建立 **ServiceBusService** 物件。請使用您的命名空間、共用存取簽章 (SAS) 金鑰名稱和值，來取代「mynamespace」、「sharedaccesskeyname」及「sharedaccesskey」。

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

SAS 金鑰名稱和值的值可以在 Azure 入口網站連接資訊中找到，或者在 [伺服器總管] 中選取服務匯流排命名空間時於 Visual Studio [**屬性**] 視窗中找到 (如上一節所示)。

	bus_service.create_queue('taskqueue')

**create_queue** 也支援其他選項，而可讓您覆寫訊息存留時間或佇列大小上限等預設佇列設定。下列範例會將佇列大小上限設為 5GB，並將存留時間設為 1 分鐘：

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## 如何傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式必須在 **ServiceBusService** 物件上呼叫 **send_queue_message** 方法。

下列範例說明如何使用 *send_queue_message* 將測試訊息傳送至名為 **taskqueue** 的佇列：

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。此佇列大小會在建立時定義，上限是 5 GB。

## 如何從佇列接收訊息

您可以在 **ServiceBusService** 物件上使用 **receive_queue_message** 方法，從佇列接收訊息。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

如果將 **peek\\_lock** 參數設為 **False**，則當您讀取訊息後，訊息便會從佇列中刪除。您可以將參數 **peek\\_lock** 設為 **True**，來讀取 (查看) 並鎖定訊息，避免系統從佇列刪除訊息。

隨著接收作業讀取及刪除訊息之行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果您將 **peek_Lock** 參數設為 **True**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。在應用程式完成訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它會在 **Message** 物件上呼叫 **Delete** 方法，以完成接收程序的第二個階段。**delete** 方法會將訊息標示為已取用，並將其從佇列中移除。

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。如果接收者應用程式因為某些原因無法處理訊息，它可以在 **Message** 物件上呼叫 **unlock** 方法。這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 MSDN 參考：[佇列、主題和訂用帳戶][]。

[Azure Management Portal]: http://manage.windowsazure.com
[Python Azure 封裝]: https://pypi.python.org/pypi/azure
[佇列、主題和訂用帳戶]: http://msdn.microsoft.com/library/azure/hh367516.aspx
 

<!---HONumber=July15_HO2-->