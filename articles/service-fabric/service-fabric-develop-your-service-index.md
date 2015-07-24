<properties
   pageTitle="開發 Service Fabric 服務"
   description="概念資訊和教學課程可協助您了解如何開發使用可靠動作項目或可靠服務程式設計模型的 Service Fabric 服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/14/2015"
   ms.author="ryanwi"/>

# 開發 Service Fabric 服務
此頁面包含概觀、概念文章和教學課程的連結，可協助您了解如何開發 Service Fabric 服務。Service Fabric 提供兩個高階程式設計模型，可用來建置以下服務：可靠動作項目 API 和可靠服務 API。雖然兩者都建置於相同的 Service Fabric 核心，但會在並行處理、資料分割以及通訊方面的簡易性和彈性之間進行不同的權衡。了解這兩個模型相當實用，以便您可以在應用程式中選擇特定服務的適當架構。

- [選擇程式設計模型](service-fabric-choose-framework.md)
- [Service Fabric 動作項目模型簡介](service-fabric-reliable-actors-introduction.md)
- [可靠服務程式設計模型簡介](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## 可靠動作項目程式設計模型
 可靠動作項目可提供一種非同步、單一執行緒的動作項目模型。動作項目代表分散在叢集中的狀態和計算單位，藉以達到高延展性。可靠動作項目模型會利用由基礎 Service Fabric 平台所提供的分散式存放區，針對應用程式開發人員提供高可用性且一致的狀態管理。若要深入了解，請閱讀：

- [開始使用可靠動作項目](service-fabric-reliable-actors-get-started.md)
- [動作項目生命週期和記憶體回收](service-fabric-reliable-actors-lifecycle.md)
- [Fabric Actor 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)
- [Azure Service Fabric Actor 類型序列化的注意事項](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

與動作項目通訊的說明如下所述：

- [Service Fabric Actor 模型簡介](service-fabric-reliable-actors-introduction.md#actor-communication)。
- [與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)

這些文章將討論實用的設計模式和案例：

- [動作項目模型設計模式](service-fabric-reliable-actors-patterns-introduction.md)  
- [模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)
- [模式：分散式網路和圖形](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)
- [模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

可靠動作項目方法所提供的簡易回合式並行存取。並行處理、計時器和提醒以及重新進入的說明如下列這些文章所述：

- [並行](service-fabric-reliable-actors-introduction.md#concurrency)
- [與並行相關的事件和效能計數器](service-fabric-reliable-actors-diagnostics.md)
- [動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)
- [動作項目計時器](service-fabric-reliable-actors-timers-reminders.md)
 
您可以在這裡找到設定可靠動作項目相關資訊：

- [KVSActorStateProvider 組態](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [設定可靠動作項目 - ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

可靠動作項目會發出事件和效能計數器，可用來診斷和監視您的服務：

- [動作項目診斷](service-fabric-reliable-actors-diagnostics.md)
- [動作項目事件](service-fabric-reliable-actors-events.md)


## 可靠服務程式設計模型
可靠服務提供您簡易、功能強大、最高階的程式設計模型，以協助您針對應用程式的重要項目進行表達。若要深入了解，請閱讀：

- [開始使用可靠服務](service-fabric-reliable-services-quick-start.md)
- [程式設計模型概觀](../service-fabric-reliable-services-service-overview.md)  
- [架構](service-fabric-reliable-services-platform-architecture.md)
- [可靠集合](service-fabric-reliable-services-reliable-collections.md)
- [設定可設定狀態可靠服務](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [可靠服務程式設計模型進階用法](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

透過與可靠服務和抽象層進行通訊，用戶端可以用來探索並與服務端點進行通訊，如下所述：

- [與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)
- [服務通訊模型](service-fabric-reliable-services-communication.md)
- [可靠服務架構所提供的預設通訊堆疊](service-fabric-reliable-services-communication-default.md)
- [適用於可靠服務的 WCF 式通訊堆疊](service-fabric-reliable-services-communication-wcf.md)
- [開始使用 Microsoft Azure Service Fabric Web API 服務搭配 OWIN 自我裝載 (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

可靠服務會發出事件和效能計數器，可用來診斷和監視您的服務：

- [可設定狀態可靠服務診斷](service-fabric-reliable-services-diagnostics.md)
 

<!---HONumber=July15_HO2-->