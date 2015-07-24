<properties
   pageTitle="Azure Service Fabric Actor 診斷與效能監視"
   description="本文將說明 Fabric Actor 執行階段中的診斷與效能監視功能，包括其發出的事件與效能計數器。"
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
   ms.date="04/22/2015"
   ms.author="abhisram"/>

# Fabric Actor 的診斷與效能監視
Fabric Actor 執行階段會發出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件與[效能計數器](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)，以供深入了解執行階段如何運作，並協助進行疑難排解與效能監視。

## EventSource 事件
Fabric Actor 執行階段的 EventSource 名稱為 "Microsoft-ServiceFabric-Actors"。當在 [Visual Studio 中正在偵錯](service-fabric-debugging-your-application.md)動作項目應用程式時，事件來源中的事件會出現在 [[診斷事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] 視窗中。

Service Fabric 也會提供選項將這些事件導向至 [Application Insights](http://azure.microsoft.com/services/application-insights/)。如需詳細資訊，請參閱＜[Service Fabric 的 Application Insights 設定](service-fabric-diagnostics-application-insights-setup.md)＞一文。

可協助您收集和/或檢視 EventSource 事件的其他工具和技術範例包括 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Azure 診斷](../cloud-services-dotnet-diagnostics.md)、[語意記錄](https://msdn.microsoft.com/library/dn774980.aspx)和 [Microsoft TraceEvent 程式庫](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

### 關鍵字
所有屬於 Fabric Actor EventSource 的事件皆有一或多個相關聯的關鍵字。這會啟動篩選所選的事件。以下為已定義的關鍵字位元：

|Bit|說明|
|---|---|
|0x1|可彙總 Fabric Actor 執行階段作業的重要事件集。|
|0x2|描述動作項目方法呼叫的事件集。如需詳細資訊，請參閱[動作項目簡介主題](service-fabric-reliable-actors-introduction.md#actors)。|
|0x4|與動作項目狀態相關的事件集。如需詳細資訊，請參閱[可設定狀態動作項目](service-fabric-reliable-actors-introduction.md#stateful-actors)主題。|
|0x8|與動作項目的回合式並行相關的事件集。如需詳細資訊，請參閱[並行](service-fabric-reliable-actors-introduction.md#concurrency)主題。|

## 效能計數器
Fabric Actor 執行階段定義下列效能計數器類別。

|類別|說明|
|---|---|
|Service Fabric 動作項目|Service Fabric 動作項目特定的計數器，例如儲存動作項目狀態花費的時間。|
|Service Fabric 動作項目方法|Service Fabric 動作項目所實作方法特定的計數器，例如叫用動作項目方法的頻率。|

上述的類別每一個都有一或多個計數器。

Windows 作業系統中預設可用的 [Windows 效能監視器](https://technet.microsoft.com/library/cc749249.aspx)應用程式可用於收集與檢視效能計數器資料。[Azure 診斷](../cloud-services-dotnet-diagnostics.md)是另一個收集效能計數器資料並上傳至 Azure 資料表的選項。

### 效能計數器執行個體名稱
含大量動作項目服務或動作項目服務資料分割的叢集將有大量的動作項目效能計數器執行個體。效能計數器執行個體名稱可幫助識別效能計數器執行個體相關聯的特定[資料分割](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)與動作項目方法 (如果適用)。

#### `Service Fabric Actor` 類別
`Service Fabric Actor` 類別的計數器執行個體名稱格式如下：

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* - 是與效能計數器執行個體相關聯的 Service Fabric 資料分割識別碼的字串表示法。資料分割識別碼是 GUID，其字串表示法是使用 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法與格式規範 "D" 所產生。

*ActorRuntimeInternalID* - 是 Fabric Actor 執行階段所產生 64 位元整數的字串表示法，供內部使用。這包含在效能計數器執行個體名稱中，以確保其唯一性，並避免與其他效能計數器執行個體名稱衝突。使用者不應該嘗試解譯效能計數器執行個體名稱的這個部分。

以下為屬於 'Service Fabric Actor' 類別之計數器的計數器執行個體名稱範例：

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

在上述範例中，`2740af29-78aa-44bc-a20b-7e60fb783264` 是 Service Fabric 資料分割識別碼的字串表示法，`635650083799324046` 是為了內部使用執行階段所產生的 64 位元識別碼。

#### `Service Fabric Actor Method` 類別
`Service Fabric Actor Method` 類別的計數器執行個體名稱格式如下：

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* -效能計數器執行個體相關聯的動作項目方法名稱。方法名稱的格式取決於 Fabric Actor 執行階段某個邏輯，該邏輯會在名稱的可讀性與 Windows 上效能計數器執行個體名稱長度上限之間取得平衡。

*ActorsRuntimeMethodId* - 是 Fabric Actor 執行階段所產生 32 位元整數的字串表示法，供內部使用。這包含在效能計數器執行個體名稱中，以確保其唯一性，並避免與其他效能計數器執行個體名稱衝突。使用者不應該嘗試解譯效能計數器執行個體名稱的這個部分。

*ServiceFabricPartitionID* - 是與效能計數器執行個體相關聯的 Service Fabric 資料分割識別碼的字串表示法。資料分割識別碼是 GUID，其字串表示法是使用 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法與格式規範 "D" 所產生。

*ActorRuntimeInternalID* - 是 Fabric Actor 執行階段所產生 64 位元整數的字串表示法，供內部使用。這包含在效能計數器執行個體名稱中，以確保其唯一性，並避免與其他效能計數器執行個體名稱衝突。使用者不應該嘗試解譯效能計數器執行個體名稱的這個部分。

以下為屬於 'Service Fabric Actor Method' 類別之計數器的計數器執行個體名稱範例：

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

在上述範例中，`ivoicemailboxactor.leavemessageasync` 是方法名稱，`2` 是為了內部使用執行階段產生的 32 位元識別碼，`89383d32-e57e-4a9b-a6ad-57c6792aa521` 是 Service Fabric 資料分割識別碼的字串表示法，`635650083804480486` 是為了內部使用執行階段產生的 64 位元識別碼。

## 事件與效能計數器清單

### 動作項目方法事件與效能計數器
Fabric Actor 執行階段會發出下列與[動作方法](service-fabric-reliable-actors-introduction.md#actors)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ActorMethodStart|7|詳細資訊|0x2|動作項目執行階段即將叫用動作項目方法|
|ActorMethodStop|8|詳細資訊|0x2|動作項目方法已完成執行，亦即執行階段對動作項目方法的非同步呼叫已傳回，且動作項目方法傳回的工作已完成。|
|ActorMethodThrewException|9|警告|0x3|執行動作項目方法期間擲回例外狀況，無論是在對動作項目方法的執行階段非同步呼叫期間，或執行動作項目方法傳回的工作期間。此事件表示需要調查的動作項目程式碼發生某種失敗。|

Fabric Actor 執行階段會發佈與執行動作項目方法相關的下列效能計數器。

|類別名稱|計數器名稱|說明|
|---|---|---|
|Service Fabric 動作項目方法|叫用數目/秒|每秒叫用動作項目服務方法的次數|
|Service Fabric 動作項目方法|每個叫用的平均毫秒數|執行動作項目服務方法花費的時間 (單位為毫秒)|
|Service Fabric 動作項目方法|擲回的例外狀況數/秒|每秒動作項目服務方法擲回例外狀況的次數|

### 並行事件與效能計數器
Fabric Actor 執行階段會發出下列與[並行](service-fabric-reliable-actors-introduction.md#concurrency)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|詳細資訊|0x8|在動作項目每個新回合開始時會寫入此事件。其包含擱置中的動作項目呼叫數目，這些呼叫正等待取得強制執行回合式並行的各動作項目鎖定。|

Fabric Actor 執行階段會發佈下列與並行相關的效能計數器。

|類別名稱|計數器名稱|說明|
|---|---|---|
|Service Fabric 動作項目|等待動作項目鎖定的動作項目呼叫數|擱置中的動作項目呼叫數目，這些呼叫正等待取得強制執行回合式並行的各動作項目鎖定。|

### 動作項目狀態管理事件與效能計數器
Fabric Actor 執行階段會發出下列與[動作狀態管理](service-fabric-reliable-actors-introduction.md#actor-state-management)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ActorSaveStateStart|10|詳細資訊|0x4|動作項目執行階段即將儲存動作項目狀態。|
|ActorSaveStateStop|11|詳細資訊|0x4|動作項目執行階段已完成儲存動作項目狀態。|

Fabric Actor 執行階段會發佈下列與動作項目狀態管理相關的效能計數器。

|類別名稱|計數器名稱|說明|
|---|---|---|
|Service Fabric 動作項目|每個儲存狀態作業的平均毫秒數|儲存動作項目狀態花費的時間 (單位為毫秒)|

### 與無狀態動作項目執行個體相關的事件
Fabric Actor 執行階段會發出下列與[無狀態動作項目執行個體](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ServiceInstanceOpen|3|資訊|0x1|已開啟無狀態動作項目執行個體。這意指在此執行個體內可以為此資料分割建立動作項目 (可能也有其他執行個體)。|
|ServiceInstanceClose|4|資訊|0x1|已關閉無狀態動作項目執行個體。這意指在此執行個體內不再為此資料分割建立動作項目。沒有新的要求將傳遞到已在此執行個體內建立的動作項目。任何進行中的要求完成後，將終結動作項目。|

### 與可設定狀態的動作項目複本相關的事件
Fabric Actor 執行階段會發出下列與[可設定狀態的動作項目複本](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|資訊|0x1|可設定狀態的動作項目複本已將角色變為主要。這意指將在此複本內建立此資料分割的動作項目。|
|ReplicaChangeRoleFromPrimary|2|資訊|0x1|可設定狀態的動作項目複本已將角色變為非主要。這意指在此複本內不再為此資料分割建立動作項目。沒有新的要求將傳遞到已在此複本內建立的動作項目。任何進行中的要求完成後，將終結動作項目。|

### 動作項目啟動與停用事件
Fabric Actor 執行階段會發出下列與[動作項目啟動與停用](service-fabric-reliable-actors-lifecycle.md)相關的事件。

|事件名稱|事件識別碼|層級|關鍵字|說明|
|---|---|---|---|---|
|ActorActivated|5|資訊|0x1|動作項目已啟動。|
|ActorDeactivated|6|資訊|0x1|動作項目已停用。|
 

<!---HONumber=July15_HO2-->