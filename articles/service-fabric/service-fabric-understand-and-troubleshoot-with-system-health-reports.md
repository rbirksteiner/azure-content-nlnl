<properties
   pageTitle="使用系統健康情況報告進行疑難排解"
   description="說明系統健康狀態報告，以及如何將它們用於疑難排解叢集或應用程式問題"
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

# 使用系統健康情況報告進行疑難排解

Service Fabric 元件會針對叢集中的所有實體提供現成的報告。[健康狀況存放區](service-fabric-health-introduction.md#health-store)會建立和刪除以系統報告為基礎的實體，並將這些實體組織為階層以擷取實體的互動。

> [AZURE.NOTE]若要了解健康情況相關概念，請詳細閱讀 [Service Fabric 健康情況模型](service-fabric-health-introduction.md)。

系統健康情況報告可讓您全盤掌握叢集和應用程式功能，並透過健康情況標記問題。系統健康情況報告會針對應用程式和服務，確認實體是否已實作，以及從 Service Fabric 的角度來確認其是行為是否正確。報告並不會監控任何服務商務邏輯的健康情況，也不會偵測是否有無回應的處理程序。使用者服務可使用其邏輯的特定資訊讓健康情況資料更豐富。

> [AZURE.NOTE]監視程式健康情況報告只有在系統元件建立實體「之後」才會顯示。刪除實體時，健康情況存放區會自動刪除所有與其相關聯的健康情況報告。建立新實體執行個體時的處理方式亦同 (例如，建立新服務複本執行個體時)：所有與舊執行個體相關聯的報告都會從存放區刪除及清除。

以 *"System."* 前置詞開頭的來源會識別系統元件報告。監視程式不能對來源使用相同的前置詞，因為含有無效參數的報告會被拒絕。讓我們來看看部分系統報告並了解何者觸發它們，以及了解如何修正它們所代表的可能問題。

> [AZURE.NOTE]Service Fabric 會繼續新增條件式興趣的報告，藉此改善叢集/應用程式中狀況的可見性。

## 叢集系統健康情況報告
叢集健康情況實體會自動建立在健康情況存放區中，因此如果一切正常運作，就不會產生系統報告。

### 網路上的芳鄰遺失
當 System.Federation 偵測到網路上的芳鄰遺失時，會回報錯誤。報告來自各個節點，且節點識別碼包含在屬性名稱中。如果整個 Service Fabric 環形中有一個網路上的芳鄰遺失，我們一般會預期有兩個事件產生 (會報告兩端的間距)。如果有多個網路上的芳鄰遺失，會有更多的事件產生。報告會將全域租用逾時指定為 TTL，且只要條件仍在作用中，就會每半個 TTL 重新傳送一次報告。事件過期時將會自動移除，因此如果報告節點關閉，它仍可從健康情況存放區中正確清除。

- 來源識別碼：System.Federation
- 屬性：以 "Neighborhood" 為開頭，且包含節點資訊。
- 後續步驟：調查網路上的芳鄰遺失原因。例如：檢查叢集節點之間的通訊。

## 節點系統健康情況報告
System.FM (代表容錯移轉管理員服務) 是管理叢集節點相關資訊的授權單位。所有節點都應該有一份來自 System.FM 的報告顯示其狀態。當節點停用時，會移除節點的實體。

### 節點運作中/關閉
當節點加入環形時，System.FM 會回報正常 (節點正常運作中)；當節點離開環形時，則會回報錯誤 (節點已關閉進行升級，或故障)。由健康情況存放區建置的健康情況階層會根據 System.FM 節點報告，對部署的實體採取行動。它會將節點視為所有已部署實體的虛擬父系。如果節點關閉、未回報，或擁有與實體相關聯之執行個體相異的執行個體，則該節點上的已部署實體將不會透過查詢公開。當 FM 回報節點已關閉或重新啟動 (新執行個體) 時，健康情況存放區會自動清除僅能在已關閉節點或先前的節點執行個體上存在的已部署實體。

- 來源識別碼：System.FM
- 屬性：狀態
- 後續步驟：如果節點已關閉進行升級，應該在升級後就會重新啟動，而在這種情況下，健康情況狀態應會切換回正常。如果節點沒有重新啟動或故障，便需要進一步調查。

以下項目說明帶有正常健康情況狀態的 System.FM 事件 (適用於運作中節點)：

```powershell

PS C:> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### 憑證到期
當節點所使用的憑證即將到期時，System.FabricNode 會回報警告。每個節點有三個憑證：Certificate_cluster、 Certificate_server 和 Certificate_default_client。如果過期時間至少超過兩週，報告類型就會是正常；如果過期時間在兩週內，則報告類型會是警告。這些事件的 TTL 是無限制的，只有節點離開叢集時才會被移除。

- 來源識別碼：System.FabricNode
- 屬性：以 "Certificate" 為開頭，且包含關於憑證類型的詳細資訊。
- 後續步驟：如果憑證即將到期，請更新憑證。

### 負載容量違規
如果 Service Fabric 負載平衡器偵測到節點容量違規，就會回報警告。

 - 來源識別碼：System.PLB
 - 屬性：以 "Capacity" 為開頭。
 - 後續步驟：檢查提供的度量，並在節點上檢視目前容量。

## 應用程式系統健康情況報告
System.CM (代表叢集管理員服務) 是管理應用程式相關資訊的授權單位。

### 狀況
建立或更新應用程式時，System.CM 會回報正常。刪除應用程式時，它會通知健康情況存放區，以便從存放區移除。

- 來源識別碼：System.CM
- 屬性：狀態
- 後續步驟：如果建立應用程式，它應該會產生 CM 健康情況報告。否則，請發出查詢以檢查應用程式狀態 (例如 Powershell cmdlet Get-ServiceFabricApplication -ApplicationName <applicationName>)。

以下說明 fabric:/WordCount 應用程式上的狀態事件。

```powershell
PS C:> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## 服務系統健康情況報告
System.FM (代表容錯移轉管理員服務) 是管理服務相關資訊的授權單位。

### 狀況
建立服務時，System.FM 會回報正常。刪除服務時，它會從健康情況存放區刪除實體。

- 來源識別碼：System.FM
- 屬性：狀態

以下說明 fabric:/WordCount/WordCountService 服務上的狀態事件。

```powershell
PS C:> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### 未放置的複本違規
如果 System.PLB 無法找到讓一或多個服務複本放置的位置，它會回報警告。當報告過期時會被移除。

- 來源識別碼：System.FM
- 屬性：狀態
- 後續步驟：檢查服務條件約束。檢查位置目前的狀態。

## 分割區系統健康情況報告
System.FM (代表容錯移轉管理員服務) 是管理服務分割區相關資訊的授權單位。

### 狀況
建立分割區且狀況良好時，System.FM 會回報正常。刪除分割區時，它會從健康情況存放區刪除實體。如果分割區低於最小複本計數，它會回報錯誤。如果分割區高於最小複本計數，但低於目標複本計數，則會回報警告。如果分割區處於仲裁遺失狀態，FM 會回報錯誤。其他重要事件：當重新設定花費的時間比預期長，或者建置的時間比預期長，則會回報警告。建置或重新設定的預計時間可根據服務案例設定。例如：如果服務 (如 SQL Azure) 有 TB 的狀態 ，則建置的時間會比小數量狀態的服務長。

- 來源識別碼：System.FM
- 屬性：狀態
- 後續步驟：如果健康情況狀態不是正常，有可能是因為部分複本並沒有正確建立/開啟/提升為主要或次要。在許多情況下，根本原因是在開啟或變更角色實作時發生服務錯誤。

以下顯示狀況良好的分割區。

```powershell
PS C:> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

以下顯示分割區的健康情況低於目標複本計數。後續步驟：取得分割區說明，其中會說明設定方式：MinReplicaSetSize 為 2，TargetReplicaSetSize 為 7。接著取得叢集中的節點數：5。因此在此情況下，不能放置 2 個複本。

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:> @(Get-ServiceFabricNode).Count
5
```

### 複本條件約束違規
如果 System.PLB 偵測到複本條件約束違規，且無法放置分割區複本時，會回報錯誤。

- 來源識別碼：System.PLB
- 屬性：以 "ReplicaConstraintViolation" 為開頭。

## 複本系統健康情況報告
System.RA (代表重新設定代理程式元件) 是複本狀態的授權單位。

### 狀況
建立複本時，System.RA 會回報正常。

- 來源識別碼：System.RA
- 屬性：狀態

以下顯示狀況良好的複本：

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### 複本開啟狀態
叫用 API 呼叫時，此健康情況報告說明會包含開始時間 (UTC)。如果複本開啟所花費的時間超過設定期間 (預設值：30 分鐘)，System.RA 會回報錯誤。如果 API 影響服務可用性，則報告發出速度就會快上許多 (可設定間隔，預設值 30 秒)。這個時間包含複寫器開啟及服務開啟所花費的時間。如果開啟完成，則屬性會變更為正常。

- 來源識別碼：System.RA
- 屬性：ReplicaOpenStatus。
- 後續步驟：如果健康情況狀態不正常，請檢查複本開啟所花費的時間超過預期的原因。

### 緩慢服務 API 呼叫
如果呼叫使用者服務程式碼所花費的時間超過設定的時間，System.RAP 和 System.Replicator 就會回報警告。當呼叫完成時，警告就會被清除。

- 來源識別碼：System.RAP 或 System.Replicator
- 屬性：緩慢 API 的名稱。該說明會提供有關 API 擱置時間的詳細資訊。
- 後續步驟：調查呼叫所花費時間超過預期的原因。

下列範例說明處於仲裁遺失狀態的分割區，以及調查原因時需進行的步驟。若其中一項複本健康情況狀態為警告，我們就會取得其健康情況。它會顯示服務作業所花費的時間超過預期 (由 System.RAP 回報事件)。查看完此資訊後，下一步就是查看服務程式碼並詳加調查。在此情況下，具狀態服務的 RunAsync 實作會擲回未處理的例外狀況。請注意，因為複本會進行回收，所以您可能不會看到任何處於警告狀態的複本。請嘗試重新取得健康情況，然後查看複本識別碼中是否有任何差異，如此可在特定情況下提供您線索。

```powershell
PS C:> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

在偵錯工具中啟動錯誤的應用程式時，[診斷事件] 視窗會顯示從 RunAsync 所擲回的例外狀況：

![Visual Studio 2015 診斷事件：RunAsync 在 fabric:/HelloWorldStatefulApplication 中失敗。][1]

Visual Studio 2015 診斷事件：RunAsync 在 fabric:/HelloWorldStatefulApplication 中失敗。

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### 複寫佇列已滿
如果複寫佇列已滿，System.Replicator 會回報警告。在主要上，會發生此狀況通常是由於一或多個次要複本緩慢而無法認可作業所造成。在次要上，這通常是因為服務緩慢而無法套用作業所造成。當佇列有空間時，警告就會被清除。

- 來源識別碼：System.Replicator
- 屬性：PrimaryReplicationQueueStatus 或 SecondaryReplicationQueueStatus (根據複本角色而定)

## DeployedApplication 系統健康情況報告
System.Hosting 是已部署實體的授權單位。

### 啟用
當應用程式在節點上成功啟用，System.Hosting 就會回報正常；反之如果失敗，則會回報錯誤。

- 來源識別碼：System.Hosting
- 屬性：啟用包含首度發行版本。
- 後續步驟：如果狀況不佳，請調查啟用失敗的原因。

以下說明成功啟用的情況：

```powershell
PS C:> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### 下載
如果應用程式套件下載失敗，System.Hosting 會回報錯誤。

- 來源識別碼：System.Hosting
- 屬性：Download:<RolloutVersion>
- 後續步驟：調查節點上下載失敗的原因。

## DeployedServicePackage 系統健康情況報告
System.Hosting 是已部署實體的授權單位。

### 服務封裝啟用
如果節點上的服務封裝成功啟用，System.Hosting 會回報正常；反之如果失敗，則會回報錯誤。

- 來源識別碼：System.Hosting
- 屬性：啟用
- 後續步驟：調查啟用失敗的原因。

### 程式碼封裝啟用
如果成功啟用，System.Hosting 會針對每個程式碼封裝回報正常。如果啟用失敗，它會依設定回報警告；如果程式碼封裝無法啟用，或者因為錯誤數超過 'CodePackageHealthErrorThreshold' 的設定而結束，則 Hosting 會回報錯誤。如果服務封裝中有多個程式碼封裝，就會針對每個封裝產生啟用報告。

- 來源識別碼：System.Hosting
- 屬性：以 CodePackageActivation 前置詞為開頭，且包含程式碼封裝和進入點名稱。CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>。例如：CodePackageActivation:Code:SetupEntryPoint。

### 服務類型註冊
如果服務類別已成功註冊，System.Hosting 會回報正常。如果註冊未及時完成 (透過 ServiceTypeRegistrationTimeout 設定)，則會回報錯誤。如果服務類型是從節點中取消註冊，則是因為執行階段已關閉。Hosting 就會回報警告。

- 來源識別碼：System.Hosting
- 屬性：以 ServiceTypeRegistration 前置詞為開頭，且包含服務類型名稱。例如"ServiceTypeRegistration:FileStoreServiceType"

以下顯示狀況良好的已部署服務封裝。

```powershell
PS C:> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### 下載
如果服務封裝下載失敗，System.Hosting 會回報錯誤。

- 來源識別碼：System.Hosting
- 屬性：Download:<RolloutVersion>
- 後續步驟：調查節點上下載失敗的原因。

### 升級驗證
如果驗證在升級期間失敗，或者節點上的升級失敗，System.Hosting 會回報錯誤。

- 來源識別碼：System.Hosting
- 屬性：以 FabricUpgradeValidation 前置詞為開頭，包含升級版本。
- 說明：指向發生的錯誤。

## 後續步驟
[如何檢視 Service Fabric 健康情況報告](service-fabric-view-entities-aggregated-health.md)

[如何在本機上監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->