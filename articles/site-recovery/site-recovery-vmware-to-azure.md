<properties
	pageTitle="設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護" 
	description="描述 Azure Site Recovery，以協調內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器至 Azure 的複寫、容錯移轉和復原。" 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="raynew"/>

# 設定在內部部署 VMware 虛擬機器或實體伺服器與 Azure 之間的保護

本文說明如何將 Site Recovery 部署至：

- **保護 VMware 虛擬機器**—協調內部部署 VMware 虛擬機器對 Azure 的複寫、容錯移轉和復原
- **保護實體伺服器**—使用 Azure Site Recovery 服務來協調內部部署實體 Windows 和 Linux 伺服器至 Azure 的複寫、容錯移轉和復原。

文章包含概觀、部署必要條件和設定指示。在文章結尾，您的 VMware 虛擬機器或實體伺服器將被複寫至 Azure。如果您遇到問題，請將您的問題張貼到 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 什麼是 Azure Site Recovery？

Azure Site Recovery 可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。進一步了解 [Azure Site Recovery](site-recovery-overview.md)。

## 它如何保護內部部署資源？

透過在許多[部署案例](site-recovery-overview.md)中協調、簡化複寫、容錯移轉和容錯回復，Site Recovery 可以協助保護您的內部部署資源。如果想要保護您的內部部署 VMware 虛擬機器或 Windows 或 Linux 實體伺服器，以下是 Site Recovery 如何能夠幫助：

- 可讓 VMware 使用者將虛擬機器複寫到 Azure。
- 允許將內部部署實體伺服器複寫至 Azure。
- 提供單一位置來設定和管理複寫、容錯移轉和復原。
- 提供從內部部署基礎結構容錯移轉至 Azure，以及從 Azure 至內部部署容錯回復 (還原) 的簡單方式。
- 實作復原計劃，針對連結在多部機器上的工作負載輕鬆容錯移轉。
- 提供多 VM 一致性，讓執行特定工作負載的虛擬機器和實體伺服器可以同時復原到一致的資料點。
- 支援透過網際網路、透過站對站 VPN 連線或透過 Azure ExpressRoute 進行的資料複寫。
- 提供 VMware 虛擬機器自動化的探索。


## 我需要什麼？

此圖表顯示部署元件。

![新增保存庫](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

以下是您需要的內容：

**元件** | **部署** | **詳細資料**
--- | --- | ---
**設定伺服器** | <p>在與 Site Recovery 相同的訂用帳戶中部署為 Azure 標準 A3 虛擬機器。</p> <p>您在 Site Recovery 入口網站中設定</p> | 這個伺服器可在 Azure 中的受保護機器、處理序伺服器與主要目標伺服器之間協調通訊。它會在容錯移轉發生時在 Azure 中設定複寫並協調復原。
**主要目標伺服器** | <p>部署為 Azure 虛擬機器－做為以 Windows Server 2012 R2 資源庫映像為基礎的 Windows 伺服器 (以保護 Windows 機器)，或做為以 OpenLogic CentOS 6.6 組件庫映像為基礎的 Linux 伺服器 (以保護 Linux 機器)。</p> <p>共有兩種大小選項可供使用－標準 A3 及標準 D14。<p><p>此伺服器與相同的 Azure 網路連接，以做為設定伺服器。</p><p>您會在 Site Recovery 入口網站中設定</p> | <p>它會使用在您的 Azure 儲存體帳戶之 Blob 儲存體上所建立的附加 VHD，從您的受保護機器接收並保留複寫的資料。</p>   
**處理序伺服器** | <p>部署做為執行 Windows Server 2012 R2 的內部部署虛擬或實體伺服器</p> <p>建議您將它放置在與您要保護之機器相同的網路與 LAN 區段上，但只要受保護的機器具有 L3 網路可見性，它就可以在不同的網路上運作。<p>您會在 Site Recovery 入口網站中設定它，並向設定伺服器註冊。</p> | <p>受保護的機器會傳送複寫資料給內部部署處理序伺服器。它具有磁碟快取功能，可快取本身接收的複寫資料。它會對該資料執行數個動作。</p><p>它藉由在傳送資料到主要目標伺服器之前，快取、壓縮和加密資料來將資料最佳化。</p><p>它會處理行動服務的推送安裝。</p><p>它會執行 VMware 虛擬機器的自動探索。</p>
**內部部署機器** | 內部部署機器是在 VMware Hypervisor 上執行的虛擬機器，或是執行 Windows 或 Linux 的實體伺服器。 | 您會設定適用虛擬機器和伺服器的複寫設定。您可以讓個別機器容錯移轉，或更常見地，隨著包含多個虛擬機器的復原方案一起容錯移轉。
**行動服務** | <p>在您想要保護的每個虛擬機器或實體伺服器上安裝</p><p>可以手動安裝或推入並由處理序伺服器自動安裝。 | 服務會取得每個受保護的電腦上資料的 VSS 快照集，並將其移至處理序伺服器，接著將它複製到主要目標伺服器。
**Azure Site Recovery 保存庫** | 訂閱 Site Recovery 服務之後設定。 | 您在 Site Recovery 保存庫中註冊伺服器。保存庫可在您的內部部署站台與 Azure 之間協調資料複寫、容錯移轉及復原等作業。
**複寫機制** | <p>**透過網際網路**—透過受保護的內部部署伺服器與 Azure，使用受保護的 SSL/TLS 通訊通道經由公用網際網路連線來通訊與複寫資料。這是預設選項。</p><p>**VPN/ExpressRoute**－透過 VPN 連線，在內部部署伺服器與 Azure 之間進行通訊與複寫資料。您必須在內部部署網站與 Azure 網路之間設定站對站 VPN 或 [ExpressRoute](../expressroute-introduction.md) 連接。</p><p>您會選取要在 Site Recovery 部署期間複寫的方式。機制設定後若不會影響已受保護伺服器上的保護，您便無法變更機制。| <p>這些選項都不會要求您開啟受保護機器上的任何輸入網路連接埠。所有的網路通訊是從內部部署站台起始。</p> 

您可以在 [Site Recovery 元件](site-recovery-components.md)中深入了解 Site Recovery 元件、提供者和代理程式。

## 容量規劃

考量的主要領域為：

- **來源環境**—VMware 基礎結構、來源機器設定和需求。
- **元件伺服器**—處理序伺服器、設定伺服器和主要目標伺服器 

### 來源環境的考量

- **磁碟最大大小**—可以連接至虛擬機器的磁碟的目前大小上限為 1 TB。因此，可以複寫的來源磁碟的最大大小也是限制為 1 TB。
- **每一來源的最大大小**—單一來源機器的最大大小是 31 TB (含 31 個磁碟)，以及對主要目標伺服器佈建一個 D14 執行個體。 
- **每一主要目標伺服器的來源數目**—可以使用單一主要目標伺服器保護的多個來源機器。不過，無法跨多個主要目標伺服器保護單一來源機器，因為在磁碟複寫時，會在 Azure Blob 儲存體上建立可反映磁碟大小的 VHD，並將其附加至主要目標伺服器做為資料磁碟。  
- **每一來源每日變更率上限**—考慮每一來源建議的變更率時，有三個需要考量的因素。針對目標式考量，來源上每個作業的目標磁碟需要兩個 IOP。這是因為目標磁碟上將會發生舊資料的讀取和新資料的寫入。 
	- **處理序伺服器支援的每日變更率**—來源機器不能跨越多個處理序伺服器。單一處理序伺服器可以支援多達 1 TB 的每日變更率。因此，1 TB 是來源機器所支援的最大變換。 
	- **目標磁碟支援的最大輸送量**—每一來源磁碟的最大變換不能超過 144 GB/天 (以 8K 寫入大小)。請參閱主要目標小節中的資料表，以取得各種寫入大小的目標的輸送量和 IOP。必須將此數字除以 2，因為每個來源 IOP 會在目標磁碟上產生 2 個 IOP。 
	- **儲存體帳戶支援的最大輸送量**—來源不能跨越多個儲存體帳戶。假設某個儲存體帳戶每秒可接受最多 20,000 個要求，並且每個來源 IOP 會在主要目標伺服器上產生 2 個 IOP，建議您將來源的 IOP 數目保留為 10,000。 

### 元件伺服器的考量

表 1 摘要說明設定和主要目標伺服器的虛擬機器大小。

**元件** | **部署的 Azure 執行個體** | **核心** | **記憶體** | **最大磁碟** | **磁碟大小**
--- | --- | --- | --- | --- | ---
設定伺服器 | 標準 A3 | 4 | 7 GB | 8 | 1023 GB
主要目標伺服器 | 標準 A4 | 8 | 14 GB | 16 | 1023 GB
 | 標準 D14 | 16 | 112 GB | 32 | 1023 GB

**表 1**

#### 處理序伺服器考量

通常處理序伺服器的大小取決於跨所有受保護的工作負載的每日變更率。主要的考量事項包括：

-	您需要足夠的計算才能執行內嵌壓縮和加密之類的工作。
-	處理序伺服器使用磁碟快取。確定建議的快取空間和磁碟輸送量可供使用，以在加速網路瓶頸或中斷時促進儲存的資料變更。 
-	確保有足夠的頻寬，使得處理序伺服器可以將資料上傳到主要目標伺服器，以提供持續的資料保護。 

表 2 提供處理序伺服器指導方針的摘要。

**資料變更率** | **CPU** | **記憶體** | **快取磁碟大小**| **快取磁碟輸送量** | **輸入/輸出頻寬**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5GHz) | 4 GB | 600 GB | 每秒 7 至 10 MB | 30 Mbps/21 Mbps
300 至 600 GB | 8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz) | 6 GB | 600 GB | 每秒 11 至 15 MB | 60 Mbps/42 Mbps
600 GB 至 1 TB | 12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz) | 8 GB | 600 GB | 每秒 16 至 20 MB | 100 Mbps/70 Mbps
> 1 TB | 部署另一個處理序伺服器 | | | | 

