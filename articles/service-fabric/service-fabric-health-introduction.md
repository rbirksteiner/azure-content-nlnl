<properties
   pageTitle="Service Fabric 健康狀態監視簡介"
   description="本文章說明 Azure Service Fabric 健康狀態監視的模型，包含健康狀態實體、報告和評估。"
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

# Service Fabric 健康狀態監視簡介
Service Fabric 導入了健康狀態模型，提供豐富、彈性且可延伸的健康狀態評估與報告。這包含幾乎即時地監視叢集狀態以及其中所執行服務。您可以輕鬆地取得健康狀態資訊，並在重疊顯示和造成大量中斷之前，採取動作以更正潛在問題。一般模型即是服務根據其本機檢視傳送報告，且資訊會進行彙總以提供整體叢集層級檢視。

Service Fabric 元件會使用此健康狀態模型來報告其目前狀態。此外，您可以使用相同機制來報告應用程式的健康狀態。健康狀態報告的高品質和豐富功能取決於您的專屬自訂條件，將決定您能夠如何輕鬆地偵測並修正執行應用程式的問題。

> [AZURE.NOTE]我們會啟動健康狀態子系統作為受監視升級所需。Service Fabric 提供受監視升級，以便了解如何在不需停機且最小化使用者中斷下升級叢集或應用程式，並具有完整叢集和應用程式可用性。為了達成此目的，升級會依照已設定升級原則檢查健康狀態，並讓升級僅在健康狀態到達所需臨界值時繼續執行。否則，升級會自動回復或暫停，讓系統管理員有機會來修正問題。若要深入了解應用程式升級，請參閱[這篇文章](service-fabric-application-upgrade.md)。

## 健康狀態存放區
健康狀態存放區會保留叢集中實體的健康狀態相關資訊，以便輕鬆進行擷取和評估。其會實作為 Service Fabric 保存的可設定狀態服務，以確保高可用性和延展性。健康狀態存放區是 fabric:/System 應用程式的一部分，且會在叢集已啟動且正在執行時提供使用。

## 健康狀態實體和階層
健康狀態實體會在邏輯階層中組合管理，用來擷取不同實體之間的互動和相依性。實體和階層會依照從 Service Fabric 元件所收到的報告，自動透過健康狀態存放區進行建置。

健康狀態實體是 Service Fabric 實體的鏡像 (例如，健康狀態應用程式實體符合部署在叢集中的應用程式，而健康狀態節點實體符合 Service Fabric 叢集節點)。健康狀態階層會擷取系統實體的互動，並作為進階健康狀態評估的基礎。您可以在＜[Service Fabric 技術概觀](service-fabric-technical-overview.md)＞中了解重要 Service Fabric 概念。如需應用程式的詳細資訊，請移至＜[Service Fabric 應用程式模型](service-fabric-application-model.md)＞。

健康狀態實體和階層可讓您有效率地進行報告、偵錯和監視叢集和應用程式。健康狀態模型可針對叢集中許多移動的項目提供精確且**細微**的健康狀態呈現。

![健康狀態實體。][1] 階層中的健康狀態實體將依照父系-子系關聯性組合管理。

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

健康狀態實體包含：

- **叢集**。表示 Service Fabric 叢集的健康狀態。叢集健康狀態報告會說明影響整個叢集的條件，且無法將範圍縮小至一個或多個狀況不良的子系條件。範例：網路分割或通訊問題而造成叢集核心分裂。

- **節點**。表示 Service Fabric 節點的健康狀態。節點健康狀態報告會說明影響節點功能的條件，且通常會影響在其中執行的所有已部署實體。範例：節點用完磁碟空間 (或其他電腦全域屬性，例如記憶體、連接等) 或節點已關閉。節點實體將透過節點名稱 (字串) 來識別。

- **應用程式**。表示叢集中所執行應用程式執行個體的健康狀態。應用程式健康狀態報告會說明影響應用程式整體健康狀態的條件，且無法將範圍縮小至個別的子系條件 (服務或已部署應用程式)。範例：應用程式中不同服務之間的端對端互動。應用程式實體將透過應用程式名稱 (URI) 來識別。

- **服務**。表示叢集中所執行服務的健康狀態。應用程式健康狀態報告會說明影響服務整體健康狀態的條件，且無法將範圍縮小至資料分割或複本。範例：造成所有資料分割發生問題的服務組態 (例如連接埠或外部檔案共用)。服務實體將透過服務名稱 (URI) 來識別。

