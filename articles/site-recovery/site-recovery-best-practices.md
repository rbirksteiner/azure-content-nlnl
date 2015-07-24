<properties
	pageTitle="Site Recovery 部署的最佳作法"
	description="Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉及復原協調至 Azure 或次要資料中心。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>

# Site Recovery 部署的最佳作法


<a id="overview" name="overview" href="#overview"></a>

## 本文內容

本文包含您應該在部署 Azure Site Recovery 之前閱讀並實作的最佳作法。如果您在尋找 Site Recovery 的簡介和相關的部署案例，請參閱 [Site Recovery 概觀](site-recovery-overview.md)。

如果您在閱讀本文後有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 準備 Azure

- Azure 帳戶：您需要一個 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。如果您沒有此帳戶，請先前往[免費試用](pricing/free-trial/)。
- 閱讀有關 Site Recovery 的[定價](pricing/details/site-recovery/)。 
- Azure 儲存體：如果要將具有複寫功能的 Site Recovery 部署至 Azure，就需要 Azure 儲存體帳戶。您可以於部署期間設定，或在開始之前先準備好此帳戶。此帳戶應啟用異地複寫。應該與 Azure Site Recovery 保存庫位於相同的區或，且與相同的訂用帳戶相關聯。請參閱 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)。 

## 虛擬機器

如果想要複寫至 Azure 儲存體，請注意下列事項：

- **支援的作業系統**：您可以部署 Site Recovery 來協調虛擬機器保護工作，此類虛擬機器可執行任何 Azure 所支援的作業系統。這包括大部分的 Windows 和 Linux 版本。
- VHDX 支援：雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
- Azure 需求：請確認您想要保護的所有虛擬機器均符合 Azure 的需求。

