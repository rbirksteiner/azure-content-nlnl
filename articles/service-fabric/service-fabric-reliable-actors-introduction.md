<properties
   pageTitle="Azure Service Fabric Actor 概觀"
   description="Azure Service Fabric Actor 程式設計模型簡介"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Azure Service Fabric Actor 簡介
Azure Service Fabric Actor 是 [Service Fabric](service-fabric-technical-overview.md) 的動作項目程式設計模型。Service Fabric 是一種平台，可為雲端與內部建置高可靠性、可調整性且可輕鬆部署與管理的應用程式。

Fabric Actor 提供非同步、單一執行緒的動作項目模型。動作項目代表狀態與計算的單位。動作項目分散在整個叢集中，藉以達到高延展性。當裝載動作項目的處理序失敗時，系統會在另一個處理序中建立動作項目。同樣的，當正在執行動作項目主機處理序的節點失敗，系統會在另一節點的主機處理序中重新建立動作項目。動作項目執行階段會利用基礎的 Service Fabric 平台提供的分散式存放區，為動作項目提供高可用性且一致狀態的管理。這會讓開發與維護動作項目型分散式雲端應用程式變更極為容易。

## 動作項目
動作項目是被隔離的單一執行緒元件，能夠封裝狀態和行為。動作項目與 .NET 物件類似，因此能提供給開發人員自然的程式設計模型。每個動作項目都是動作項目類型的執行個體，就像是 .NET 物件是 .NET 類型的執行個體。例如，有的動作項目類型會實作計算機的功能，而該類型會有許多動作項目分散到叢集的各種節點上。每一個這類的動作項目都有唯一的動作項目識別碼識別。

動作項目會藉由以要求-回應模式傳遞非同步訊息，來與剩餘的系統互動，包括其他動作項目。這些互動在介面中定義為非同步方法。例如，實作計算器功能的動作項目類型介面可定義如下。

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

動作項目類型可實作上述介面，如下所示：

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

