<properties
   pageTitle="疑難排解 Service Fabric 應用程式升級"
   description="本文涵蓋升級 Service Fabric 應用程式的一些常見問題，以及解決方式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/24/2015"
   ms.author="subramar"/>

# 疑難排解應用程式升級

本文涵蓋升級 Service Fabric 應用程式的一些常見問題，以及解決方式。

## 疑難排解失敗的應用程式升級

當升級失敗時，**Get-ServiceFabricApplicationUpgrade** 命令的輸出會包含一些偵錯失敗的其他資訊。這項資訊可用來：

1. 識別失敗類型
2. 識別失敗原因
3. 隔離失敗元件以便進一步調查

只要 Service Fabric 偵測到失敗，就會提供此資訊，不論 **FailureAction** 是回復或暫停升級。

### 識別失敗類型

在 **Get-ServiceFabricApplicationUpgrade** 的輸出中，**FailureTimestampUtc** 會識別 Service Fabric 偵測升級失敗以及觸發 **FailureAction** 的時間戳記 (UTC)。**FailureReason** 會識別失敗的三個可能高階原因之一：

1. UpgradeDomainTimeout - 指出特定的升級網域花太多時間完成且 **UpgradeDomainTimeout** 過期。
2. OverallUpgradeTimeout - 指出整體升級花太多時間完成且 **UpgradeTimeout** 過期。
3. HealthCheck - 指出升級網域升級之後，根據指定的健康狀態原則，應用程式仍然健康狀態不良，且 **HealthCheckRetryTimeout** 過期。

這些項目只有在升級失敗且啟動回復時，才會出現在輸出中。視失敗的類型而定，將會顯示進一步的資訊。

### 調查升級逾時

升級逾時失敗通常是由服務可用性問題造成的。以下輸出是典型的升級，其中服務複本或執行個體無法在新的程式碼版本中啟動。**UpgradeDomainProgressAtFailure** 欄位在失敗時擷取任何擱置中升級工作的快照集。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

在此範例中，我們可以看到升級網域 *MYUD1* 的升級失敗，兩個磁碟分割 (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* 和 *4b43f4d8-b26b-424e-9307-7a7a62e79750*) 已停滯，無法將主要複本 (*WaitForPrimaryPlacement*) 放在目標節點 *Node1* 和 *Node4* 上。**Get-ServiceFabricNode** 命令可以用來確認這兩個節點都在升級網域 *MYUD1* 中。*UpgradePhase* 指出 *PostUpgradeSafetyCheck*，表示這些安全檢查是在升級網域中的所有節點完成升級之後發生。綜合這些資訊，指向應用程式程式碼新版本的潛在問題。最常見的問題是開啟或升級至主要程式碼路徑的服務錯誤。

*PreUpgradeSafetyCheck* 的 *UpgradePhase* 表示在實際執行升級之前準備升級網域有問題。此案例中最常見的問題是關閉主要程式碼路徑或從其中降級的服務錯誤。

目前的 **UpgradeState** 是 *RollingBackCompleted*，因此原始升級必須以回復 **FailureAction** 執行，這樣會自動在失敗時回復升級。如果原始升級以手動 **FailureAction** 執行，則升級會處於暫止狀態，以允許應用程式的即時偵錯。

### 調查健康狀態檢查失敗

升級網域中的所有節點完成升級，通過所有安全檢查之後，可以由各種其他問題觸發健康狀態檢查失敗。以下輸出是由於失敗的健康狀態檢查的典型升級失敗。**UnhealthyEvaluations** 欄位會根據使用者指定的[健康狀態原則](service-fabric-health-introduction.md)，擷取升級失敗時所有失敗的健康狀態檢查的快照集。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

調查健康狀態檢查失敗首先需要了解 Service Fabric 健康狀態模型，但是即使沒有這類深入了解，我們可以看到兩個服務的健康狀態不良：*fabric:/DemoApp/Svc3* 和 *fabric:/DemoApp/Svc2*，以及錯誤健康狀態報告 (在本例中為"InjectedFault")。在此範例中，4 個服務中有 2 個服務健康狀態不良，低於預設目標 0% 健康狀態不良 (*MaxPercentUnhealthyServices*)。

升級在失敗時暫止，方法是在啟動升級時指定手動 **FailureAction**，因此我們可以在採取任何進一步的動作之前，視需要調查失敗狀態中的即時系統。

### 從暫止升級復原

使用回復 **FailureAction**，因為升級會自動在失敗時回復，所以不需要復原。使用手動 **FailureAction**，有數個復原選項：

1. 手動觸發回復
2. 以手動方式繼續進行升級的其餘部分
3. 繼續監視的升級

**Start-ServiceFabricApplicationRollback** 命令可以在任何時候用來啟動回復應用程式。此命令成功傳回時，回復要求即已在系統中註冊，馬上就會啟動。

