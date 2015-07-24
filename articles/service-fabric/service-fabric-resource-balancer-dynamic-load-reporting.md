<properties
   pageTitle="動態負載報告"
   description="資源平衡器的動態負載報告概觀"
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

# 動態負載報告概觀

在執行階段期間，具狀態與無狀態的服務物件可以透過 ReportLoad 方法 (IStatefulServicePartition 和 IStatelessServicePartition 介面的成員) 來報告負載。執行階段負載值的報告非常重要，因為它可以確保服務精確封裝成節點，並協助中央 Service Fabric 資源平衡器精確追蹤資源使用率，讓節點上的服務資源體驗更加順暢。

請注意，當複本報告負載時，會先與其他負載報告一起進行本機批次處理，再將單一報告傳送至資源平衡器。此程序表示如果服務很快就重複報告負載，實際上只會將最後一個報告傳送給資源平衡器。

當 Service Fabric 資源平衡器執行時，會檢查所有節點彙總的全部負載資訊，並執行部分檢查。這些檢查包含叢集資訊清單中所定義的度量平衡臨界值和活動臨界值。它們會決定叢集的失衡程度是否需要執行資源平衡器，以及是否有任何特定節點容量已超過所定義的任何度量容量。在容量超過的情況中，資源平衡器會先考慮容量超過且共用度量 (超過容量) 之節點上的服務。若是叢集失衡的情況，資源平衡器會考慮所有與報告失衡度量之服務度量相關的所有服務。如果 Service Fabric 資源平衡器判斷有任何情況屬實，即會執行模擬，嘗試尋找更好的服務排列方式。

服務應在負載變更時報告負載，且不自行執行任何彙總或負載報告的平滑處理。

請注意，從服務報告負載當時起，這些負載報告即會取代服務建立時所定義的預設主要和次要負載值，並成為 Service Fabric 資源平衡器在進行平衡或放置決策時所使用的新負載值。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊，請參閱：[資源平衡器架構](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO2-->