- **資料分割**。表示服務資料分割的健康狀態。資料分割健康狀態報告會說明影響整體複本集的條件。範例：複本的數目低於目標計數或仲裁遺失中的資料分割。資料分割實體將透過分割識別碼 (GUID) 來識別。

- **複本**。表示可設定狀態服務複本或無狀態服務執行個體的健康狀態。這是最小單位的看門狗，且系統元件可以針對應用程式進行報告。範例：針對可設定狀態服務，若主要複本無法將作業複寫至次要資料庫，或複寫並未依照預期進度繼續執行，則主要複本會進行報告。若無狀態執行個體在執行時資源不足或發生連線問題，則無狀態執行個體會進行報告。複本實體將透過資料分割識別碼 (GUID) 以及複本或執行個體識別碼 (完整) 來識別。

- **已部署應用程式**。表示*節點上所執行應用程式*的健康狀態。已部署應用程式健康狀態報告會描述專屬於節點上應用程式的條件，且無法將範圍縮小至部署在相同節點上的服務封裝。範例：應用程式封裝無法在該節點上進行下載，或在節點上設定應用程式安全性主體時發生問題。已部署應用程式將透過應用程式名稱 (URI) 和節點名稱 (字串) 來識別。

- **已部署服務封裝**。表示叢集中節點所執行應用程式服務封裝的健康狀態。其會描述專屬於服務封裝的條件，該條件對於相同應用程式之相同節點上的其他服務封裝不會造成影響。範例：服務封裝中的程式碼封裝無法啟動，或無法讀取組態封裝。已部署服務封裝將透過應用程式名稱 (URI)、節點名稱 (字串) 和服務資訊清單名稱 (字串) 來識別。

健康狀態模型的細微性可讓您輕鬆地偵測並更正問題。例如，若服務沒有回應，則有可能會報告應用程式執行個體狀況不良；不過，這並不理想，因為問題可能不會影響該應用程式中的所有服務。報告應套用至狀況不良的服務上，或者若更多資訊指向特定子系資料分割，則套用至該資料分割上。資料將自動顯示在階層中的上層：狀況不良的資料分割將顯示於服務和應用程式層級。這可協助您更快速找出並解析問題的根本原因。

健康狀態階層是由父系-子系關聯性所組成。叢集是由節點和應用程式所組成；應用程式包含服務和已部署應用程式；已部署應用程式包含已部署服務封裝。服務包含資料分割，且每個資料分割包含一個或多個複本。節點和已部署實體之間具有特殊關聯性。如果節點透過其授權單位系統元件 (容錯移轉管理員服務) 報告為狀況不良，其會影響已部署應用程式、服務封裝和部署在其中的複本。

健康狀態階層會依照最新健康狀態報告來表示系統的最新狀態，且幾乎是即時資訊。內部和外部看門狗可以依照應用程式特定邏輯或自訂監視的條件來報告相同實體。使用者報告會與系統報告同時存在。

投入時間規劃如何報告和回應健康狀態，同時設計服務讓大型雲端服務更輕鬆進行偵錯、監視和後續作業。

## 健康狀態
Service Fabric 會使用三種健康狀態來描述實體的健康狀態是否良好：「Ok」、「Warning」和「Error」。傳送至健康狀態存放區的任何報告皆必須指定這些狀態的其中之一。健康狀態評估結果即為這些狀態的其中之一。

可能的健康狀態為：

- 「Ok」：實體的健康狀態良好。報告實體本身或其子系 (適用時) 沒有已知問題。

- 「Warning」：實體遇到某些問題，但不屬於狀況不良 (例如，不會造成任何功能問題的非預期延遲)。在某些情況中，「Warning」狀況可能會自行修正，而不需任何特殊的介入，且對於提供目前狀態的可見度來說相當實用。在其他情況中，「Warning」狀況可能會惡化為嚴重問題，但不需使用者介入。

- 「Error」：實體狀況不良。因為實體無法正常運作，故應採取動作來修正實體的狀態。

- 「Unknown」：實體不存在於健康狀態存放區中。此結果可從分散式查詢取得，如同取得 Service Fabric 節點或應用程式。這些查詢會合併來自多個系統元件的結果。若其他系統元件具有尚未到達健康狀態存放區的實體，或實體已從健康狀態存放區清除，則合併的查詢將使用「未知」健康狀態來填入健康狀態結果。

## 健康狀態原則
健康狀態存放區會套用健康狀態原則，依照它的報告及其子系以判斷實體的健康狀態是否良好。

