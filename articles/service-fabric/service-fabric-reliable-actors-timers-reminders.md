<properties
   pageTitle="Azure Service Fabric 動作項目計時器和提醒"
   description="Azure Service Fabric Actor 的計時器和提醒簡介。"
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


# 動作項目計時器
動作項目計時器提供 .NET 計時器的簡單包裝函式，例如採用動作項目執行階段所提供回合式並行保證的回呼方法。

動作項目可以在其基底類別使用 `RegisterTimer` 和 `UnregisterTimer` 方法註冊和取消其計時器。下列範例示範如何使用計時器 API。API 和 .NET 計時器非常類似。在下列範例中，當計時器到期時動作項目執行階段將呼叫 `MoveObject` 方法，而其保證接受回合式的並行存取，也就是說直到此回呼完成執行，沒有其他動作項目方法或計時器/提醒會進行回呼。

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return TaskDone.Done;
    }
}
```

當回呼完成執行後，就會啟動下一期間的計時器。這表示回呼執行時計時器將會停止，並在回呼完成時重新啟動。

如果動作項目是像上述範例中的可設定狀態的動作項目，當回呼完成時，動作項目執行階段會儲存動作項目的狀態。如果儲存狀態時發生錯誤，將會停用該動作項目物件並啟動新的執行個體。不修改動作項目狀態的回呼方法可以註冊為唯讀的計時器回呼，可透過在計時器回呼指定「唯讀」屬性達成，如＜[唯讀方法](service-fabric-reliable-actors-introduction.md#readonly-methods)＞一節所述。

作為記憶體回收的一部分，停用動作項目後也會停用所有的計時器，而在此之後不會叫用任何計時器回呼。此外，動作項目執行階段並不保留任何停用前執行中的計時器資訊。由動作項目來決定任何未來重新啟動時所需計時器的註冊。如需詳細資訊，請參閱＜[動作項目記憶體回收](service-fabric-reliable-actors-lifecycle.md)＞一節。

## 動作項目提醒
提醒是一個會在指定時間於動作項目上觸發持續性回呼的機制。其功能類似計時器，但不同於計時器提醒會在所有情況下觸發直到動作項目明確取消註冊該「提醒」。具體而言，動作項目的停用和容錯移轉會觸發提醒，因為動作項目執行階段所保存的動作項目提醒相關資訊。

提醒僅支援可設定狀態的動作項目。無狀態動作項目不能使用提醒。動作項目狀態提供者負責儲存已由動作項目註冊的提醒相關資訊。

若要註冊提醒，動作項目會呼叫基底類別提供的 `RegisterReminder` 方法，如下列範例所示。

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

在上述範例中，`"Pay cell phone bill"` 為該提醒名稱，是動作項目用來識別唯一提醒的一個字串。`BitConverter.GetBytes(amountInDollars)` 為與提醒相關聯的內容。其會作為提醒回呼的引數傳回給動作項目，也就是 `IRemindable.ReceiveReminderAsync`。

使用提醒的動作項目必須實作 `IRemindable` 介面，如下列範例所示。

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

當觸發提醒時，網狀架構動作項目執行階段會叫用動作項目上的 `ReceiveReminderAsync` 方法。動作項目可以註冊多個提醒，而每當觸發任一個這些提醒時，便會叫用 `ReceiveReminderAsync` 方法。動作項目可以使用傳遞至 `ReceiveReminderAsync` 方法的提醒名稱，以找出已觸發的提醒。

當 `ReceiveReminderAsync` 呼叫完成，動作項目執行階段會儲存動作項目狀態。如果儲存狀態時發生錯誤，將會停用該動作項目物件並啟動新的執行個體。若要指定不需在提醒回呼完成時儲存的狀態，呼叫 `RegisterReminder` 方法建立提醒時，`ActorReminderAttributes.ReadOnly` 旗標可以在 `attributes` 參數中設定。

若要取消註冊提醒，應該呼叫 `UnregisterReminder` 方法，如下列範例所示。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

如上所示，`UnregisterReminder` 方法會接受 `IActorReminder` 介面。動作項目基底類別支援 `GetReminder` 方法，在傳遞進提醒名稱時可以用來擷取 `IActorReminder` 介面。這很方便，因為動作項目不需保存從 `RegisterReminder` 方法呼叫傳回的 `IActorReminder` 介面。
 

<!---HONumber=July15_HO2-->