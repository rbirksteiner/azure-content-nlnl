<properties
   pageTitle="如何檢視 Azure Service Fabric 實體的彙總健康情況"
   description="說明如何透過健康情況查詢和一般查詢，來查詢、檢視和評估 Azure Service Fabric 實體的彙總健康情況。"
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# 如何檢視 Service Fabric 健康情況報告
Service Fabric 導入了由健康情況實體組成的[健康情況模型](service-fabric-health-introduction.md)，系統元件和監視程式可以向健康情況實體報告它們所監視的本機情況。[健康情況存放區](service-fabric-health-introduction.md#health-store) 會彙總所有健康情況資料，以判斷實體是否狀況良好。

根據預設，會將系統元件傳送的健康情況報告填入叢集。如需詳細資訊，請參閱[使用系統健康情況報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)。

Service Fabric 會提供多種方式，以取得實體彙總健康情況：

- [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)或其他視覺效果工具

- 健康情況查詢 (透過 Powerhsell/API/REST)

- 一般查詢，此查詢會傳回一份實體清單，這些實體的其中一個屬性即為健康情況 (透過 Powershell/API/REST)

為了示範這些選項，我們會使用具有 5 個節點的本機叢集。預設中便存在的 **fabric:/System** 應用程式旁邊，有一些已部署的其他應用程式，其中一個是 **fabric:/WordCount**。此應用程式包含具狀態服務，且已設有 7 個複本。因為僅有 5 個節點，所以系統元件會將低於目標計數的分割區加上「警告」旗標。

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Service Fabric 總管中的健康情況
Service Fabric 總管提供叢集的視覺化檢視。在下圖中，您可以看到：

- 應用程式 **fabric:/WordCount** 是「紅色」(「錯誤」狀態)，因為它有 MyWatchdog 所回報的屬性「可用性」錯誤事件。

- 應用程式的其中一個服務 **fabric:/WordCount/WordCount.Service** 是「黃色」(「警告」狀態)。如上所述，服務已設有 7 個複本，但不能都放上去，因為我們只有 5 個節點。雖然這裡沒有顯示，但因為系統報告的緣故，所以服務分割區為「黃色」。「黃色」分割區觸發了「黃色」服務。

- 因為應用程式是「紅色」，所以叢集為「紅色」。

評估會使用叢集資訊清單和應用程式資訊清單中的預設原則，也就是嚴格的原則 (不容許任何失敗)。

![Service Fabric 總管的叢集檢視。][1]

Service Fabric 總管的叢集檢視。

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]深入了解 [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)。

