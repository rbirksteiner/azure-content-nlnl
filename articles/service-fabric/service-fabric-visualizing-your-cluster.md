<properties
   pageTitle="使用 Service Fabric 總管視覺化叢集"
   description="Service Fabric 總管是一種實用的 GUI 工具，可檢查和管理 Microsoft Azure Service Fabric 叢集中的節點與雲端應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2015"
   ms.author="jesseb"/>

# 使用 Service Fabric 總管視覺化叢集

Service Fabric 總管是一種視覺工具，可檢查和管理 Microsoft Azure Service Fabric 叢集中的節點與雲端應用程式。Service Fabric 總管可以連接到本機開發叢集和 Azure 叢集。如需 Service Fabric PowerShell Cmdlet 的資訊，請參閱**後續步驟**。

> [AZURE.NOTE]Azure 中尚無法建立 Service Fabric 叢集。

## Service Fabric 總管簡介

請確定您的本機開發環境是依照以下指示而設定：[設定 Service Fabric 開發環境](service-fabric-get-started.md)。

從您的本機安裝路徑 (%Program Files%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe) 執行 Service Fabric 總管。此工具將自動連接至本機開發叢集 (如果存在)。它會顯示叢集上的資訊，例如：

- 在叢集上執行的應用程式
- 叢集節點的相關資訊
- 應用程式和節點的健康情況事件
- 叢集中應用程式的負載
- 應用程式升級狀態的監視

![Service Fabric 叢集和已部署應用程式的視覺化呈現][servicefabricexplorer]

其中一個重要的視覺效果是叢集儀表板上看到的叢集對應 (例如按一下 [Onebox/本機叢集])。叢集對應會顯示一組升級網域和故障網域，以及哪些節點對應至哪些網域。請參閱 [Service Fabric 技術概觀](service-fabric-technical-overview.md)，以熟悉 Service Fabric 的主要概念。

![叢集對應會顯示每個節點屬於哪些升級網域和故障網域][clustermap]


## 檢視應用程式和服務

Service Fabric 總管可讓您探索在叢集上執行的應用程式。展開 [應用程式檢視]，以檢視應用程式、服務、分割區和複本的詳細資訊。

下圖顯示名為 **"fabric:/Stateful1Application"** 的應用程式，有一個名為 **"fabric:/Stateful1Application/MyFrontEnd"** 的無狀態服務和一個名為 **"fabric:/Stateful1Application/Stateful1"** 的具狀態服務。無狀態服務有一個分割區，以及一個在 **Node.4** 上執行的複本。具狀態服務有兩個分割區，每個分割區各有 3 個複本在數個不同的節點上執行。

![檢視在 Service Fabric 叢集上執行的應用程式][applicationview]

按一下應用程式、服務、分割區或複本，會提供該實體的詳細資訊。下圖顯示具狀態服務內其中一個主要複本的服務複本健康情況儀表板。這包括複本的角色、它在其中執行的節點、接聽的位址，其檔案在磁碟中的位置，以及健康情況事件。

![Service Fabric 複本的詳細資訊][replicadetails]


## 連接至遠端 Service Fabric 叢集

若要檢視遠端 Service Fabric 叢集，請按一下 [連接]，以顯示 [連接到 Service Fabric 叢集] 對話方塊。輸入叢集的 [ServiceFabric 端點]，然後按一下 [連接]。Service Fabric 端點通常是連接埠 19000 上，您的叢集服務接聽的公用名稱。

![設定與遠端 Service Fabric 叢集的連接][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

- [Testability 概觀](service-fabric-testability-overview.md)。
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [使用 PowerShell 部署 Service Fabric 應用程式](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
 

<!---HONumber=July15_HO2-->