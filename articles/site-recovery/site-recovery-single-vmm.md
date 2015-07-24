
<properties
	pageTitle="利用單一 VMM 伺服器設定保護"
	description="Azure Site Recovery 可協調將位於內部部署 VMM 雲端中的虛擬機器複寫、容錯移轉及復原至 Azure 或至次要 VMM 雲端的作業。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="05/04/2015"
	ms.author="raynew"/>

#  利用單一 VMM 伺服器設定保護

## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

如果您在基礎結構中只有單一 VMM 伺服器，可以部署 Site Recovery，以便將 VMM 雲端中的虛擬機器複寫至 Azure，或者您可以在單一 VMM 伺服器上的雲端之間進行複寫。建議您只有在無法部署兩部 VMM 伺服器 (每個站台一個) 時才這麼做，因為在此部署中，容錯移轉與復原並不順暢。如需復原，您將需要從 Azure Site Recovery 主控台外部手動容錯移轉 VMM 伺服器 (使用 Hyper-V 管理員主控台中的 Hyper-V 複本)。

您可以透過好幾種方法，利用單一 VMM 伺服器設定複寫：

### 獨立部署

將獨立的 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 和 Hyper-V 複本，將此虛擬機器複寫至次要站台。若要縮短停機時間，可以在 VMM 虛擬機器上安裝 SQL Server。如果 VMM 使用的是遠端 SQL Server，您必須先復原該 SQL Server，然後再復原 VMM 伺服器。

![獨立虛擬 VMM 伺服器](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### 叢集部署

若要讓 VMM 具有高可用性，可以將 VMM 部署為 Windows 容錯移轉叢集中的虛擬機器。如果因為 VMM 可以確保工作負載的可用性，並防止 VMM 執行所在主機的硬體容錯移轉而由 VMM 管理重要工作負載，此做法相當實用。若要使用 Site Recovery 部署單一 VMM 伺服器，應該透過延伸叢集，跨地理位置上不同的站台部署 VMM 虛擬機器。VMM 所使用的 SQL Server 資料庫應該使用複本位於次要站台的 SQL Server AlwaysOn 可用性群組保護。如果發生損毀，VMM 伺服器及其對應的 SQL Server 資料庫會從次要站台容錯移轉並存取。

![叢集虛擬 VMM 伺服器](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## 開始之前

- 逐步解說步驟說明如何使用單一獨立式 VMM 伺服器部署 Site Recovery。
- 請確定您已備妥這些[必要條件](site-recovery-vmm-to-vmm.md/#before-you-start)，然後再開始部署。
- 單一 VMM 伺服器必須設定至少兩個雲端。一個雲端做為受保護的雲端，另一個雲端則將執行保護。
- 您想要保護的雲端必須包含下列項目：
	- 一或多個 VMM 主機群組
	- 每個主機群組中的一或多個 Hyper-V 主機
	- 每個主機伺服器上的一或多個 Hyper-V 虛擬機器

如果您遇到設定這個案例的問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。



## 設定單一伺服器部署

1. 如果未部署 VMM，請在安裝 SQL Server 資料庫所在的虛擬機器上設定 VMM。讀取[系統需求](https://technet.microsoft.com/library/dn771747.aspx) 
2. 在 VMM 伺服器上設定至少兩個雲端。深入了解：

	- [使用 System Center 2012 R2 VMM 的私人雲端新功能](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)和 [VMM 2012 和雲端](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)。 
	- [設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [在 VMM 中建立私人雲端](https://technet.microsoft.com/library/jj860425.aspx)與[逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。
3. 將您要保護的虛擬機器所在的來源 Hyper-V 主機伺服器新增到您將要保護的雲端 (來源雲端)。將目標 Hyper-V 主機伺服器新增到將提供保護之 VMM 伺服器上的雲端。
4. [建立](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) Azure Site Recovery 保存庫，並產生保存庫註冊金鑰。
4. 在 VMM 伺服器上[安裝](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) Azure Site Recovery 提供者，並在保存庫中註冊伺服器。 
5. 請確定雲端有出現在 Site Recovery 入口網站，並[設定雲端保護設定](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings)。
	- 在 [來源位置] 和 [目標位置] 中，指定單一 VMM 伺服器的名稱。
	- 在 [複寫方法] 中，為初始複寫選取 [透過網路]，因為雲端位於相同的伺服器上。

6. (選擇性) [設定網路對應](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping)：

	- 在 [來源] 和 [目標] 中，指定單一 VMM 伺服器的名稱。
	- 在 [來源上的網路] 中，選取設定為您要保護的雲端的 VM 網路。
	- 在 [目標上的網路] 中，選取設定為您要用於保護的雲端的 VM 網路。
	- 網路對應可以在相同 VMM 伺服器上的兩個虛擬機器 (VM) 網路之間設定。如果相同的 VMM 網路存在於兩個不同的站台，您可以在相同的網路之間對應。
7. 在 VMM 雲端中，為您要保護的虛擬機器[啟用保護](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection)。 
7. 在 Hyper-V 管理員主控台中，使用 Hyper-V 複本設定 VMM 虛擬機器的複寫。VMM 虛擬機器不應該加入至任何 VMM 雲端中。


## 容錯移轉和復原

### 建立復原計畫

復原計畫會將應該一起容錯移轉和復原的虛擬機器群組在一起。

1. 當您在 [來源] 和 [目標] 中建立復原計畫時，指定單一 VMM 伺服器的名稱。在 [選取虛擬機器] 中，將會顯示與主要雲端相關聯的虛擬機器。
2. 接著，[建立並自訂復原計畫](https://msdn.microsoft.com/library/azure/dn337331.aspx)。


### 復原

發生損壞時，可以使用下列步驟復原工作負載：

1. 將複本 VMM 虛擬機器從 Hyper-V 管理員主控台手動容錯移轉至復原站台。
2. 復原 VMM 虛擬機器之後，您可以從入口網站登入 Hyper-V 復原管理員主控台，然後在主要站台和次要站台之間進行虛擬機器非計劃性的容錯移轉。
3.  非計劃性的容錯移轉完成之後，使用者可以在主要站台上存取所有資源。


 

<!---HONumber=July15_HO2-->