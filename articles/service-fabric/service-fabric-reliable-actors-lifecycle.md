<properties
   pageTitle="Azure Service Fabric Actor 生命週期"
   description="解釋 Azure Service Fabric Actor 的生命週期與廢棄項目收集"
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
   ms.date="03/17/2015"
   ms.author="amanbha"/>


#動作項目生命週期與廢棄項目收集
當第一次呼叫動作項目時，及若動作項目一段時間未使用而停用時 (動作項目執行階段收集的廢棄項目)，會啟用動作項目。若要設定時間間隔，請參閱以下＜動作項目廢棄項目收集＞一節。

動作項目啟用時會發生什麼事？

- 當動作項目有呼叫卻尚未為使用中時，會建立新的動作項目。
- 載入其狀態 (如果是可設定狀態的動作項目)
- 呼叫 `OnActivateAsync` 方法 (在動作項目實作中會被覆寫)。
- 會新增至使用中動作項目資料表。

動作項目停用時會發生什麼事？

- 動作項目若一段時間未使用，便會自使用中動作項目資料表中移除。
- 呼叫 `OnDeactivateAsync` 方法 (在執行暫實作中會被覆寫)，這會清除該動作項目的所有計時器。

> [AZURE.TIP]Fabric Actor 執行階段會發出某些[與動作項目啟用和停用相關的事件](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events)。這些項目對於診斷與效能監視很有幫助。

## 動作項目廢棄項目收集
動作項目執行階段會定期掃描是否有一段時間未使用的動作項目並予以停用。一旦停用，CLR 會將這類動作項目當作廢棄項目收集。

就廢棄項目收集的用途而言，什麼算是「一直在使用中」？

- 一直收到呼叫。
- 一直叫用 `IRemindable.ReceiveReminderAsync` 方法 (僅適用於動作項目使用提醒時)。

值得注意的是，如果動作項目使用計時器，且已叫用其計時器回撥，則**不**算是「一直使用中」。

在進入廢棄項目收集的細節前，最重要的是定義下列詞彙：

- *掃描間隔* - 這是動作項目執行階段掃描其使用中動作項目資料表中，是否有動作項目可作為廢棄項目收集的間隔。預設值為 1 分鐘。
- *閒置逾時* - 這是動作項目必須維持未使用 (閒置) 的時間長度，過此時間後即將動作項目作為廢棄項目收集。預設值為 60 分鐘。

通常不需要變更這些預設值。但若有需要，可使用 `ActorGarbageCollection` 屬性在組件層級為該組件中所有的動作項目類型，或在動作項目類型層級中，變更這些間隔。以下範例顯示 HelloActor 的廢棄項目收集間隔變更。

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

若要在組件層級變更 `ActorGarbageCollection` 屬性的預設值，請將下列程式碼片段新增至 `AssemblyInfo.cs`。

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

對於使用中動作項目資料表的每個動作項目，動作項目執行階段會持續追蹤動作項目已閒置的時間 (亦即未使用)。動作項目執行階段每隔 `ScanIntervalInSeconds` 就會檢查每個動作項目，查看其是否可以作為廢棄項目收集，如果已閒置 `IdleTimeoutInSeconds`，就會將其收集。

只要使用動作項目，其閒置時間就會重設為 0。在此之後，只有當動作項目再次閒置達 `IdleTimeoutInSeconds` 時，才會將動作項目作為廢棄項目收集。請回想一下，當動作項目介面方法或動作項目提醒回撥執行時，動作項目會視為已使用。如果動作項目的計時器回撥執行時，**不會**將動作項目視為已使用。

下圖為說明這些概念的範例。

![][1]

此範例假設在使用中動作項目資料表中只有一個使用中動作項目，並顯示動作項目方法呼叫、提醒者與計時器對此動作項目生命週期的影響。範例中有幾下幾點值得注意：

- 範例中的 ScanInterval 與 IdleTimeout 分別設為 5 和 10 (單位在此不重要，因為我們的目的只是為了說明概念)。
- 系統會依照 ScanInterval 為 5 的定義，在 T=0、5、10、15、20、25 時掃描是否有可作為廢棄項目收集的動作項目。
- 定期計時器會在 T=4、8、12、16、 20、24 時引發，其回撥會執行。這不會影響動作項目的閒置時間。
- 在 T=7 的動作項目方法呼叫會將閒置時間重設為 0，並延遲動作項目的廢棄項目收集。
- 動作項目提醒回撥會在 T=14 執行，並進一步延遲動作項目的廢棄項目收集。
- 在 T=25 的廢棄項目收集期間，動作項目的閒置時間最後會超過為 10 的 IdleTimeout，並會將動作項目作為廢棄項目收集。

請注意，動作項目正在執行其中一個方法時，無論在執行該方法時花費了多久的時間，絕對不會將動作項目作為廢棄項目收集。如先前所述，執行動作項目介面方法和提醒回撥會將動作項目的閒置時間重設為 0，來防止廢棄項目收集。執行計時器回撥不會將閒置時間重設為 0。不過，計時器回撥完成執行之前，會延遲動作項目的廢棄項目收集。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
 

<!---HONumber=July15_HO2-->