
<properties
   pageTitle="Service Fabric 應用程式升級：升級參數"
   description="本文說明與升級 Service Fabric 應用程式相關的各種參數。"
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
   ms.date="04/15/2015"
   ms.author="subramar"/>



# 應用程式升級參數

本文說明在 Service Fabric 應用程式升級期間套用的各種參數。參數包含應用程式名稱和版本，和控制逾時的旋鈕與升級時套用的健康狀態檢查，並且指定升級失敗時必須套用的原則。


<br>

| 參數 | 說明 |
| --- | --- |
| ApplicationName | 正在升級的應用程式名稱。範例：fabric:/VisualObjects、fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | 升級目標的應用程式類型的版本。 |
| FailureAction | FailureAction 屬性描述如果升級失敗，Service Fabric 所採取的動作。應用程式可能已回復到更新之前的最後一個版本 (回復)，或在目前升級網域停止應用程式升級，並將升級模式變更為手動。允許的值為回復和手動。 |
| HealthCheckWaitDurationSec | 在升級網域上完成升級之後，Service Fabric 評估應用程式健康狀態的等待時間 (以秒為單位)。這段期間也可視為應用程式在被視為健康之前應該執行的時間。如果健康狀態檢查都通過，則升級程序會繼續進行下一個升級網域。如果健康狀態檢查失敗，Service Fabric 會等候一定間隔 (UpgradeHealthCheckInterval)，再重試一次健康狀態檢查，直到達到 HealthCheckRetryTimeout 為止。預設值和建議值為 0 秒。 |
| HealthCheckRetryTimeoutSec | Service Fabric 在將升級宣告為失敗之前，繼續執行健康狀態評估的持續時間 (以秒為單位)。預設值為 600 秒。此期間會在達到 HealthCheckWaitDuration 之後開始計算。在此 HealthCheckRetryTimeout 之內，Service Fabric 可能會執行多個應用程式健康狀態的健康狀態檢查。預設值為 10 分鐘，建議您針對您的應用程式適當地加以自訂。 |
| HealthCheckStableDurationSec | 移至下一個升級網域或完成升級之前，為了確認應用程式是否穩定的等待持續時間 (以秒為單位)。這個等候持續時間是用來在執行健康狀態檢查後，防止未偵測到的健康狀態變更。預設值為 0 秒鐘，建議您針對您的應用程式適當地加以自訂。 |
| UpgradeDomainTimeoutSec | 升級單一升級網域的時間上限 (以秒為單位)。如果達到此逾時，升級會停止，並且執行 UpgradeFailureAction 所指定的動作。預設值為永不 (無限)，建議您針對您的應用程式適當地加以自訂。 |
| UpgradeTimeout | 逾時 (以秒為單位) 適用於整個升級。如果達到此逾時，升級會停止，並且會觸發 UpgradeFailureAction。預設值為永不 (無限)，建議您針對您的應用程式適當地加以自訂。 |
| UpgradeHealthCheckInterval | 在 ClusterManager 區段中指定_叢集__資訊清單_ (未指定為升級 Cmdlet 的一部分)，此設定會指定檢查健康狀態的頻率 (預設值為 60 秒)。 |






<br>
## 應用程式升級期間的服務健康狀態評估

<br>健康狀態評估準則是選擇性的。如果啟動升級時未指定健康狀態評估準則，則 Service Fabric 會使用升級之應用程式執行個體的 ApplicationManifest.xml 中指定的應用程式健康狀態原則。


<br>

| 參數 | 說明 |
| --- | --- |
| ConsiderWarningAsError | 預設值為 False。升級期間評估應用程式健康狀態時，將應用程式的警告健康狀態事件視為錯誤。根據預設，Service Fabric 不會將警告健康狀態事件評估為失敗 (錯誤)，因此，即使有警告事件，升級還是可以繼續執行。 |
| MaxPercentUnhealthyDeployedApplications | 預設值和建議值為 0。指定應用程式被視為不健康和更新失敗之前，應用程式不健康之已部署的應用程式數目上限 (請參閱[健康狀態章節](service-fabric-health-introduction.md))。這是在節點上封裝之應用程式的健康狀態，因此這項資訊有助於在升級期間以及對部署在節點上的不健康應用程式封裝 (損毀等等)，偵測立即問題。在典型的案例中，會將應用程式複本負載平衡至其他節點，因此可以讓應用程式健康狀態良好，以便繼續升級。藉由指定嚴謹的 MaxPercentUnhealthyDeployedApplications 健康狀態，Service Fabric 可以快速偵測應用程式封裝的問題，並導致失敗快速升級。 |
| MaxPercentUnhealthyServices | 預設值和建議值為 0。指定應用程式被視為不健康和更新失敗之前，不健康之應用程式執行個體中服務的數目上限。 |
| MaxPercentUnhealthyPartitionsPerService | 預設值和建議值為 0。指定服務被視為不健康之前，不健康之服務中分割的數目上限。 |
| MaxPercentUnhealthyReplicasPerPartition | 預設值和建議值為 0。指定分割被視為不健康之前，不健康之分割中複本的數目上限。 |
| UpgradeReplicaSetCheckTimeout | 無狀態服務 - 在單一升級網域內，Service Fabric 會嘗試確保服務的額外執行個體可用。如果目標執行個體計數超過一個，Service Fabric 會等到有一個以上的執行個體可用，最多到逾時值上限 (由 UpgradeReplicaSetCheckTimeout 屬性指定)。如果逾時已到期，Service Fabric 會繼續進行升級，不論服務執行個體數目。如果目標執行個體計數是一個，Service Fabric 不會等待，它會立即繼續進行升級。可設定狀態的服務 - 在單一升級網域內，Service Fabric 會嘗試確保複本集有仲裁。Service Fabric 會等待有仲裁可用，最多到逾時值上限 (由 UpgradeReplicaSetCheckTimeout 屬性指定)。如果逾時已到期，Service Fabric 會繼續進行升級，不論是否有仲裁。當向前 (如預期般在升級繼續時套用) 時，這項設定設為永不 (無限)，而當回復 (在升級遇到錯誤並且回復時套用) 時，設為 900 秒。 |
| ForceRestart | 如果您更新組態或資料封裝，而未更新服務程式碼，Service Fabric 不會重新啟動服務，除非 ForceRestart 屬性設為 true，做為 API 呼叫的一部分。更新完成時，Service Fabric 會通知服務，新的組態封裝或資料封裝已可使用。服務會負責套用變更。必要時，服務可以自行重新啟動。 |



<br><br>可以針對應用程式執行個體的各個服務類型指定 MaxPercentUnhealthyServices、MaxPercentUnhealthyPartitionsPerService 和 MaxPercentUnhealthyReplicasPerPartition 準則。這是為了確保應用程式 (包含不同的服務類型)，對於每個服務類型可以有不同的評估原則。例如，特定應用程式執行個體的無狀態閘道服務類型，可以有與可設定狀態的引擎服務類型不同的 MaxPercentUnhealthyPartitionsPerService。

## 後續步驟


[升級教學課程](service-fabric-application-upgrade-tutorial.md)

[進階主題](service-fabric-application-upgrade-advanced.md)

[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)

[資料序列化](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=July15_HO2-->