**Resume-ServiceFabricApplicationUpgrade** 命令可以用來以手動方式繼續升級的其餘部分，一次一個升級網域。在此模式中，系統只會執行安全檢查 - 不會執行其他健康狀態檢查。此命令只能在 *UpgradeState* 顯示 *RollingForwardPending* 時使用，表示目前升級網域已完成升級，但是下一個升級網域尚未啟動 (擱置中)。

**Update-ServiceFabricApplicationUpgrade** 命令可以用來繼續監視的升級，並且執行安全和健康狀態檢查。

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

升級會從上一次暫止的升級網域繼續，並使用相同的升級參數和健康狀態原則。如有需要，當繼續升級時，上述輸出中顯示的任何升級參數和健康狀態原則可以在相同命令中變更。在此範例中，升級會在「監視」模式中繼續，且所有其他參數維持不變，因此會使用與以前相同的參數和健康狀態原則。

## 進一步疑難排解

### Service Fabric 不遵循指定的健康狀態原則

可能的原因 1：

Service Fabric 將所有百分比轉譯為健康狀態評估的實體實際數字 (例如複本、資料分割和服務)，並一律會無條件進位到最接近的實體整數。例如，如果最大值 _MaxPercentUnhealthyReplicasPerPartition_ 是 21%，而有 5 個複本，則 Service Fabric 在評估資料分割健康狀態時可允許最多 2 個複本 (亦即，`Math.Ceiling (5*0.21)`) 健康狀態不良。健康狀態原則應該據此設定，進行計算。

可能的原因 2：

健康狀態原則是以服務總計的百分比指定，而不是根據特定服務執行個體。例如，在升級之前，假設應用程式有四個服務執行個體 A、B、C 和 D，其中服務 D 健康狀態不良，但是對應用程式沒有顯著影響。我們想要在升級期間略過已知健康狀態不良的服務 D，並將參數 *MaxPercentUnhealthyServices* 設為 25%，假設只有 A、B 和 C 需要是健康狀態良好。不過，在升級期間，D 會在 C 健康狀態不良時變成健康狀態良好。在此案例中，升級仍然會順利完成，因為只有 25% 的服務健康狀態不良，由於 C 意外地健康狀態不良，而不是 D，可能會造成非預期的錯誤。在此情況下，D 應該被模式化為與 A、B 和 C 不同的服務類型。因為可以根據每個服務類型指定健康狀態原則，所以可以根據其在應用程式中的角色，對不同的服務套用不同的健康狀態不良百分比臨界值。

### 我未對應用程式升級指定健康狀態原則，但是升級還是因為我從未指定的逾時而失敗

當未對升級要求提供健康狀態原則時，會從目前應用程式版本的 *ApplicationManifest.xml* 取用 (例如，如果將應用程式 X 從 v1 升級至 v2，則會使用為應用程式 X v1 指定的應用程式健康狀態原則)。如果應該對升級使用不同的健康狀態原則，則需要指定原則做為應用程式升級 API 呼叫的一部分。請注意，指定為 API 呼叫一部分的原則只適用於升級持續時間。在完成升級後，會使用 *ApplicationManifest.xml* 中指定的原則。

### 指定了不正確的逾時。

使用者可能想要知道如果逾時設定不一致時會發生什麼情況，例如，*UpgradeTimeout* 小於 *UpgradeDomainTimeout*。答案是會傳回錯誤。其他也會發生此情況的案例是如果 *UpgradeDomainTimeout* 小於 *HealthCheckWaitDuration* 和 *HealthCheckRetryTimeout* 的總和，或如果 *UpgradeDomainTimeout* 小於 *HealthCheckWaitDuration* 和 *HealthCheckStableDuration* 的總和。

### 我的升級耗費太多時間

完成升級所花費的時間取決於所指定的各種健康狀態檢查和逾時，分別又是取決於您的應用程式升級所花費的時間 (包括複製封裝、部署及穩定化)。使用逾時太過激烈，可能表示會有更多失敗的升級，因此建議保守地從較長的逾時開始。

逾時與升級時間之間的互動方式快速複習：

升級網域的升級完成時間無法快於 *HealthCheckWaitDuration* + *HealthCheckStableDuration*。

升級失敗發生時間無法快於 *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*。

升級網域的升級時間受到 *UpgradeDomainTimeout* 限制。如果 *HealthCheckRetryTimeout* 和 *HealthCheckStableDuration* 兩者都為非零且應用程式的健康狀態會保持來回切換，則升級最終會在 *UpgradeDomainTimeout* 逾時。目前升級網域的升級開始時，*UpgradeDomainTimeout* 就會開始倒數計時。

## 後續步驟

[升級教學課程](service-fabric-application-upgrade-tutorial.md)

[升級參數](service-fabric-application-upgrade-parameters.md)

[進階主題](service-fabric-application-upgrade-advanced.md)

[資料序列化](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=July15_HO2-->