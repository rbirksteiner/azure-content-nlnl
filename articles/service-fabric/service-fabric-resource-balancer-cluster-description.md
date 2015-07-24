<properties
   pageTitle="資源平衡器叢集描述"
   description="指定資源平衡器的叢集描述"
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

# 叢集描述

Service Fabric 資源平衡器可提供數種機制來描述叢集。在執行階段期間，資源平衡器會使用這些資訊，以確保放置服務的方式可保障在叢集中執行之服務的高可用性，以及叢集資源的最大使用率。資源平衡器用來描述叢集的功能包括容錯網域、升級網域、節點屬性和節點容量。除此之外，資源平衡器有一些可以調校其效能的組態選項。

## 重要概念

### 容錯網域：

容錯網域可讓叢集管理員定義可能在相同時間因為共用實體的相依性 (例如電源和網路來源) 而發生錯誤的實體節點。容錯網域通常代表這些共用相依性的相關階層，包括可能從容錯網域樹狀目錄中的較高點一起發生錯誤的更多節點。下圖顯示依據階層式容錯網域來架構的數個節點 (以資料中心、機架和刀鋒排序)。

![容錯網域][Image1]

 在執行階段期間，Service Fabric 資源管理員會考慮叢集中的容錯網域，並嘗試針對指定的服務分散複本，以免將它們全都放在不同的容錯網域中。在任何一個容錯網域發生錯誤時，此程序有助確保該服務和其狀態的可用性沒有遭到破壞。下圖顯示即使有足夠的空間將服務複本集中在一個或兩個網域中，仍會將其分散於數個容錯網域中。

![容錯網域][Image2]

容錯網域是在叢集資訊清單內進行設定。每個節點皆定義為位於特定的容錯網域內。在執行階段期間，資源管理員會結合全部節點的報告，以開發系統中所有容錯網域的完整概觀。

### 升級網域

升級網域是資源管理員會使用的另一項資訊。與容錯網域類似，升級網域會描述大約在相同時間關閉以進行升級的節點集。升級網域不是階層式，因此可以視為一種標記。

容錯網域是依據叢集中節點的實體配置來定義，升級網域則是由叢集管理員依據原則來決定。這些原則與叢集內的升級相關。升級網域越多的話，升級就越精確，因為其可限制執行叢集和服務的影響，並防止任何錯誤對大量的服務造成影響。根據其他原則 (例如 Service Fabric 在升級網域之後、移到下一個網域之前應等待的時間)，較多的升級網域也可能增加完成叢集升級所需的時間。

基於這些理由，資源管理員會收集升級網域的資訊，並將複本分散至叢集中的升級網域，就像容錯網域一樣。升級網域不一定與容錯網域一對一對應，但通常也不應該用來一對一對應。下圖顯示位於先前定義之容錯網域上的數個升級網域層。資源管理員仍會將複本分散到網域中，以免複本集中在某些容錯網域或升級網域中，藉此確保服務的高可用性，不受升級或叢集中的錯誤所影響。

![升級網域][Image3]

升級網域和容錯網域都會設為叢集資訊清單內節點定義的一部分，如下所示：

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
- 在 Azure 部署中，容錯網域和升級網域是由 Azure 指派；因此，您適用的節點與 Azure 基礎結構選項中的角色定義不包含容錯網域或升級網域資訊。

### 節點屬性
節點屬性是使用者定義的索引鍵/值組，其可針對指定節點提供額外的中繼資料。節點屬性的範例包括節點是否有硬碟機或視訊卡，其硬碟機的磁針數、核心和其他實體屬性。

節點屬性也可用來指定較抽象的屬性，以協助放置原則決策。例如，您可將「顏色」指派給叢集內的數個節點，做為分割成不同叢集區段的依據。程式碼範例顯示，節點的節點屬性是透過叢集資訊清單設為節點類型定義的一部分，其之後可套用到叢集內的多個節點。

NodeName、NodeType、FaultDomain 和 UpgradeDomain 位置屬性均有預設值。Service Fabric 會自動為您提供預設值，以便您在建立服務時使用這些值。使用者不應使用相同的名稱來指定自己的位置屬性。

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

在執行階段期間，資源平衡器會使用節點屬性資訊，以確保需要特定功能的服務會放在適當的節點上。

### 節點容量
節點容量是一種索引鍵/值組，用來定義特定節點可以使用的特定資源名稱和數量。程式碼範例顯示某個節點，它具有名為 "MemoryInMb" 的度量容量，而且預設有 2048 MB 的可用記憶體。容量是透過叢集資訊清單來定義，就像節點屬性一樣。

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![節點容量][Image4]

由於在節點上執行的服務，可以透過報告負載來更新其容量需求，因此資源平衡器會定期檢查節點是否達到或超過其度量的任何容量。如果是，資源平衡器可將服務移至負載較少的節點，以減少資源爭用並提升整體效能和使用率。