每個介面方法所傳回工作的引數與結果類型必須是[資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。

這些方法的實作並不涉及處理鎖定或其他並行問題，因為動作項目執行階段會為動作項目方法提供回合式並行。如需詳細資訊，請參閱＜[並行](#concurrency)＞一節。

> [AZURE.TIP]Fabric Actor 執行階段會發出某些[事件和與動作項目方法相關的效能計數器](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)。這些項目對於診斷與效能監視很有幫助。

值得一提的是下列動作項目介面方法的專屬方法：- 動作項目介面方法無法多載。- 動作項目介面方法不能有輸出、參照或選擇性參數。

## 動作項目通訊
動作項目用戶端 API 能夠在動作項目執行個體與動作項目用戶端之間提供通訊。為了與動作項目通訊，用戶端會建立一個動作項目 Proxy 物件來實作動作項目介面。用戶端會叫用 Proxy 物件上的方法來與動作項目互動。動作項目 Proxy 可用於用戶端對動作項目，及動作項目對動作項目的通訊。繼續我們的計算機範例，接下來會為計算機動作項目撰寫如下所示的用戶端程式碼：

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

如上例所示，有兩段資訊用於建立動作項目 Proxy 物件 - 動作項目 ID 與應用程式名稱。動作項目識別碼是可唯一識別動作項目的識別碼。應用程式名稱是動作項目所部署為 [Service Fabric 應用程式](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors)的名稱。

動作項目是虛擬的，亦即其永遠存在。您不需要明確建立，也無須摧毀動作項目。動作項目執行階段會在第一次收到動作項目的要求時自動啟動動作項目。如果動作項目已一段時間未使用，動作項目執行階段會將它作為廢棄項目回收，日後再視需要啟動。如需詳細資訊，請參閱＜[動作項目生命週期和記憶體回收](service-fabric-reliable-actors-lifecycle.md)＞一節。

動作項目用戶端 API 也提供位置透明度與容錯移轉。用戶端上的 `ActorProxy` 類别會執行必要的解決方案，然後找到動作項目服務[資料分割](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)，在此裝載在指定 ID 的動作項目，並會開啟與該動作項目通訊的管道。`ActorProxy` 會在通訊失敗及容錯移轉時重新嘗試。這表示動作項目實作能夠從相同的用戶端取得重複的訊息。

## 並行
動作項目執行階段為動作項目方法提供簡單的回合式並行。這表示動作項目代碼內永遠只能有一個執行緒為使用中。

一個回合包含完整執行動作項目方法以回應其他動作項目或用戶端的要求，或完整執行[計時器/提醒](service-fabric-reliable-actors-timers-reminders.md)回撥。即使這些方法和回呼非同步，但動作項目執行階段並不使其交錯。必須完整完成一個回合，才能允許進行下一回合。換句話說，計時器/提醒回撥目前正在執行的動作項目方法或計時器/提醒回撥必須完整完成，才能對方法或回撥進行新的呼叫。如果已從方法或回撥傳回執行，且方法或回撥傳回的工作已完成，則方法或回撥視為已完成。值得強調的是，即使是在不同的方法、計時器與回撥之間，仍會遵守回合式並行。

動作項目執行階段會藉由在某回合的開頭取得一個各動作項目鎖定，然後在回合結束時釋放該鎖定，來強制回合式並行。因此，回合式並行會依各個動作項目強制執行，不會在動作項目之間強制執行。動作項目方法和計時器/提醒回撥可代表不同的動作項目同時執行。

以下範例說明上述概念。如果有一個動作項目類型實作兩個非同步方法 (假設為 *Method1* 與 *Method2*)，也就是計時器與提醒。下圖顯示代表這兩個屬於此動作項目類型的動作項目 - *ActorId1* 與 *ActorId2* - 執行這些方法與回撥的時間軸範例。

![][1]

上圖遵循的慣例為：

- 每一個垂直線顯示代表特定動作項目執行方法或回撥時的邏輯流程。
- 每個垂直線上標示的事件依時間順序發生，新發生的事件排在舊事件下方。
- 時間軸使用不同的顏色來對應不同的動作項目。
- 反白顯示用於指出代表方法或回撥保留各動作項目鎖定的持續期間。

在上圖中得注意以下幾點：

- 當 *Method1* 代表 *ActorId2* 執行以回應用戶端要求 *xyz789* 時，另一個抵達的用戶端要求 *abc123* 也需要 *Method1* 由 *ActorId2* 執行。不過，*Method1* 的後者執行會在前者執行完成後才會開始。同樣的，由 *ActorId2* 註冊的提醒會在 *Method1* 正在執行時引發，以回應用戶端要求 *xyz789*。提醒回撥只會在 *Method1* 的這兩個執行都完成後才執行。所有的一切都是因為對 *ActorId2* 強制執行回合式並行。
- 同樣的，也會對 *ActorId1* 強制執行回合式並行，如代表依序發生的 *ActorId1* 執行 *Method1*、*Method2* 和計時器回撥所示。
- 代表*ActorId1* 執行 *Method1* 與代表 *ActorId2* 執行重疊。這是因為回合式並行只會在動作項目內強制執行，不會在動作項目間強制執行。
- 在某些方法/回撥執行中，方法/回撥傳回的 `Task` 會在方法傳回後完成。在其他執行中，已經依照方法/回撥傳回的時間完成 `Task`。在任一情況下，只會在上述兩種情況都發生後，亦即方法/回撥都傳回且 `Task` 完成後，才會釋放各動作項目鎖定。

動作項目執行階段依預設允許重新進入。這表示如果動作項目 A 的動作項目方法在動作項目 B 上呼叫方法，然後反過來在動作項目 A 上呼叫另一個方法，則當該方法若是同一個邏輯呼叫鏈結內容的一部分，則允許執行該方法。所有的計時器與提醒呼叫的開頭都是新的邏輯呼叫內容。請參閱＜[重新進入](service-fabric-reliable-actors-reentrancy.md)＞一節以瞭解更多詳細資訊。

動作項目執行階段在控制叫用這些方法的狀況下，提供這些並行保證。例如，動作項目執行階段會對為回應接收用戶端要求而進行的方法叫用，以及對計時器與提醒回撥，提供這些保證。然而，如果動作項目程式碼在動作項目執行階段提供的機制之外直接叫用這些方法，則執行階段無法提供任何並行保證。例如，如果在某個與動作項目方法所傳回的工作不相關聯的工作內容中叫用方法，或如果從動作項目自行建立的執行緒叫用方法，則執行階段無法提供並行保證。因此若要執行背景作業，動作項目應使用遵守回合式並行的[動作項目計時器或動作項目提醒](service-fabric-reliable-actors-timers-reminders.md)。

> [AZURE.TIP]Fabric Actor 執行階段會發出某些[事件和與並行相關的效能計數器](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters)。這些項目對於診斷與效能監視很有幫助。

## 動作項目狀態管理
Fabric Actor 可讓您建立無狀態或可設定狀態的動作項目。

### 無狀態的動作項目
無狀態的動作項目正如其名，不需要任何由動作項目執行階段管理的狀態。無狀態的動作項目衍生自動作項目基底類別。其可以有成員變數，並可以有該動作項目整個生命週期中存留的變數，正如同任何其他的 .NET 類型一樣。但如果動作項目執行個體在閒置一段時間後被當成廢棄項目回收，則該執行個體會失去儲存在其成員變數中的狀態。同樣的，會因為容錯移轉而失去狀態，這會在升級、資源平衡作業中發生，或在動作項目處理序失敗或裝載動作項目處理序的節點失敗時發生。

以下是無狀態動作項目的範例。

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### 可設定狀態的動作項目
在動作項目廢棄項目回收與容錯移轉之間保留可設定狀態的動作項目其狀態。從 `Actor<TState>` 基底類別衍生的可設定狀態的動作項目，其中 `TState` 是必須在廢棄項目回收與容錯移轉之間保留的狀態類型。透過基底類別 `Actor<TState>` 的 `State` 屬性可在動作項目方法中存取狀態。以下為存取狀態之可設定狀態的動作項目範例。

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

動作項目狀態的類型必須是[資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。

> [注意] 請參閱[序列化上的可靠動作項目附註](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)一文，以取得如何定義介面和 Actor State 類型的詳細資訊。

#### 動作項目狀態提供者
動作項目狀態提供者會儲存與擷取狀態。可在組件內依狀態提供者特定的屬性，對各動作項目或所有動作項目設定狀態提供者。動作項目執行階段中包含一些預設的動作項目狀態提供者。狀態的持久性和可靠性由狀態提供者提供的保證決定。動作項目啟動時，其狀態會載入到記憶體中。動作項目方法完成時，動作項目執行階段會呼叫狀態提供者的方法自動儲存修改的狀態。如果儲存狀態期間發生失敗，動作項目執行階段會回收該動作項目執行個體。系統會從狀態提供者中以最後的一致狀態建立新動作項目執行個體並載入。

依預設，可設定狀態的動作項目會使用索引鍵值存放區動作項目狀態提供者。此狀態提供者建置在由 Service Fabric 平台提供的分散式索引鍵值存放區之基礎上。如需詳細資訊，請參閱[狀態提供者選擇](service-fabric-reliable-actors-platform.md#actor-state-provider-choices)主題。

> [AZURE.TIP]Fabric Actor 執行階段會發出某些[事件和與動作項目狀態管理相關的效能計數器](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters)。這些項目對於診斷與效能監視很有幫助。

#### 唯讀方法
依預設，動作項目執行階段會在動作項目方法呼叫、計時器回撥與提醒回撥完成時儲存動作項目狀態。儲存狀態完成前不允許其他的動作項目呼叫。儲存狀態花費的時間視狀態提供者而定，這段期間不會允許動作項目中的其他動作項目呼叫。

有一些動作項目方法不會修改狀態，在此狀況下，需要花費額外的時間儲存狀態，這會影響系統的整體輸送量。若要避免此狀況發生，您可以將不修改狀態的方法和計時器回撥標示為唯讀。

下列範例顯示如何使用 `Readonly` 屬性將動作項目方法標示為唯讀。

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

以類似的方式也可使用 `Readonly` 屬性標示計時器回撥。對於提醒，會將唯讀旗標作為引數傳遞 `RegisterReminder` 方法，系統會叫用此方法來註冊提醒。

## 後續步驟
### 概念
[動作項目生命週期和記憶體回收](service-fabric-reliable-actors-lifecycle.md)

[動作項目計時器與提醒](service-fabric-reliable-actors-timers-reminders.md)

[動作項目事件](service-fabric-reliable-actors-events.md)

[動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)

[Fabric Actor 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)

[設定 KVSActorStateProvider 動作項目](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->