> [AZURE.NOTE]健康狀態原則可在叢集資訊清單 (適用於叢集和節點健康狀態評估) 或應用程式資訊清單 (適用於應用程式評估及其任何子系) 中指定。健康狀態評估要求也適用於自訂健康狀態評估原則，並僅用於該評估。

根據預設，Service Fabric 會針對父系-子系階層式關聯性套用嚴格的規則 (所有項目必須是狀況良好)；只要其中一個子系具有一個狀況不良的事件時，系統就會將父系視為狀況不良。

### 叢集健康狀態原則
叢集健康狀態原則用來評估叢集和節點的健康狀態。可以在叢集資訊清單中進行定義。若不存在，則預設為預設原則 (0 容許失敗)。其中包含：

- **ConsiderWarningAsError**。指定是否要在健康狀態評估期間將「Warning」健康狀態報告視為錯誤。預設值：False。

- **MaxPercentUnhealthyApplications**。應用程式的最大容許百分比，在系統將叢集視為「Error」之前可能屬於狀況不良。

- **MaxPercentUnhealthyNodes**。節點的最大容許百分比，在系統將叢集視為「Error」之前可能屬於狀況不良。在大型叢集中，永遠存在關閉或需要修復的節點，因此應設定此百分比來容許錯誤。

下列是來自叢集資訊清單的摘要：

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### 應用程式健康狀態原則
應用程式健康狀態原則會針對應用程式及其子系，說明事件和子系狀態彙總的評估如何完成。它可以在應用程式資訊清單中進行定義，即應用程式封裝中的 ApplicationManifest.xml。若未指定，則當健康狀態報告或子系處於「Warning」或「Error」健康狀態時，Service Fabric 會假設實體狀況不良。可設定的原則包含：

- **ConsiderWarningAsError**。指定是否要在健康狀態評估期間將「Warning」健康狀態報告視為錯誤。預設值：False。

- **MaxPercentUnhealthyDeployedApplications**。已部署應用程式的最大容許百分比，在系統將應用程式視為「Error」之前可能屬於狀況不良。計算方式為將狀況不良的已部署應用程式數目，除以應用程式目前部署在叢集中的節點數目。針對較少的節點數目，計算會四捨五入以容許一個失敗。預設值：0%。

- **DefaultServiceTypeHealthPolicy**。指定預設服務類型健康狀態原則，並取代適用於應用程式中所有服務類型的預設健康狀態原則。

- **ServiceTypeHealthPolicyMap**。依照服務類型來對應服務健康狀態原則，以便針對特定服務類型取代預設的服務類型健康狀態原則。例如，在包含無狀態的閘道服務類型和可設定狀態引擎服務類型的應用程式中，無狀態和可設定狀態服務的健康狀態原則可以進行不同的設定。依照服務類型來指定原則，以便針對行服務的健康狀態進行更細微的控制。

### 服務類型健康狀態原則
服務類型健康狀態原則會指定評估和彙總子系服務的方式。其中包含：

- **MaxPercentUnhealthyPartitionsPerService**。在系統將服務視為「狀況不良」之前狀況不良資料分割的最大容許百分比。預設值：0%。

- **MaxPercentUnhealthyReplicasPerPartition**。在系統將資料分割視為「狀況不良」之前狀況不良分割的最大容許百分比。預設值：0%。

- **MaxPercentUnhealthyServices**。在系統將應用程式視為「狀況不良」之前狀況不良服務的最大容許百分比。預設值：0%

下列是來自應用程式資訊清單的摘要：

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## 健康狀態評估
使用者或自動化服務可以在任何時間點評估任何實體的健康狀態。若要評估實體健康狀態，健康狀態存放區會彙總實體上的所有健康狀態報告，並評估其所有子系 (適用時)。健康狀態彙總演算法會使用健康狀態原則，以便指定如何評估健康狀態報告，以及如何彙總子系健康狀態 (適用時)。

### 健康狀態報告彙總
一個實體可以具有由不同回報者針對不同屬性 (系統元件或看門狗) 所傳送的多個健康狀態報告。彙總會使用相關聯的健康狀態原則，特別是應用程式的 ConsiderWarningAsError 成員或叢集健康狀態原則，以便指定如何評估警告。

已彙總健康狀態是由實體上的**最差**健康狀態報告所觸發。若存在至少一個「Error」健康狀態報告，則已彙總健康狀態會顯示為「Error」。

![健康狀態報告彙總，包含「Error」報告。][2]

「Error」健康狀態報告會觸發健康狀態實體成為「Error」狀態。

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