## 健康情況查詢
Service Fabric 會針對每種支援的[實體類型](service-fabric-health-introduction.md#health-entities-and-hierarchy)公開健康情況查詢。您可透過 API (FabricClient.HealthManager 上的方法)、Powershell Cmdlet 和 REST 來存取查詢。這些查詢會傳回實體的完整健康情況資訊，包括彙總健康情況狀態、回報的實體健康情況事件、子健康情況狀態 (如果適用)，以及實體狀況不佳時的健康情況不佳評估。

> [AZURE.NOTE]系統會在健康情況實體完全填入健康情況存放區時，將實體傳回給使用者：也就是有系統報告的實體，舉凡階層鏈結上的作用中 (未刪除) 及父實體均有系統報告。如果無法達成上述任何條件，健康情況查詢會傳回顯示為何不傳回實體的例外狀況。

健康情況查詢需要傳入的實體識別碼，識別碼會視實體類型而定。查詢會接受選擇性的健康情況原則參數。如果未指定，會使用叢集或應用程式資訊清單的[健康情況原則](service-fabric-health-introduction.md#health-policies)進行評估。查詢也接受只傳回部分事件或子事件的篩選，這些事件與所指定的篩選相符。

> [AZURE.NOTE]輸出篩選會套用在伺服器端，所以訊息回覆的大小會減少。建議使用篩選來限制傳回的資料，而不是在用戶端上套用篩選。

實體健康情況含有下列資訊：

- 實體的彙總健康情況狀態。這是健康情況存放區根據實體健康情況報告、子健康情況狀態 (如果適用) 和健康情況狀態原則而得出的結果。深入了解[實體健康情況評估](service-fabric-health-introduction.md#entity-health-evaluation)。  

- 實體上的健康情況事件。

- 針對可以有子系的實體，提供所有子系的健康情況狀態集合。健康情況狀態包含實體識別碼和彙總健康情況狀態。若要取得子系的完整健康情況，請傳入子系識別碼，呼叫子實體類型的查詢健康情況。

- 如果實體的狀況不佳，則健康情況不佳的評估會指向觸發實體狀態的報告。

## 取得叢集健康情況
傳回叢集實體的健康情況。包含應用程式和節點 (叢集的子系) 的健康情況狀態。輸入：

- [選擇性] 應用程式健康情況原則對應，加上用來覆寫應用程式資訊清單原則的健康情況原則。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件、節點及應用程式 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要取得叢集健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetClusterHealthAsync 方法。

以下方法可取得叢集健康情況：

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

以下方法使用自訂的叢集健康情況原則和針對節點與應用程式的篩選，來取得叢集健康情況。請注意，這會建立包含所有輸入資料的 System.Fabric.Description.ClusterHealthQueryDescription。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
取得叢集健康情況的 Cmdlet 是 Get-ServiceFabricClusterHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。叢集的狀態：5 個節點、系統應用程式和如上述所設定的 fabric:/WordCount。

以下 Cmdlet 會使用預設的健康情況原則，取得叢集健康情況。彙總健康情況狀態為「警告」，因為 fabric:/WordCount 應用程式處於「警告」狀態。請注意健康情況不佳的評估，會如何詳細顯示觸發彙總健康情況的條件。

```xml
PS C:> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

以下 Powershell Cmdlet 會使用自訂的應用程式原則，取得叢集的健康情況。它會篩選結果，以取得只有「錯誤」或「警告」的應用程式和節點。如此一來，就不會傳回任何狀況良好的節點。只有 fabric:/WordCount 應用程式符合應用程式篩選。由於自訂原則針對 fabric:/WordCount 應用程式，指定將警告視為錯誤，因此應用程式以及叢集，均被評估為「錯誤」。

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## 取得節點的健康情況
傳回節點實體的健康情況。包含報告的節點健康情況事件。輸入：

- [必要] 可識別節點的節點名稱。

- [選擇性] 用來評估健康情況的叢集健康情況原則設定。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要透過 API 取得節點健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetNodeHealthAsync 方法。

以下方法會取得指定節點名稱的節點健康情況。

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

以下方法會透過 System.Fabric.Description.NodeHealthQueryDescription 傳入事件篩選和自訂原則，取得指定節點名稱的節點健康情況。

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
取得節點健康情況的 Cmdlet 是 Get-ServiceFabricNodeHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。以下 Cmdlet 會使用預設的健康情況原則，取得節點健康情況。

```powershell
PS C:> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

以下 Cmdlet 會取得叢集中所有節點的健康情況。

```powershell
PS C:> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## 取得應用程式健康情況
傳回應用程式實體的健康情況。包含已部署應用程式和服務子系的健康情況狀態。輸入：

- [必要] 可識別應用程式的應用程式名稱 (URI)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件、服務和已部署應用程式 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要取得應用程式健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetApplicationHealthAsync 方法。

以下方法會取得指定應用程式名稱 URI 的應用程式健康情況。

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

以下方法會透過 System.Fabric.Description.ApplicationHealthQueryDescription 指定篩選和自訂原則，取得指定應用程式名稱 URI 的應用程式健康情況。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
取得應用程式健康情況的 Cmdlet 是 Get-ServiceFabricApplicationHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。

以下 Cmdlet 會傳回 fabric:/WordCount 應用程式的健康情況。

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

以下 Powershell 會傳入自訂原則，並篩選子系和事件。

```powershell
PS C:> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## 取得服務健康情況
傳回服務實體的健康情況。包含分割區的健康情況狀態。輸入：

- [必要] 可識別服務的服務名稱 (URI)。
- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。
- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件和分割區 (例如只傳回錯誤，或者傳回錯誤或警告等等)。

### API
若要透過 API 取得服務健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetServiceHealthAsync 方法。

以下範例會使用指定的服務名稱 (URI)，取得服務的健康情況：

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

以下方法會透過 System.Fabric.Description.ServiceHealthQueryDescription 指定篩選和自訂原則，取得指定服務名稱 URI 的服務健康情況。

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
取得服務健康情況的 Cmdlet 是 Get-ServiceFabricServiceHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。

以下 Cmdlet 會使用預設的健康情況原則，取得服務健康情況。

```powershell
PS C:> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## 取得分割區健康情況
傳回分割區實體的健康情況。包含複本的健康情況狀態。輸入：

- [必要] 可識別分割區的分割識別碼 (GUID)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件和複本 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要透過 API 取得分割區健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetPartitionHealthAsync 方法。若要指定選擇性參數，請建立 System.Fabric.Description.PartitionHealthQueryDescription。

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
取得分割區健康情況的 Cmdlet 是 Get-ServiceFabricPartitionHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。

以下 Cmdlet 會取得字數統計服務之所有分割區的健康情況。

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## 取得複本健康情況
傳回複本的健康情況。輸入：

- [必要] 可識別複本的分割識別碼 (GUID) 和複本識別碼。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則參數。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要透過 API 取得複本健康情況，請建立 FabricClient，並在其 HealthManager 上呼叫 GetReplicaHealthAsync 方法。使用 System.Fabric.Description.ReplicaHealthQueryDescription 指定進階參數。

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
取得複本健康情況的 Cmdlet 是 Get-ServiceFabricReplicaHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。

以下 Cmdlet 會針對服務的所有分割區取得主要複本健康情況。

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 取得已部署應用程式的健康情況
傳回部署在節點實體上的應用程式健康情況。包含已部署的服務封裝健康情況狀態。輸入：

- [必要] 可識別已部署應用程式的應用程式名稱 (URI) 和節點名稱 (字串)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件和已部署的服務封裝 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要透過 API 取得部署在節點上之應用程式的健康情況，請建立 FabricClient 並在其 HealthManager 上呼叫 GetDeployedApplicationHealthAsync 方法。若要指定選擇性參數，請建立 System.Fabric.Description.DeployedApplicationHealthQueryDescription。

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
取得已部署應用程式健康情況的 Cmdlet 是 Get-ServiceFabricDeployedApplicationHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。若要找出應用程式的部署位置，請執行 Get-ServiceFabricApplicationHealth，並查看部署的應用程式子系。

以下 Cmdlet 會取得部署在 Node.1 節點上的 fabric:/WordCount 應用程式健康情況。

```powershell
PS C:> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 取得已部署服務封裝的健康情況
傳回已部署服務封裝實體的健康情況。輸入：

- [必要] 可識別已部署之服務封裝的應用程式名稱 (URI)、節點名稱 (字串) 及服務資訊清單名稱 (字串)。

- [選擇性] 用來覆寫應用程式資訊清單原則的應用程式健康情況原則。

- [選擇性] 篩選以便只傳回具有特定健康情況狀態的事件 (例如只傳回錯誤，或者傳回錯誤或警告等)。

### API
若要透過 API 取得已部署服務封裝的健康情況，請建立 FabricClient 並在其 HealthManager 上呼叫 GetDeployedServicePackageHealthAsync 方法。

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
取得已部署服務封裝健康情況的 Cmdlet 是 Get-ServiceFabricDeployedServicePackageHealth。首先會使用 Connect-ServiceFabricCluster Cmdlet 連接到叢集。若要查看應用程式的部署位置，請執行 Get-ServiceFabricApplicationHealth，查看部署的應用程式。若要查看應用程式中的服務封裝為何，請檢視 Get-ServiceFabricDeployedApplicationHealth 輸出中已部署服務封裝的子系。

以下 Cmdlet 會取得部署在 Node.1 節點上 fabric:/WordCount 應用程式的 WordCount.Service 服務封裝健康情況。此實體的 System.Hosting 報告具有成功的服務封裝和進入點啟用，以及成功的服務類型註冊。

```powershell
PS C:> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 一般查詢
一般查詢會傳回指定類型的 Service Fabric 實體清單。這些查詢會透過 API (FabricClient.QueryManager 上的方法)、Powershell Cmdlet 和 REST 來公開。這些查詢會從多個元件彙總子查詢。其中一個元件是 [健康情況存放區](service-fabric-health-introduction.md#health-store)，它會針對每個查詢結果填入彙總健康情況狀態。

> [AZURE.NOTE]一般查詢會傳回實體的彙總健康情況狀態，但不包含豐富的健康情況資料。如果實體狀況不佳，您可以使用健康情況查詢加以追蹤，以取得所有的健康情況資訊，例如事件、子系健康情況狀態和健康情況不佳的評估。

如果一般查詢傳回實體的「未知」健康情況狀態，則可能是健康情況存放區沒有實體的完整資料，或是對健康情況存放區的子查詢失敗 (例如通訊錯誤、健康情況存放區已節流等等)。請針對實體使用健康情況查詢加以追蹤。如果子查詢發生暫時性錯誤 (例如網路問題)，這麼做就可能會成功，或是更詳細說明為何未從健康情況存放區公開實體。

包含實體的 HealthState 的查詢為：

- 節點清單。傳回叢集中的節點清單。
  - API：FabricClient.QueryManager.GetNodeListAsync。
  - Powershell：Get-ServiceFabricNode。
- 應用程式清單。傳回叢集中的應用程式清單。
  - API：FabricClient.QueryManager.GetApplicationListAsync。
  - Powershell：Get-ServiceFabricApplication。
- 服務清單。傳回應用程式中的服務清單。
  - API：FabricClient.QueryManager.GetServiceListAsync。
  - Powershell：Get-ServiceFabricService。
- 分割區清單。傳回服務中的分割區清單。
  - API：FabricClient.QueryManager.GetPartitionListAsync。
  - Powershell：Get-ServiceFabricPartition。
- 複本清單。傳回分割區中的複本清單。
  - API：FabricClient.QueryManager.GetReplicaListAsync。
  - Powershell：Get-ServiceFabricReplica。
- 已部署應用程式的清單。傳回節點上已部署應用程式的清單。
  - API：FabricClient.QueryManager.GetDeployedApplicationListAsync。
  - Powershell：Get-ServiceFabricDeployedApplication。
- 已部署服務封裝的清單。傳回已部署應用程式中的服務封裝清單。
  - API：FabricClient.QueryManager.GetDeployedServicePackageListAsync。
  - Powershell：Get-ServiceFabricDeployedApplication。

### 範例

以下方法會取得叢集中健康情況不佳的應用程式：

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

以下 Cmdlet 會取得 fabric:/WordCount 應用程式的應用程式詳細資料。請注意，健康情況狀態為「警告」。

```powershell
PS C:> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

以下 Cmdlet 會取得健康情況狀態為「警告」的服務。

```powershell
PS C:> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## 叢集和應用程式升級
受監視的叢集與應用程式在升級期間，Service Fabric 會檢查健康情況，以確保一切都能維持在健康情況良好的狀態。如果依據設定的原則發現狀況不佳的情形，則升級會暫停，以允許使用者互動或自動回復。

您可以在叢集升級期間，取得叢集升級狀態，其中包含的健康情況不佳評估會指向叢集中狀況不佳的項目。如果因為健康情況問題使升級遭到回復，升級狀態會保留最後一批狀況不佳的原因，讓管理員可以調查問題出在哪裡。同樣地，在應用程式升級期間，應用程式升級狀態也會含有健康情況不佳的評估。

以下顯示修改後的 fabric:/WordCount 應用程式的應用程式升級狀態。監視程式回報了它其中一個複本的錯誤。因為健康情況檢查未通過，所以會將升級回復。

```powershell
PS C:> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

深入了解 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md)。

## 健康情況疑難排解
叢集或應用程式中發生問題時，查看叢集或應用程式的健康情況，可以找出發生問題的原因。健康情況不佳的評估會詳細顯示觸發目前狀況不佳狀態的原因。您可視需要深入調查狀況不佳的子系實體以找出問題。

## 後續步驟
[使用系統健康情況報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[新增自訂 Service Fabric 健康情況報告](service-fabric-report-health.md)

[如何在本機上監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->