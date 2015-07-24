<properties 
   pageTitle="使用 Service Fabric 的應用程式案例和設計" 
   description="應用程式的類別。使用無狀態和可設定狀態的服務的應用程式設計" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Service Fabric 應用程式案例

Service Fabric 和 Azure 提供可靠且彈性的雲端基礎結構平台，可讓您執行許多類型的商務應用程式和服務。這些應用程式和微服務可以是無狀態或可設定狀態，而且它們是跨虛擬機器進行資源平衡，以達到最佳效率。Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。您可以輕鬆地根據變更的資源需求向上或向下調整您的應用程式。

Azure 中的 Service Fabric 平台適用於下列應用程式和服務的類別：

- **高可用性服務**：Service Fabric 服務提供極快速的容錯移轉。多個次要服務複本仍然可以使用。如果節點因為硬體失敗而停機，其中一個次要複本會立即升級為主要複本，只會產生對於客戶而言可以忽略的服務損失。服務可以快速且輕鬆地從幾個執行個體向上調整為數千個執行個體，然後向下調整為幾個執行個體，視您的資源需求而定。您可以使用 Service Fabric 來建置及管理這些可擴充雲端服務的生命週期。

- **可延展服務**：可以分割個別服務，以便在叢集中向外延展。此外，可以建立多個 Service Fabric 服務，並向外延展。
 
- **非靜態資料的計算**：Service Fabric 可讓您建置資料 I/O 和計算密集、可設定狀態的應用程式。Service Fabric 應用程式中的處理和資料資源會放在一起，因此，當您的應用程式需要快速讀取和寫入時，與外部資料快取或儲存體層相關聯的網路延遲會消除。例如，假設您有一個應用程式執行接近即時的廣告選取項目，其往返時間 < 100 毫秒，廣告選取項目規則的計算會提供回應靈敏的經驗給使用者。
 
- **工作階段架構的互動式應用程式**：如果您的應用程式，例如線上遊戲或立即訊息，需要低度延遲讀取和寫入，Service Fabric 會很有用。Service Fabric 可讓您建置這些互動、可設定狀態的應用程式，而不需要建立不同的存放區或無狀態應用程式所需的快取。
 
- **分散式圖形處理**：社交網路的成長已開始大幅增加平行分析大型圖形的需求。快速調整和平行載入處理讓 Service Fabric 成為處理大型圖形的自然平台。Service Fabric 可讓您為群組建置高度可延展服務，例如社交網路、商務智慧和科學研究。
 
- **資料分析和工作流程**：Service Fabric 的快速讀取/寫入可以成為必須可靠地處理事件或資料流的應用程式的依靠。Service Fabric 也會啟用描述處理管線的應用程式，結果必須是可靠的並且通過，而不會對下一個處理階段造成損失，包括交易和財務系統，其中資料一致性和計算保證是不可或缺的。

## 設計由無狀態與可設定狀態微服務組成的應用程式 ##
使用 Azure 雲端服務背景工作角色建置應用程式即為無狀態服務的範例。相較之下，無狀態微服務會維護要求及其回應以外的授權狀態，但是透過簡單 API 提供狀態複寫。可設定狀態的服務是所有應用程式高可用性 (HA) 民主化，而不只是資料庫和其他資料存放區。這是設計中的自然進度，因為應用程式已從使用單純的關聯式資料庫的 HA，進展到 NoSQL 資料庫，而現在應用程式本身就會管理對於必須保持計算的「熱」資料的狀態。

建置由微服務組成的應用程式時，您通常有無狀態 Web Apps (ASP.NET、node.js 等等) 的組合，呼叫無狀態和可設定狀態的商務中間層服務，使用 Service Fabric 部署命命，全部部署到相同的 Service Fabric 叢集。每個微服務的調整、可靠性和資源使用量的獨立性，在開發和生命週期管理中提供靈活性。
  
可設定狀態的微服務簡化應用程式設計，因此不需要為滿足純無狀態應用程式的可用性與延遲需求時傳統上需要的其他佇列與快取。可設定狀態服務有高可用性、低延遲性的特性，這表示完整管理應用程式的移組件變少。下列圖表說明設計無狀態與可設定狀態之應用程式之間的差異。在可設定狀態的情況下，藉由運用[可靠的服務](../Service-Fabric/service-fabric-reliable-services-introduction.md)和[可靠的執行者](service-fabric-reliable-actors-introduction.md)程式設計模型，可設定狀態的服務會減少應用程式的複雜度，同時達到高輸送量和低度延遲。

## 使用無狀態服務建置的應用程式##
![使用無狀態服務的應用程式][Image1]

## 使用可設定狀態的服務建置的應用程式##
![使用無狀態服務的應用程式][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟


開始使用 Service Fabric [可靠的服務](service-fabric-reliable-services-quick-start.md)和[可靠的執行者](service-fabric-reliable-actors-get-started.md)程式設計模型建置無狀態與可設定狀態的服務。

另請參閱下列主題：

[定義和管理服務狀態](service-fabric-concepts-state.md)

[服務的可用性](../service-fabric-concepts-availability-services.md)

[Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)

[分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=July15_HO2-->