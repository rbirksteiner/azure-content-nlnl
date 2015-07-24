<properties
   pageTitle="資源平衡器服務描述"
   description="設定資源平衡器使用的服務描述概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# 服務描述概觀

## 放置條件約束
在服務上的[放置條件約束](../service-fabric-placement-constraint)是一種機制，其中特定服務執行個體會選取它需要的節點屬性。它們和節點屬性一樣是索引鍵/值組，可描述屬性名稱和服務對值的需求。個別陳述式可以和簡單的布林邏輯群組在一起，以建立必要的條件約束。請注意，Service Fabric 資源平衡器會解譯條件約束。

服務的放置條件約束可透過服務或應用程式資訊清單來定義，亦可直接在程式碼中定義。

服務資訊清單提供 ServiceTypes 的定義。

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

應用程式資訊清單提供 ServiceTemplates 或 DefaultServices 的定義。

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

下圖顯示在具有節點的叢集中，可放置這項服務的唯一有效節點為 N5。

![放置條件約束][Image1]

如果服務針對某個屬性指定條件約束，但該屬性的特定節點尚未定義，則不論指定的值為何，皆不會將該節點視為相符項目。

## 服務同質性
服務同質性是一種特殊形式的放置條件約束，可描述基於各種原因彼此具有本機相依性的不同服務，這表示這兩個服務必須在相同節點上執行才能運作。雖然這種架構不是 Service Fabric 應用程式的常態，Service Fabric 仍會將它辨識為舊版應用程式特定類型可能的轉換架構，而提供這項功能。

建立兩個服務之間的服務同質性可確保這些服務的主要複本一律會並存在同一個節點上。

同質關聯性是由父子式階層表示，也就是會有「父系」和「子系」。在關聯性中，第一個建立的服務為父系，第二個建立的服務為子系。關聯性會模型化為「硬性」條件約束。

服務同質性目前具有下列限制：

- 不能跨無狀態與具狀態的服務使用。
- 無法跨含不同執行個體計數的無狀態服務使用，例如，兩個無狀態服務在建立時都應具有相同的 InstanceCount 屬性。
- 不能跨含不同複本數目的變動性或持續性具狀態服務使用，例如，兩個服務都應指定相同的 Target 和 Min ReplicaSetSizes 值。
- 不能搭配分割服務使用。每個服務皆必須有 FABRIC_PARTITION_SCHEME_SINGLETON 資料分割配置。
- 同質關聯性和服務描述的其他屬性一樣，都會在建立服務時設定，而且無法修改。
- 不允許服務的鏈結。如果有多個服務必須帶入同質關聯性，則必須使用「星型」模型。

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

程式碼範例顯示 DefaultServices 定義的替代使用。

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

程式碼範例示範如何在建立包含的應用程式之後，建立同質關聯性。

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 資源平衡度量
服務放置條件約束和節點屬性代表硬式規則和原則 (其描述哪些服務的排列有效)，而資源平衡度量通常有助於描述叢集內服務的最佳排列方式。服務所定義的度量有數個不同的屬性，以描述叢集內的度量使用方式。

### 使用預設度量
首先要了解的重點是，對服務來說，處理資源度量完全是選擇性的，且 Service Fabric 資源平衡器會預設使用部分「內建」的系統度量來執行基本資源平衡，而不需要服務或叢集管理員採取任何動作。Service Fabric 資源平衡器會在叢集內追蹤及平衡的預設度量為 PrimaryCount、ReplicaCount 和 Count。PrimaryCount 是節點上主要具狀態服務複本的計數。PrimaryCount 是節點上所有具狀態服務複本的計數。Count 是節點上所有服務物件 (包含無狀態和具狀態服務) 的總計數。通常，PrimaryCount 度量會被視為最重要的，因此具有最高權數，如下一節所述。PrimaryCount 度量後接 ReplicaCount 度量，最後再接 Count 度量。

下圖顯示三個具狀態服務 (每個服務含三個複本) 的預設平衡範例。

![預設度量][Image2]

對許多服務來說，預設度量通常已經足夠，除非有其他度量或功能的特定需求，不然都應使用預設度量。

### 定義自訂的度量
如果特定服務的預設度量不足，或者服務針對特定資源 (例如磁碟空間或記憶體) 有未知的需求，就應該使用自訂度量。當服務有一或多個必須取得平衡以防止爭用的資源時，自訂度量就很有用。這些度量的使用方式在叢集內的複本之間有很大的差異，例如，某個主要複本可能會使用 100% 的度量，而另一個可能只使用 20%。您也可以使用自訂的度量來擷取電腦上嚴格限制的任何資源，例如記憶體、磁碟空間或連線。有時候，您也可以建立其他自訂度量，以擷取或代表在節點上沒有上限的度量，但務必要從服務的方面來代表工作及資源耗用，舉「目前未完成交易」的度量來說：雖然此度量不太可能針對指定節點來限制「容量」，仍可透過確保這項度量平均分散於整個叢集而獲得效能優勢。

