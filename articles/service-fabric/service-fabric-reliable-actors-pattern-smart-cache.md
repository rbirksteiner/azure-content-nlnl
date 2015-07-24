<properties
   pageTitle="Azure Service Fabric Actor 智慧快取設計模式"
   description="如何在以 Web 為基礎應用程式上，使用 Service Fabric Actor 作為快取基礎結構的設計模式"
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

# Service Fabric Actor 設計模式：智慧型快取
Web 層、快取層、儲存層和 (偶爾) 背景工作層的組合差不多是現今應用程式的標準部分。快取層效能通常很重要，而且事實上本身可能就包含多個階層。許多快取是簡單的機碼值組配對，而其他作為快取的系統如 [Redis](http://redis.io)，則提供更豐富的語意。不過，任何特殊的快取層將會在語意受到限制，更重要的，那是另一個需要管理的層。如果取而代之，物件只是在本機變數保存狀態，而這些物件可自動快照或保留至永久性存放區呢？ 此外，豐富的集合如清單、已排序的集、佇列和任何其他這方面的自訂類型，皆模組化為成員變數和方法。

![][1]

## 排行榜範例
以排行榜為範例，排行榜物件需要維護玩家及其分數的排序清單，好讓我們可以進行查詢。例如，「前 100 名玩家」或在排行榜中找出相對於 +-N 該玩家上方和下方的玩家位置。使用傳統工具的典型解決方案需要 'GET' 排行榜物件 (可支援插入<Player  Points>稱為分數之新 tuple 的集合)，排序後再 'PUT' 回快取。為了一致性，我們可能會 LOCK (GETLOCK, PUTLOCK) 排行榜物件。讓我們使用以動作項目為基礎的解決方案，其狀態和行為綁在一起。有兩個選項：

* 將排行榜集合實作為動作項目的部分，
* 或將動作項目用作為我們可以在成員變數中保留的集合介面。首先讓我們看看介面可能如下所示：

## 智慧快取程式碼範例 – 排行榜介面

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

接下來，我們實作這個介面和第二個選項，並封裝這個集合的行為至動作項目中：

## 智慧型的快取程式碼範例 – 排行榜動作項目

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

類別的狀態成員會提供動作項目的狀態，而在上面的範例程式碼中，也提供方法來讀取/寫入資料。

## 智慧快取程式碼範例 - LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

不需傳送資料，不需鎖定，只要處理分散式執行階段中的遠端物件及服務多個用戶端，就像是單一應用程式只服務單一用戶端的單一物件。以下是範例用戶端：

## 智慧的快取程式碼範例 – 呼叫排行榜動作項目

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

輸出如下所示：

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## 調整架構
您可能感到上述範例會在排行榜執行個體遇到瓶頸。如果舉例來說，我們計劃支援數以萬計位玩家呢？ 一個可以處理的方法，可能是導入無狀態彙總作為緩衝處理，保存部分的分數 (比方說小計)，並再定期傳送給可以維護最終排行榜的排行榜動作項目。我們稍後將詳細討論此「彙總」技巧。此外，我們就不必考慮 Mutex、旗號或傳統上正確行為並行程式必要的其他並行建構。以下是另一個快取範例，將示範可實作進動作項目的豐富語意。這次我們實作優先順序佇列的邏輯 (號碼越低，優先順序就越高)，作為動作項目實作的一部分。IJobQueue 介面如下所示：

## 智慧快取程式碼範例 – 工作佇列介面

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

我們還需要定義工作項目：

## 智慧快取程式碼範例 – 工作

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

最後，我們將 IJobQueue 介面實作進粒度。請注意，為了清楚起見，我們在這裡省略了優先順序佇列的實作詳細資料。可以在隨附的範例中找到實作範例。

## 智慧快取程式碼範例 – 工作佇列

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

輸出如下所示：

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## 動作項目提供彈性
在上述排行榜和 JobQueue 範例中，我們使用了兩種不同技術：

* 排行榜範例中我們將排行榜物件封裝為動作項目中的私人成員變數，並只為這個物件的狀態和功能提供一個介面。

* 相反地，在 JobQueue 範例中我們將動作項目實作為優先順序佇列本身，而非另一個在其他地方定義的參考物件。

動作項目提供彈性，讓開發人員定義豐富的物件結構作為動作項目的一部分，或動作項目以外的圖形參考物件。以快取詞彙來說，動作項目可以事後寫入或貫穿式寫入，或我們可以在成員變數的資料粒度使用不同的技術。換句話說，我們擁有保存什麼項目以及何時保存的完整控制權。我們不需要保存暫時性狀態，或是我們可以從已儲存狀態建立的狀態。那麼要如何填入這些動作項目快取呢？ 有數種方式可以達到此目的。動作項目提供虛擬方法稱為 OnActivateAsync() 和 OnDectivateAsync()，讓我們知道動作項目的執行個體何時啟動及停用。請注意，第一個要求傳送給動作項目後，動作項目便會隨選啟動。我們可以使用 OnActivateAsync() 填入隨選狀態與貫穿式讀取，或許是從一個外部穩定存放區。或著，假設匯率動作項目根據最新貨幣匯率提供轉換函式，我們也可以填入計時器的狀態。這個動作項目可以定期從外部服務填入其狀態 (假設為每隔 5 秒)，並使用其狀態轉換函式。請參閱下面的範例：

## 智慧快取程式碼範例 – 速率轉換器

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

基本智慧快取提供：

* 記憶體所要求服務的高輸送量/低延遲。
* 沒有爭用的項目在持續性存放區，所要求的單一執行個體路由和單一執行緒序列化。
* 語意的作業，例如加入佇列 (工作項目)。
* 輕鬆實作貫穿式寫入或事後寫入。
* 自動收回近期使用最少 (Least Recently Used，LRU) 項目 (資源管理)。
* 自動的彈性和可靠性。


## 後續步驟
[模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)

[模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)

[模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png
 

<!---HONumber=July15_HO2-->