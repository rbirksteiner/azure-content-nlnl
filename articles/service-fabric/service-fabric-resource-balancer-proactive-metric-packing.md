<properties
   pageTitle="主動度量封裝"
   description="資源平衡器中主動度量封裝的使用概觀"
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

# 主動度量封裝

Service Fabric 資源平衡器的常見組態是為了讓每個節點上的每個度量達到相等的使用率。資源平衡器也負責在新的服務要求出現時，為該服務尋找位置。如果沒有足夠的可用空間放置新的服務複本 (所有服務分割的所有複本)，資源平衡器會嘗試移動現有的工作負載，以為其建立空間。雖然這非常正常，但視叢集的飽和度和工作負載分散程度而定，這項作業可能會很耗時。

例如，假設叢集已充分利用，但客戶想要加入具有較大預設負載 (例如一或多個度量的最大節點容量) 的新服務時，資源平衡器可能需要移動許多複本，才能加入新的服務。此外，如果是具狀態且較大的服務，因為資料需要複製，所以執行必要的動作可能需要一些時間。上述這兩個情況都可能會增加新服務的建立時間。雖然服務通常可以容忍偶爾緩慢的建立時間，但某些工作負載卻無法容忍而必須盡快建立，這表示在穩定狀態下，資源平衡器必須確定叢集「經過重組」，以提供更高機率來保障有足夠可用的空間給新的工作負載。

主動度量封裝 (又稱為重組) 機制會跟資源平衡器的平衡階段一起搭配目標執行，以將服務建立時間降至最低，方法是將負載封裝到較少的節點上，而不是在平衡期間將其分散。為度量進行重組設定時，資源平衡器的目標是要達到最大平均標準差，而不是平衡時使用的最少平均標準差。

使用最大偏差時，資源平衡器會嘗試盡可能在某些節點上放置最多的服務，並盡可能讓最多節點保留空白。除此之外，其中一個放置新服務的基本條件約束是：複本不能位於同一個升級網域或容錯網域中。由於目標是要能夠快速地加入新服務，因此資源平衡器應將目標設在保持升級網域和容錯網域間負載分佈的最小標準差 (每個升級網域/容錯網域的服務負載總和)。這樣一來，每個升級網域/容錯網域的可用空間量都會相同。重組也會遵守同質性、放置約束限制和節點度量容量等系統中的所有其他條件約束。

## 資源平衡器叢集組態
在叢集資訊清單中，資源平衡器下方的下列數個不同組態值可定義度量封裝功能的整體行為：

### DefragmentationMetrics – 資源平衡器應該考慮進行主動封裝/重組的度量。

這份清單中應指定所有已設定的度量 (如同活動和平衡臨界值清單)。如果度量值指定為 "true"，即會將其視為重組度量，而設為值 "false" (或此清單中未指定) 就不會將它視為重組度量。

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

在資源平衡器執行平衡階段 (該階段會進行度量封裝邏輯) 之前，平衡臨界值會控制叢集與特定度量間的片段程度。如果將度量視為重組度量，平衡臨界值即為資源平衡器進行叢集重組之前，每個升級網域/容錯網域允許存在的最常使用節點與最少使用節點之比率下限。如果任何升級或容錯網域的這項比率小於臨界值，重組階段會開始運作。

下圖顯示兩個範例，其中針對指定度量的平衡臨界值是 10。

![平衡臨界值][Image1]

請注意，在這個階段中，節點上的「使用率」並不會考慮節點容量所判斷的節點大小，而只會考慮針對特定度量目前報告的節點絕對使用量。

如果未指定度量，預設值為 1。在此情況下，將會執行重組，直到叢集中每個容錯網域和每個升級網域至少有一個空的節點為止。

值 0 表示平衡階段期間不考慮此度量，不論它是否為重組度量皆然。

程式碼範例顯示，度量的平衡臨界值是透過叢集資訊清單內的 FabricSettings 項目，針對每個度量進行設定。

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[資源平衡器架構](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png
 

<!---HONumber=July15_HO2-->