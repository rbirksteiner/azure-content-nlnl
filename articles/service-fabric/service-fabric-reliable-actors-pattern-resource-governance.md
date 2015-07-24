<properties
   pageTitle="Azure Service Fabric Actor 資源管理設計模式"
   description="如何將需調整有限資源應用程式模組化的 Service Fabric Actor 設計模式"
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
   ms.author="claudioc"/>

# Azure Service Fabric Actor 設計模式：資源管理
企業或其他開發人員能輕鬆地辨識此模式和相關案例，他們在內部部署或雲端擁有無法立即調整的有限資源，或需要將大規模的應用程式與資料移至雲端。

這些企業的有限資源 (像是資料庫)，皆在相應增加的硬體上執行。任何有長期企業經驗的人都知道，這是常見的內部部署狀況。即使是在雲端規模，我們也看過這種情況，當雲端服務嘗試與超過 64K TCP 上限的位址/連接埠 Tuple 連線，或嘗試連接到會限制並行連線數目的雲端架構的資料庫時便會發生。

過去通常是透過以訊息為基礎的中介軟體進行節流，或以自訂建立的共用及外觀機制解決。這些很難正確設定，尤其是當我們需要調整中介層，同時維持正確的連接計數。不但脆弱而且還很複雜。

事實上，如同「智慧快取」模式一般，這種模式橫跨多個案例和已經有可行的有限資源系統客戶。他們建置系統時，同時需要將服務、狀態記憶體及永續狀態的穩定儲存體向外延展。

下圖將說明這種案例：

![][1]

## 模組化快取與動作項目的案例
基本上，我們將存取資源模組化成一個或多個動作項目，並作為至資源或群組的資源的 Proxy (例如連接)。您可以透過個別動作項目或協調動作項目直接管理資源動作項目。為了讓您更具體了解詳情，我們將說明滿足常見於因效能和延展性考量，而進行儲存體層資料分割 (也稱為分區化) 的需求。我們的第一個選項很基本：我們可以使用靜態函式對應和解析動作項目至下游資源。這類函數可以傳回像是指定輸入的連接字串。這完全取決於我們實作該函式的方式。當然，這種方法有其缺點，例如靜態親和性會導致重新分割資源或重新將動作項目對應至資源變得很困難。以下是一個非常簡單的範例，我們利用使用者識別碼和使用區域識別資料庫伺服器，以進行模數算術判定資料庫的名稱。

## 資源控管程式碼範例 – 靜態解析度

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

簡單但不是很有彈性。現在讓我們來看一下更進階的和有用的方法。首先，我們會模組化同質的實體資源和動作項目。這是透過呼叫稱為「解析程式」的動作項目，並了解使用者、邏輯資料分割與實體資源之間的對應。解析程式會維護持續性存放區中的資料，不過也有已快取的資料方便查閱。如我們先前在「智慧快取」模式中的「匯率」範例中所見，解析程式可以使用計時器主動擷取最新資訊。一旦使用者的動作項目解析所需使用的資源後，會將其快取在本機變數稱為_解析度，並在存留期間使用該資源。我們傾向以查詢為基礎的解析度 (下面說明) 高於簡單或範圍雜湊，因其在作業中所提供的彈性，如調整輸入/輸出或將使用者從一個資源移至另一個資源。

![][2]

在上圖中，我們看到動作項目 B23 首先解析其資源 (也稱為解析度) — DB1 並將其快取。後續作業現在就可以使用已快取的解析度存取有限資源。因為動作項目支援單一執行緒執行，開發人員不再需要擔心資源的並行存取。使用者和解析程式動作項目如下所示：

## 資源控管程式碼範例 – 解析程式

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : Actor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

資源管理 – 解析程式範例

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : Actor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## 存取具有限功能的資源
現在讓我們看看另一個範例。寶貴資源如資料庫、儲存體帳戶和具有限輸送量功能的檔案系統獨佔存取。我們的案例如下：我們想要使用負責處理並保存事件的動作項目 (稱為 EventProcessor) 以處理事件。為了簡單起見，在此案例中為處理 .CSV 檔案。雖然我們可以依照上述資料分割方法向外延展資源，我們還是必須應付並行處理的問題。也就是為何我們選擇使用以檔案為基礎的範例來說明此特定點，從多個動作項目寫入單一檔案將會引發並行處理問題。若要解決此問題，我們將介紹另一個有限資源獨佔擁有權的動作項目，稱為 EventWriter。其案例如下圖所示：

![][3]

我們將 EventProcessor 動作項目標示為「無狀態背景工作」，可讓執行階段在叢集中視需要調整。因此在上圖中，我們並未為這些動作項目使用任何識別碼。換句話說，無狀態的動作項目是執行階段維護的背景工作集區。下列範例程式碼，EventProcessor 動作項目執行兩項工作：首先決定使用哪些 EventWriter (及其資源)，並叫用選擇的動作項目撰寫已處理的事件。為了簡單起見，我們選擇「事件類型」作為 EventWriter 動作項目的識別碼。換句話說，在這個事件類型只會也只有一個 EventWriter 提供資源的單一執行緒和獨佔存取。

## 資源控管程式碼範例 – 事件處理器

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

現在讓我們看一下 EventWriter 動作項目。除了控制有限資源的獨佔存取外，其實沒有太多其他功用，而在此案例中，還有檔案和寫入事件的存取權。
## 資源控管程式碼範例 - 事件寫入器

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

擁有單一動作項目負責資源，可讓我們加入功能，例如緩衝處理。我們可以緩衝處理進入事件，並使用計時器定期或在緩衝區已滿時寫入這些事件。以下是以計時器為基礎的簡單範例：
## 資源控管程式碼範例 - 有緩衝處理的事件寫入器

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

雖然上述程式碼會正常運作，但用戶端並不會知道該事件是否已寫入基礎存放區。若要允許緩衝處理並讓用戶端深入了解其要求的情況，我們將介紹下列方法，以讓用戶端等候直到事件寫入 .CSV 檔案：

## 資源控管程式碼範例 - 非同步批次處理

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

我們將使用此類別建立和維護一份未完成的作業 (以封鎖用戶端)，並在我們將已緩衝處理的事件寫至儲存體後，一次將其全部處理完成。在 EventWriter 類別中，我們需要做三件事：將動作項目類別標示為「可重新進入」、傳回 SubmitNext() 的結果以及排清計時器。修改後的程式碼如下：

## 資源控管程式碼範例 - 非同步批次處理的緩衝處理

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

似乎很簡單？ 確實如此。而輕鬆的代價卻是消耗企業電源。使用這個架構會得到：

* 獨立的資源定址位置。
* 以簡單地變更作為資源代表動作項目數目為基礎，可調整的集區大小。
* 用戶端協調的集區使用量 (如所示) 或伺服器端 (試想像圖中每個集區的前面都有一個動作項目)。
* 可擴充的集區加法 (只需新增代表新資源的動作項目)。
* 動作項目 (如之前所示範) 可以依需求將結果從後端資源快取出來，或可以使用計時器預先快取並減少後端資源的叫用。
* 有效率的非同步分派。
* 一個不限於中介軟體專家，而是任何開發人員皆熟悉的程式碼撰寫環境。

此模式在開發人員需要以有限資源進行開發，或建置大型的向外延展系統的案例中很常見。


## 後續步驟
[模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)

[模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)

[模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png
 

<!---HONumber=July15_HO2-->