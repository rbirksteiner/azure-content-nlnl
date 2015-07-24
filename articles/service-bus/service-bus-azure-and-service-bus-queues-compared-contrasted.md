<properties 
   pageTitle="Azure 佇列和服務匯流排佇列 - 異同比較"
   description="分析 Azure 所提供之兩種佇列類型之間的差異和相似性。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sethm" />

# Azure 佇列和服務匯流排佇列 - 異同比較

本文將分析 Microsoft Azure 目前所提供之兩種佇列類型之間的差異和相似性：Azure 佇列和服務匯流排佇列。透過使用這項資訊，您可以比較和比對個別的技術，而且對於哪一種方案最符合您的需求，也能夠做出更旁徵博引的決定。

## 簡介

Microsoft Azure 支援兩種佇列機制：**Azure 佇列**和**服務匯流排佇列**。

**Azure 佇列**是 [Azure 儲存體](http://azure.microsoft.com/services/storage/) 基礎結構的一部分，具有簡單的 REST 架構 Get/Put/Peek 介面，而且能夠在服務內部和服務之間提供可靠且持續的訊息傳遞。

**服務匯流排佇列**是較廣泛之 [Azure 訊息](http://azure.microsoft.com/services/messaging/)基礎結構的一部分，這個基礎結構支援佇列處理，以及發佈/訂閱、Web 服務遠端處理和整合模式。如需服務匯流排佇列、主題/訂用帳戶和轉送的詳細資訊，請參閱[服務匯流排訊息模式的概觀](https://msdn.microsoft.com/library/hh410103.aspx)。

雖然這兩種佇列技術同時存在，不過 Azure 佇列較早引進，做為建置在 Azure 儲存體服務之上的專用佇列儲存機制。服務匯流排佇列則是建置在較廣泛的「代理傳訊」基礎結構之上，這個基礎結構的設計目的是為了整合可能跨多種通訊協定、資料合約、信任網域和 (或) 網路環境的應用程式或應用程式元件。

本文會分別討論 Azure 所提供之兩種佇列技術的功能行為和實作差異，藉以進行比較。本文也會引導您選擇最符合應用程式開發需求的功能。

## 技術選擇考量

Azure 佇列和服務匯流排佇列都是 Microsoft Azure 目前所提供之訊息佇列服務的實作。每種佇列都有稍微不同的功能集，表示您可以根據特定方案的需求或正在解決的商務/技術問題選擇其中一種佇列，或是使用這兩種佇列。

在判斷哪一種佇列技術適合給定方案的目的時，方案架構設計人員和開發人員應該考慮下列建議。如需詳細資訊，請參閱下一節。

身為方案架構設計人員/開發人員，**您應該在下列情況下考慮使用 Azure 佇列**：

- 您的應用程式必須在佇列中儲存超過 80 GB 的訊息，而且訊息的存留期短於 7 天。

- 您的應用程式想要在佇列內部追蹤處理訊息的進度。如果處理訊息的工作者損毀，這就很有用。後續工作者可以接著使用該項資訊，從先前工作者停止的地方繼續處理。

- 您需要有針對佇列執行之所有交易的伺服器端記錄。

身為方案架構設計人員/開發人員，**您應該在下列情況下考慮使用服務匯流排佇列**：

- 您的方案必須能夠接收訊息，而不需要輪詢佇列。使用服務匯流排時，您可以使用服務匯流排支援的 TCP 通訊協定，進行長期輪詢接收作業來達成這個目的。

- 您的方案需要使用佇列來提供保證的先進先出 (FIFO) 排序傳遞。

- 您想要在 Azure 中和在 Windows Server (私人雲端) 上有相稱體驗。如需詳細資訊，請參閱 [Windows Server 的服務匯流排](https://msdn.microsoft.com/library/dn282144.aspx)。

- 您的方案必須能夠支援自動重複偵測。

- 您想要讓應用程式將訊息當成長時間執行的平行資料流來處理 (訊息是透過訊息上的 [SessionId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) 屬性與資料流相關聯)。在這個模型中，取用端應用程式中的每個節點都會競爭取得資料流而不是訊息。將資料流提供給取用端節點時，節點可以檢查應用程式資料流使用交易的狀態。

- 從佇列傳送或接收多個訊息時，您的方案需要交易行為和不可部分完成性。

- 應用程式特有工作負載的存留時間 (TTL) 特性可能會超過 7 天的期限。

- 您的應用程式所處理的訊息可能會超過 64 KB，但是不太可能會接近 256 KB 的限制。

- 您需要提供角色型存取模型給佇列，並且針對傳送者和接收者提供不同的權限。

- 您的佇列大小不會成長超過 80 GB。

- 您想要使用 AMQP 1.0 標準型傳訊代理人。如需 AMQP 的詳細資訊，請參閱[服務匯流排 AMQP 概觀](https://msdn.microsoft.com/library/jj841072.aspx)。

- 您可以預見最後會從佇列架構的點對點通訊移轉至允許緊密整合其他接收者 (訂戶) 的訊息交換模式，而且每個接收者都會接收傳送至佇列之部分或所有訊息的獨立複本。後者是指服務匯流排原本提供的發佈/訂閱功能。

- 您的訊息方案必須能夠支援「最多一次」傳遞保證，而不需要建置其他基礎結構元件。

- 您想要能夠批次發佈及取用訊息。

- 您需要與 .NET Framework 中的 Windows Communication Foundation (WCF) 通訊堆疊完全整合。

## 比較 Azure 佇列和服務匯流排佇列

下列各節中的表格以邏輯方式分組佇列功能，讓您一眼就能比較 Azure 佇列和服務匯流排佇列所提供的功能。

## 基本功能

本節將比較 Azure 佇列和服務匯流排佇列所提供的一些基本佇列功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|排序保證|**否** <br/><br>如需詳細資訊，請參閱＜其他資訊＞一節的第一個注意事項。</br>|**是 - 先進先出 (FIFO)**<br/><br>(透過使用訊息工作階段)|
|傳遞保證|**至少一次**|**至少一次**<br/><br/>**最多一次**|
|交易支援|**否**|**是**<br/><br/>(透過使用本機交易)|
|接收行為|**非封鎖**<br/><br/>(如果找不到新的訊息，便立即完成)|**含/不含逾時的封鎖**<br/><br/>(提供長期輪詢，或稱為[「Comet 技術」](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**非封鎖**<br/><br/>(僅限透過使用 .NET Managed API)|
|推送型 API|**否**|**是**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/jj908682.aspx) 和 [OnMessage 工作階段](https://msdn.microsoft.com/library/dn790528.aspx)的 .NET API。|
|接收模式|**查看與租用**|**查看與鎖定**<br/><br/>**接收與刪除**|
|獨佔存取模式|**以租用為基礎**|**以鎖定為基礎**|
|租用/鎖定持續時間|**30 秒 (預設值)**<br/><br/>**7 天 (上限)** (您可以使用 [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API 更新或釋放訊息租用)|**60 秒 (預設值)**<br/><br/>您可以使用 [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API 更新訊息鎖定。|
|租用/鎖定精確度|**訊息層級**<br/><br/>(每個訊息都可以具有不同的逾時值，您稍後可以在處理訊息時，視需要使用 [UpdateMessage](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API 更新這個逾時值)|**佇列層級**<br/><br/>(每個佇列都有套用至所有訊息的鎖定精確度，但是您可以使用 [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API 更新鎖定)|
|批次接收|**是**<br/><br/>(在擷取訊息時明確指定訊息計數，最多 32 個訊息)|**是**<br/><br/>(隱含啟用預先擷取屬性或明確透過使用交易)|
|批次傳送|**否**|**是**<br/><br/>(透過使用交易或用戶端批次)|

### 其他資訊

- Azure 佇列中的訊息通常是先進先出，但有時也不按順序。舉例來說，訊息的可視性逾時期限到期時 (例如，由於處理期間用戶端應用程式損毀所致)。當可視性逾時到期時，訊息會再次出現在佇列上，以便其他工作者清除佇列中的訊息。此時，佇列中可能放入新出現的訊息 (等待再次從佇列中清除)，而且是最初在它之後才加入佇列的訊息後面。

- 如果您已使用 Azure 儲存體、Blob 或資料表，而且開始使用佇列，則保證您可獲得 99.9% 的可用性。如果您將 Blob 或資料表搭配服務匯流排佇列使用，則您將獲得較低的可用性。

- 服務匯流排佇列中的保證 FIFO 模式需要使用訊息工作階段。如果應用程式在處理以**查看與鎖定**模式所接收的訊息時損毀，下一次佇列接收者接受訊息工作階段時，將會在存留時間 (TTL) 期限到期之後，從失敗的訊息開始處理。

- Azure 佇列是設計來支援標準佇列案例，例如使應用程式元件脫鉤以增加延展性及容錯能力、進行負載調節，以及建置程序工作流程。

- 服務匯流排佇列支援「至少一次」的傳遞保證。此外，也可支援「最多一次」語意，方法是使用工作階段狀態來儲存應用程式狀態，並且使用交易以不可部分完成的方式接收訊息並更新工作階段狀態。Azure 工作流程服務會使用這項技術來保證「最多一次」傳遞。

- Azure 佇列提供跨佇列、資料表和 BLOB 之統一且一致的程式設計模型，適合開發人員和營運團隊使用。

- 服務匯流排佇列支援在單一佇列內容中進行本機交易。

- 服務匯流排所支援的「接收與刪除」模式可讓您降低訊息作業計數 (以及關聯的成本)，但是也會降低傳遞保證。

- Azure 佇列可讓租用延長訊息的租用。這可讓工作者維持短期的訊息租用。因此，如果某個工作者損毀，就可以由另一個工作者快速地重新處理訊息。此外，如果工作者需要的處理時間超過目前的租用時間，也可以延長訊息的租用。

- Azure 佇列提供您可以在將訊息加入佇列或從佇列中清除訊息時設定的可視性逾時。此外，您可以在執行階段使用不同的租用值來更新訊息，並且在相同佇列的訊息之間更新不同的值。服務匯流排鎖定逾時定義於佇列中繼資料內；不過，您可以透過呼叫 [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) 方法更新鎖定。

- 在服務匯流排佇列中，封鎖接收作業的最大逾時值是 24 天。但是，REST 架構的最大逾時值為 55 秒。

- 服務匯流排所提供的用戶端批次允許佇列用戶端將多個訊息批次處理成單一傳送作業。批次處理僅適用於非同步傳送作業。

- Azure 佇列的 200TB 上限 (當您虛擬化帳戶時則更多) 和無限佇列等功能，使它成為 SaaS 提供者的理想平台。

- Azure 佇列提供彈性、高效能的委派存取控制機制。

## 進階功能

本節將比較 Azure 佇列和服務匯流排佇列所提供的進階功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|排程傳遞|**是**|**是**|
|自動處理無效信件|**否**|**是**|
|增加佇列存留時間值|**是**<br/><br/>(經由可視性逾時的就地更新)|**是**<br/><br/>(由專用 API 函式提供)|
|有害訊息支援|**是**|**是**|
|就地更新|**是**|**是**|
|伺服器端交易記錄|**是**|**否**|
|儲存體度量|**是**<br/><br/>**分鐘度量**：提供可用性、TPS、API 呼叫計數、錯誤計數等即時度量，全都即時 (每分鐘彙總一次並在生產環境中發生狀況的短短數分鐘內回報)。如需詳細資訊，請參閱[關於儲存體分析度量](https://msdn.microsoft.com/library/hh343258.aspx)。|**是**<br/><br/>(透過呼叫 [GetQueues](https://msdn.microsoft.com/library/hh293128.aspx) 來進行大量查詢)|
|狀態管理|**否**|**是**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)、[Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)、[Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)、[Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)|
|訊息自動轉送|**否**|**是**|
|清除佇列函式|**是**|**否**|
|訊息群組|**否**|**是**<br/><br/>(透過使用訊息工作階段)|
|每個訊息群組的應用程式狀態|**否**|**是**|
|重複偵測|**否**|**是**<br/><br/>(可在傳送者端設定)|
|WCF 整合|**否**|**是**<br/><br/>(提供現成的 WCF 繫結)|
|WF 整合|**自訂**<br/><br/>(需要建置自訂 WF 活動)|**原生**<br/><br/>(提供現成的 WF 活動)|
|瀏覽訊息群組|**否**|**是**|
|依 ID 擷取訊息工作階段|**否**|**是**|

### 其他資訊

- 這兩種佇列技術都可讓訊息排定在稍後傳遞。

- 佇列自動轉送可讓數以千計的佇列將其訊息自動轉送至單一佇列，而接收端應用程式將從中取用訊息。您可以使用這個機制來達成安全性、控制流程，並在每個訊息發佈者之間隔離儲存體。

- Azure 佇列支援更新訊息內容。您可以使用這項功能，將狀態資訊和累加進度更新保存至訊息中，以便從最後已知的檢查點處理訊息，而不用從頭開始處理。使用服務匯流排佇列時，您可以透過使用訊息工作階段來實現相同案例。工作階段可讓您使用 [SetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) 和 [GetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) 儲存和擷取應用程式處理狀態。

- 只有服務匯流排佇列支援的無效信件處理可用於隔離接收端應用程式無法順利處理的訊息，或是由於存留時間 (TTL) 屬性過期而無法送達目的地的訊息。TTL 值會指定訊息保留在佇列中的時間長度。在服務匯流排中，當 TTL 期限到期時，訊息將會移至稱為 $DeadLetterQueue 的特殊佇列。

- 為了在 Azure 佇列中找出「有害」訊息，應用程式會在清除佇列中的訊息時，檢查訊息的 DequeueCount 屬性。如果 DequeueCount 超過給定的臨界值，應用程式就會將訊息移至應用程式定義的「無效信件」佇列。

- Azure 佇列可讓您取得針對佇列執行之所有交易的詳細記錄，以及彙總的度量資料。這兩個選項有助於偵錯和了解應用程式的 Azure 佇列使用狀況，也有助於對您的應用程式進行效能微調，以及降低使用佇列的成本。

- 服務匯流排所支援之「訊息工作階段」的概念可讓屬於特定邏輯群組的訊息與給定的接收者產生關聯，進而在訊息與其個別的接收者之間建立類似工作階段的密切關係。您可以設定訊息上的 [SessionID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) 屬性，以在服務匯流排中啟用這項進階功能。然後，接收者就可以接聽特定的工作階段 ID，並且接收共用指定之工作階段識別項的訊息。

- 服務匯流排佇列所支援的重複偵測功能會根據 [MessageID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 屬性的值，自動移除傳送至佇列或主題的重複訊息。

## 容量和配額

本節將從可能適用之容量和配額的觀點來比較 Azure 佇列和服務匯流排佇列。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|佇列大小上限|**200 TB**<br/><br/>(限制為單一儲存體帳戶容量)|**1 GB 到 80 GB**<br/><br/>(在建立佇列和[啟用分割](https://msdn.microsoft.com/library/dn520246.aspx)時定義 - 請參閱＜其他資訊＞一節)|
|訊息大小上限|**64 KB**<br/><br/>(使用 **Base64** 編碼時，則為 48 KB)<br/><br/>Azure 可以結合佇列和 Blob 來支援大型訊息，因此您最多可以將 200GB 的單一項目加入佇列。|**256 KB**<br/><br/>(包括標頭和主體，標頭大小上限：64 KB)|
|訊息 TTL 上限|**7 天**|**無限制**|
|佇列數目上限|**無限制**|**10,000**<br/><br/>(每個服務命名空間，可以增加)|
|並行用戶端數目上限|**無限制**|**無限制**<br/><br/>(100 個並行連接限制只適用於以 TCP 通訊協定為基礎的通訊)|

### 其他資訊

- 服務匯流排會強制執行佇列大小限制。佇列大小上限是在建立佇列時指定的，而且可以具有 1 到 80 GB 之間的值。如果達到在建立佇列時所設定的佇列大小值，其他內送訊息將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。如需服務匯流排中配額的詳細資訊，請參閱[服務匯流排配額](https://msdn.microsoft.com/library/ee732538.aspx)。

- 您可以建立 1、2、3、4 或 5 GB 大小的服務匯流排佇列 (預設值為 1 GB)。啟用分割時 (這是預設值)，服務匯流排會為您指定的每 GB 建立 16 個資料分割。因此，如果您建立 5 GB 大小的佇列，每 GB 有 16 個資料分割，則佇列大小上限會變成 (5 * 16) = 80 GB。您可以在 Azure 管理入口網站上檢視分割的佇列或主題項目，藉此查看其大小上限。

- 使用 Azure 佇列時，如果訊息內容不是 XML 安全內容，則必須經過 **Base64** 編碼。如果您對訊息進行 **Base64** 編碼，使用者裝載最多可為 48 KB，而非 64 KB。

- 使用服務匯流排佇列時，儲存在佇列中的每個訊息都包含兩個部分：標頭和主體。訊息大小總計不得超過 256 KB。

- 當用戶端透過 TCP 通訊協定與服務匯流排佇列通訊時，單一服務匯流排佇列的並行連接數目上限會限制為 100。這個數目是在傳送者和接收者之間共用的。如果達到這個配額，其他連接的後續要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。這項限制不會加諸於使用 REST API 連接至佇列的用戶端。

- 如果您的單一服務匯流排服務命名空間需要超過 10,000 個佇列，您可以連絡 Azure 支援小組並要求增加。若要讓服務匯流排擴充到超過 10,000 個佇列，您也可以使用 Azure 管理入口網站來建立其他命名空間。

## 管理和作業

本節將比較 Azure 佇列和服務匯流排佇列所提供的管理功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|管理通訊協定|**REST over HTTP/HTTPS**|**REST over HTTPS**|
|執行階段通訊協定|**REST over HTTP/HTTPS**|**REST over HTTPS**<br/><br/>**AMQP 1.0 標準 (TCP 和 TLS)**|
|.NET Managed API|**是**<br/><br/>(.NET Managed 儲存體用戶端 API)|**是**<br/><br/>(.NET Managed 代理傳訊 API)|
|Native C++|**是**|**否**|
|Java API|**是**|**是**|
|PHP API|**是**|**是**|
|Node.js API|**是**|**是**|
|任意中繼資料支援|**是**|**否**|
|佇列命名規則|**長度最多 63 個字元**<br/><br/>(佇列名稱的字母必須是小寫)|**長度最多 260 個字元**<br/><br/>(佇列名稱不區分大小寫)|
|取得佇列長度函式|**是**<br/><br/>(如果訊息過了 TTL 而到期卻未遭刪除，則為近似值)|**是**<br/><br/>(精確的時間點值)|
|查看函式|**是**|**是**|

### 其他資訊

- Azure 佇列支援可套用至佇列描述的任意屬性，格式為名稱/值組。

- 這兩種佇列技術都提供不需要鎖定訊息即可查看訊息的功能，這項功能在實作佇列總管/瀏覽器工具時很有用。

- 與 REST over HTTP 相較之下，服務匯流排的 .NET 代理傳訊 API 利用全雙工 TCP 連接來提升效能，而且可支援 AMQP 1.0 標準通訊協定。

- Azure 佇列名稱長度可以是 3-63 個字元，而且可以包含小寫字母、數字和連字號。如需詳細資訊，請參閱[為佇列和中繼資料命名](https://msdn.microsoft.com/library/dd179349.aspx)。

- 服務匯流排佇列名稱長度最多 260 個字元，而且命名規則的限制較少。服務匯流排佇列名稱可以包含字母、數字、句號 (.)、連字號 (-) 和底線 (_)。

## 效能

本節將從效能的觀點來比較 Azure 佇列和服務匯流排佇列。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|最大輸送量|**每秒最多 2,000 個訊息**<br/><br/>(以 1 KB 訊息為基準)|**每秒最多 2,000 個訊息**<br/><br/>(以 1 KB 訊息為基準)|
|平均延遲|**10 毫秒**<br/><br/>(停用 [TCP Nagle](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) 時)|**20-25 毫秒**|
|節流行為|**以 HTTP 503 代碼拒絕**<br/><br/>(已節流的要求不會視為可計費的要求)|**以例外狀況/HTTP 503 拒絕**<br/><br/>(已節流的要求不會視為可計費的要求)|

### 其他資訊

- 單一 Azure 佇列每秒最多可以處理 2,000 筆交易。交易是指 **Put**、**Get** 或 **Delete** 作業。傳送單一訊息至佇列 (**Put**) 會視為一筆交易，不過接收訊息通常是兩個步驟的程序，其中包含擷取 (**Get**)，接著要求從佇列中移除訊息 (**Delete**)。因此，成功的清除佇列作業通常包含兩筆交易。批次擷取多個訊息可降低這種情況的影響，因為您在單一交易中最多可**取得** 32 個訊息，並接著**刪除**每個訊息。如需改善輸送量，您可以建立多個佇列 (儲存體帳戶可以有無限個佇列)。

- 當您的應用程式達到 Azure 佇列的最大輸送量時，佇列服務通常會傳回「HTTP 503 伺服器忙碌中」回應。如果發生這種情況，應用程式應該使用指數輪詢延遲來觸發重試邏輯。

- 在處理與儲存體帳戶位於相同位置 (區域) 之託管服務的小型訊息 (小於 10 KB) 時，Azure 佇列的平均延遲為 10 毫秒。

- Azure 佇列和服務匯流排佇列都會拒絕對正在節流之佇列提出的要求，藉以強制執行節流行為。不過，這兩者都不會將已節流的要求視為可計費的要求。

- 對服務匯流排佇列進行的基準測試顯示，單一佇列可以達到每秒最多 2,000 個訊息的訊息輸送量 (訊息大小大約為 1 KB)。若要達到更高的輸送量，請使用多個佇列。如需使用服務匯流排達到效能最佳化的詳細資訊，請參閱[使用服務匯流排代理傳訊的效能改進最佳作法](https://msdn.microsoft.com/library/hh528527.aspx)。

- 達到服務匯流排佇列的最大輸送量時，會傳回 [ServerBusyException](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) (使用 .NET 代理傳訊 API 時) 或 HTTP 503 (使用 REST API 時) 給佇列用戶端，表示佇列正在進行節流。

## 驗證和授權

本節將討論 Azure 佇列和服務匯流排佇列所支援的驗證和授權功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|驗證|**對稱金鑰**|**對稱金鑰**|
|存取控制模型|透過 SAS 權杖進行委派存取。|透過 ACS 進行 RBAC|
|識別提供者同盟|**否**|**是**|

### 其他資訊

- 對這兩種佇列技術提出的每項要求都必須經過驗證。不支援具有匿名存取的公用佇列。使用 SAS 時，您可以發佈唯寫 SAS、唯讀 SAS 或甚至是完整存取 SAS 來解決這種情況。

- Azure 佇列所提供的驗證配置需要使用對稱金鑰，這個金鑰是雜湊式訊息驗證碼 (HMAC)、使用 SHA-256 演算法所計算並且編碼為 **Base64** 字串。如需個別通訊協定的詳細資訊，請參閱[驗證對儲存體帳戶的存取](https://msdn.microsoft.com/library/hh225339.aspx)。服務匯流排佇列支援使用對稱金鑰的類似模型。如需詳細資訊，請參閱[使用服務匯流排的共用存取簽章驗證](https://msdn.microsoft.com/library/dn170477.aspx)。

- 服務匯流排所支援的 Microsoft Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 提供三種不同的角色：**管理員**、**傳送者**和**接收者**，Azure 佇列目前不支援這項服務。

- 由於服務匯流排提供 ACS 整合，因此可讓您與 Active Directory (透過使用 ADFS) 以及其他常見的 Web 識別提供者形成同盟。

## 成本

本節將從成本的觀點來比較 Azure 佇列和服務匯流排佇列。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|佇列交易成本|**$0.0005**<br/><br/>(每 10,000 筆交易)|**基本層**：**$0.05**<br/><br/>(每百萬個作業)|
|可計費的作業|**全部**|**僅限傳送/接收**<br/><br/>(其他作業免費)|
|閒置交易|**可計費**<br/><br/>(查詢空的佇列會視為一筆可計費的交易)|**可計費**<br/><br/>(針對空的佇列進行接收會視為一個可計費的訊息)|
|儲存成本|**$0.07**<br/><br/>(每個 GB/月)|**$0.00**|
|輸出資料傳輸成本|**$0.12 - $0.19**<br/><br/>(根據地理位置)|**$0.12 - $0.19**<br/><br/>(根據地理位置)|

### 其他資訊

- 資料傳輸的收費依據是在給定的計費週期內，經由網際網路離開 Azure 資料中心的資料量總計。

- 位於相同區域之 Azure 服務之間的資料傳輸不另收費。

- 在撰寫本文時，所有輸入資料傳輸也不另收費。

- 對服務匯流排佇列執行訊息作業時，ACS 交易的成本微不足道。服務匯流排會根據訊息 Factory 物件的每個單一執行個體各取得一個 ACS 權杖。然後重複使用這個權杖直到過期為止 (大約 20 分鐘之後)。因此，服務匯流排中的訊息作業量不會直接與支援這些作業所需的 ACS 交易數量成正比。

- 由於支援長期輪詢，因此在需要低度延遲傳遞的情況下使用服務匯流排佇列可能會符合成本效益。

>[AZURE.NOTE]所有成本都可能會變更。上表反映撰寫本文時的目前定價，不包括目前可能適用的任何促銷優惠方案。如需 Azure 的最新定價資訊，請參閱 [Azure 定價](http://azure.microsoft.com/pricing/)頁面。如需服務匯流排定價的詳細資訊，請參閱 [服務匯流排定價]((http://azure.microsoft.com/pricing/details/service-bus/)。

## 結論

透過深入了解這兩種技術，您將能夠對要使用哪種佇列技術及何時使用，做出更旁徵博引的決定。關於何時使用 Azure 佇列或服務匯流排佇列的決定明顯取決於許多因素。這些因素可能主要取決於應用程式及其架構的個別需求。如果您的應用程式已經使用 Microsoft Azure 的核心功能，您可能會想要選擇 Azure 佇列，尤其是需要在服務之間進行基本通訊和傳訊，或是需要大小可超過 80 GB 的佇列時。

由於服務匯流排佇列提供許多進階功能 (例如工作階段、交易、重複偵測、自動處理無效信件和持久的發佈/訂閱功能)，如果您要建置混合應用程式或您的應用程式額外需要這些功能，則比較適合選擇這種佇列。

## 後續步驟

下列文章提供有關使用 Azure 佇列或服務匯流排佇列的更多指引和資訊。

- [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)
- [如何使用佇列儲存體服務](../storage-dotnet-how-to-use-queues.md)
- [使用服務匯流排代理傳訊的效能改進最佳作法](https://msdn.microsoft.com/library/hh528527.aspx)
- [Azure 服務匯流排的佇列和主題簡介](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [服務匯流排的開發人員指南](http://www.cloudcasts.net/devguide/)
- [「深入探討 Azure 資料表和佇列」](http://www.microsoftpdc.com/2009/SVC09)
- [Azure 儲存體架構](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [使用 Azure 中的佇列服務](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [了解 Azure 儲存體計費 - 頻寬、交易和容量](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)
 

<!---HONumber=July15_HO2-->