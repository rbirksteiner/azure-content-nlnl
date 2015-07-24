<properties
	pageTitle="設定內部部署 VMM 網站和 Azure 之間的保護"
	description="Azure 站台復原可協調將位於內部部署 VMM 雲端中的 Hyper-V 虛擬機器複寫、容錯移轉及復原至 Azure 的作業。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/07/2015"
	ms.author="raynew"/>

#  設定內部部署 VMM 網站和 Azure 之間的保護

## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

本案例指南說明如何部署 Site Recovery，為執行於虛擬機器 (位於 VMM 私人雲端中的 Hyper-V 主機伺服器上) 的工作負載協調和自動化保護。在這個案例中，虛擬機器會使用 Hyper-V 複本，從主要 VMM 站台複寫到 Azure 中。

本指南包含案例的必要條件，並示範如何設定 Site Recovery 保存庫、取得安裝於來源 VMM 伺服器上的「 Azure Site Recovery 提供者」、在保存庫註冊伺服器、加入 Azure 儲存體帳戶、在 Hyper-V 主機伺服器上安裝 Azure Site Recovery 代理程式、設定將套用到所有受保護虛擬機器之 VMM 雲端的保護設定、然後啟用那些虛擬機器的保護。測試容錯移轉，確認一切如預期般運作以完成動作。