虛擬機器功能 | 支援 | 詳細資料
---|---|---
主機作業系統 | Windows Server 2012 R2 | 如果不支援，則先決條件檢查會失敗
客體作業系統 | <p>Windows Server 2008 R2 或更新版本</p><p>Linux：Centos、openSUSE、SUSE、Ubuntu</p> | 如果不支援，則先決條件檢查會失敗。請更新 VMM 主控台中的值。
客體作業系統架構 | 64 位元 | 如果不支援，則先決條件檢查會失敗
作業系統磁碟大小 | 最大 1023 GB | 如果不支援，則先決條件檢查會失敗
作業系統磁碟計數 | 1 | 如果不支援，則先決條件檢查會失敗。請更新 VMM 主控台中的值
資料磁碟計數 | 16 或更少 (最大值是所建立之虛擬機器大小的函數。16 = XL) | 如果不支援，則先決條件檢查會失敗
資料磁碟 VHD 大小 | 最大 1023 GB | 如果不支援，則先決條件檢查會失敗
網路介面卡 | 支援多個介面卡 |
靜態 IP 位址 | 支援 | 如果主要虛擬機器使用靜態 IP 位址，您可以指定 Azure 中將建立之虛擬機器的靜態 IP 位址
iSCSI 磁碟 | 不支援 | 如果不支援，則先決條件檢查會失敗
共用 VHD | 不支援 | 如果不支援，則先決條件檢查會失敗
FC 磁碟 | 不支援 | 如果不支援，則先決條件檢查會失敗
硬碟格式| <p>VHD</p><p>VHDX</p> |
虛擬機器名稱| 介於 1 到 63 個字元。只能使用字母、數字和連字號。應該以字母或數字為開頭和結尾 | 更新 Site Recovery 中虛擬機器內容的值
虛擬機器類型 | <p>第 1 代</p> <p>第 2 代 - Windows</p> | 支援第 2 代虛擬機器：具備包含 1 或 2 個資料磁碟區、磁碟格式為 VHDX、磁碟大小小於 300GB 之基本磁碟的作業系統磁碟。不支援 Linux 第 2 代虛擬機器。[了解詳細資訊](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## VMM 伺服器

Site Recovery 可以協調 System Center Virtual Machine Manager (VMM) 雲端中位於 Hyper-V 主機伺服器上之虛擬機器的複寫工作：- 從站上的 VMM 伺服器複寫到 Azure (使用 Hyper-V 複本) - 複寫到次要的內部部署站台 (使用 Hyper-V 複本)。我們建議您在主要站台與次要站台中各部署一部 VMM 伺服器。但您可以視需要為兩個站台[部署單一 VMM 伺服器](site-recovery-single-vmm.md)。- 複寫到次要的內部部署站台 (使用 SAN)。您需要有主要和次要資料中心，且每個站台中有一部 VMM 伺服器。如果想要部署具有 Site Recovery 功能的 VMM，您必須設定 VMM 基礎結構。如果您沒有 VMM 伺服器，請參閱[這裡](site-recovery-hyper-v-site-to-azure.md)以了解更多。


### 確認 VMM 版本

支援的 VMM 版本 | 支援的 Site Recovery 案例 | 支援的 Hyper-V 主機 (來源/目標) | 支援的提供者/代理程式
---|---|---|---
至少為更新彙總套件 5 的 System Center 2012 R2 上的 VMM (僅叢集中的虛擬) | 使用 SAN 複寫的內部部署對內部部署保護 | 至少為具有最新更新的 Windows Server 2012 | 最新版 Azure Site Recovery 提供者 (安裝在 VMM 伺服器上) |
System Center 2012 R2 上的 VMM (建議選項) (叢集或獨立) | <p>使用 Hyper-V 複寫的內部部署對內部部署保護</p> <p>使用 Hyper-V 複寫的內部部署對 Azure 保護</p> | <p>至少為具有最新更新的 Windows Server 2012</p><p>Windows Server 2012 R2 (來源，不適用於目標)</p> | <p>最新版 Azure Site Recovery 提供者 (安裝在 VMM 伺服器上)</p> <p>最新版 Azure 復原服務代理程式 (安裝在僅用於複寫至 Azure 的 Hyper-V 主機伺服器上)</p>
具有最新累積更新的 System Center 2012 SP1 上的 VMM (叢集或獨立) | 內部部署對內部部署保護 | 具有最新更新的 Windows Server 2012 | 最新版 Azure Site Recovery 提供者 (安裝在 VMM 伺服器上) |

### 設定 VMM 雲端基礎結構

如果是要使用 Site Recovery 部署的 VMM 案例，您必須建立至少兩個私人雲端 (來源與目標 VMM 伺服器上各一)，以獲得私人雲端模式的優點，還可使用 VMM 主控台定義、管理和存取雲端及其基礎資源。VMM 雲端會收集和呈現一組彙總的資源，資源的使用方式會受到雲端容量設定和使用者角色配額的限制。自助使用者不需要完全了解雲端的基礎結構，即可管理和使用雲端的資源。使用者可以輕鬆地加入資源，以減少或增加雲端的彈性。如果您需要設定雲端，請使用下列資源：


- [使用 System Center 2012 R2 VMM 的私人雲端新功能](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 和雲端](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)。
- [設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [在 VMM 中建立私人雲端](https://technet.microsoft.com/zh-tw/library/jj860425.aspx)
- [逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。




## 提供者和代理程式

提供者和代理程式會安裝在內部部署伺服器，讓伺服器可以連接到 Azure 中的 Site Recovery。它們會使用加密的 HTTPS 連線，透過網際網路進行連接。

- 提供者需求：在部署期間，您會在內部部署伺服器上安裝幾個元件：
	- 在 VMM 伺服器上：在想要於保存庫中註冊的 VMM 伺服器上，安裝 Azure Site Recovery 提供者。
	- 在位於 VMM 雲端中的 Hyper-V 主機伺服器上：安裝 Azure 復原服務代理程式。
	- 在沒有 VMM 伺服器的 Hyper-V 主機伺服器上：在每部 Hyper-V 主機伺服器或叢集節點上，安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式。
-  提供者安裝：安裝提供者和代理程式時，請注意：
	-  您應該在 Site Recovery 保存庫內的所有伺服器上安裝相同版本的提供者。不支援在單一保存庫內使用不同版本。
	-  如果您想要從位於 VMM 雲端中的 Hyper-V 伺服器複寫至 Azure，VMM 伺服器應執行 System Center 2012 R2。如果您想要複寫到次要資料中心 VMM，則應該執行 System Center 2012 SP1 或 R2。
- **Proxy**：您不需要加入防火牆例外，或建立特定的 Proxy。如果想針對提供者連接，使用自訂的 Proxy，以下是您在開始部署之前必須執行的事項：

	- 先設定自訂的 Proxy 伺服器，再安裝提供者。
	- 允許這些 URL 通過防火牆：
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- 如果您要部署含 VMM 的 Site Recovery，且您使用自訂 Proxy，則會使用您在 Site Recovery 入口網站的自訂 Proxy 設定中指定的 Proxy 認證，自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。您必須設定 Proxy 伺服器，才能成功驗證此帳戶。



## 內部部署連線能力

- 提供者連線能力：提供者和代理程式會安裝在內部部署伺服器上，讓伺服器可以連接到 Site Recovery。它們會使用加密的 HTTPS 連線，透過網際網路連接至 Site Recovery。您不需要加入防火牆例外，或建立特定的 Proxy。
- 網際網路連線能力：您需要的伺服器網際網路連線能力如下：
	- 如果您要保護 VMM 雲端中 Hyper-V 主機伺服器上的虛擬機器，則只有 VMM 伺服器必須連線到網際網路。
	- 如果您要保護沒有 VMM 伺服器的 Hyper-V 主機伺服器上的虛擬機器，則只有 Hyper-V 主機伺服器必須連線到網際網路。
	- 您想要保護的虛擬機器不需要任何網際網路連線。
- VPN 站台對站台：您不需要 VPN 站台對站台連線，即可連接到 Site Recovery。但如果您有站台對站台連線，就能像在容錯移轉前一樣，存取已容錯移轉的虛擬機器。請注意，當複寫至 Azure 時，您會在內部部署站台與特定 Azure 網路之間，設定 VPN 站台對站台網路。加密的複寫流量不會用到它，而會透過網際網路流到您訂用帳戶中的 Azure 儲存體帳戶。
- 容錯移轉後的連線能力：為了確保在容錯移轉至 Azure 後，使用者可以連接到虛擬機器，請執行以下操作：
	- 在容錯移轉前啟用虛擬機器的 RDP。
	- 在容錯移轉後使用站台對站台連線，就能像之前一樣連接到這些虛擬機器，或者啟用機器的 RDP 端點。

## 儲存體

- Azure 儲存體帳戶：如果要複寫至 Azure ，您需要 Azure 儲存體帳戶。此帳戶應啟用異地複寫。應該與 Azure Site Recovery 保存庫位於相同的區或，且與相同的訂用帳戶相關聯。若要深入了解，請參閱 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)。
- 儲存體對應：如果複寫內部部署 VMM 伺服器上的虛擬機器，您可以設定儲存體對應，以確保虛擬機器可在容錯移轉之後，以最佳方式連接至儲存體。當您在兩個內部部署 VMM 站台之間執行複寫，則根據預設，會將複本虛擬機器儲存在目標 Hyper-V 主機伺服器的指定位置上。您可以在來源與目標 VMM 伺服器上，設定 VMM 儲存體分類之間的對應。如果想要使用此功能，請在開始部署之前，確定已先設定好儲存體分類。[深入了解](site-recovery-storage-mapping.md)儲存體對應。
- **SAN**：如果要使用 SAN 複寫節點，在兩個內部部署站台之間執行複寫，請注意：
	- 您只能使用 SAN 複寫，將 Hyper-V 虛擬機器複寫到次要資料中心。您無法複寫至 Azure。
	- 您可以使用現有的 SAN 環境。您不需要在 SAN 陣列上執行任何變更。
	- 您必須檢查您的 SAN 陣列是否[受支援](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。
	- 您必須有兩部 VMM 伺服器在來源和目標站台中，才能進行 SAN 部署。
	- 如果想要部署 SAN 複寫，請確定您的 Hyper-V 主機叢集是執行 Windows Server 2012 或 2012 R2。[了解](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)不同版本的 Hyper-V 所支援的客體作業系統。
	- 您必須探索及分類 VMM 中的 SAN 存放裝置。
	- 如果您還沒複寫，則在探索之後，必須建立 LUN 並在 VMM 主控台中配置儲存體。如果已經複寫，可以略過此步驟。
	- 如需完整指示，請參閱此篇[文章](site-recovery-vmm-san.md)。


## 網路

- **深入了解網路考量**：[閱讀更多](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)網路考量和最佳作法。

- 設定網路對應：網路對應是部署 VMM 和 Site Recovery 時的重要項目。它會以最佳方式將複寫的虛擬機器放在目標 Hyper-V 主機伺服器上，並確保複寫的虛擬機器會在容錯移轉後連接到適當的網路。當您複寫至 Azure 或次要資料中心時，可以設定網路對應：
	- 網路對應至 Azure：如果您複寫至 Azure 網路對應，請確保在相同網路上容錯移轉的所有虛擬機器，不論它們的復原方案為何，均能彼此連接。此外，如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。如果您未設定網路對應，則只能連接在相同復原方案中容錯移轉的機器。
	- 網路對應至次要站台：如果您複寫至次要 VMM 站台網路對應，請確保虛擬機器會在容錯移轉後連接到適當的網路，且複本虛擬機器會以最佳方式放置在 Hyper-V 主機伺服器上。如果您沒有設定網路對應，則複寫的機器將不會連接到任何 VM 網路。
	- [深入了解](site-recovery-network-mapping)網路對應。
- 設定 VMM 網路：
	- 在 VMM 中正確設定邏輯和 VM 網路。請參閱[邏輯網路](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)和 [VM 網路](https://technet.microsoft.com/library/jj721575.aspx)的相關資訊。
	- 確認來源 VMM 伺服器上的虛擬機器已連接到 VM 網路。
	- 檢查該 VM 網路是否已連結到與雲端相關聯的邏輯網路。
	- 如果您複寫至 Azure，請在 Azure 中建立虛擬網路。請注意，多個 VM 網路可對應至單一 Azure 網路。請參閱[虛擬網路組態工作](https://msdn.microsoft.com/library/azure/dn133795.aspx)。

## 最佳化效能

- 作業系統磁碟區大小：當您將虛擬機器複寫到 Azure 時，作業系統磁碟區必須小於 127 GB。如果磁碟區大小超過此值，您可以在開始部署之前，手動將磁碟區移動至不同的磁碟。
- 資料磁碟大小：如果複寫至 Azure，您可以在虛擬機器上擁有最多 32 個資料磁碟，每個資料磁碟的上限為 1 TB。您可以有效地複寫及容錯移轉最多 32 TB 的虛擬機器。
- 復原方案限制：Site Recovery 可擴充至數千個虛擬機器。復原方案是設計做為應用程式的模型，而由於應用程式應該一起進行容錯移轉，因此我們將一個復原方案中的機器數目限制為 50。
- Azure 服務限制：每個 Azure 訂用帳戶均隨附一組對核心、雲端服務等的預設限制。我們建議您執行容錯移轉測試，以驗證您訂用帳戶中的資源可用性。您可以透過 Azure 支援修改這些限制。
- 容量規劃：如需指引，請參閱[Hyper-V 複本的容量規劃工具](http://www.microsoft.com/en-in/download/details.aspx?id=39057) (英文)。
- 複寫頻寬：如果您的複寫頻寬不足，請注意：
	- **ExpressRoute**：Site Recovery 可搭配 Azure ExpressRoute 和 WAN 最佳化工具，例如 Riverbed。[深入了解](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) ExpressRoute。
	- 複寫流量：Site Recovery 只會使用資料區塊 (而非整個 VHD) 來執行智慧型初始複寫。只會在複寫進行期間複寫變更。
	- 網路流量：您可以根據目的地 IP 位址和連接埠，以原則設定 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)，藉此控制用於複寫的網路流量。此外，如果您使用 Azure 備份代理程式複寫至 Azure Site Recovery，則您可以設定該代理程式的節流。[閱讀更多資訊](https://support.microsoft.com/kb/3056159)。
- **RTO**：如果您想要測量可以預期的 Site Recovery 復原時間目標 (RTO)，我們建議您執行容錯移轉測試並檢視 Site Recovery 工作，來分析需要多少時間才能完成作業。如果您容錯移轉至 Azure，我們建議您藉由整合 Azure 自動化和復原方案，將所有的手動操作自動化，來取得最佳的 RTO。
- **RPO**：當您複寫至 Azure 時，Site Recovery 支援幾乎同步的復原點目標 (RPO)。這是假設您的資料中心與 Azure 之間有足夠的頻寬。

## 容錯移轉
- 主要站台中斷：如果您從某個內部部署資料中心複寫至另一個，且您的主要站台上這兩個資料中心發生中斷情形，請從 Site Recovery 入口網站執行未規劃的容錯移轉。主要資料中心不需要有連線能力即可執行容錯移轉。
- 容錯移轉至次要站台後保留 IP 位址：如果您想要保留來源虛擬機器的 IP 位址，請在來源虛擬機器容錯移轉至次要資料中心後，依照[這裡](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) (英文) 所述的步驟操作。
- 容錯移轉至 Azure 後保留 IP 位址：您可以在虛擬機器的 [設定] 索引標籤中，指定要提供給已容錯移轉 VM 的 IP。如需詳細資訊，請參閱如何[設定虛擬機器的網路內容](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines)。
- 保留公用 IP 位址：如果想要在容錯移轉至次要站台後保留公用 IP 位址，且您的 ISP 支援，Site Recovery 不會阻止您這麼做。您無法在容錯移轉至 Azure 後保留公用 IP 位址。
- 在 Azure 中保留非 RFC 內部位址：您可以在容錯移轉至 Azure 後，保留非 RFC 1918 的位址空間。
- 部分容錯移轉至次要資料中心：如果您將部分站台容錯移轉到次要資料中心，且想要連回主要站台，您可以使用站台對站台 VPN，將次要站台上已容錯移轉的應用程式，連接到在主要站台上執行的基礎結構元件。請注意，如果整個子網路容錯移轉，您可以保留虛擬機器的 IP 位址。如果您容錯移轉部分的子網路，則無法保留虛擬機器的 IP 位址，因為無法分割站台之間的子網路。
- 部分容錯移轉至 Azure：如果您將部分站台容錯移轉至 Azure，且想要連回主要站台，您可以使用站台對站台 VPN，將 Azure 中已容錯移轉的應用程式，連接到在主要站台上執行的基礎結構元件。請注意，如果整個子網路容錯移轉，您可以保留虛擬機器的 IP 位址。如果您容錯移轉部分的子網路，則無法保留虛擬機器的 IP 位址，因為無法分割站台之間的子網路。
- 保留磁碟機代號：如果想要在容錯移轉後保留虛擬機器上的磁碟機代號，您可以將虛擬機器的 SAN 原則設定為 [開啟]。[閱讀更多資訊](https://support.microsoft.com/kb/3031135)。
- 容錯移轉至 Azure 後路由用戶端要求：Site Recovery 可搭配 Azure 流量管理員，在容錯移轉後，將用戶端要求路由至您的應用程式。您可以使用復原方案中的指令碼 (搭配 Azure 自動化) 執行 DNS 更新。

## 整合

- 與其他 BCDR 技術的整合：Site Recovery 已與其他商務持續性和災害復原 (BCDR) 技術整合。若為以應用程式為基礎的複寫，我們建議您使用 SQL Sever AlwaysOn 複寫執行資料庫的虛擬機器，前端虛擬機器則使用 Hyper-V 複本進行複寫。您需要在主要和次要站台中具有 SQL Server Enterprise 授權，以及在訂用帳戶有執行中的 Azure 虛擬機器，才能使用 AlwaysOn。Site Recovery 內建複寫功能很有用，它可讓應用程式處理某些停機時間；而此時，我們會建議使用 Site Recovery 協調資料庫、應用程式和前端虛擬機器的複寫工作。此方法可以節省您所需 SQL Server 版本和授權數目的開銷，以及節省讓虛擬機器持續在 Azure 中執行的成本。

## 後續步驟

檢閱這些最佳作法後，就可以開始部署 Site Recovery：

- [設定內部部署 VMM 站台和 Azure 之間的保護](site-recovery-vmm-to-azure.md)
- [設定內部部署 Hyper-V 站台和 Azure 之間的保護](site-recovery-hyper-v-site-to-azure.md)
- [設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-to-vmm.md)
- [利用 SAN 設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-san.md)
- [利用單一 VMM 伺服器設定保護](site-recovery-single-vmm.md)
 

<!---HONumber=July15_HO2-->