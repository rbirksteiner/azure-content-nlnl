<properties
   pageTitle="Azure Service Fabric 動作項目事件"
   description="Azure Service Fabric Actor 事件簡介。"
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


# 動作項目事件
動作項目事件會將最佳效果通知從動作項目傳送到用戶端。動作項目事件是為了動作項目與用戶端之間的通訊而設計，「不」應用於動作項目與動作項目之間的通訊。

下列程式碼片段顯示如何在應用程式中使用動作項目事件。

定義描述動作項目所發佈事件的介面。此介面必須衍生自 `IActorEvents` 介面。方法的引數必須是[資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。當事件通知是單向且為最佳效果時，方法必須傳回無效。

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

宣告由動作項目介面中動作項目發佈的事件。

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    [Readonly]
    Task<string> GetGameScore();
}
```

在用戶端上，實作事件處理常式。

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

在用戶端上，對發佈事件的動作項目建立 Proxy，並訂閱事件。

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);
proxy.SubscribeAsync(new GameEventsHandler()).Wait();
```

發生容錯移轉時，動作項目會容錯移轉至不同的程序或節點。動作項目 Proxy 會管理使用中的訂用帳戶，並自動重新訂閱。您可以透過 `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API 控制重新訂閱間隔。若要取消訂閱，請使用 `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API。

在動作項目上，當事件發生時只發佈事件。如果有訂閱者訂閱事件，動作項目執行階段會將事件傳送至通知。

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), State.Status.Score);
```
 

<!---HONumber=July15_HO2-->