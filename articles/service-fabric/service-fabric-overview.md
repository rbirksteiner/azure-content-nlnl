<properties 
   pageTitle="Service Fabric 概觀" 
   description="組成微服務的 Service Fabric 概觀。Service Fabric 是分散式系統平台，用於建置可擴充、可靠且可輕鬆管理的雲端應用程式。" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Service Fabric 概觀
Service Fabric 是一種分散式系統平台，用來建置可調整、可靠且輕鬆管理的雲端應用程式。Service Fabric 可解決開發與管理雲端應用程式時遭遇的重大挑戰。透過使用 Service Fabric，開發人員與管理員能夠避免解決複雜的基礎結構問題，改為專注於實作關鍵且嚴格要求的工作負載，並了解其為可調整、可信賴及可管理的。Service Fabric 代表新一代的中介軟體平台，可建置與管理這些企業級的 Tier-1 雲端規模服務。

## 由微服務組成的應用程式 ##
Service Fabric 可讓您建置並管理由微服務組成的應用程式，這類應用程式可調整且可靠，以非常高的密度在共用的機器集區上執行 (一般稱為 Service Fabric 叢集)。其提供精密的執行階段，可建置分散式、可擴充的無狀態與可設定狀態的微服務與完整應用程式管理功能，以佈建、部署、監視、升級/修補及刪除所部署的應用程式。

Service Fabric 提供技術支援給目前許多的 Microsoft 服務，例如 Azure SQL Database、Azure DocumentDB、Cortana、Power BI、Microsoft Intune、Azure 事件中樞，以及許多核心 Azure 服務和商務用 Skype 等等。

Service Fabric 針對建立「雲端原生」服務量身打造而成，此類服務可視需要以小規模開始，接著擴充為包含成千上萬個機器的大規模服務，並能在區域中或區域間的可用性設定組之間建立 Service Fabric 叢集。

現今的網際網路級別服務是使用微服務建立。例如通訊協定閘道、使用者設定檔、購物車、清查處理、佇列、快取等，都是微服務。Service Fabric 是微服務平台，會給予每一個可以是無狀態或可設定狀態的微服務唯一的名稱。

Service Fabric 為由微服務組成的應用程式，提供完整的執行階段與生命週期管理功能。其將微服務裝載在容器中，這些容器部署在整個 Service Fabric 叢集之中並已啟動。正如同密度增加程度的順序可透過從 VM 移至容器來決定，當從容器移至微服務時，會有類似的密度程度順序。例如，一個建立在 Service Fabric 上的 Azure SQL Database 叢集是由數百個機器所組成，這些機器執行數萬個容器，而容器總共裝載了數十萬個資料庫 (每個資料庫都是 Service Fabric 的可設定狀態微服務)。事件中樞與上述的其他服務也是同樣的狀況。這也是為何超大規模一詞可用於描述 Service Fabric 的容量，如果容器的密度高，微服務就有超大的規模。

![Service Fabric 平台][Image1]

## 無狀態與可設定狀態的 Service Fabric 微服務

無狀態微服務 (例如通訊協定閘道、Web Proxy 等) 不會維護該服務任何要求及其回應之外的任何可變動狀態。Azure 雲端服務背景工作角色即為無狀態服務的範例。可設定狀態的微服務 (例如使用者帳戶、資料庫、裝置、購物車、佇列等) 會維護要求及其回應外的可變動授權狀態。現今的網際網路級別應用程式包含無狀態與可設定狀態微服務的組合。
 
為何可設定狀態的微服務很重要？ 為何不所有一切都使用可設定狀態服務就好了？ 有兩個原因：

1) 能夠在同一部機器上完成程式碼與資料，因此可建構高輸送量、低延遲性、容錯 OLTP 服務，如互動式店面、搜尋、物聯網 (IoT) 系統、貿易系統、信用卡處理、詐欺偵測系統，及個人記錄管理等。

2) 應用程式設計簡化成可設定狀態的微服務，因此不需要為滿足純無狀態應用程式的可用性與延遲需求時傳統上需要的其他佇列與快取。可設定狀態服務有高可用性、低延遲性的特性，這表示完整管理應用程式的移組件變少。

如需應用程式模式與使用 Service Fabric 設計的詳細資訊，請參閱＜[應用程式案例](../service-fabric-application-scenarios)＞

## 應用程式生命週期管理
從開發到部署、每日管理、維護，到最終的解除委任，Service Fabric 為雲端應用程式的完整應用程式生命週期管理 (ALM) 提供第一等的支援。

Service Fabric ALM 功能讓應用程式管理員/IT 操作員能夠使用簡單、低接觸的工作流程來佈建、部署、修補與監視應用程式。這些內建的工作流程能大幅降低 IT 操作員的負擔，讓應用程式持續可用。

大多數的應用程式都結合了無狀態與可設定狀態的微服務，及其他一起部署的 EXE/執行階段。Service Fabric 擁有應用程式與封裝微服務的強式型別，因此能部署多個應用程式執行個體，每個執行個體都能獨立管理與升級。重要的是，Service Fabric 能夠部署*任何*的可執行檔或執行階段，並使其可靠。例如，可用於部署 ASP.NET 5、node.js、指令碼，或可組成應用程式的任何一切。
  
如需應用程式生命週期管理的詳細資訊，請參閱＜[應用程式生命週期](../service-fabric-application-lifecycle)＞

## 主要功能
藉由使用 Service Fabric，您可以：

- 開發可大幅調整且自我修復的應用程式。

- 採用「機器的資料中心」法開發。本機開發環境的程式碼與在 Azure 資料中心執行的程式碼相同。
 
- 開發由微服務、可執行檔及其他您所選擇的應用程式架構 (例如 ASP.NET、nodejs 等) 組成的應用程式。

- 開發無狀態與可設定狀態的 (微) 服務，並使其高度可靠。

- 使用可設定狀態的 (微) 服務取代快取和佇列，以簡化應用程式的設計。
 
- 部署應用程式 (單位為秒)。

- 部署至 Azure，或部署至執行 Windows Server 的內部部署雲端，但零程式碼變更。只要撰寫一次，就能部署至任何的 Service Fabric 叢集。

- 部署密度比虛擬機器高的應用程式，亦即為每部機器部署數百或數千個應用程式。

- 並列部署相同應用程式的不同版本，每個版本可獨立升級。
 
- 無須停機即可管理可設定狀態應用程式的生命週期，包括即時升級與非即時升級。

- 使用 .NET API、Powershell 或 REST 介面管理應用程式。
 
- 獨立地升級與修補應用程式內的微服務。

- 監視和診斷應用程式的健全狀況，並設定用來執行自動修復的原則。

- 應用程式是根據可用資源調整的，因此能輕鬆地向上或向下調整 Service Fabric 叢集。

- 監視自我修復資源平衡器如何協調應用程式在 Service Fabric 叢集之間的重新分配，以從故障中復原，並根據可用資源最佳化負載的散佈。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需詳細資訊：請參閱＜[技術概觀](../service-fabric-technical-overview)＞。

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=July15_HO2-->