請注意，雖然節點類型的屬性區段中也會列出指定的度量，但如果是可以在執行階段期間使用之節點的屬性，則最好列為容量。將指定度量另外列為屬性時，可讓依據「最低硬體需求」的服務查詢含有放置約束限制的節點；如果在執行階段期間，其他服務也會使用資源的話，就可能需要這麼做。我們建議您也將其納為容量，讓資源平衡器可以採取其他動作。

新的服務建立時，Service Fabric 叢集資源平衡器會使用現有節點的容量及現有服務的使用量相關資訊，判斷是否有足夠容量可容納整個新的服務。如果容量不足，就會拒絕建立服務要求並顯示錯誤訊息，指出剩餘的叢集容量不足。


### 資源平衡器組態

在叢集資訊清單中，下列幾個不同組態值可定義資源平衡器的整體行為：

- 在資源平衡器回應之前，平衡臨界值會控制叢集與特定度量間的不平衡程度。平衡臨界值是指資源平衡器重新平衡叢集之前，允許存在的最常使用節點與最少使用節點之比率上限。

下圖顯示兩個範例，其中針對指定度量的平衡臨界值是 10。

![平衡臨界值][Image5]

請注意，在這個階段中，節點上的「使用率」並不會考慮節點容量所判斷的節點大小，而只會考慮針對特定度量目前報告的節點絕對使用量。

程式碼範例顯示，度量的平衡臨界值是透過叢集資訊清單內的 FabricSettings 項目，針對每個度量進行設定。

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- 活動臨界值可限制資源平衡器執行的頻率，方法是限制資源平衡器在出現大量絕對負載時的回應情況。如此一來，如果以特定的度量來說，叢集不是非常忙碌，資源平衡器就不會執行，即使叢集內有少量度量非常不平衡亦然。這種方法可防止進行實質效益很低的叢集重新平衡時帶來的資源浪費。下圖顯示度量的平衡臨界值為 4，且活動臨界值為 1536。

![活動臨界值][Image6] 請注意，活動和平衡臨界值都必須超過相同的度量，才會執行資源平衡器。觸發兩個不同的度量之一時，並不會執行資源平衡器。

程式碼範例顯示，如同平衡臨界值，活動臨界值是透過叢集資訊清單內的 FabricSettings 項目，針對每個度量進行設定。

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – 控制資源平衡器掃描資訊且必須檢查條件約束違規的頻率。條件約束違規包含：不符合的放置約束限制、所需執行個體或複本數量不足的服務、超過部分度量容量的節點、超載的容錯或升級網域，或使用平衡和活動臨界值以及叢集節點上之目前負載檢視導致的叢集失衡。重新整理間隔的定義是以秒為單位，而且預設值為 1。或者，您可以使用 MinConstraintCheckInterval 和 MinPlacementInterval 這兩個新的間隔來控制條件約束檢查與位置的頻率，如果定義這些新的參數，就無法使用與定義 PLBRefreshInterval。

- MinConstraintCheckInterval – 控制資源平衡器掃描資訊且必須檢查條件約束違規的頻率。條件約束違規包含：不符合的放置約束限制、所需執行個體或複本數量不足的服務、超過部分度量容量的節點、超載的容錯或升級網域，或使用平衡和活動臨界值以及叢集節點上之目前負載檢視導致的叢集失衡。條件約束檢查間隔的定義是以秒為單位。如果未定義條件約束檢查間隔，其預設值會等於 PLBRefreshInterval 的值 (兩個值不能同時指定)。

- MinPlacementInterval – 控制資源平衡器檢查是否有需要放置的新執行個體或複本並嘗試加以放置的頻率。放置間隔的定義是以秒為單位。如果未定義放置間隔，其預設值會等於 PLBRefreshInterval 的值 (兩個值不能同時指定)。

- MinLoadBalancingInterval – 建立資源平衡回合間隔的最短時間量。如果資源平衡器已針對來自上次 PLBRefreshInterval 間隔期間執行掃描的資訊採取動作，就至少要等到相同時間之後才會再次執行。它的定義是以秒為單位，而且預設值為 5。

請注意，這些值都比較積極，但如果平衡和活動的臨界值符合指定的度量，負載平衡整體來說，只會發生在叢集中。程式碼範例顯示特定的叢集是否不需要精確和作用中的負載平衡；您可以透過 FabricSettings 項目中的下列組態，將這些值設為比較保守。在此範例組態中，兩個條件約束檢查之間最短的時間距離為 10 秒，5 秒用於放置，而負載平衡會每隔 5 分鐘發生一次。請注意，在此情況下不需定義 PLBRefreshInterval。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

在下列第二個範例組態中，我們定義了 PLBRefreshInterval 和 MinLoadBalancingInterval。由於 PLBRefreshInterval 設為 2 秒，MinPlacementInterval 和 MinConstraintCheckInterval 的值也必須設定為 2 秒。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[資源平衡器架構](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=July15_HO2-->