<properties 
   pageTitle="事件中樞驗證和安全性模型概觀"
   description="事件中樞常見問題集"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="06/09/2015"
   ms.author="sethm" />

# 事件中樞驗證和安全性模型概觀

事件中樞安全性模型符合下列需求：

- 唯有提供有效憑證的裝置能將資料傳送到事件中樞。

- 裝置無法模擬另一個裝置。

- 可封鎖惡意裝置，避免該裝置將資料傳送到事件中樞。

## 裝置驗證

事件中樞安全性模型是以[共用存取簽章 (SAS)](https://msdn.microsoft.com/library/dn170477.aspx) 權杖和事件發佈者的組合為基礎。事件發佈者能定義事件中樞的虛擬端點。發佈者只能用來將訊息傳送到事件中樞。您無法接收來自發佈者的訊息。

一般而言，事件中樞會針對每個裝置採用一個發佈者。系統會將所有傳送到事件中樞之任何發佈行者的訊息排入該事件中樞內的佇列。發佈者允許更精密的存取控制和節流。

系統會為每個裝置指派一個唯一權杖，該權杖會上傳至裝置。權杖的產生機制讓每個唯一權杖都能授與相異唯一發佈者的存取權限。擁有權杖的裝置只能傳送到一個發佈者，無法再傳送到其他發佈者。如果多個裝置共用同一個權杖，這些裝置會共用一個發佈者。

您可以為裝置配備授與事件中樞直接存取權限的權杖，不過我們不建議這樣做。任何擁有這類權杖的裝置都可以將訊息直接傳送到該事件中樞。這類裝置將不受節流所約束。此外，您無法將這類裝置加入黑名單，禁止其傳送到該事件中樞。

所有權杖都經過 SAS 金鑰簽署。一般而言，所有權杖都會經過同一個金鑰簽署。裝置無法感知金鑰，如此能避免裝置製造權杖。

### 建立金鑰

在建立命名空間時，服務匯流排會產生名為 **RootManageSharedAccessKey** 的 256 位元 SAS 金鑰。這個金鑰能授與命名空間的傳送、聆聽及管理權限。您可以建立額外的金鑰。建議您產生授與特定事件中樞之傳送權限的金鑰。針對本主題的其他內容，我們假設您將此金鑰命名為 `EventHubSendKey`。

以下範例會在建立事件中樞時建立僅限傳送的金鑰：

```C#

// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, amespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that allows sending to that Event Hub.
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, ventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);

```

### 產生權杖

您可以使用 SAS 金鑰來產生權杖。您只能為每個裝置產生一個權杖。您可以使用下列方法來產生權杖。所有權杖都是以 **EventHubSendKey** 金鑰產生。每個權杖均有一個指派的唯一 URI。

	public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)

在呼叫這個方法時，您應該將 URI 指定為 `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`。此 URI 對所有權杖來說是完全相同的，但 `PUBLISHER_NAME` 除外，每個權杖的該項目應該是不同的。在理想的情況下，`PUBLISHER_NAME` 代表收到該權杖之裝置的識別碼。

這個方法會產生具有下列結構的權杖：

	SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}

以秒數為單位指定的權杖到期 (從 1970 年 1 月 1 日 開始)。以下是權杖的範例：

	SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey

一般而言，權杖的週期與裝置週期相近或大於裝置週期。如果裝置有能力取得新權杖，就可以使用週期較短的權杖。

### 傳送資料的裝置

一旦建立權杖之後，每個裝置即會佈建自己的唯一權杖。

當裝置將資料傳送到事件中樞時，裝置會為自己的權杖加上傳送要求標記。若要預防攻擊者竊聽及竊取權杖，裝置和事件中樞之間的通訊必須透過加密通道。

### 將裝置列入黑名單

如果權杖遭攻擊者竊取，攻擊者便可以模擬權杖遭竊的裝置。將裝置列入黑名單能使裝置無法使用，直到裝置擁有使用不同發佈者的權杖為止。

## 後端應用程式的驗證

為了驗證取用裝置產生之資料的後端應用程式，事件中樞會採用與服務匯流排主題所用之模型相似的安全性模型。事件中樞取用者群組等同於服務匯流排主題的訂用帳戶。如果建立取用者群組的要求伴隨著授與事件中樞或事件中樞所屬之命名空間管理權限的權杖，用戶端便可以建立取用者群組。如果接收要求伴隨著授與取用者群組、事件中樞或事件中樞所屬之命名空間接收權限的權杖，用戶端便可以取用來自取用者群組的資料。

目前版本的服務匯流排不支援個別訂用帳戶的 SAS 規則。相同情況亦適用於事件中樞取用者群組。我們會在日後將這兩項功能加入 SAS 支援。

由於個別取用者群組的 SAS 驗證不存在，因此您可以使用 SAS 金鑰來保護所有使用相同金鑰的取用者群組。這個方法能讓應用程式取用來自事件中樞之任何取用者群組的資料。

### 在 ACS 中建立服務身分識別、信賴憑證者及規則

ACS 支援以多種方法來建立服務身分識別、信賴憑證者及規則，不過最簡單的方法是使用 [SBAZTool](http://code.msdn.microsoft.com/windowsazure/Authorization-SBAzTool-6fd76d93)。例如：

1. 建立 **EventHubSender** 的服務身分識別。這會傳回所建立之服務身分識別的名稱和它的金鑰：

		sbaztool.exe exe -n <namespace> -k <key>  makeid eventhubsender

2. 將事件中樞的「傳送宣告」授與 **EventHubSender**：

		sbaztool.exe -n <namespace> -k <key> grant Send /AuthTestEventHub eventhubsender

3. 為 Consumer Group 1 的接收者建立服務身分識別：

		sbaztool.exe exe -n <namespace> -k <key> makeid consumergroup1receiver

4. 將 **ConsumerGroup1** 的「聆聽宣告」授與 `consumergroup1receiver`：

		sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup1 consumergroup1receiver

5. 為 **Consumer Group 2** 的接收者建立服務身分識別：

		sbaztool.exe exe -n <namespace> -k <key>  makeid consumergroup2receiver

6. 將 **ConsumerGroup2** 的「聆聽宣告」授與 `consumergroup2receiver`：

		sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup2 consumergroup2receiver

## 後續步驟

若要深入了解事件中樞，請造訪下列主題：

- [事件中樞概觀]
- [使用事件中樞的完整範例應用程式]。
- 使用服務匯流排佇列的[佇列訊息解決方案]。

[事件中樞概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[佇列訊息解決方案]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=62-->