如果您遇到設定這個案例的問題，請將問題張貼在 [Azure 復原服務論壇](http://go.microsoft.com/fwlink/?LinkId=313628)。

## 開始之前

確認您已備妥這些必要條件：
### Azure 必要條件

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。如果您沒有此帳戶，請先前往[免費試用](http://aka.ms/try-azure)。此外，您可以參閱 [Azure Site Recovery 管理員價格](http://go.microsoft.com/fwlink/?LinkId=378268)。
- 您將需要 Azure 儲存體帳戶才能將複寫的資料儲存至 Azure。此帳戶必須啟用異地複寫。應該與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。若要深入了解設定 Azure 儲存體，請參閱 [Microsoft Azure 儲存體簡介](http://go.microsoft.com/fwlink/?LinkId=398704)。
- 您必須確認您想要保護的虛擬機器符合 Azure 要求。如需詳細資料，請參閱[虛擬機器支援](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A)。

### VMM 先決條件
- 您將需要在在 System Center 2012 R2 上執行的 VMM 伺服器。
- 任何包含您想要保護之虛擬機器的 VMM 伺服器都必須執行 Azure Site Recovery 提供者。它會在部署 Azure Site Recovery 期間安裝。
- 在您想要保護的 VMM 伺服器上，您至少需要一個雲端。這個雲端應該包含：
	- 一或多個 VMM 主機群組。
	- 每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。
	- 來源 Hyper-V 伺服器上的一或多部虛擬機器。虛擬機器應該為第 1 代。
- 深入了解設定 VMM 雲端：
	- 在[使用 System Center 2012 R2 VMM 的私人雲端新功能](http://go.microsoft.com/fwlink/?LinkId=324952)和 [VMM 2012 和雲端](http://go.microsoft.com/fwlink/?LinkId=324956)中進一步了解私人 VMM 雲端。
	- 深入了解[設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- 在您備妥雲端網狀架構元素之後，[在 VMM 中建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324953)與[逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324954)中瞭解建立私人雲端。

### Hyper-V 的必要條件

- 主機 Hyper-V 伺服器至少必須執行附帶 Hyper-V 角色的 Windows Server 2012 R2，並且已安裝最新更新。
- 如果您在叢集中執行 Hyper-V，請注意，如果您具有靜態 IP 位址叢集，並不會自動建立叢集代理。您必須手動設定叢集代理。如需指示，請參閱[設定 Hyper-V 複本代理人](http://go.microsoft.com/fwlink/?LinkId=403937)。
- 您想要管理保護的任何 Hyper-V 主機伺服計或叢集都必須包含在 VMM 雲端中。

下圖顯示 Azure Site Recovery 為協調流程與複寫所使用的不同通訊通道與連接埠

![E2A 拓撲](./media/site-recovery-vmm-to-azure/E2ATopology.png)

### 網路對應的必要條件
當您在 Azure 網路對應中保護虛擬機器，請對應來源 VMM 伺服器上的 VM 網路和目標 Azure 網路，以啟用下列項目：

- 在相同網路上容錯移轉的所有機器都可以彼此連接，無論它們隸屬於哪個復原計畫都一樣。
- 如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。
- 如果您未設定網路對應，則只有在相同復原計畫中容錯移轉的虛擬機器才能在容錯移轉到 Azure 之後彼此連接。

如果您想要部署網路對應，您需要下列項目：

- 您想要在來源 VMM 伺服器上保護的虛擬機器應該連線到 VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。
- 複寫的虛擬機器可在容錯移轉之後連線的 Azure 網路。您將在容錯移轉時選取此網路。此網路應該和您的 Azure Site Recovery 訂用帳戶在相同的區域中。
- 深入了解網路對應：
	- [在 VMM 中設定邏輯網路](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [在 VMM 中設定 VM 網路和閘道](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [在 Azure 中設定和監視虛擬網路](http://go.microsoft.com/fwlink/?LinkId=402555)


## 步驟 1：建立 Site Recovery 保存庫

1. 從您想要註冊的 VMM 伺服器登入[管理入口網站](https://portal.azure.com)。


2. 展開
3. [*資料服務*] 和 [*復原服務*]，然後按一下 [*Site Recovery 保存庫*]。*
3. 按一下 [建立新項目]，然後按一下 [快速建立]。


4. 在 [名稱] 中，輸入保存庫的易記識別名稱。

5. 在 [區域] 中，選取保存庫的地理區域。可用的地理區域包括 [東亞]、[西歐]、[美國西部]、[美國東部]、[北歐] 和 [東南亞]。
6. 按一下 [建立保存庫]。

	![新增保存庫](./media/site-recovery-vmm-to-azure/ASRE2AVMM_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫在主要 [復原服務] 頁面上會列為 [*使用中*]。</P>


## 步驟 2：產生保存庫註冊金鑰

在保存庫中產生註冊金鑰。下載 Azure Site Recovery 提供者並將其安裝在 VMM 伺服器之後，您將使用此金鑰在保存庫中註冊 VMM 伺服器。

1. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

	![快速啟動圖示](./media/site-recovery-vmm-to-azure/ASRE2AVMM_QuickStartIcon.png)

2. 在下拉式清單中，選取 [Between an on-premises Hyper-V site and Microsoft Azure]。
3. 在 [準備 VMM 伺服器] 中，按一下 [產生註冊金鑰檔案]。該金鑰檔案會自動產生，並在產生後會維持 5 天有效。如果您沒有從 VMM 伺服器存取 Azure 入口網站，您必須將這個檔案複製到伺服器。

	![註冊金鑰](./media/site-recovery-vmm-to-azure/ASRE2AVMM_RegisterKey.png)

## 步驟 3：安裝 Azure Site Recovery 提供者

4. 在 [快速啟動] 頁面上，按一下 [準備 VMM 伺服器] 中的 [下載要在 VMM 伺服器上安裝的 Microsoft Azure Site Recovery 提供者]，以取得最新版的提供者安裝檔案。

2. 在來源 VMM 伺服器上執行此檔案。如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將其安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。然後在其他節點上安裝提供者。請注意，如果您要升級提供者，您必須在所有節點上升級，因為它們都應該執行相同的提供者版本。


3. 在 [必要條件檢查] 中，選取停止 VMM 服務以開始執行提供者安裝程式。服務隨即停止，並將在安裝程式完成時自動重新啟動。如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。

	![必要條件](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderPrereq.png)

4. 在 [Microsoft Update] 中，您可以選擇加入以取得更新。啟用這項設定時，會根據您的 Microsoft Update 原則來安裝提供者更新。

	![Microsoft Update](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderUpdate.png)

安裝提供者之後，請繼續安裝以在保存庫中註冊伺服器。

5. 在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接到網際網路的方式。選取 [Use default system proxy settings]，以使用在伺服器上設定的預設網際網路連線設定。

	![網際網路設定](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderProxy.png) - 如果您想要使用自訂 Proxy，您應該在安裝提供者之前進行設定。設定自訂的 Proxy 設定時，將執行測試來檢查 Proxy 連線。- 如果您使用自訂 Proxy，或您的預設 Proxy 需要驗證，您將必須輸入 Proxy 的詳細資料，包括 Proxy 位址和連接埠。- 下列 URL 應該能夠從 VMM 伺服器與 Hyper-V 主機存取 - *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net - 允許 [Azure 資料中心 IP 範圍](http://go.microsoft.com/fwlink/?LinkId=511094)中所述的 IP 位址和 HTTPS (443) 通訊協定。您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

	- 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。設定 proxy 伺服器，讓此帳戶可以成功進行驗證。在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。若要這樣做，請開啟 [設定] 工作區、展開 [安全性]、按一下 [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。您必須重新啟動 VMM 服務，這項設定才會生效。

6. 在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
7. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。
8. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。在叢集設定中，指定 VMM 叢集角色名稱。


	![伺服器註冊](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderRegKeyServerName.png)

8. 在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。這個動作只需要在每個伺服器上進行一次。如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。


9. 在 [資料加密] 中，您可以指定一個位置來儲存自動為資料加密產生的 SSL 憑證。如果您在 Azure 站台復原入口網站中為 Azure 所保護的雲端啟用資料加密，則會使用此憑證。請保護此憑證的安全。當您執行至 Azure 的容錯移轉時，您將選取它來將加密的資料解密。

	![伺服器註冊](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderSyncEncrypt.png)

8. 按一下 [註冊] 完成此程序。註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。伺服器會顯示在保存庫中 [伺服器] 頁面上 [資源] 索引標籤的結尾。

## 步驟 4：建立 Azure 儲存體帳戶

如果您沒有 Azure 儲存體帳戶，請按一下 [**新增 Azure 儲存體帳戶**]。此帳戶應啟用異地複寫。此帳戶應與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。


![儲存體帳戶](./media/site-recovery-vmm-to-azure/ASRE2AVMM_StorageAgent.png)

## 步驟 5：安裝 Azure 復原服務代理程式

在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

1. 在 [快速入門] 頁面上，按一下 [<b>下載 Azure Site Recovery 服務代理程式並安裝於主機</b>]，取得最新版的代理程式安裝檔案。

	![Install Recovery Services Agent](./media/site-recovery-vmm-to-azure/ASRE2AVMM_InstallHyperVAgent.png)

2. 在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上執行安裝檔案。
3. 在 [**檢查先決條件**] 頁面上，按 [<b>下一步</b>]。將自動安裝任何缺少的必要元件。

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/ASRE2AVMM_AgentPrereqs.png)

4. 在 [安裝設定] 頁面上，指定您要安裝代理程式的位置，並選取將在其中安裝備份中繼資料的快取位置。然後按一下 [<b>安裝</b>]。

## 步驟 6：設定雲端保護設定

註冊 VMM 伺服器之後，您就可以設定雲端保護設定。當您安裝提供者之後，即可啟用 [將雲端資料與保存庫同步] 選項，如此一來，VMM 伺服器上的所有雲端將會出現在保存庫的 [受保護的項目]<b></b> 索引標籤中。

![發佈的雲端](./media/site-recovery-vmm-to-azure/ASRE2AVMM_CloudsList.png)


1. 在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。
2. 在 [受保護項目] 索引標籤上，按一下要設定的雲端，並移至 [設定] 索引標籤。
3. 在 [<b>目標</b>] 中，選取 [<b>Microsoft Azure</b>]。
4. 在 [<b>儲存體帳戶</b>] 中，選取您想要用來複寫您的虛擬機器的 Azure 儲存體帳戶。
5. 將 [<b>加密儲存的資料</b>] 設為 [<b>關閉</b>]。此設定指定應該將內部部署與 Azure 之間複寫的資料加密。
6. 在 [<b>複製頻率</b>] 中保留預設設定。這個值指定應在來源與目標位置之間同步處理資料的頻率。
7. 在 [<b>保留復原點的時間</b>] 中，保留預設設定。使用預設值 0 時，只會在複本主機伺服器上儲存主要虛擬機器的最新復原點。
8. 在 [<b>應用程式一致快照的頻率</b>] 中保留預設設定。這個值指定建立快照的頻率。快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。如果您設定一個值，請確定此值小於您設定的其他復原點數目。
9. 在 [<b>複寫開始時間</b>] 中，指定初次將資料複寫至 Azure 的開始時間。將會使用 Hyper-V 主機伺服器的時區。建議您將初次複寫排定在離峰時段進行。

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/ASRE2AVMM_CloudSettings.png)

儲存設定之後，會建立一個工作，並可在 [<b>工作</b>] 索引標籤上進行監視。VMM 來源雲端中的所有 Hyper-V 主機伺服器會設定進行複寫。

儲存之後，可在 [<b>設定</b>] 索引標籤上修改雲端設定。若要修改目標位置或目標儲存體帳戶，您需要移除雲端組態，然後重新設定雲端。請注意，如果您變更儲存體帳戶，則只會對修改儲存體帳戶之後才啟用保護的虛擬機器套用變更。現有的虛擬機器不會移轉至新的儲存體帳戶。</p>

## 步驟 7：設定網路對應
開始網路對應之前，請確認來源 VMM 伺服器上的虛擬機器已連線到 VM 網路。此外，請建立一或多個 Azure 虛擬網路。請注意，多個 VM 網路可對應至單一 Azure 網路。

1. 在 [快速入門] 頁面上，按一下 [對應網路]。
2. 在 [**網路**] 索引標籤的 [**來源位置**] 中，選取來源 VMM 伺服器。在 [**目標位置**] 中選取 [Azure]。
3. 在 [**來源**] 網路中，會顯示與 VMM 伺服器相關聯的 VM 網路清單。在 [**目標**] 網路中會顯示與訂用帳戶相關聯的 Azure 網路。
4. 選取來源 VM 網路，然後按一下 [**對應**]。
5. 在 [**選取目標網路**] 頁面上，選取您要使用的目標 Azure 網路。
6. 按一下打勾記號完成對應程序。

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/ASRE2AVMM_MapNetworks.png)

儲存設定之後，工作會開始追蹤對應進度，可在 [工作] 索引標籤上進行監視。對應來源 VM 網路的任何現有複本虛擬機器都會連線到目標 Azure 網路。連線到來源 VM 網路的新虛擬機器都會在複寫之後連線到對應的 Azure 網路。如果您修改與新網路的現有對應，複本虛擬機器將使用新設定進行連線。

請注意，如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。

## 步驟 8：對虛擬機器啟用保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。請注意：

- 虛擬機器必須符合 Azure 需求。請在《規劃指南》的<a href="http://go.microsoft.com/fwlink/?LinkId=402602">必要條件和支援</a>中查看這些需求。
- 若要啟用保護功能，您必須為虛擬機器設定作業系統和作業系統磁碟屬性。當您使用虛擬機器範本在 VMM 中建立虛擬機器時，您可以設定屬性。您也可以在虛擬機器屬性的 [**一般**] 和 [**硬體組態**] 索引標籤上，為現有的虛擬機器設定這些屬性。若未在 VMM 中設定這些屬性，您將可在 Azure 站台復原入口網站中加以設定。

![Create virtual machine](./media/site-recovery-vmm-to-azure/ASRE2AVMM_EnableNew.png)

![Modify virtual machine properties](./media/site-recovery-vmm-to-azure/ASRE2AVMM_EnableExisting.png)


1. 若要啟用保護，請在虛擬機器所在雲端中的 [<b>虛擬機器</b>] 索引標籤上，按一下 [<b>啟用保護</b>]，然後選取 [<b>加入虛擬機器</b>]
2. 從雲端中所有虛擬機器的清單，選取您要保護的虛擬機器。

	![啟用虛擬機器保護](./media/site-recovery-vmm-to-azure/ASRE2AVMM_SelectVM.png)

	您可以在 [工作] 索引標籤中追蹤「啟用保護」動作的進度，包括初始複寫。執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。啟用保護並複寫虛擬機器之後，您將能夠在 Azure 中檢視這些虛擬機器。


	![虛擬機器保護工作](./media/site-recovery-vmm-to-azure/ASRE2AVMM_VMJobs.png)

3. 驗證虛擬機器屬性，並視需要加以修改。

	![Verify virtual machines](./media/site-recovery-vmm-to-azure/VMProperties.png)

4. 在虛擬機器屬性的 [設定] 索引標籤上可以修改下列網路屬性。

    1. 目標虛擬機器的網路介面卡數目－目標虛擬機器上的網路介面卡數目取決於選取的虛擬機器的大小。目標虛擬機器的網路介面卡數目是來源虛擬機器上網路介面卡數目的最小值和選取的虛擬機器大小支援的網路介面卡的最大數目。  

	1. 目標虛擬機器的網路－虛擬機器連接的網路取決於來源虛擬機器網路的網路對應。如果來源虛擬機器有一個以上的網路介面卡，且來源網路對應至目標上的不同網路，則使用者必須選擇其中一個目標網路。

	1. 每個網路介面卡的子網路－針對每個網路介面卡，使用者可以選擇容錯移轉的虛擬機器會連接的子網路。

	1. 目標 IP－如果來源虛擬機器的網路介面卡是設定為使用靜態 IP，則使用者可以提供目標虛擬機器的 IP。使用者可以使用此功能，在容錯移轉之後保留來源虛擬機器的 IP。如果未提供任何 IP，在容錯移轉時會將任何可用的 IP 提供給網路介面卡。如果由使用者提供的目標 IP 已由 Azure 中執行的其他某部虛擬機器使用，則容錯移轉會失敗。

		![修改網路屬性](./media/site-recovery-vmm-to-azure/MultiNic.png)

## 測試您的部署
若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：

- 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。
- 容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。

### 建立復原計畫

1. 在 [**復原方案**] 索引標籤上，新增一個新方案。指定一個名稱、在 [**來源類型**] 中指定 [**VMM**]、在 [**來源**] 中指定來源 VMM 伺服器，在 [目標] 中指定 Azure。

	![建立復原計畫](./media/site-recovery-vmm-to-azure/ASRE2AVMM_RP1.png)

2. 在 [**選取虛擬機器**] 頁面上，選取要新增到復原計畫的虛擬機器。這些虛擬機器將新增到復原計畫的預設群組—群組 1。最多 100 部虛擬機器已在單一復原計畫中經過測試。

	- 如果您將虛擬機器屬性加入計畫之前，想要確認這些屬性，請按一下屬性所在之雲端的屬性頁面上的虛擬機器。您也可以在 VMM 主控台中設定虛擬機器屬性。
	- 顯示的所有虛擬機器已啟用保護。此清單包含啟用保護並已完成初始複寫的虛擬機器，以及利用初始複寫擱置啟用保護的虛擬機器。只有已完成初始複寫的虛擬機器可做為復原計畫的一部分進行容錯移轉。因此，請確認計畫中虛擬機器的初始複寫狀態，再開始復原計畫容錯移轉。


	![建立復原計畫](./media/site-recovery-vmm-to-azure/ASRE2AVMM_SelectVMRP.png)

建立復原計畫之後，它會出現在 [**復原計畫**] 索引標籤中。您也可以加入 [[Azure 自動化 Runbook](site-recovery-runbook-automation.md)] 至復原計劃，以自動化容錯移轉時間的動作。

### 執行測試容錯移轉

有兩種方式可以測試容錯移轉至 Azure。

- 在沒有 Azure 網路的情況下測試容錯移轉—這種測試容錯移轉可以檢查虛擬機器是否正確地出現在 Azure 中。在容錯移轉之後，虛擬機器不會連線到任何 Azure 網路。
- 利用 Azure 網路測試容錯移轉—這種測試容錯移轉可以檢查整個復寫環境是否如預期般出現並進行容錯移轉，虛擬機器會連線到只訂的目標 Azure 網路。針對子網路處理的測試容錯移轉，可根據複本虛擬機器的子網路得知測試虛擬機器的子網路。這和一般的複寫不同，一般複寫的複本虛擬機器子網路是根據來源虛擬機器的子網路得知。

如果您想要針對啟用保護的虛擬機器執行測試容錯轉移至 Azure ，卻不想指定 Azure 目標網路，您不需要作任何準備。若要利用目標 Azure 網路執行測試容錯移轉，您必須建立新的 Azure 網路，該網路會與您的 Azure 生產網路隔離 (您在 Azure 中建立新網路的預設行為)，您也必須設定基礎結構，讓複寫的虛擬機器如預期般運作。例如，具有網域控制站和 DNS 的虛擬機器可以使用 Azure Site Recovery 複寫到 Azure，而且可以使用測試容錯移轉，在測試網路中加以建立。若要執行測試容錯移轉，請依照下列步驟執行：

1. 在將用於內部部署虛擬機器之實際測試容錯移轉的相同網路中，執行具有網域控制站和 DNS 之虛擬機器的測試容錯移轉。
2. 將配置給已容錯移轉之 DNS 虛擬機器的 IP 位址記下來。
3. 在將用於容錯移轉的 Azure 虛擬網路中，新增 IP 位址做為 DNS 伺服器的位址。
4. 指定 Azure 測試網路，執行來源內部部署虛擬機器的測試容錯移轉。
5. 在驗證測試容錯移轉可如預期般運作之後，請將復原方案的測試容錯移轉標示為完成，然後將網域控制站和 DNS 虛擬機器的測試容錯移轉標示為完成。

如果要執行測試容錯移轉，請執行下列動作：

1. 在 [復原計畫] 索引標籤上，選取計畫，然後按一下 [測試容錯移轉]。
1. 在 [確認測試容錯移轉] 頁面上，選取 [無] 或特定的 Azure 網路。請注意，如果您選取 [無]，測試容錯移轉將會檢查虛擬機器是否正確地複寫至 Azure，但並不會檢查您的複寫網路設定。

	![沒有網路](./media/site-recovery-vmm-to-azure/ASRE2AVMM_TestFailoverNoNetwork.png)

1. 如果已啟用雲端的資料加密，當您開啟選項以啟用雲端的資料加密時，請在 [**加密金鑰**] 中選取在 VMM 伺服器上安裝提供者時發出的憑證。
1. 在 [工作] 索引標籤上，您可以追蹤容錯移轉進度。您應該可以在 Azure 入口網站中看到該虛擬機器測試複本。如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。
1. 當容錯移轉到達 [完成測試] 階段時，請按一下 [完成測試] 以完成測試容錯移轉。您可以向下切入到 [工作] 索引標籤，來追蹤容錯移轉進度和狀態，並執行任何所需的動作。
1. 在容錯移轉之後，您將可以在 Azure 入口網站中看到該虛擬機器測試複本。如果您設定從內部部署網路存取虛擬機器，您可以初始化虛擬機器的「遠端桌面」連線。請注意：

    1. 確認虛擬機器成功啟動。
    1. 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。您也必須在虛擬機器上新增 RDP 端點。您可以利用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 來執行這個動作。
    1. 容錯移轉之後，如果您使用公用 IP 位址，利用 [遠端桌面] 連接到 Azure 中的虛擬機器，請確定您沒有任何網域原則會阻止您使用公用位址連接到虛擬機器。

1.  測試完成之後，請執行下列動作：
	- 按一下 [測試容錯移轉完成]。清除測試環境，以自動關閉電源及刪除測試虛擬機器。
	- 按一下 [記事] 記錄並儲存關於測試容錯移轉的任何觀察。

## <a id="runtest" name="runtest" href="#runtest"></a>監視活動
<p>您可以使用*工作*索引標籤和*儀表板*檢視及監視 Azure Site Recovery 保存庫執行的主要工作，包括設定雲端的保護、啟用和停用虛擬機器的保護、執行容錯移轉 (計劃性、非計劃性或測試)，以及認可非計劃性容錯移轉。</p>

<p>您可以從*工作*索引標籤檢視工作、向下鑽研工作詳細資料和錯誤、執行工作查詢以擷取符合特定條件的工作、將工作匯出至 Excel，以及重新啟動失敗的工作。</p>

<p>您可以從*儀表板*下載最新版的提供者和代理程式安裝檔案、取得保存庫的組態資訊、查看由保存庫管理其保護的虛擬機器數目、查看最近的工作、管理保存庫憑證，以及重新同步虛擬機器。</p>

<p>如需與工作和儀表板互動的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=398534">作業和監視指南</a>。</p>

##<a id="next" name="next" href="#next"></a>接續步驟
<UL>
<LI>若要在完整生產環境中規劃及部署 Azure Site Recovery，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 的規劃指南</a>及 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery 的部署指南</a>。</LI>


<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</LI></UL>
 

<!---HONumber=July15_HO2-->