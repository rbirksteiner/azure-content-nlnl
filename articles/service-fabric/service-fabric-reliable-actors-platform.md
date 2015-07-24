<properties
   pageTitle="網狀架構動作項目如何使用 Service Fabric 平台"
   description="此文章說明網狀架構動作項目如何使用 Service Fabric 平台的功能。從行動項目開發人員的觀點，涵蓋 Service Fabric 平台的概念。"
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
   ms.date="04/23/2015"
   ms.author="abhisram"/>

# 網狀架構動作項目如何使用 Service Fabric 平台

## 動作項目的 Service Fabric 應用程式模型概念
動作項目會使用 Service Fabric 應用程式模型管理應用程式的生命週期。每個動作項目類型皆對應至一個 Service Fabric [服務類型](service-fabric-application-model.md#describe-a-service)。動作項目程式碼是 [封裝](service-fabric-application-model.md#package-an-application)為Service Fabric 應用程式和 [部署](service-fabric-deploy-remove-applications.md#deploy-an-application)至叢集。

讓我們看看[使用 Visual Studio 建立](service-fabric-reliable-actors-get-started.md)的動作項目專案範例，來說明上述概念。

在 Visual Studio 中建立解決方案後，應用程式資訊清單、服務資訊清單和 Settings.xml 組態檔將包含在動作項目服務專案。如以下螢幕擷取畫面所示。

![][1]

動作項目所封裝的應用程式類型及其版本，可藉由查看包含在動作項目服務專案的應用程式資訊清單中找到。下列程式碼片段便是來自應用程式資訊清單的範例。

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

動作項目類型所對應的服務類型，可藉由查看包含在動作項目服務專案的服務資訊清單中找到。下列程式碼片段便是來自服務資訊清單的範例。

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

使用 Visual Studio 建立應用程式封裝時，[組建輸出] 視窗中的記錄檔會指出應用程式封裝的位置。例如：

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

以下是上述位置的部分清單 (為了簡潔故省略完整清單)：

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───zh-tw
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───zh-tw
                    System.Fabric.Common.Internal.Strings.resources.dll

上述清單中顯示實作 VoicemailBox 動作項目的組件，將包含在應用程式封裝的服務封裝中程式碼封裝內。

Visual Studio 解決方案包含用來部署應用程式和從叢集移除應用程式的 PowerShell 指令碼。指令碼已圈選於以下螢幕擷取畫面。

![][2]

應用程式的後續管理 (也就是升級和最終刪除)，也是使用 Service Fabric 應用程式管理機制執行。如需詳細資訊，請參閱主題＜[應用程式模型](service-fabric-application-model.md)＞、＜[應用程式部署和移除](service-fabric-deploy-remove-applications.md)＞，和＜[應用程式升級](service-fabric-application-upgrade.md)＞。

## 動作項目服務的可調整性
叢集系統管理員可以在叢集中為每個服務類型建立一個或多個動作項目服務。每個動作項目服務可以有一個或多個資料分割 (類似任何其他 Service Fabric 服務)。為多個服務建立服務類型的能力 (這會對應到動作項目類型) 和為服務建立多個資料分割的能力，可讓動作項目應用程式進行調整。請參閱＜[延展性](service-fabric-concepts-scalability.md)＞一文以取得詳細資訊。

> [AZURE.NOTE]無狀態動作項目服務都需要有 1 的 [執行個體](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)計數。不支援資料分割中的一個無狀態動作項目服務有多個執行個體。因此，無狀態動作項目服務無法選擇增加執行個體計數來達成延展性。他們必須使用[延展性文章](service-fabric-concepts-scalability.md)所述的延展性選項。

## 動作項目的 Service Fabric 資料分割概念
動作項目的動作項目識別碼會對應至動作項目服務的資料分割。動作項目建立於動作項目識別碼所對應的資料分割中。建立動作項目時，動作項目執行階段會寫入 [EventSource 事件](service-fabric-reliable-actors-diagnostics.md#eventsource-events)，指出動作項目會建立在哪個資料分割。以下是此事件的範例，將指出有識別碼 `-5349766044453424161` 的動作項目建立於服務 `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService` 與應用程式 `fabric:/VoicemailBoxAdvancedApplication` 的資料分割 `0583c745-1bed-43b2-9545-29d7e3448156`。

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

另一個有識別碼 `-4952641569324299627` 的動作項目則建立於相同服務但不同的資料分割 `c146fe53-16d7-4d96-bac6-ef54613808ff`，如下列事件指出。

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*附註：*為求簡潔而省略上述事件的一些欄位。

資料分割識別碼可用來取得資料分割的其他資訊。例如，[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 工具可用來檢視其所屬的資料分割、服務和應用程式的相關資訊。以下螢幕擷取畫面顯示資料分割的相關資訊 `c146fe53-16d7-4d96-bac6-ef54613808ff`，其中包含有識別碼 `-4952641569324299627` 的動作項目，如上述範例中所示。

![][3]

動作項目可以由程式設計方式取得資料分割識別碼、服務名稱、應用程式名稱和其他 Service Fabric 的平台特定資訊，透過 `Host.ActivationContext` 和 `Host.StatelessServiceInitialization` 或 `Host.StatefulServiceInitializationParameters` 動作項目類型衍生自基底類別的成員。下列程式碼片段將顯示一個範例：

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### 無狀態動作項目的 Service Fabric 資料分割概念
無狀態動作項目是由 Service Fabric 無狀態服務的資料分割所建立。動作項目識別碼可以判斷動作項目會建立在哪個資料分割底下。無狀態動作項目服務的[執行個體](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)計數必須是 1。不支援將執行個體計數變更為其他任何值。因此，動作項目會在資料分割內部的單一服務執行個體中建立。

> [AZURE.TIP]網狀架構動作項目執行階段會發出一些[無狀態動作項目執行個體的相關事件](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances)。這些項目對於診斷與效能監視很有幫助。

無狀態動作項目建立時，動作項目執行階段會寫入一個 [EventSource 事件](service-fabric-reliable-actors-diagnostics.md#eventsource-events)，並指出動作項目建立於哪些資料分割和執行個體。以下是此事件的範例，將指出有識別碼 `abc` 的動作項目建立於服務 `fabric:/HelloWorldApplication/HelloWorldActorService` 與應用程式 `fabric:/HelloWorldApplication` 的資料分割 `8c828833-ccf1-4e21-b99d-03b14d4face3` 中的執行個體 `130745709600495974`。

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*附註：*為求簡潔而省略上述事件的一些欄位。

### 可設定狀態的動作項目的 Service Fabric 資料分割概念
可設定狀態的動作項目是由 Service Fabric 具狀態服務的資料分割所建立。動作項目識別碼可以判斷動作項目會建立在哪個資料分割底下。服務的每個資料分割可以有一個或多個 [複本](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)，放置在叢集中不同節點上。擁有多個複本可為動作項目狀態提供可靠性。資源管理員會根據叢集中可用的錯誤和升級網域，來最佳化放置的位置。相同資料分割的兩個複本永遠不會放在相同節點上。動作項目識別碼所對應的資料分割主要複本，永遠都會建立動作項目。

> [AZURE.TIP]網狀架構動作項目執行階段會發出一些[可設定狀態的動作項目執行個體的相關事件](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas)。這些項目對於診斷與效能監視很有幫助。

請回想在[稍早所呈現的 VoiceMailBoxActor 範例中](#service-fabric-partition-concepts-for-actors)，有識別碼 `-4952641569324299627` 的動作項目是在資料分割 `c146fe53-16d7-4d96-bac6-ef54613808ff` 內所建立。該範例的 EventSource 事件也指出動作項目建立在該資料分割的複本 `130745418574851853`。這是建立動作項目的時，該資料分割的主要複本。下列 Service Fabric Explore 螢幕擷取畫面進一步確認這一點。

![][4]

## 動作項目狀態提供者選項
動作項目執行階段中包含一些預設的動作項目狀態提供者。若要為動作項目服務選擇適當的狀態提供者，就必須了解狀態提供者如何使用基礎服務網狀架構平台功能，讓動作項目狀態高度可用。

依預設，可設定狀態的動作項目會使用索引鍵值存放區動作項目狀態提供者。此狀態提供者建置在由 Service Fabric 平台提供的分散式索引鍵值存放區之基礎上。該狀態永久儲存在裝載主要[複本](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)節點的本機磁碟上，和複寫及永久儲存在裝載次要複本節點的本機磁碟上。只有複本仲裁將其狀態認可至本機磁碟時，狀態才完成儲存。索引鍵值存放區具有進階功能，可以偵測到不一致，例如為 False 的進度並自動加以更正。

動作項目執行階段也包含 `VolatileActorStateProvider`。此狀態提供者會將狀態複寫到複本，但狀態仍會保留在記憶體中複本上。如果一個複本中斷和恢復運作，其狀態會從另一個複本重建。但是如果所有複本 (狀態複本) 同時當機，狀態資料將會遺失。因此，此狀態提供者是適用於應用程式資料可以安然度過幾個複本的失敗，並且可以安然度過計劃的容錯移轉，例如升級。如果所有複本都遺失，資料需要使用非 Service Fabric 的外部機制來重建。您可以設定具狀態的動作項目使用變動性動作項目狀態提供者，透過加入 `VolatileActorStateProvider` 屬性到動作項目類別或組件層級屬性。

下列程式碼片段會示範如何為所有組件中的動作項目，變更沒有明確的狀態提供者屬性，使用 `VolatileActorStateProvider`。

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

下列程式碼片段示範如何將特定動作項目類型的狀態提供者，在此例中為 `VoicemailBox`，變更為 `VolatileActorStateProvider`。

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

請注意，變更狀態提供者需要重建動作項目服務。狀態提供者不能作為應用程式升級的一部分進行變更。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
 

<!---HONumber=July15_HO2-->