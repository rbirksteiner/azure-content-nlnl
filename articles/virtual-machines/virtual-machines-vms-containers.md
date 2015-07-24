<properties 
	title="Virtual Machines and Containers in Azure" 
	pageTitle="Azure 中的虛擬機器和容器" 
	description="描述虛擬機器、Docker 和 Linux 容器，以及它們各自在 Azure 中的群組使用方式，包括每一項的優點，以及每種方法順暢運作的案例。" 
	services="virtual-machines" 
	solutions="infrastructure" 
	documentationCenter="virtual-machines" 
	authors="squillace" 
	manager="timlt" 
/>
	

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="infrastructure" 
	ms.workload="infrastructure" 
	ms.date="07/02/2015" 
	ms.author="rasquill" 
/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Azure 中的虛擬機器和容器

Azure 提供您建立在虛擬機器上的絕佳雲端解決方案，虛擬機器是根據實際的電腦硬體而模擬出來的電腦，為了是要靈活移動軟體部署，並且比實體硬體更能大幅強化資源的運用。過去幾年來，多虧有容器的 [Docker](https://www.docker.com) 方法和 docker 生態系統，Linux 容器技術大幅擴充了可開發和管理分散式軟體的方式。由於容器中的應用程式程式碼與主機 Azure VM 獨立，也與相同 VM 上的其他容器獨立，除了 Azure VM 賦予您的靈活度之外，您還可以在應用程式層級做更多的開發，擁有更高的部署靈活度。

**但這已經是眾所周知的舊聞了。** 「真正的」新聞是，Azure 甚至還提供您更多的 Docker 優點：

- [建立 Docker 主機](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)有[許多](virtual-machines-docker-with-xplat-cli.md)[不同](virtual-machines-docker-with-portal.md)的[方式](virtual-machines-docker-ubuntu-quickstart.md)，讓容器能符合您的情況
- 有 [Azure 資源管理員](resource-group-overview.md)和[資源群組範本](resource-group-authoring-templates.md)可簡化部署和更新複雜的分散式應用程式
- 能與大量專屬和開放原始碼組態管理工具進行整合

此外，因為您可以透過程式設計方式在 Azure 上建立 VM 和 Linux 容器，所以您也可以使用 VM 和容器「協調流程」工具來建立虛擬機器 (VM) 的群組，以及在 Linux 容器和 (即將可行的) [Windows Server 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)兩者內部署應用程式。

本文不只會討論這些高層級的概念，它也包含大量連結供您了解在 Azure 上與容器和叢集使用相關的詳細資訊、教學課程以及產品。如果您已經了解上述內容，只是需要連結，連結[就在這裡](#tools-for-working-with-containers)。

## 虛擬機器與容器之間的差異

虛擬機器是在 [Hypervisor](http://en.wikipedia.org/wiki/Hypervisor) 提供的獨立硬體虛擬化環境內執行。在 Azure 中，[虛擬機器](http://azure.microsoft.com/services/virtual-machines/)服務會為您處理所有作業：您只需要選擇作業系統、依您想要執行的方式加以設定，或者上傳您自己自訂的 VM 映像，就可以建立虛擬機器。「虛擬機器」是一種經過長時間測試、歷經考驗而根深蒂固的技術，而且有許多工具可用來管理作業系統，以及設定您安裝及執行的應用程式。從主機作業系統並不會看見虛擬機器中執行的任何事物，而從虛擬機器內執行的應用程式或使用者的角度來看，虛擬機器就像是一部獨立存在的實體電腦。

[Linux 容器](http://en.wikipedia.org/wiki/LXC)&mdash;包含使用 docker 工具以及其他方法建立及裝載的容器&mdash;不需要或不使用 Hypervisor 提供隔離。相反地，容器主機使用 Linux 核心的程序和檔案系統隔離功能，但是容器 (及其應用程式) 只能使用某些核心功能及其專屬隔離的檔案系統 (最低限度)。從容器內執行的應用程式的角度來看，容器就像是唯一的作業系統執行個體。包含在其中的應用程式看不見處理程序或其容器以外的任何其他資源。

因為在這種隔離和執行模型中，Docker 主機電腦的核心是共用的，而且容器的磁碟需求現在不包含整個作業系統，所以容器的啟動時間和所需的磁碟儲存體額外負荷兩者就變得非常非常小。

這種方式非常酷。

[Windows Server 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)對於在 Windows 執行的應用程式提供與 Linux 容器相同的優點。Windows Server 容器支援 docker 映像格式與 docker API。如此一來，使用與 Mac 和 Linux 上類似的命令就可以開發、發佈、擷取以及部署使用 Windows Server 容器的應用程式。這是除了 [Microsoft Visual Studio 中的新 docker 支援](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)之外的好處。較大的[容器生態系統](https://msdn.microsoft.com/virtualization/windowscontainers/about/container_ecosystem)將提供每個人工具來執行使用容器必須執行的工作。

這種方式也非常酷。

### 真的這麼好嗎？

是，也不是。容器就像任何其他技術一樣，沒有辦法避免分散式應用程式需要進行的所有工作。不過在此同時，容器真的會改變：

- 可以多快速開發和廣泛地共用應用程式程式碼
- 可以多快速，以及有多少信心測試應用程式程式碼
- 可以多快速，以及有多少信心部署應用程式程式碼

話雖如此，請記住容器是在容器主機，也就是在作業系統上執行，而在 Azure 中是在 Azure 虛擬機器中執行。即使您非常喜歡容器的概念，您還是需要一個裝載容器的 VM 基礎結構，但好處是，容器並不在意是在哪一個 VM 上執行 (雖然容器是否需要 Linux 或 Windows 執行環境很重要)。

## 容器適用的情況？

容器適用很多情況，但是比較鼓勵 ([Azure 雲端服務](http://azure.microsoft.com/services/cloud-services/)和 [Azure 服務網狀架構](service-fabric-overview.md)亦同) 建立單一服務、[微服務]導向的分散式應用程式，這類應用程式的設計是基於多個較小型、可組合的組件，而非基於較大型、強烈相關聯的元件。

尤其是對於像 Azure 這種您在需要時可以租用 VM 的公用雲端環境，特別適合。您不只可以獲得隔離、快速的部署以及協調流程工具，還能更有效率決定應用程式基礎結構。

例如，您目前有一個部署，有一個大型、高可用性的分散式應用程式，由 9 個 Azure VM 組成。如果此應用程式的元件可以部署在容器中，您可能只需要使用 4 個 VM，然後將您的應用程式元件部署在 20 個容器內，做為備援與負載平衡。

這只是一個範例，當然，您或許也可以在您的案例中這樣進行，您可以調整使用更多容器，而不是更多 Azure VM 達到最高的使用率，並且比以前更有效率地使用剩餘的整體 CPU 負載。

此外，還有許多不需要微服務方式的案例；您將會最了解微服務和容器是否對您最有幫助。

### 容器對開發人員的優點

一般而言，我們很容易看到容器技術持續進步，但是還有更多明確的優點。我們來看看 Docker 容器的範例。本主題目前不會深入討論 Docker (請閱讀 [Docker 是什麼？](https://www.docker.com/whatisdocker/)了解詳細內容，或 [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29))，但是 Docker 及其生態系統能夠對開發人員和 IT 專業人員提供極大的優點。

開發人員能夠很快速地開始使用 Docker 容器，因為使用 Linux 容器最簡單：

- 開發人員可以使用簡單、累加的命令來建立容易部署的固定映像，而且可以使用 dockerfile 自動化建置這些映像
- 開發人員可以使用簡單、[git](https://git-scm.com/) 式的發送和提取命令，輕鬆將這些映像共用到[公用](https://registry.hub.docker.com/)或[私用 docker 登錄](virtual-machines-docker-registry-on-azure-blob-storage.md) 
- 開發人員可以考慮隔離應用程式元件，而不是電腦
- 開發人員可以使用大量的工具，了解 docker 容器和不同的基本映像

### 容器對作業人員和 IT 專業人員的優點

結合容器和虛擬機器，也對 IT 和作業專業人員有好處。

- 內含的服務與 VM 主機執行環境隔離
- 內含的程式碼可驗證為完全相同
- 內含的服務可以啟動、停止，以及在開發、測試和生產環境之間快速移動

等諸如此類的功能，不過還有更多&mdash;刺激既有的企業，其中專業的資訊技術組織有一項工作是調整資源 (包括單純處理電力問題)，不僅配合各種讓企業生存下去所需的工作，還能提升客戶滿意度與影響範圍。小型企業、獨立軟體廠商和新創公司的需求完全相同，但是它們對於需求可能會有不同的描述。

## 虛擬機器適用的情況？

虛擬機器提供雲端運算的骨幹，這是不變的。虛擬機器雖然啟動速度慢一點、磁碟使用量較大，而且沒有直接對應到微服務架構，但是它們有非常重要的優點：

1. 虛擬機器預設能為主機電腦提供更穩固的預設安全性保護
2. 虛擬機器支援任何主要的作業系統和應用程式組態
3. 虛擬機器具備存在已久的工具生態系統進行命令與控制
4. 虛擬機器為主機容器提供執行環境

最後一項很重要，因為視應用程式的呼叫而定，內含的應用程式仍然需要特定的作業系統和 CPU 類型。請務必記住您要在 VM 上安裝容器，因為 VM 包含您想要部署的應用程式，容器無法取代 VM 或作業系統。

## VM 和容器的高階功能比較

下表說明在 VM 和 Linux 容器之間非常高階、不需要太多額外工作的功能差異。請注意，根據您自己的應用程式需求，可能需要增加或減少某些功能，如同所有軟體一般，額外進行一些工作可以支援更多功能，尤其是安全性部分。

| 功能 | VM | 容器 |
| :------------- |-------------| ----------- |
| 「預設」安全性支援 | 到比較高的程度 | 到稍微較低的程度 |
| 磁碟上需有記憶體 | 整套作業系統及應用程式 | 僅限應用程式需求 |
| 啟動花費時間 | 相當長的時間：作業系統啟動和應用程式載入 | 相當短的時間：因為核心已在執行中，只需要啟動應用程式 |
| 可攜性 | 經過適當準備則可攜 | 在映像格式內可攜；通常較小 | 
| 映像自動化 | 根據 OS 和應用程式有很大的差異 | [Docker 登錄](https://registry.hub.docker.com/)；其他

## 建立和管理 VM 群組和容器群組

到目前為止，任何架構師、開發人員或 IT 作業專家可能會想：「我如果可以自動化所有這些事物，這就真的是「資料中心即服務！」。

您說得沒錯，它的確可以，而且有任意數目的系統，您可能已經使用其中一些系統。您可以使用指令碼 (通常使用 [CustomScriptingExtension for Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) 或 [CustomScriptingExtension for Linux](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)) 管理 Azure VM 的群組並插入自訂程式碼。您可以&mdash;而且可能已經&mdash;使用 PowerShell 或 Azure CLI 指令碼自動化您的 Azure 部署，[像是這樣](virtual-machines-create-multi-vm-deployment-xplat-cli.md)。

您通常還會移轉到如 [Puppet](https://puppetlabs.com/) 和 [Chef](https://www.chef.io/) 等工具，大量自動化建立和設定 VM。([這裡](#tools-for-working-with-containers)有許多搭配 Azure 使用這些工具的連結)。

### Azure 資源群組範本

最近，Azure 發行了 [Azure 資源管理](virtual-machines-azurerm-versus-azuresm.md) REST API，以及更新的 PowerShell 和 Azure CLI 工具可輕鬆使用。您可以使用 [Azure 資源管理員範本](https://msdn.microsoft.com/library/azure/dn835138.aspx)，搭配使用下列的 Azure 資源管理 API，來部署、修改或重新部署整個應用程式拓撲：

- [使用範本的 Azure Preview 入口網站](https://github.com/Azure/azure-quickstart-templates)&mdash;提示：使用 [DeployToAzure] 按鈕
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)
- [Azure Powershell 模組](virtual-machines-deploy-rmtemplates-azure-cli.md)


### 部署和管理整個 Azure VM 群組和容器群組

有幾個很受歡迎的系統可以部署整個 VM 群組，並且在系統上面安裝 Docker (或其他 Linux 容器主機系統) 做為可自動化的群組。如需直接連結，請參閱下面的[容器和工具](#containers-and-vm-technologies)一節。有幾個系統可以做到更大或更小的程度，只是這份清單並不詳盡。這要視您的技能和案例而定，可能不一定有用。

Docker 有自己的 VM 建立工具 ([docker-machine](virtual-machines-docker-machine.md)) 以及一個負載平衡、docker-container 叢集管理工具 ([swarm](virtual-machines-docker-swarm.md))。此外，[Azure Docker VM 延伸模組](https://github.com/Azure/azure-docker-extension/blob/master/README.md)預設會支援 [`docker-compose`](https://docs.docker.com/compose/)，它可以跨越多個容器部署設定好的應用程式容器。

此外，您可以試試 [Mesosphere 的資料中心作業系統 (DCOS)](http://docs.mesosphere.com/install/azurecluster/)。DCOS 是根據開放原始碼 [mesos](http://mesos.apache.org/) 的「分散式系統核心」，可讓您將您的資料中心視為一個可定址的服務。DCOS 擁有幾個重要系統的內建套件，例如 [Spark](http://spark.apache.org/) 和 [Kafka](http://kafka.apache.org/) (以及其他)，以及例如 [Marathon](https://mesosphere.github.io/marathon/) (容器控制系統) 和 [Chronos](https://mesosphere.github.io/chronos/) (分散式排程器) 的內建服務。Mesos 衍生自在 Twitter、AirBnb 和其他 Web 規模的企業學習到的工作。

而 [Kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure) 則是一個 VM 和容器群組管理的開放原始碼系統，衍生自在 Google 學習到的工作。您甚至可以使用 [Kubernetes 搭配 Weave 提供網路支援](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)。

[Deis](http://deis.io/overview/) 是一個開放原始碼的「平台即服務」(PaaS)，可以輕鬆部署和管理您自己伺服器上的應用程式。Deis 建立在 Docker 和 CoreOS 上，提供輕量級 PaaS 以及以 Heroku 為靈感來源的工作流程。您可以輕鬆[建立 3 個節點的 Azure VM 群組，並且在 Azure 上安裝 Deis](virtual-machines-deis-cluster.md)，然後[安裝 Hello World Go 應用程式](virtual-machines-deis-cluster.md#deploy-and-scale-a-hello-world-application)。

[CoreOS](virtual-machines-linux-coreos-how-to.md) 是一個 Linux 散發套件，有最佳化的使用量、Docker 支援以及稱為 [rkt](https://github.com/coreos/rkt) 的自有容器系統，也有一個稱為 [fleet](virtual-machines-linux-coreos-fleet-get-started.md) 的容器群組管理工具。

Ubuntu 是另一個非常受歡迎的 Linux 散發套件，能完整支援 Docker，也支援 [Linux (LXC 式) 叢集](https://help.ubuntu.com/lts/serverguide/lxc.html)。

## 使用 Azure VM 和容器的工具

要使用容器和 Azure VM 必須使用工具。本節提供的清單，只包含與容器、群組和較大型組態相關、一些最有用或最重要的概念與工具，以及搭配這些工具所使用的協調流程工具。

> [AZURE.NOTE]這個領域的變化出乎意料地快速，我們將盡全力讓本主題及其連結保持在最新狀態，不過這可能也是個不可能的任務。請確定搜尋有興趣的主題，以獲得最新資訊！

### 容器和 VM 技術

一些 Linux 的容器技術：

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS 和 rkt](https://github.com/coreos/rkt)
- [Open Container Project](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Windows Server 容器連結：

- [Windows Server 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker 連結：

- [Visual Studio 2015 RC Tools for Docker - Preview](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker 工具：

- [Docker daemon](https://docs.docker.com/installation/#installation)
- Docker 用戶端
	- [Windows Docker Client on Chocolatey](https://chocolatey.org/packages/docker)
	- [Docker 安裝指示](https://docs.docker.com/installation/#installation)


Microsoft Azure 上的 Docker：

- [Azure 上 Linux 的 Docker VM 延伸模組](virtual-machines-docker-vm-extension.md)
- [Azure Docker VM 延伸模組使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組](virtual-machines-docker-with-xplat-cli.md)
- [從 Azure Preview 入口網站使用 Docker VM 延伸模組](virtual-machines-docker-with-portal.md)
- [在 Azure Marketplace 中快速開始使用 Docker](virtual-machines-docker-ubuntu-quickstart.md)
- [如何在 Azure 上使用 docker-machine](virtual-machines-docker-machine.md)
- [如何在 Azure 上搭配 swarm 使用 docker](virtual-machines-docker-swarm.md)
- [在 Azure 上開始使用 Docker 和 Compose](virtual-machines-docker-compose-quickstart.md)
- [使用 Azure 資源群組範本在 Azure 上快速建立 Docker 主機](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
- 內含應用程式對 `compose` 的[內建支援](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys)
- [在 Azure 上實作 Docker 私用登錄](virtual-machines-docker-registry-on-azure-blob-storage.md)

Linux 散發套件和 Azure 範例：

- [CoreOS](virtual-machines-linux-coreos-how-to.md)

組態、叢集管理以及容器協調流程：

- [CoreOS 上的 Fleet](virtual-machines-linux-coreos-fleet-get-started.md)

-	Deis
	- [建立一個 3 個節點的 Azure VM 群組、安裝 Deis，然後啟動 Hello World Go 應用程式](virtual-machines-deis-cluster.md)
	
-	Kubernetes
	- [使用 CoreOS 和 Weave 自動部署 Kubernetes 叢集的完整指南](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
	
-	[Mesos](http://mesos.apache.org/)
	-	[Mesosphere 的資料中心作業系統 (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)。
	
-	[Jenkins](https://jenkins-ci.org/) 和 [Hudson](http://hudson-ci.org/)
	- [部落格：適用於 Azure 的 Jenkins 從屬外掛程式](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub 儲存機制︰適用於 Azure 的 Jenkins 儲存體外掛程式](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 從屬外掛程式](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 儲存體外掛程式](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
	
-	[Chef](https://docs.chef.io/index.html)
	- [Chef 和虛擬機器](virtual-machines-windows-install-chef-client.md)
	- [影片：Chef 是什麼，以及如何運作？](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

-	[Azure 自動化](http://azure.microsoft.com/services/automation/)
	- [影片：如何在 Linux VM 上使用 Azure 自動化](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
	
-	Powershell DSC for Linux
    - [部落格：如何使用 Powershell DSC for Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub：Docker 用戶端 DSC](https://github.com/anweiss/DockerClientDSC)

## 後續步驟

試試 [Docker](https://www.docker.com) 和 [Windows Server 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)。

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[微服務]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=July15_HO2-->