請注意，如果服務有定義任何自訂度量，該服務就不會使用預設的系統提供度量。預設度量仍然可用，但必須在建立時就明確將它們重新包含在服務的度量清單中。

### 度量名稱
當您建立服務時，只要定義度量名稱，即可建立自訂的度量。請注意，Service Fabric 資源平衡器會依據度量的名稱來建立度量關聯，因此如果度量在節點定義內或跨服務間做為容量使用，則度量名稱必須完全符合，Service Fabric 資源平衡器才能將它們產生關聯。

### 度量權數
當服務定義多個度量時，您也可以定義這些度量的度量權數。不同的度量權數可指示 Service Fabric 資源平衡器哪些度量對服務的功能來說更加重要。例如，記憶體內部的佇列可能會受到網路頻寬的影響，但是最大的限制可能還是來自節點上實際的記憶體使用量。因此，雖然佇列可能會有多個度量，但代表記憶體使用量的度量會具有最高權數。同樣地，持續性資料庫可能要仰賴記憶體和磁碟使用量，雖然有限的記憶體會降低處理複雜查詢的能力，但用完磁碟空間會阻礙後續的儲存作業，後者可能會造成更嚴重的問題。因此，持續性存放區可能會選擇將磁碟空間設為具有較高權數的計數。

請注意，只有當 Service Fabric 資源平衡器無法完全平衡一組度量，而必須尋找方案以讓某個度量整體上最終比其他度量更為失衡時，才會在其中使用權數。在此情況下，比起權數較高的計數，會將權數較低的計數判斷為平衡優先順序較低，因此 Service Fabric 資源平衡器就會提供偏好較高加權計數平衡器的提案。

此外，當 Service Fabric 資源平衡器發現有多個服務依據名稱定義了相同的度量，就會將這些度量視為相同的度量。在度量相同但是權數不同的情況下，資源平衡器會平均指定的權數以判斷要在執行階段期間使用的實際權數。因此，如果某個服務將權數定義為零，表示它不需要平衡，但另一個服務將相同計數定義為具有高權數時，實際權數會取兩者中間的值。

![度量權數][Image3]

此範例說明兩個不同度量權數案例導致的兩個不同平衡結果，其中更高優先順序的度量整體上會更妥善分佈。在左邊的範例中，B 是較佳的平衡；而右邊，A 是更佳的平衡。由於此範例中沒有平衡方案可以平衡這兩個度量，因此 Service Fabric 資源平衡器會使用度量的權數來判斷何者比較重要，也就是在平衡期間要偏好哪個度量。

請注意，如果服務需要追蹤度量，但不需要針對這項度量進行平衡，則可將計數權數設為零。例如，假設某個度量在數個節點上都有明確的限制，但它的「跨節點平均使用率」度量 (也就是一般資源平衡的保證) 並不重要。對於這一類不需要平衡但必須強制在節點上設定限制的度量，可將平衡權數定義為零。在執行階段期間，Service Fabric 資源平衡器會強制執行容量，但不會嘗試主動平衡度量，即使該度量的使用情況在節點之間非常失衡亦同。

### 主要或次要角色的度量預設負載
在定義服務的度量時，請提供當服務為主要或次要角色時，該度量預期的耗用量。這項資訊不僅有助資源平衡器能一開始就以有效率的方式放置服務，還可以在整個存留期間，做為相關度量之實際服務使用情況的適當近似值。Service Fabric 資源平衡器不只會在放置您的服務時自動將此度量耗用量納入考量，在它必須基於平衡或因應叢集中的其他變更來移動複本時，也會這麼做。如果服務的負載相當容易預測且非常穩定，表示您可藉由設定這些值，讓服務選擇不在執行階段期間報告負載。

下列程式碼範例顯示某個服務完全定義兩個自訂的度量，一個用於記憶體使用量，另一則用於磁碟空間。

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

程式碼範例顯示服務資訊清單的 ServiceTypes 定義。

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

程式碼範例顯示應用程式資訊清單的 ServiceTemplates 定義。

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
程式碼範例顯示應用程式資訊清單的 DefaultServices 定義。``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

由於預設負載值不會更新 (除非服務特別選擇要在執行階段期間透過程式碼來報告負載)，因此這些值也可用來建立更多「容量保留」資源平衡模型。比方說，如果工作負載一般分為數個大小值區，且已知數目的工作單位可以隨時放在節點上，則可建立「單位」的自訂度量，再根據單位來定義節點容量和服務預設負載。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[資源平衡器架構](service-fabric-resource-balancer-architecture.md)、[放置條件約束](service-fabric-placement-constraint.md)

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=July15_HO2-->