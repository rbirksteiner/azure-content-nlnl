<properties
   pageTitle="技術概觀"
   description="Service Fabric 技術概觀。討論重要概念與架構概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Service Fabric 技術概觀

Service Fabric 是分散式系統平台，可讓您輕鬆建置可擴充、可靠、低延遲且可輕鬆管理的雲端應用程式。換句話說，您可以專注於業務需求，讓 Service Fabric 負責確保應用程式隨時可用及擴充。

## 重要概念

叢集 - 由虛擬或實體機器連結組成的網路，應用程式執行個體可部署於其中。叢集可擴充至數千部機器。

節點 - 叢集中可定址的單位。節點具有放置屬性和唯一識別碼等特性。節點可以加入叢集，在 Fabric.exe 執行的作業系統上，與其執行個體相互關聯。

應用程式/應用程式類型 - (微) 服務的集合。應用程式類型可視為一或多個服務類型的容器。請參閱[應用程式模型](service-fabric-application-model.md)文章，了解叢集 (其本身包含多個節點) 如何由多個應用程式類型組成。

服務/服務類型 - 程式碼和執行獨立函數 (其可啟動並單獨執行) 的組態，例如佇列服務或資料庫服務。應用程式類型可能包含一或多個服務類型。服務類型共有兩種：

- 無狀態服務：服務具有狀態，且會保存至外部儲存體 (例如 Azure 資料庫或 Azure 資料表存放區)。當此服務的執行個體在節點上執行時，若該節點關閉，另一個執行個體會在其他節點上自動啟動。

- 具狀態服務：服務具有狀態，且可透過複寫叢集中其他節點上的複本達到高度可靠性。具狀態服務具有主要及多個次要複本。當此服務的複本在節點上執行時，若該節點關閉，新的複本會在其他節點上自動啟動。若這又是主要複本的話，則次要複本會自動升級成新的主要複本。

應用程式執行個體 - 在叢集中，您可以為應用程式類型建立許多應用程式執行個體，並分別給予特定的名稱。每個應用程式執行個體都能單獨管理，脫離相同或不同類型的其他應用程式執行個體獨自建立版本。此外，應用程式執行個體也會定義資源和安全性隔離。

服務執行個體 - 已針對服務類型具現化的程式碼。每個服務執行個體都有以 `fabric:/` 開頭的唯一名稱，且會與已命名的特定應用程式執行個體相互關聯。

應用程式套件 - 服務程式碼封裝與組態檔的集合，兩者結合用於特定應用程式。這些是已部署的實體檔案，且只是檔案和資料夾格式配置而已。例如，電子郵件應用程式的應用程式套件可能包含佇列服務封裝、前端服務封裝以及資料庫服務封裝。

程式設計模型 - Service Fabric 提供兩種建置應用程式的程式設計模型：

- 可靠服務 - 根據 `StatelessService` 和 `StatefulService` .NET 類別建置無狀態與具狀態服務的 API，會將狀態儲存在.NET 可靠集合中 (字典及佇列)，並能插入各種不同通訊堆疊，例如 Web API 和 WCF。此程式設計模型適用於需要跨多個狀態單位執行計算作業的應用程式。

- 可靠執行者 - 透過虛擬執行者程式設計模型建置無狀態與具狀態物件的 API，適用於具有多個獨立狀態單位及計算需求的應用程式。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
若要深入了解 Service Fabric，請參閱：

- [應用程式模型](service-fabric-application-model.md)
- [應用程式案例](service-fabric-application-scenarios.md)
 

<!---HONumber=July15_HO2-->