**資料表 2**

其中：

- 輸入是下載頻寬 (來源與處理序伺服器之間的內部網路)。
- 輸出是上傳頻寬 (處理序伺服器與主要目標伺服器之間的網際網路)。輸出數目假設平均 30% 的處理序伺服器壓縮。
- 針對快取磁碟，建議所有的處理序伺服器的個別作業系統磁碟最少有 128 GB。
- 針對快取磁碟輸送量，使用了下列儲存體進行基準測試：採用 RAID 10 配置的 8 個 10000 轉 RPM 的 SAS 磁碟機。

#### 設定伺服器考量

每個設定伺服器可以利用 3 到 4 個磁碟區支援多達 100 個來源機器。若已超過這些數字，建議您部署另一個設定伺服器。請參閱表 1 以取得設定伺服器的預設虛擬機器屬性。

#### 主要目標伺服器與儲存體帳戶的考量

每個主要目標伺服器的儲存體是由作業系統磁碟、保留磁碟區和資料磁碟組成。在 Site Recovery 入口網站中所定義的保留時段持續時間中，保留磁碟機會維護磁碟變更的日誌。如需虛擬機器伺服器屬性的主要目標，請參閱表 1。表 3 顯示如何使用 A4 的磁碟。

**執行個體** | **作業系統磁碟** | **保留** | **資料磁碟**
--- | --- | --- | ---
 | | **保留** | **資料磁碟**
標準 A4 | 1 個磁碟 (1 * 1023 GB) | 1 個磁碟 (1 * 1023 GB) | 15 個磁碟 (15 * 1023 GB)
標準 D14 | 1 個磁碟 (1 * 1023 GB) | 1 個磁碟 (1 * 1023 GB) | 31 個磁碟 (15 * 1023 GB)

**資料表 3**

主要目標伺服器的容量計劃仰賴於：

- Azure 儲存體效能和限制
	- 每一磁碟 500 個 IOP
	- 每一儲存體帳戶 20000 個要求
	- 根據這些數目，理想的情況下，一個儲存體帳戶支援 40 個磁碟，每個磁碟可處理 500 個 IOP。 
-	每日變更率 
-	保留磁碟區儲存體。

請注意：

- 一個來源不能跨越多個儲存體帳戶。這會套用到屬於您設定保護時選取的儲存體帳戶的資料磁碟。作業系統和保留磁碟通常屬於自動部署的儲存體帳戶。
- 需要的保留儲存體磁碟區取決於每日變更率和保留的天數。每個主要目標伺服器需要的保留儲存體 = 每日來自來源的變換總計 * 保留天數。 
- 每個主要目標伺服器只有一個保留磁碟區。保留磁碟區會在連接到主要目標伺服器的磁碟之間共用。例如：
	- 如果有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8K 大小)，則這可轉譯為每一磁碟 240 個 IOP (每一來源 IO 目標磁碟上 2 個作業)。每一磁碟的 IOP 限制 500，在 Azure 內是 240 個 IOP。
	- 在保留磁碟區上，這會變成 120 * 5 = 600 個 IOP，而這可能會變成瓶頸。在此案例中，加入更多磁碟到保留的磁碟區，並跨越以做為 RAID 等量磁碟區設定是不錯的策略。因為 IOP 會分散到多個磁碟機，這將可改進效能。要加入至保留磁碟區的磁碟機數目如下所示：
		- 來自來源環境的 IOP 總計 / 500
		- 每日來自來源環境的變換總計 (未壓縮) / 287 GB。287 GB 是每日的目標磁碟支援的最大輸送量。此度量會隨著寫入大小 8K 的因數而異，因為在此情況下，8K 是假設的寫入大小。例如，如果寫入大小是 4K，則輸送量會是 287/2。而且如果寫入大小為 16K，那麼輸送量會是 287*2。
- 儲存體帳戶所需的數目 = 來源 IOP 總計 / 10000。


## 開始之前