若不存在任何「Error」報告，和一個或多個「Warning」，則已彙總健康狀態可能是「Warning」或「Error」，取決於 ConsiderWarningAsError 原則旗標。

![健康狀態報告彙總，包含「Warning」報告和 ConsiderWarningAsError 錯誤。][3]

健康狀態報告彙總包含「Warning」報告和 ConsiderWarningAsError 錯誤 (預設值)。

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### 子系健康狀態彙總
實體的已彙總健康狀態會反映子系健康狀態 (適用時)。彙總子系健康狀態的演算法會依照實體類型使用適用的健康狀態原則。

![子系實體健康狀態彙總。][4]

以健康狀態原則為基礎的子系彙總。

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

評估所有子系之後，健康狀態存放區會依照以實體和子系類型為基礎的原則，採用其中已設定的狀況不良最大百分比來彙總健康狀態。

- 若所有子系具有「Ok」狀態，則子系已彙總的健康狀態為「Ok」。

- 若子系具有「Ok」和「Warning」狀態，則子系已彙總的健康狀態為「Warning」。

- 若具有「Error」狀態的子系不符合狀況不良子系的最大允許百分比，則已彙總健康狀態為「Error」。

- 若具有「Error」狀態的子系符合狀況不良子系的最大允許百分比，則已彙總健康狀態為「Warning」。

## 健康狀態報告
系統元件和內部/外部看門狗可以針對 Service Fabric 實體進行報告。*回報者*會依照其所監視的某些條件來決定**本機**受監視實體的健康狀態。回報者不需查看任何全域狀態和彙總資料。而且不需要這樣做，因為這會使回報者成為複雜的有機體，需要查看許多項目才能推斷所要傳送的資訊。

若要將健康狀態資料傳送至健康狀態存放區，回報者需要識別受影響的實體，並建立健康狀態報告。接著報告可以透過使用 FabricClient.HealthManager.ReportHealth 的 API、Powershell 或 REST 來進行傳送。

### 健康狀態報告
叢集中每個實體的健康狀態報告包含下列資訊：

- SourceId。用來識別健康狀態事件回報者的唯一字串。

