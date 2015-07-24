<properties
   pageTitle="資源平衡器架構"
   description="Service Fabric 資源平衡器的架構概觀"
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

# 資源平衡器架構概觀

![資源平衡器架構][Image1]

Service Fabric 資源平衡器是由單一的集中式資源平衡服務和存在於每個節點上的元件構成，其會在概念上分別與 Service Fabric 容錯移轉管理員和本機 Service Fabric 節點共存。

本機代理程式負責收集和緩衝處理本機節點上執行之服務的負載報告、將報告傳送到資源平衡器服務，並將失敗和其他事件報告給容錯移轉管理員和資源平衡器 (上述 1 和 2)。資源平衡器服務與容錯移轉管理員會共同合作以回應系統中的載入事件和其他事件，例如複本或節點失敗、負載報告以及已建立和刪除的服務和應用程式。例如，當複本失敗，容錯移轉管理員會要求 Service Fabric 資源平衡器依據不同節點的負載資料來建議取代位置。同樣地，當收到新的服務要求時，容錯移轉管理員也會要求資源平衡器提供放置該服務的建議位置。在這些情況下，資源平衡器會回應各種服務組態 (3) 變更，然後由容錯移轉管理員來執行。在上述範例中，容錯移轉管理員會在節點上建立新的複本，以產生最佳的整體平衡 (4)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

資源平衡功能：

- [描述叢集](service-fabric-resource-balancer-cluster-description.md)
- [描述服務](service-fabric-resource-balancer-service-description.md)
- [報告動態負載](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [主動度量封裝](service-fabric-resource-balancer-proactive-metric-packing.md)
- [節點緩衝區百分比](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=July15_HO2-->