**元件** | **需求** | **詳細資料**
--- | --- | --- 
**Azure 帳戶** | 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](pricing/free-trial/)開始。
**Azure 儲存體** | <p>您將需要 Azure 儲存體帳戶來儲存複寫的資料</p><p>帳戶應該啟用異地複寫</p><p>與 Azure Site Recovery 服務位於相同的區域並與相同的訂用帳戶相關聯。</p><p>若要深入了解，請閱讀 [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)</p>
**Azure 虛擬網路** | 您需要 Azure 虛擬網路以部署設定伺服器與主要目標伺服器。需與 Azure Site Recovery 保存庫位於相同的訂用帳戶與區域中。
**Azure 資源** | 請確認您有足夠的 Azure 資源以部署所有元件。在 [Azure 訂用帳戶限制](../azure-subscription-service-limits.md)中深入了解。
**Azure 虛擬機器** | <p>您想要保護的虛擬機器應該符合 [Azure 必要條件](site-recovery-best-practices.md)。</p><p>**磁碟計數**—單一受保護伺服器上可支援最多 31 個磁碟</p><p>**磁碟大小**—個別磁碟容量不應該超過 1023 GB</p><p>**叢集**—不支援叢集伺服器</p><p>**開機**—不支援整合可延伸韌體介面 (UEFI)/可延伸韌體介面 (EFI) 開機</p><p>**磁碟區**—不支援 Bitlocker 加密的磁碟區</p><p> **伺服器名稱**—名稱應包含 1 到 63 個字元 (字母、數字和連字號)。名稱必須以字母或數字開頭，並以字母或數字結尾。機器受到保護之後，您可以修改 Azure 的名稱。</p>
**設定伺服器** | <p>將在您的訂用帳戶中為設定伺服器建立以 Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器。它會建立為具有保留的公用 IP 位址的新雲端服務中的第一個執行個體。</p><p>安裝路徑應該只有英文字元。</p>
**主要目標伺服器** | <p>Azure 虛擬機器，標準 A4 或 D14。</p><p>安裝路徑應該只有英文字元。例如，執行 Linux 的主要目標伺服器之路徑應為 **/usr/local/ASR**。</p></p>
**處理序伺服器** | <p>您可以在執行最新版更新之 Windows Server 2012 R2 的實體或虛擬機器上部署處理序伺服器。在 C:/ 上安裝。</p><p>建議您將此伺服器放在與您要保護的機器相同的網路與子網路上。</p><p>在程序伺服器上安裝 VMware vSphere CLI 5.5.0。處理序伺服器上需要 VMware vSphere CLI 元件，才能探索 由 vCenter 伺服器管理的虛擬機器或 ESXi 主機上執行的虛擬機器。</p><p>安裝路徑應該只有英文字元。</p>
**VMware** | <p>VMware vCenter 伺服器，管理您的 VMware vSphere Hypervisor。它應該執行 vCenter 5.1 或 5.5 版，並且具備最新的更新。</p><p>一或多個 vSphere Hypervisor，包含您要保護的 VMware 虛擬機器。Hypervisor 應該執行 ESX/ESXi 5.1 或 5.5 版或更新版本並且具備最新的更新。</p><p>VMware 虛擬機器應該已安裝並執行 VMware 工具。</p>
**Windows 機器** | <p>受保護的實體伺服器或執行 Windows 的 VMware 虛擬機器有一些需求。</p><p>支援的 64 位元作業系統：**Windows Server 2012 R2**、**Windows Server 2012** 或 **Windows Server 2008 R2 包含至少 SP1**。</p><p>主機名稱、掛接點、裝置名稱、Windows 系統路徑 (例如：C:\\Windows) 應該僅使用英文。</p><p>作業系統應該安裝在 C:\\ 磁碟機上。</p><p>僅支援基本磁碟。不支援動態磁碟。</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>您必須提供系統管理員帳戶 (必須是 Windows 機器上的本機系統管理員)，才能在 Windows 伺服器上推入安裝行動服務。如果提供的帳戶是非網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。若要執行此動作，請在 HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。若要加入登錄項目，請從 CLI 開啟 cmd 或 PowerShell，然後輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。[深入了解](https://msdn.microsoft.com/library/aa826699.aspx)關於存取控制。</p><p>容錯移轉之後，如果您想要使用遠端桌面連線到 Azure 中的 Windows 虛擬機器，請確認內部部署機器的遠端桌面已啟用。如果您不透過 VPN 防火牆連線，規則應該允許透過網際網路的遠端桌面連線。</p>
**Linux 機器** | <p> 支援的 64 位元作業系統：**Centos 6.4、6.5、6.6**；**Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)**、**SUSE Linux Enterprise Server 11 SP3**。</p><p>受保護機器上的防火牆規則應該允許它們連線到 Azure 中的設定與主要目標伺服器。</p><p>在受保護機器上的 /etc/hosts 檔案，應該包含將本機主機名稱對應至所有 NIC 相關聯的 IP 位址的項目 </p><p>如果您想要在容錯移轉之後使用安全殼層用戶端 (ssh) 連接到執行 Linux 的 Azure 虛擬機器，請確定在受保護電腦上的安全殼層服務是設定為在系統開機時自動啟動，而且防火牆規則允許使用 ssh 對其連線。</p><p>主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (例如 /etc/、/usr) 應該僅使用英文。</p><p>可以對內部部署機器的下列儲存體啟用保護：檔案系統：EXT3、ETX4、ReiserFS、XFS/Multipath 軟體-裝置對應工具 (多重路徑)/磁碟區管理員：不支援使用 HP CCISS 控制器儲存體的 LVM2\\實體伺服器。</p>
**第三方** | 在這個案例中某些部署元件取決於第三方廠商軟體才能正常運作。如須完整清單，請參閱[第三方廠商軟體注意事項和資訊](#third-party)

## Deployment

圖形摘要列出部署步驟。

![部署步驟](./media/site-recovery-vmware-to-azure/VMWare2AzureSteps.png)



## 步驟 1：建立保存庫

1. 登入[管理入口網站](https://portal.azure.com)。


2. 展開 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。


3. 按一下 [新建] > [快速建立]。

4. 在 [名稱] 中，輸入保存庫的易記識別名稱。

5. 在 [區域] 中，選取保存庫的地理區域。若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](pricing/details/site-recovery/) (英文) 中的＜各區域上市情況＞。

6. 按一下 [建立保存庫]。

	![新增保存庫](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要 [復原服務] 頁面上會列為 [使用中]。

## 步驟 2：部署設定伺服器

### 設定伺服器設定

1. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

	![快速啟動圖示](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. 在下拉式清單中，選取 [**在附帶 VMware/實體伺服器的內部部署站台與 Azure 之間**]。
3. 在 [**準備目標 (Azure) 資源**] 按一下 [**部署設定伺服器**]。

	![部署設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS2.png)

4. 在 [**新增設定伺服器詳細資料**] 中指定：

	- 設定伺服器的名稱和連接到它的認證。
	- 選取伺服器所在地方的 Azure 網路。 
	- 指定要指派給伺服器的內部 IP 位址與子網路。請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。使用任何其他可用的 IP 位址。
	
	![部署設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMware_CSDetails2.png)

5. 當您按一下 [**確定**]，會在您的訂用帳戶中為設定伺服器建立以 Azure Site Recovery Windows Server 2012 R2 資源庫映像為基礎的標準 A3 虛擬機器。這個虛擬機器的建立，可在附帶保留公用 IP 位址的新雲端服務中做為第一個執行個體。您可以在 [**作業**] 索引標籤中監視進度。

	![監視進度](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

6.  部署設定伺服器之後，請在 Azure 入口網站的 [**虛擬機器**] 頁面中注意指派給此伺服器的公用 IP 位址。然後在 [**端點**] 索引標籤上注意對應至私人連接埠 443 的公用 HTTPS 連接埠。稍後當您在主要目標與處理序伺服器上註冊此設定伺服器的時候，您會需要這個資訊。這個設定伺服器利用這些端點進行部署：

	- HTTPS：公用連接埠是用於透過網際網路協調元件伺服器與 Azure 之間的通訊。私人連接埠 443 是用於透過 VPN 協調元件伺服器與 Azure 之間的通訊。
	- 自訂：公用連接埠用於透過網際網路的容錯回復工具通訊。
	- 私人連接埠 9443 是用於透過 VPN 的容錯回復工具通訊。
	- PowerShell：私人連接埠 5986
	- 遠端桌面：私人連接埠 3389
	
	![VM 端點](./media/site-recovery-vmware-to-azure/ASRVMWare_VMEndpoints.png)

    >[AZURE.WARNING]切勿刪除或變更設定伺服器部署期間建立的任何端點的公用或私用通訊埠編號。

設定伺服器使用保留的 IP 位址部署在自動建立的 Azure 雲端服務。需要保留的位址才能確保雲端服務上的虛擬機器 (包括設定伺服器) 重新開機後，設定伺服器雲端服務 IP 位址仍是相同的。當解除委任設定伺服器時，將必須手動將保留的公用 IP 位址取消保留，否貝它仍將維持保留。每一訂用帳戶保留的公用 IP 位址預設限制為 20。[深入了解](https://msdn.microsoft.com/library/azure/dn630228.aspx)有關保留的 IP 位址。

### 在保存庫中註冊設定伺服器

1. 在 [**快速入門**] 頁面上，按一下 [**準備目標資源**] > [**下載註冊金鑰**]。金鑰檔案會隨即自動產生。該金鑰在產生後會維持 5 天有效。將它複製到設定伺服器。
2. 在 [**虛擬機器**] 中，從虛擬機器清單選取設定伺服器。開啟 [**儀表板**] 索引標籤，然後按一下 [**連接**]。[**開啟**] 下載的 RDP 檔案以使用遠端桌面登入設定伺服器。當您第一次登入時，Azure Site Recovery 設定伺服器安裝精靈會自動執行。

	![註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationSplashscreen.png)

3. 在 [**協力廠商軟體安裝**] 中，按一下 [**我接受**] 來下載並安裝 MySQL。

	![MySQL 安裝](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLEULA.png)

4. 在 [**MySQL 伺服器詳細資料**] 中，建立認證來登入 MySQL 伺服器執行個體。

	![MySQL 認證](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLPWD.png)

5. 如果設定伺服器連接到透過站對站 VPN 連線到內部部署網路的 Azure 虛擬網路，請在 [**選取網路設定**] 中選取 [**VPN 模式**]。

	![VPN 註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVPN.png)

6. 在 [**網際網路設定**] 中，指定設定伺服器將連線到網際網路的方式。請注意：

	- 如果您想要使用自訂 proxy，您應該在安裝提供者之前進行設定。
	- 當您按 [**下一步**] 時，將會執行測試來檢查 Proxy 連線。
	- 如果您使用自訂 Proxy，或者您的預設 Proxy 需要驗證，您必須輸入 Proxy 詳細資料，包含位址、連接埠和認證。
	- 下列 URL 應可透過 Proxy 存取：
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net - 如果您有 IP 位址式防火牆規則，請確保規則設定為允許來自設定伺服器對 [Azure 資料中心 IP 範圍](https://msdn.microsoft.com/zh-TW/library/azure/dn175718.aspx)中所述的 IP 位址和 HTTPS (443) 通訊協定通訊。您必須具有打算使用以及美國西部之 Azure 區域的白名單 IP 範圍。

	![Proxy 註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationProxy.png)

7. 在 [**提供者錯誤訊息當地語系化設定**] 中，指定您想要錯誤訊息顯示的語言版本。

	![錯誤訊息註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationLocale.png)

7. 在 [**Azure Site Recovery 註冊**] 中，瀏覽並選取您複製到伺服器的金鑰檔案。

	![金鑰檔註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVault.png)

4. 在精靈的完成頁面上選取這些選項：

	- 選取 [**啟動帳戶管理對話方塊**] 來指定在您完成精靈後，應該開啟 [管理帳戶] 對話方塊。
	- 選取 [**為 Cspsconfigtool 建立桌面圖示**] 以在設定伺服器上加入桌面捷徑，讓您可以在任何時間開啟 [**管理帳戶**] 對話方塊，而不必重新執行精靈。

	![完成註冊](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationFinal.png)

5. 按一下 [**完成**] 來完成精靈。隨即產生複雜密碼。將其複製到安全的位置。您在設定伺服器上驗證與註冊處理序與主要目標伺服器時將會需要這個密碼。這個密碼也可以用來確認設定伺服器通訊中的通道完整性。您可以重新產生複雜密碼，但是屆時您也必須以新的複雜密碼再次註冊主要目標與處理序伺服器。

	![複雜密碼](./media/site-recovery-vmware-to-azure/passphrase.png)

註冊完成後，設定伺服器會列在保存庫的 [**設定伺服器**] 頁面上。

### 設定和管理帳戶

在部署期間，Site Recovery 會對下列動作要求認證：

- 當您註冊虛擬機器以使用 vCenter 伺服器自動化探索時。
- 當您加入機器以進行保護時，如此 Site Recovery 即可以在機器上安裝行動服務。

註冊設定伺服器之後，您可以開啟 [**管理帳戶**] 對話方塊來新增及管理應該用於這些動作的帳戶。有好幾種方法可執行這項操作：

- 針對設定伺服器 (cspsconfigtool) 安裝程式最後一頁上的對話方塊，開啟您選擇建立的捷徑。
- 開啟完成設定伺服器安裝程式上的對話方塊。

1. 在 [**管理帳戶**] 中，按一下 [**加入帳戶**]。您也可以修改並刪除現有的帳戶。

	![管理帳戶](./media/site-recovery-vmware-to-azure/ASRVMWare_ManageAccount.png)

2. 在 [**帳戶詳細資料**] 中，指定要用於 Azure 的帳戶名稱和認證 (網域/使用者名稱)。

	![管理帳戶](./media/site-recovery-vmware-to-azure/ASRVMWare_AccountDetails.png)

### 連線到設定伺服器 

有兩種方式可以連線到設定伺服器：

- 透過 VPN 站對站或 ExpressRoute 連線
- 透過網際網路 

請注意：

- 網際網路連線使用虛擬機器的端點搭配伺服器的公用虛擬 IP 位址。
- VPN 連線會使用伺服器的內部 IP 位址與端點私人連接埠。
- 它是一次性的決策，決定是否要從您的內部部署伺服器透過 VPN 連線或網際網路連接 (控制和複寫資料) 到在 Azure 中執行的各種元件伺服器 (設定伺服器、主要目標伺服器)。您之後無法變更此設定。如果您這樣做，將需要重新部署案例並重新保護您的機器。  

#### 設定 VPN 連線 (選擇性) 

如果您想使用 VPN 或 ExpressRoute 連線，請執行下列：

1. 如果您目前尚未設定連線，請在此處深入瞭解：

	- [ExpressRoute 或 VPN - 哪一個最適合我](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/)
	- [設定與 Azure 虛擬機器的站對站連線](../vpn-gateway-site-to-site-create.md)
	- [設定 ExpressRoute](../expressroute-configuring-exps.md)
2. 在保存庫中，按一下 [**伺服器**] > [**設定伺服器**] > [設定伺服器] > [**設定**]。
3. 在 [**連線設定**] 中，確定 [**連線類型**] 設為 **VPN**。請注意，如果您已設定 VPN 且沒有內部部署站台的網際網路存取，請確認您已選取 VPN 選項。如果您未選取，處理序伺服器就無法傳送複寫資料給其公用端點上的主要目標伺服器。

	![啟用 VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## 步驟 3：部署主要目標伺服器

1. 在 [**準備目標 (Azure) 資源**] 中，按一下 [**部署主要目標伺服器**]。
2. 指定主要目標伺服器的詳細資料和認證。伺服器將部署在與您註冊之設定伺服器相同的 Azure 網路。當您按一下以完成動作時，隨即會建立附帶 Windows 或 Linux 資源庫映像的 Azure 虛擬機器。

	![目標伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

請注意，任何子網路中的前四個 IP 位址是保留給內部 Azure 使用。指定任何其他可用的 IP 位址。

3. 利用這些端點建立 Windows 主要目標伺服器虛擬機器：

	- 自訂：處理序伺服器可使用公用連接埠透過網際網路傳送複寫資料。處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
	- 自訂 1：處理序伺服器可使用公用連接埠透過網際網路傳送控制中繼資料。處理序伺服器可使用私人連接埠 9080 透過 VPN 傳送控制中繼資料給主要目標伺服器。
	- PowerShell：私人連接埠 5986
	- 遠端桌面：私人連接埠 3389

4. 利用這些端點建立 Linux 主要目標伺服器虛擬機器：

	- 自訂：處理序伺服器可使用公用連接埠透過網際網路傳送複寫資料。處理序伺服器可使用私人連接埠 9443 透過 VPN 傳送複寫資料給主要目標伺服器。
	- 自訂 1：處理序伺服器可使用公用連接埠透過網際網路傳送控制中繼資料。處理序伺服器可使用私人連接埠 9080 透過 VPN 傳送控制資料給主要目標伺服器
	- SSH：私人連接埠 22

    >[AZURE.WARNING]切勿刪除或變更主要目標伺服器部署期間建立的任何端點的公用或私用通訊埠編號。

5. 在 [**虛擬機器**] 中，等待虛擬機器啟動。

	- 如果您已利用 Windows 設定伺服器，請記下遠端桌面詳細資料。
	- 如果您已利用 Linux 設定，並透過 VPN 連線，請注意虛擬機器的內部 IP 位址。如果您透過網際網路連線，請注意公用 IP 位址。

6.  登入伺服器以完成安裝，並在設定伺服器上註冊該伺服器。
7.  如果您在執行 Windows：

	1. 啟動與虛擬機器的遠端桌面連線。第一次登入指令碼會在 PowerShell 視窗中執行。不要關閉它。完成時，主機代理程式設定工具會自動開啟註冊伺服器。
	2. 在 [**主機代理程式設定**] 中，指定設定伺服器的內部 IP 位址與連接埠 443。即使您並非透過 VPN 模式連線，您還是可以使用內部位址與私人連接埠 443，因為虛擬機器附加於和設定伺服器相同的 Azure 網路。讓 [**使用 HTTPS**] 保持啟用狀態。輸入您先前所記下的設定伺服器複雜密碼。按一下 [**確定**] 以註冊伺服器。請注意，您可以忽略頁面上的 NAT 選項。它們不會被使用。
	3. 如果估計的保留磁碟機需要超過 1 TB，您需要使用虛擬磁碟設定保留磁碟區 (R:) 和[儲存空間](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
	
	![Windows 主要目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

8. 如果您在執行 Linux：
	1. 在 [**準備目標 (Azure) 資源**] 中，按一下 [**下載並安裝其他軟體 (僅適用 Linux 主要目標伺服器)**] 以下載 Linux 主要目標伺服器封裝。將下載的 tar 檔案複製到使用 sftp 用戶端的虛擬機器。或者，您可以登入已部署的 Llinux 主要目標伺服器，並使用 *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* 來下載檔案。2. 使用安全殼層用戶端登入伺服器。請注意，如果您已透過 VPN 連線到 Azure 網路，請使用內部 IP 位址。否則請使用外部 IP 位址與 SSH 公用端點。
	3. 執行下列項目以從執行了 gzip 的安裝程式解壓縮檔案：**tar –xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64*** ![Linux 主要目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)
	4. 請確認您在解壓縮 tar 檔案內容的目錄中。
	5. 使用命令 **echo *`<passphrase>`* >passphrase.txt**，將設定伺服器複雜密碼複製到本機檔案
	6. 執行命令 “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**”。

	![註冊目標伺服器](./media/site-recovery-vmware-to-azure/Linux-MT-install.png)

9. 等候幾分鐘 (10-15)，然後在 [**伺服器**] > [**設定伺服器**] 頁面上檢查主要目標伺服器在 [**伺服器詳細資料**] 索引標籤上是否列為已註冊。如果您在執行 Linux 而且伺服器並未註冊，請再次從 /usr/local/ASR/Vx/bin/hostconfigcli 執行主機設定工具。您必須藉由執行 chmod 做為根使用者，以設定存取權限。

	![確認目標伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

>[AZURE.NOTE]請注意，註冊完成之後，最多需要 15 分鐘，設定伺服器下才會列出主要目標伺服器。若要立即更新，請按一下設定伺服器頁面底部的重新整理按鈕，以重新整理設定伺服器。

## 步驟 4：部署內部部署處理序伺服器

>[AZURE.NOTE]建議您在處理序伺服器上設定靜態 IP 位址，以保障在重新開機後可持續使用相同位址。

1. 按一下 [快速入門] > [**安裝處理序伺服器內部部署**] > [**下載與安裝處理序伺服器**]。

	![安裝處理序伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. 將下載的 zip 檔案複製到您要安裝處理序伺服器的伺服器。zip 檔案包含兩個安裝檔案：

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. 解壓縮封存，並將安裝檔案複製到伺服器上的位置。
4. 執行 **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** 安裝檔案，然後遵循指示。這樣可以安裝部署所需第三方廠商元件。
5. 然後執行 **Microsoft-ASR_CX_8.2.0.0_Windows***。
6. 在 [**伺服器模式**] 頁面上，選取 [**處理序伺服器**]。

	![伺服器選取模式](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSelection.png)

7. 在 [**環境詳細資料**] 頁面執行下列動作：


	- 如果您想要保護 VMware 虛擬機器，請按一下 [**是**]
	- 如果您只想要保護實體伺服器，因此不需要在處理序伺服器上安裝 VMware vCLI。按一下 [**否**] 並繼續。
		
	![註冊設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerVirtualPhysical.png)

8. 安裝 VMware vCLI 時，請注意下列事項：

	- **僅支援 VMware vSphere CLI 5.5.0**。處理序伺服器無法與 vSphere CLI 的其他版本或更新搭配使用。
	- 從[這裡](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)下載 vSphere CLI 5.5.0。
	- 如果您正好在您開始安裝處理序伺服器之前安裝 vSphere CLI，並且安裝程式未偵測到它，請等候最多五分鐘的時間，再重新嘗試安裝。這可確保 vSphere CLI 偵測需要的所有環境變數已正確初始化。

8.	在 [**NIC 選取範圍的處理序伺服器**] 中，選取處理序伺服器應該使用的網路介面卡。

	![選取配接器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerNICSelection.png)

9.	在 [**設定伺服器詳細資料**] 中：

	- 針對 IP 位址和連接埠，如果您透過 VPN 連線，請指定設定伺服器的內部 IP 位址以及連接埠 443。否則，請指定公用虛擬 IP 位址和對應的公用 HTTP 端點。
	- 輸入設定伺服器的複雜密碼。
	- 如果您想要在使用自動推入安裝服務時停用驗證，請清除 [**驗證行動服務軟體簽章**]。簽章驗證需要處理序伺服器的網際網路連線能力。
	- 按 [下一步]。

	![註冊設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerConfigServer.png)


11. 在 [**選取安裝磁碟機**] 中，選取快取磁碟機。處理序伺服器需要至少有 600 GB 可用空間的快取磁碟機。然後按一下 [安裝]。

	![註冊設定伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerCacheConfig.png)

12. 請注意，您可能需要重新啟動伺服器才能完成安裝。在 [**設定伺服器**] > [**伺服器詳細資料**] 中，檢查已出現處理序伺服器，並在保存庫中成功註冊。

>[AZURE.NOTE]註冊完成之後，最多需要 15 分鐘，設定伺服器下才會列出處理伺服器。若要立即更新，請按一下設定伺服器頁面底部的重新整理按鈕，以重新整理設定伺服器
 
![驗證處理序伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

如果您在註冊處理序伺服器的時候未停用簽章驗證，您可以稍後再依下列說明停用簽章：

1. 以系統管理員身分登入處理序伺服器，並開啟 C:\\pushinstallsvc\\pushinstaller.conf 檔案進行編輯。在 **[PushInstaller.transport]** 區段下加入這一行：**SignatureVerificationChecks=”0”**。儲存並關閉檔案。
2. 重新啟動 InMage PushInstall 服務。


## 步驟 5：安裝最新的更新

在繼續之前，請確定您已安裝最新的更新。請記得依下列順序安裝更新：

1. 設定伺服器
2. 處理序伺服器
3. 主要目標伺服器

您可以在 Site Recovery **儀表板**上取得更新。針對 Linux 安裝，請從執行 gzip 的安裝程式解壓縮檔案，然後執行命令 “sudo ./install” 來安裝更新

如果是執行已安裝行動服務的虛擬機器或實體伺服器，您可以取得服務的更新，如下所示：

- 下載服務的更新，如下所示：
	- [Windows](http://download.microsoft.com/download/7/C/7/7C70CA53-2D8E-4FE0-BD85-8F7A7A8FA163/Microsoft-ASR_UA_8.3.0.0_Windows_GA_03Jul2015_release.exe)
	- [RHELP6-64](http://download.microsoft.com/download/B/4/5/B45D1C8A-C287-4339-B60A-70F2C7EB6CFE/Microsoft-ASR_UA_8.3.0.0_RHEL6-64_GA_03Jul2015_release.tar.gz)
	- [OL6-64](http://download.microsoft.com/download/9/4/8/948A2D75-FC47-4DED-B2D7-DA4E28B9E339/Microsoft-ASR_UA_8.3.0.0_OL6-64_GA_03Jul2015_release.tar.gz)
	- [SLES11-SP3-64](http://download.microsoft.com/download/6/A/2/6A22BFCD-E978-41C5-957E-DACEBD43B353/Microsoft-ASR_UA_8.3.0.0_SLES11-SP3-64_GA_03Jul2015_release.tar.gz)
- 或者，在更新處理序伺服器之後，您可以從處理序伺服器上的 C:\\pushinstallsvc\\repository 資料夾取得行動服務的更新版本。


## 步驟 6：加入 vCenter 伺服器或 ESXi 主機

1. 在 [**伺服器**] > [**設定伺服器**] 索引標籤上，選取設定伺服器並按一下 [**加入 vCenter 伺服器**] 以加入 vCenter 伺服器或 ESXi 主機。

	![選取 vCenter 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. 指定 vCenter 伺服器或 ESXi 主機的詳細資料並選取將用來探索詳細資料的處理序伺服器。

	- 如果 vCenter 伺服器不在預設連接埠 443 上執行，請指定 vCenter 伺服器執行所在的連接埠號碼。
	- 處理序伺服器必須與 vCenter 伺服器/ESXi 主機在相同的網路上，並且已安裝 VMware vSphere CLI 5.5.0。

	![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter4.png)


3. 探索完成之後，vCenter 伺服器將會列在設定伺服器詳細資料之下。

	![vCenter 伺服器設定](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)

4. 如果您使用非系統管理員帳戶，若要加入 vCenter 伺服器或 ESXi 主機，請確定帳戶具有下列權限：

	- vCenter 帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、儲存體檢視、虛擬機器和 vSphere 分散式切換權限。
	- ESXi 主機帳戶應該已啟用資料中心、資料存放區、資料夾、主機、網路、資源、虛擬機器和 vSphere 分散式切換權限



## 步驟 7：建立保護群組

1. 開啟 [**受保護項目**] > [**保護群組**] 並且按一下以加入保護群組。

	![建立保護群組](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. 在 [**指定保護群組設定**] 頁面上，指定群組名稱，並且選取您要建立群組的設定伺服器。

	![保護群組設定](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. 在 [**指定複寫設定**] 頁面上，進行將用於群組中所有機器的複寫設定。

	![保護群組複寫](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. 設定：
	- **多 VM 一致性**：如果您開啟此功能，它會在保護群組中跨機器建立共用的應用程式一致復原點。當保護群組中的所有機器都執行相同的工作負載時，這項設定就可以發揮最高的重要性。所有機器都將復原到相同的資料點。僅適用於 Windows 伺服器。
	- **RPO 閾值**：當連續資料保護複寫 RPO 超過設定的 RPO 臨界值時，就會產生警示。
	- **復原點保留**：指定保留時段。受保護的機器可以復原到這個週期內的任意點。
	- **應用程式一致快照的頻率**：指定建立包含應用程式一致快照之復原點的頻率。

您可以監視保護群組，因為它們是建立在 [**受保護項目**] 頁面上。

## 步驟 8：設定您想要保護的電腦

您必須在您想要保護的虛擬機器和實體伺服器上安裝行動服務。您可以使用兩種方式執行此動作：

- 從處理序伺服器自動推入並在每一部機器上安裝服務。
- 手動安裝服務。 

### 自動安裝行動服務

當您將機器加入保護群組時，行動服務會自動由處理序伺服器推入並安裝於每部機器上。

**自動推入會在 Windows 伺服器上安裝行動服務：**

1. 如 [步驟 5：安裝最新的更新] (\#最新的更新) 中所述，安裝程序伺服器的最新更新，並確定處理序伺服器可用。 
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 設定 Windows 防火牆，允許**檔案及印表機共用**和 **Windows Management Instrumentation**。在 Windows 防火牆設定中，選取 [允許應用程式或功能通過防火牆] 選項，並選取應用程式，如下圖所示。針對隸屬於網域中的機器，您可以利用群組原則物件設定防火牆原則。

	![防火牆設定](./media/site-recovery-vmware-to-azure/ASRVMWare_PushInstallFirewall.png)

4. 用來執行推入安裝的帳戶必須在您要保護之機器的系統管理員群組中。這些認證僅適用於推入行動服務的安裝，並且您會在將電腦加入保護群組時提供它們。
5. 如果提供的帳戶不是網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。若要執行此動作，請在 HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。若要加入登錄項目，請從 CLI 開啟 cmd 或 PowerShell，然後輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 

**自動推入會在 Linux 伺服器上安裝行動服務：**

1. 如 [步驟 5：安裝最新的更新] (\#最新的更新) 中所述，安裝程序伺服器的最新更新，並確定處理序伺服器可用。
2. 確保來源機器和處理序伺服器之間具有網路連線，且可從處理序伺服器存取來源機器。  
3. 確認帳戶是來源 Linux 伺服器上的根使用者。
4. 確保來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有 NIC 相關聯之 IP 位址的項目。
5. 在您想要保護的電腦上安裝最新的 openssh、openssh 伺服器、openssl 套件。
6. 請確定 SSH 已啟用且正在連接埠 22 上執行。 
7. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示： 

	- a) 以 root 的身分登入。
	- b) 在 /etc/ssh/sshd_config 檔案中，尋找以 **PasswordAuthentication** 開頭的那一行。
	- c) 取消註解該行，並將值從 “no” 變更為 “yes”。

		![Linux 行動性](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	- d) 尋找以 Subsystem 開頭的行並取消註解該行。
	
		![Linux 推播行動性](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility2.png)

8. 確定支援來源機器 Linux 變異。
 
### 手動安裝行動服務

用來安裝行動服務使用的軟體套件位於處理序伺服器上的 C:\\pushinstallsvc\\repository 中。登入處理序伺服器，並將適當的安裝套件複製到來源電腦，根據下表：

| 來源作業系統 | 處理序伺服器上的行動服務封裝 |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server (僅限 64 位元) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.3.0.0_Windows_GA_03Jul2015_release.exe` |
| CentOS 6.4、6.5、6.6 (僅限 64 位元) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.3.0.0_RHEL6-64_GA_03Jul2015_Release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.3.0.0_SLES11-SP3-64_GA_03Jul2015_Release.tar.gz`|
| Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.3.0.0_OL6-64_GA_03Jul2015_Release.tar.gz` |


**若要在 Windows 伺服器上手動安裝行動服務**，請執行下列動作：

1. 從上表所列的處理序伺服器目錄路徑將 **Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe** 封裝複製到來源機器。
2. 在來源電腦上執行可執行檔來安裝行動服務。
3. 遵循安裝程式的指示。
4. 選取 [**行動服務**] 做為角色，然後按 [**下一步**]。
	
	![安裝行動服務](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall1.png)

5. 將安裝目錄保留為預設安裝路徑，然後按一下 [**安裝**]。
6. 在 [**主機代理程式設定**] 中，指定設定伺服器的 IP 位址與 HTTPS 連接埠。

	- 如果您是透過網際網路連線，請指定公用虛擬 IP 位址和公用 HTTPS 端點做為連接埠。
	- 如果您是透過 VPN 連接，請指定內部 IP 位址和連接埠 443。讓 [**使用 HTTPS**] 保持勾選狀態。

	![安裝行動服務](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall2.png)

7. 指定設定伺服器複雜密碼，然後按一下 [**確定**] 來向設定伺服器註冊行動服務。

**若要從命令列執行：**

1. 將複雜密碼從 CX 複製到伺服器上的 "C:\\connection.passphrase" 檔案，並執行此命令。在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**在 Linux 伺服器上手動安裝行動服務**：

1. 根據上表，將適當的 tar 封存檔從將處理序伺服器複製到來源機器。
2. 開啟殼層程式，並藉由執行 `tar -xvzf Microsoft-ASR_UA_8.2.0.0*` 將壓縮的 tar 封存檔解壓縮到本機路徑
3. 從殼層輸入 *`echo <passphrase> >passphrase.txt`*，在解壓縮 tar 封存檔內容的本機目錄中建立 passphrase.txt 檔案。
4. 輸入 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* 來安裝行動服務。
5. 指定 IP 位址和連接埠：

	- 如果您是透過網際網路連接到設定伺服器，請在 `<IP address>` 和 `<port>` 中指定設定伺服器的虛擬公用 IP 位址和公用 HTTPS 端點。
	- 如果您要透過 VPN 連線連接，請指定內部 IP 位址和 443。

**若要從命令列執行：**

1. 將複雜密碼從 CX 複製到伺服器上的 "passphrase.txt"檔案，並執行此命令。在我們的範例中，CX 是 104.40.75.37 和 HTTPS 連接埠是 62519：

若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
若要在目標伺服器上安裝：


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE]當您加入機器到已在執行適當版本行動服務的保護群組時，那麼會略過推入安裝。


## 步驟 9：啟用保護

若要啟用保護，您會將虛擬機器和實體伺服器加入至保護群組。開始之前，請注意：

- 系統會每隔 15 分鐘探索虛擬機器一次，而且在探索之後，需要最多 15 分鐘的時間，虛擬機器才會會出現在 Azure Site Recovery 中。
- 虛擬機器上的環境變更 (例如 VMware 工具安裝) 也可能需要最多 15 分鐘的時間，才能在 Site Recovery 中更新。
- 您可以在 [**設定伺服器**] 頁面上 vCenter 伺服器/ESXi 主機的 [**上次連絡時間**] 欄位中檢查上次探索的時間。
- 如果您已建立保護群組，並新增 vCenter Server 或 ESXi 主機之後，需要 15 分鐘，Azure Site Recovery 入口網站才會重新整理並且在 [**將機器加入至保護群組**] 對話方塊中列出虛擬機器。
- 如果您想要立即繼續將機器加入至保護群組，而不想等候排程的探索，請反白顯示設定伺服器 (不要按它)，然後按一下 [**重新整理**] 按鈕。
- 當您將虛擬機器或實體機器加入保護群組時，處理序伺服器會自動推入行動服務並在來源伺服器上安裝 (如果尚未安裝)。
- 若要讓自動推入機制運作，請確認已將受保護機器如上一個步驟所述進行設定。

如下所示加入機器：

1. [**受保護項目**] > [**保護群組**] > [**機器**] 索引標籤。按一下 [**加入機器**]。做為最佳做法，我們建議保護群組應反映您的工作負載，這樣一來您才可以將執行特定應用程式的機器加入相同的群組。
2. 在 [**選取虛擬機器**] 中，如果您要保護實體伺服器，在 [**加入實體機器**] 精靈中，提供 IP 位址和易記名稱。然後選取作業系統系列。

	![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PhysicalProtect.png)

3. 在 [**選取虛擬機器**] 中，如果您要保護 VMware 虛擬機器，請選取正在負責管理您的虛擬機器 (或其執行所在的 EXSi 主機) 的 vCenter 伺服器，然後選取機器。

	![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

4. 在 [**指定目標資源**] 中，選取要用於複寫主要目標伺服器和儲存體，並選取設定是否應該用於所有虛擬機器。

	![vCenter 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

4. 在 [**指定帳戶**] 中，選取您要於在受保護的機器上安裝行動服務的帳戶。需要帳戶認證，才能自動安裝行動服務。如果您無法選取帳戶，請確定您如步驟 2 中所述設定一個。請注意，Azure 無法存取此帳戶。對於 Windows 伺服器而言，帳戶應該具有來源伺服器上的系統管理員權限。若為 Linux，帳戶必須是 root。

	![Linux 認證](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

5. 按一下核取記號，以完成將機器加入保護群組，並啟動每部機器的初始複寫。您可以在 [**作業**] 頁面上監視狀態。

	![加入 V-Center 伺服器](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs2.png)

5. 此外，您可以監視保護狀態，方法是按一下 [**受保護的項目**] > 保護群組名稱 > [**虛擬機器**]。在初始複寫完成之後且機器在同步處理資料時，仍會顯示**受保護**狀態。

	![虛擬機器作業](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)


### 設定受保護機器屬性

1. 在機器具有**受保護**狀態之後，您可以設定其容錯移轉屬性。在保護群組詳細資料中，選取機器並開啟 [**設定**] 索引標籤。
2. 在容錯移轉和 Azure 虛擬機器大小調整之後，您可以修改要提供給 Azure 中機器的名稱。您也可以選取機器要在容錯移轉之後要連結的 Azure 網路。

	![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

請注意：

- Azure 機器的名稱必須符合 Azure 需求。
- 根據預設，Azure 中的複寫虛擬機器不會與 Azure 網路連線。如果您要讓複寫的虛擬機器進行通訊，請務必為其設定相同的 Azure 網路。
- 如果您調整 VMware 虛擬機器或實體伺服器上磁碟區的大小，它會進入嚴重狀態。如果您需要修改大小，請執行下列動作：

	- a) 變更大小設定。
	- b) 在 [**虛擬機器**] 索引標籤上，選取虛擬機器，然後按一下 [**移除**]。
	- c) 在 [**移除虛擬機器**] 中，選取 [**停用保護 (用於復原切入和磁碟區調整大小)**]。此選項會停用保護，但會在 Azure 中保留復原點。

		![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure/ASRVMWare_RemoveVM.png)

	- d) 重新啟用虛擬機器的保護。當您重新啟用保護時，重新調整過大小的磁碟區資料會傳輸至 Azure。

	

## 步驟 10：執行容錯移轉

目前您只能為受保護的 VMware 虛擬機器和實體伺服器執行非計劃的容錯移轉。請注意：



- 起始容錯移轉之前，請確保設定和主要目標伺服器執行中且狀況良好。否則容錯移轉將會失敗。
- 來源機器未隨著非計劃的容錯移轉關閉。執行非計劃的容錯移轉會停止受保護伺服器的資料複寫。您將必須從保護群組刪除機器並重新加入，然後才能在非計劃的容錯移轉完成之後，再次開始保護機器。
- 如果您想要容錯移轉而不遺失任何資料，請確定主要站台虛擬機器在您起始容錯移轉之前是關閉的。

1. 在 [**復原計畫**] 頁面上，並加入復原計畫。指定計畫的詳細資料並選取 [**Azure**] 做為目標。

	![設定復原計畫](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. 在 [**選取虛擬機器**] 中，選取保護群組，然後在群組中選取要加入復原計畫的機器。[閱讀更多](site-recovery-create-recovery-plans.md)復原方案的相關資訊。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. 若需要，您可以自訂計畫以建立群組並決定機器在復原計劃中容錯移轉的順序。您也可以加入進行手動動作和指令碼的提示。復原到 Azure 時，可以使用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 加入指令碼。

4. 在 [**復原計畫**] 頁面中，選取計畫並按一下 [**非計劃性容錯移轉**]。
5. 在 [**確認容錯移轉**] 中，確認容錯移轉方向 (朝向 Azure)，然後選取容錯移轉的目標復原點。
6. 等候容錯移轉工作完成，然後確認容錯移轉如預期般運作，且複寫的虛擬機器在 Azure 中成功啟動。




## 步驟 11：來自 Azure 機器的容錯移轉失敗

[進一步了解](site-recovery-failback-azure-to-vmware.md)有關如何在 Azure 中將執行失敗的機器還原到您的內部部署環境。


## 管理您的處理序伺服器

處理序伺服器會傳送複寫資料至 Azure 中的主要目標伺服器，並探索加入至 vCenter 伺服器的新 VMware 虛擬機器。在下列情況下，您可能要變更在您的部署中的處理序伺服器：

- 如果目前的處理序伺服器關閉
- 如果您的復原點目標 (RPO) 上升到您的組織無法接受的程度。

必要時，您可以將內部部署 VMware 虛擬機器和實體伺服器的部分或所有複寫移到另一個處理序伺服器。例如：

- **失敗**—如果處理序伺服器失敗或無法使用，您可以將受保護的機器複寫移至另一個處理序伺服器。來源機器和複本機器的中繼資料將會移至新的處理序伺服器，並重新同步處理資料。新的處理序伺服器將自動連接至 vCenter 伺服器來執行自動探索。您可以在 Site Recovery 儀表板上監視處理序伺服器的狀態。
- **負載平衡以調整 RPO**—如要獲得改善的負載平衡，您可以在 Site Recovery 入口網站中選取不同的處理序伺服器，並將一或多部機器的複寫移到它，以手動進行負載平衡。在此情況下，選取的來源和複本機器的中繼資料會移至新的處理序伺服器。原始的處理序伺服器仍然連接至 vCenter 伺服器。 

### 監視處理序伺服器

如果處理序伺服器處於嚴重狀態，Site Recovery 儀表板上將顯示狀態警告。您可以按一下狀態以開啟 [事件] 索引標籤，然後向下鑽研至 [工作] 索引標籤上的特定工作。

### 修改用於複寫的處理序伺服器

1. 也可以在 Azure 入口網站中按一下 [**虛擬機器**] > [*configurationserver*] > [**伺服器詳細資料**]。
2. 在 [**處理序伺服器**] 清單中，按一下您要修改的伺服器旁的 [**變更處理序伺服器**]。
3. 在 [**變更處理序伺服器**] 對話方塊中，於 [**目標處理序伺服器**] 中選取新伺服器，然後選取您想要複寫到新伺服器的虛擬機器。按一下伺服器名稱旁的資訊圖示，以取得其相關資訊，包括可用空間、使用的記憶體。將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間就會顯示，以協助您進行負載的決策。
4. 按一下核取記號以開始複寫到新處理序伺服器。如果從某個重要的處理序伺服器中移除所有虛擬機器，儀表板中應該不會再顯示重大警告。


## 第三方廠商軟體注意事項和資訊

無須翻譯或當地語系化

Microsoft 產品或服務中執行的軟體和韌體根據或整合以下列出專案中的資料 (以下合稱「 第三方廠商程式碼」)。Microsoft 不是原始的第三方廠商程式碼作者。Microsoft 收到這類第三方廠商程式碼所根據之原始著作權標示及授權如下所示。

區段 A 中的資訊是關於以下所列專案的第三方廠商程式碼元件。這類授權和資訊之提供僅供參考用途。Microsoft 根據其產品或服務之軟體授權條款，將此第三方廠商程式碼重新授權給您。

區段 B 中的資訊是關於 Microsoft 根據原始的授權條款提供給您的第三方廠商程式碼元件。

完整的檔案可以在 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=530254)取得。Microsoft 保留未在此處明確授與的所有權利，無論是隱含、禁止反悔或其他方式皆然。

<!---HONumber=July15_HO2-->