- 實體識別碼。識別報告所套用的實體。其會依照[實體類型](service-fabric-health-introduction.md#health-entities-and-hierarchy)而有所不同：

  - 叢集：無

  - 節點：節點名稱 (字串)。

  - 應用程式：應用程式名稱 (URI)。表示叢集中已部署應用程式執行個體的名稱。

  - 服務：服務名稱 (URI)。表示叢集中已部署服務執行個體的名稱。

  - 資料分割：資料分割識別碼 (GUID)。表示資料分割的唯一識別碼。

  - 複本：可設定狀態服務複本識別碼，或無狀態服務執行個體識別碼 (Int64)。

  - 已部署應用程式：應用程式名稱 (URI) 和節點名稱 (字串)。

  - 已部署服務封裝：應用程式名稱 (URI)、節點名稱 (字串) 和服務資訊清單名稱 (字串)。

- 屬性。可讓回報者針對實體的特定屬性來分類健康狀態事件的*字串* (非固定列舉)。例如，回報者 A 可以針對 Node01 的「儲存體」屬性報告健康狀態，而回報者 B 可以針對 Node01 的「連線」屬性報告健康狀態。這兩個報告在 Node01 實體的健康狀態存放區中會視為個別的健康狀態事件。

- 說明。可讓回報者提供健康狀態事件相關詳細資訊的字串。SourceId、屬性和 HealthState 應在報告中完整描述。說明會加入人們可閱讀的報告資訊，以便系統管理員和使用者能夠更輕鬆了解。

- HealthState。描述報告健康狀態的[列舉](service-fabric-health-introduction.md#health-states)。接受的值是「OK」、「Warning」和「Error」。

- TimeToLive。指出健康狀態報告持續有效的時間範圍。可以搭配 RemoveWhenExpired，讓 HealthStore 知道如何評估過期的事件。根據預設，該值為「Infinite」，代表報告將永遠有效。

- RemoveWhenExpired。布林值。若設為「True」，則過期的健康狀態報告會自動從健康狀態存放區移除，且不會影響實體健康狀態評估。僅當報告在一段時間內有效時才會使用此值，且回報者不需明確地將其清除。其也用於從健康狀態存放區刪除報告。例如，看門狗已變更，且停止使用先前的來源和屬性傳送報告。因此其傳送的報告可以使用較小的 TTL 和 RemoveWhenExpired，以便從健康狀態存放區清除任何先前的狀態。若設定為「False」，則過期的報告會視為健康狀態評估的錯誤。其會向健康狀態存放區發出訊號，表示來源應定期報告此屬性的內容；若未發出訊號，則表示看門狗必定發生某種錯誤。看門狗健康狀態會透過假設事件錯誤來進行擷取。

- SequenceNumber。這個值是必須持續增加的正整數，因為其表示報表的順序。健康狀態存放區會使用其來偵測因網路延遲或其他問題而較晚收到的陳舊報告。針對相同的實體、來源和屬性，若序號小於或等於最新套用的項目，則報告會遭到拒絕。若未指定，則序號會自動產生。報告狀態轉換時，僅需放入序號：來源必須記住所傳送的報告，並在容錯移轉時保存資訊以進行復原。

每個健康狀態報告皆需要 SourceId、實體識別碼、屬性和 HealthState。不允許 SourceId 字串以前置詞「System.」開頭，因為這會保留給系統報告。針對相同的實體，相同的來源和屬性僅能有一個報告，若針對相同的來源和屬性產生多個報告，則其會互相覆寫，例如健康狀態用戶端 (若非批次處理) 或健康狀態存放區端。取代會依照序號來完成：較新的報告 (具有較大的序號) 會取代較舊的報告。

### 健康狀態事件
就內部而言，健全狀態存放區會保留健康狀態事件，其中包含來自報告的所有資訊加上其他中繼資料，例如報告送至健康狀態用戶端的時間，以及報告在伺服器端上修改的時間。健康狀態事件將透過[健康情況查詢](service-fabric-view-entities-aggregated-health.md#health-queries)來傳回。

新增的中繼資料包含：

- SourceUtcTimestamp：報告送至健康狀態用戶端的時間 (UTC)

- LastModifiedUtcTimestamp：上一次報告在伺服器端上修改的時間 (UTC)

- IsExpired：指出報告於健康狀態存放區執行查詢時是否已到期的旗標。僅當 RemoveWhenExpired 為「False」時事件才會過期；否則，查詢將不會傳回事件，且事件會從存放區移除。

- LastOkTransitionAt、LastWarningTransitionAt、LastErrorTransitionAt：上次轉換「Ok/Warning/Error」的時間。這些欄位會針對事件的健康狀態轉換提供歷程記錄。

狀態轉換欄位可用於更聰明的警示或「歷程記錄」的健康狀態事件資訊。所適用的案例像是：

- 當屬性處於「Warning/Error」狀態持續超過 X 分鐘時發出警示。如此可避免因暫時的狀況而發出警示。例如：當健康狀態處於「Warning」狀態持續超過 5 分鐘時發出警示，可以轉譯為 (HealthState == Warning and Now - LastWarningTransitionTime
> 

- 僅在前 X 分鐘內發生變更的情況時發出警示。如果報告之前即處於「Error」狀態，則可以忽略 (因為其先前已發出訊號)。

- 若屬性在「Warning」和「Error」之間切換，則可判斷其已處於狀況不良的時間長度 (例如，非「Ok」)。例如：若屬性狀況良好的狀態不超過 5 分鐘時發出警示，可以轉譯為：(HealthState != Ok and Now - LastOkTransitionTime > 5 minutes)。

## 範例：報告和評估應用程式健康狀態
下列範例會從來源 MyWatchdog 透過稱為 fabric:/WordCount 之應用程式上的 Powershell 來傳送健康狀態報告。健康狀態報告會在「Error」健康狀態中包含健康狀態屬性可用性的相關資訊，並包含「Infinite」的 TTL。然後它會查詢應用程式健康狀態，並傳回已彙總健康狀態錯誤和已報告健康狀態事件，作為健康狀態事件清單的一部分。

```powershell
PS C:> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## 健康狀態模型使用方式
健康狀態模型可讓雲端服務和基礎 Service Fabric 平台進行調整，因為監視及健康狀態決定會分散至叢集內的不同監視器。其他系統在叢集層級具有單一集中式服務，可剖析服務所發出的所有*潛在*實用資訊。這會防礙其延展性，且不允許其收集非常明確的資訊來協助識別問題和潛在問題，並盡可能接近根本原因。

健康狀態模型常用來監視和診斷，以便評估叢集和應用程式健康狀態，以及監視升級。其他服務會使用健康狀態資料來執行自動修復、建置叢集健康狀態歷程記錄，以及發出特定情況的警示。

## 後續步驟
[如何檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)

[使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[新增自訂 Service Fabric 健康情況報告](service-fabric-report-health.md)

[如何在本機上監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->