<properties
   pageTitle="Service Fabric 可靠服務程式設計模型的進階的用法"
   description="深入了解 Service Fabric 可靠服務程式設計模型的進階用法，以在服務中增加彈性。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2015"
   ms.author="jesseb"/>

# 可靠服務程式設計模型的進階用法
Service Fabric 可簡化撰寫和管理可靠的無狀態與具狀態服務。本指南會討論到「可靠服務」程式設計模型的進階用法，以在您的服務中取得更多控制權和彈性。在閱讀這份指南之前，請您先熟悉＜[可靠的服務程式設計模型](service-fabric-reliable-services-introduction.md)＞。

## 無狀態服務基底類別
StatelessService 基底類別提供 CreateCommunicationListener() 和 RunAsync()，這對大部分的無狀態服務來說已經足夠。StatelessServiceBase 類別以 StatelessService 為基礎，並公開其他服務生命週期事件。請參閱 [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) 和 [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) 上的開發人員參考文件以了解詳細資訊。

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize 是 Service Fabric 呼叫的第一個方法。其提供服務初始化資訊，例如服務名稱、資料分割識別碼、執行個體識別碼和程式碼封裝資訊。這裡不應執行任何複雜的處理。冗長的初始化應該透過 OnOpenAsync 執行。

- 準備使用無狀態服務執行個體時，會呼叫 `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync。這個階段可以啟動擴充服務的初始化作業。

- 無狀態服務執行個體準備正常關閉時，會呼叫 `Task OnCloseAsync(CancellationToken)` OnCloseAsync。這可能在升級服務程式碼、因負載平衡而移動服務執行個體或偵測到暫時性失敗的時侯發生。OnCloseAsync 可以用來安全地關閉任何資源、停止任何背景處理、完成外部狀態儲存或關閉現有的連接。

- 強制關閉無狀態服務執行個體時，會呼叫 `void OnAbort()` OnAbort。這個一般會在於節點上偵測到永久錯誤，或因內部失敗而 Service Fabric 無法可靠地管理服務執行個體生命週期時呼叫。

## 具狀態服務基底類別
StatefulService 基底類別對大部分的具狀態服務應已足夠。類似無狀態服務，StatefulServiceBase 類別以 StatefulService 為基礎，並公開其他服務生命週期事件。此外，其可讓您提供自訂的穩定狀態提供者，並選擇性地在次要複本上支援通訊接聽程式。請參閱 [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) 和 [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) 上的開發人員參考文件以了解詳細資訊。

- 具狀態服務變更角色，例變更為主要或次要複本時，會呼叫 `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync。其提供主要複本寫入狀態 (允許建立和寫入可靠集合)，並提供次要複本讀取狀態 (只能讀取現有可靠集合)。您可以啟動或更新背景作業以回應角色變更，例如在次要複本上執行唯讀驗證、 產生報告或資料採礦。

- `IStateProviderReplica CreateStateProviderReplica()` 具狀態服務會預期有可靠的狀態提供者。StatefulService 使用 ReliableStateManager 類別，並可提供可靠的集合 (例如字典和佇列)。如果您要自行管理狀態，或擴充其中一個內建狀態提供者的功能，你可能會想要提供自己的提供者。

- `bool EnableCommunicationListenerOnSecondary { get; }` 依預設，通訊接聽程式只會在主要複本上建立。StatefulService 和 StatefulServiceBase 可允許您覆寫這個屬性，以允許在次要複本上建立通訊接聽程式。您可能會想要允許次要複本處理唯讀要求，以改善大量讀取的工作負載輸送量。

    > [AZURE.NOTE]您必須負責確保您的次要複本不會嘗試建立或寫入可靠的集合。嘗試在次要複本上寫入會導致例外狀況，而如果未處理，將導致複本關閉並重新開啟。

StatefulServiceBase 也會提供與 StatelessServiceBase 相同的四個生命週期事件，並具有相同的語意和使用案例：

- `void OnInitialize(StatelessServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 後續步驟
如需更多與 Service Fabric 相關的進階主題，請參閱下列文件。

- [設定可設定狀態可靠服務](service-fabric-reliable-services-configuration.md)

- [Service Fabric 健康狀態簡介](../service-fabric/service-fabric-health-introduction.md)

- [使用系統健康狀態報告進行疑難排解](../service-fabric/service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [放置條件約束概觀](../service-fabric/service-fabric-placement-constraint.md)

- [保護 Azure Service Fabric 中具狀態服務的複寫流量](../service-fabric/service-fabric-replication-security.md)
 

<!---HONumber=July15_HO2-->