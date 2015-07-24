<properties
   pageTitle="節點緩衝區百分比"
   description="資源平衡器的節點緩衝區百分比角色概觀"
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

# 節點緩衝區百分比概觀

目前，客戶可以將節點的容量限制指定為條件約束，而資源平衡器會根據優先順序來遵守此條件約束。如果容量的條件約束優先順序較高 (不能違反節點容量) 且叢集節點高度使用時，有可能不會立即容錯移轉，或可能違反節點容量。

如果含主要複本的節點故障，且含次要複本的節點快達到節點容量時，會發生問題掃描。在此情況下，如果主要負載大於次要負載，次要複本一定要過度使用節點或具備複本複製，才能立即升級。

執行主動式封裝邏輯時，較高的叢集節點數量會接近節點容量限制。節點緩衝區百分比是一項功能，可提供客戶選項以指定應保留可用的節點百分比，藉此避免在容錯移轉期間增加容錯移轉時間或過度使用節點的情況。您不應將新服務的複本加入節點緩衝區空間，但資源平衡器應能夠使用總節點容量 (度量的緩衝區空間) 以進行容錯移轉，並新增遺失的複本。

即使主動度量封裝功能關閉時，您仍可使用這項功能。

程式碼範例顯示，度量的節點緩衝區百分比是透過叢集資訊清單內的 FabricSettings 項目，針對每個度量進行設定。

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

名稱為 "MetricName" 的度量值 0.1 表示，針對 "MetricName" 度量的每個節點容量都應保持 10% 的可用容量。

如果在這個區段中未指定值，則將使用預設值 0。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[資源平衡器架構](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO2-->