<properties
   pageTitle="Azure Service Fabric Actor 分散式計算模式"
   description="Azure Service Fabric 適用於平行非同步傳訊、可輕鬆管理的分散狀態和平行計算。"
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
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Service Fabric Actor 設計模式：分散式計算
我們很感激此功能，部分原因是看到現實生活中的客戶在極短的時間內，使用 Azure Service Fabric Actor (確切來說是可計算風險的蒙地卡羅模擬法) 快速計算出財務數據。

一開始，尤其是對於沒有特定領域知識的人，Azure Service Fabric 處理這類的工作負載可能不那麼顯而易見，相較於較為傳統的方法如「對應/減少」或 MPI 等。

但事實上，Azure Service Fabric 適用於平行非同步傳訊、易於管理的分散式狀態，以及平行計算，如下圖所示：

![][1]

在下列範例中，我們使用蒙地卡羅模擬法簡單計算 Pi。我們有下列動作項目：

* 處理器負責使用 PoolTask 動作項目計算 Pi。

* PoolTask 負責蒙地卡羅模擬法及將結果傳送到彙總器。

* 彙總器負責彙總結果，並將結果傳送到完結器。

* 完結器負責計算最終的結果，並列印在螢幕上。

## 分散式計算程式碼範例 – 蒙地卡羅模擬法

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(0); // stateless
            tasks.Add(task.CalculateAsync(tries, seed));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(0);
        return agg.AggregateAsync(pi);
    }
}
```

Azure Service Fabric 中常見的彙總結果是使用計時器。我們使用無狀態動作項目有兩大原因：執行階段將決定要動態需要多少個彙總器，因此讓我們按需求擴充；其將「本機地」具現化這些動作項目，換句話說，在呼叫動作項目的同一個接收器中，網路躍點數會減少。彙總器與完結器如下所示：

## 分散式計算程式碼範例 – 彙總器

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

此時我們如何透過彙總器可能地增加排行榜的規模與效能，應該很清楚。

我們絕不斷言 Azure Service Fabric 會直接取代巨量資料架構的其他分散式計算，或直接取代高效能計算。Azure Service Fabric 在某些方面就是處理得比較好。但只要能夠在 Azure Service Fabric 中製作工作流程與分散式平行計算的模型，仍能得到其所帶來簡易的好處。

## 後續步驟
[模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)

[模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)

[模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png
 

<!---HONumber=July15_HO2-->