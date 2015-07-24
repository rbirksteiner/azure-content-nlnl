<properties
	pageTitle="Site Recovery 概觀" 
	description="Azure Site Recovery 可將內部部署上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要內部部署站台。" 
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
	ms.workload="storage-backup-recovery" 
	ms.date="05/10/2015" 
	ms.author="raynew"/>

#  Site Recovery 概觀

Site Recovery 服務提供健全的商務持續性和災害復原 (BCDR) 解決方案，可協調及自動化複寫並容錯移轉至 Azure，或是次要的內部部署資料中心，藉以保護您的內部部署實體伺服器和虛擬機器。

- **簡化** - Site Recovery 讓您輕鬆設定複寫，並為您的內部部署工作負載和應用程式執行容錯移轉和復原，有助於簡化 BCDR 策略。
- **複寫** - 您可以將內部部署工作負載複寫到 Azure 儲存體，或次要資料中心。 
- **保存庫** - 若要管理複寫，您要在所選取的 Azure 區域中設定一個 Site Recovery 保存庫。所有中繼資料都會保持在該區域內。
- **中繼資料** - 任何應用程式資料都不會傳送至 Azure。Site Recovery 只需要中繼資料 (例如虛擬機器和 VMM 雲端名稱)，就能夠協調複寫和容錯移轉。 
- **Azure 的連線** - 根據您的部署案例而定，管理伺服器會與 Azure 進行通訊。例如，如果您要複寫位於內部部署 VMM 雲端的虛擬機器，VMM 伺服器會透過加密的輸出 HTTPS 連線，與 Site Recovery 進行通訊。不需要從虛擬機器或 Hyper-V 主機連線。
- **Hyper-V 複本** - Azure Site Recovery 運用 Hyper-V 複本進行複寫程序，而且如果您要在兩個內部部署 VMM 站台之間複寫，也可以使用 SAN 複寫。Site Recovery 使用智慧型複寫，只會複寫資料區塊 (而非整個 VHD) 來進行初始複寫。對於進行中的複寫，則只會複寫差異變更。Site Recovery 支援離線資料傳輸，並搭配 WAN 最佳工具使用。
- **定價** - 您可以[深入了解](pricing/details/site-recovery) Site Recovery 定價。


## 部署案例

此資料表摘要說明 Site Recovery 支援的複寫案例。

**複寫目標** | **複寫來源 (內部部署)** | **詳細資料** | **文章**
---|---|---|---
Azure | Hyper-V 站台 | 複寫一個或多個內部部署 Hyper-V 主機伺服器上定義為 Hyper-V 站台至 Azure 的虛擬機器。不需要任何 VMM 伺服器。 | [閱讀更多資訊](site-recovery-hyper-v-site-to-azure.md)
Azure| VMM 伺服器 | 將位於 VMM 雲端的一個或多個內部部署 Hyper-V 主機伺服器上的虛擬機器複寫至 Azure。 | [閱讀更多資訊](site-recovery-vmm-to-azure.md) 
Azure | 實體 Windows 伺服器 | 將實體 Windows 或 Linux 伺服器複寫至 Azure | [閱讀更多資訊](site-recovery-vmware-to-azure.md)
Azure | VMware 虛擬機器 | 將 VMWare 虛擬機器複寫至 Azure | [閱讀更多資訊](site-recovery-vmware-to-azure.md)
次要資料中心 | VMM 伺服器 | 將位於 VMM 雲端的內部部署 Hyper-V 主機伺服器上的虛擬機器複寫至另一個資料中心的次要 VMM 伺服器。 | [閱讀更多資訊](site-recovery-vmm-to-vmm.md)
次要資料中心 | 含 SAN 的 VMM 伺服器 | 將位於 VMM 雲端的內部部署 Hyper-V 主機伺服器上的虛擬機器複寫至另一個資料中心使用 SAN 複寫的次要 VMM 伺服器。| [閱讀更多資訊](site-recovery-vmm-san.md)
次要資料中心 | 單一 VMM 伺服器 | 將位於 VMM 雲端的內部部署 Hyper-V 主機伺服器上的虛擬機器複寫至相同 VMM 伺服器上的次要雲端。 | [閱讀更多資訊](site-recovery-single-vmm.md) 


## 工作負載指引

ASR 複寫技術與虛擬機器中執行的任何應用程式相容。我們已經與應用程式產品團隊合作進行了其他測試，以進一步支援每個應用程式。

**工作負載** | <p>**複寫 Hyper-V 虛擬機器**</p> <p>**(至次要站台)**</p> | <p>**複寫 Hyper-V 虛擬機器**</p> <p>**(至 Azure)**</p> | <p>**複寫 VMware 虛擬機器**</p> <p>**(至次要站台)**</p> | <p>**複寫 VMware 虛擬機器**</p><p>**(至 Azure)**</p>
---|---|---|---|---
Active Directory、DNS | Y | Y | Y | 敬請期待 
Web 應用程式 (IIS、SQL) | Y | Y | Y | 敬請期待
SCOM | Y | Y | Y | 敬請期待
Sharepoint | Y | Y | Y | 敬請期待
<p>SAP</p><p>將 SAP 站台複寫至非叢集的 Azure </p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | 敬請期待
Exchange (非 DAG) | Y | 敬請期待 | Y | 敬請期待
遠端桌面/VDI | Y | Y | Y | 敬請期待 
<p>Linux</p> <p>(作業系統和應用程式)</p> | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | Y (由 Microsoft 測試) | 敬請期待 
Dynamics AX | Y | Y | Y | 敬請期待
Dynamics CRM | 敬請期待 | 敬請期待 | Y | 敬請期待
Oracle | 敬請期待 | 敬請期待 | Y (由 Microsoft 測試) | 敬請期待


## 功能和需求 

此資料表摘要說明 Site Recovery 主要功能，以及這些功能在使用預設 Hyper-V 複本與使用 SAN 複寫至 Azure、複寫至次要站台期間的處理方式。

<table border="1">
<thead>
<tr>
	<th>功能</th><th>複寫至 Azure</th>
	<th>複寫至次要站台 (Hyper-V 複本)</th>
	<th>複寫至次要站台 (SAN)</th>
</tr>
</thead>

<tr>
<td>資料複寫</td>
<td><p>內部部署伺服器和虛擬機器的相關中繼資料會儲存在 Site Recovery 保存庫中。</p> <p>複寫的資料會儲存在 Azure 儲存體中。</p></td>
<td><p>內部部署伺服器和虛擬機器的相關中繼資料會儲存在 Site Recovery 保存庫中。</p> <p>複寫資料儲存在目標 Hyper-V 伺服器所指定的位置。</p></td>
<td><p>內部部署伺服器和虛擬機器的相關中繼資料會儲存在 Site Recovery 保存庫中。</p> <p>複寫資料儲存在目標陣列存放裝置中。</p></td>
</tr>

<tr>
<td>保存庫必要條件</td>
<td><p>含 Site Recovery 服務的 Azure 帳戶</p></td>
<td><p>含 Site Recovery 服務的 Azure 帳戶</p>
</td><td><p>含 Site Recovery 服務的 Azure 帳戶</p></td>
</tr>

<tr>
<td>複寫</td>
<td>將虛擬機器從來源 Hyper-V 主機複寫至 Azure 儲存體。容錯回復至來源位置。</td>
<td>將虛擬機器從來源 Hyper-V 主機複寫至目標 Hyper-V 主機。容錯回復至來源位置。</td>
<td>將虛擬機器從來源 SAN 存放裝置複寫至目標 SAN 裝置。容錯回復至來源位置。</td>
</tr>

<tr>
<td>虛擬機器</td>
<td>Azure 儲存體中儲存的虛擬機器硬碟</td>
<td>Hyper-V 主機上儲存的虛擬機器硬碟</td>
<td>SAN 存放裝置陣列上儲存的虛擬機器硬碟</td>
</tr>

<tr>
<td>Azure 儲存體</td>
<td>儲存複寫的虛擬機器硬碟所需</td>
<td>不適用</td>
<td>不適用</td>
</tr>

<tr>
<td>SAN 存放裝置陣列</td>
<td><p>不適用</p></td>
<td>不適用</td>
<td>SAN 存放裝置陣列必須可提供來源和目標站台使用，並由 VMM 管理</td>
</tr>

<tr>
<td>VMM 伺服器</td>
<td>只有來源站台中的 VMM 伺服器。</td>
<td>建議使用來源和目標站台中的 VMM 伺服器。您可以在單一 VMM 伺服器上的雲端之間進行複寫。</td>
<td>來源和目標 VMM 站台中的 VMM 伺服器。雲端必須包含至少一個 Hyper-V 叢集。</td>
</tr>

<tr>
<td>VMM 版本</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 (含 SP1)</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 含 VMM 更新彙總套件 5.0</p></td>
</tr>

<tr>
<td>VMM 設定</td>
<td><p>在來源和目標站台中設定雲端</p><p>在來源和目標站台中設定 VM 網路</p><p>在來源和目標站台中設定存放裝置分類 <p>在來源與目標 VMM 伺服器上安裝提供者</p></td>
<td><p>在來源站台中設定雲端</p><p>設定 SAN 存放裝置</p><p>在來源站台中設定 VM 網路</p><p>在來源 VMM 伺服器上安裝提供者</p><p>啟用虛擬機器保護</p></td>
<td><p>在來源和目標站台中設定雲端</p><p>在來源和目標站台中設定 VM 網路</p><p>在來源和目標 VMM 伺服器上設定提供者</p><p>啟用虛擬機器保護</p></td>
</tr>

<tr>
<td><p>Azure Site Recovery 提供者</p><p>用來透過 HTTPS 連線至 Site Recovery</p></td>
<td>安裝在來源 VMM 伺服器上</td>
<td>安裝在來源和目標 VMM 伺服器上</td>
<td>安裝在來源和目標 VMM 伺服器上</td>
</tr>

<tr>
<td><p>Azure 復原服務代理程式</p><p>用來透過 HTTPS 連線至 Site Recovery</p></td>
<td>在 Hyper-V 主機伺服器上安裝</td>
<td>不需要</td>
<td>不需要</td>
</tr>

<tr>
<td>虛擬機器復原點</td>
<td><p>依時間設定復原點。</p> <p>指定復原點的保留時間 (0-24 小時)</p></td>
<td><p>依數量設定復原點。</p> <p>指定應該保留的其他復原點數目 (0-15)。依預設每小時建立一個復原點</p></td>
<td>在陣列存放裝置設定中進行設定</td>
</tr>

<tr>
<td>網路對應</td>
<td><p>將 VM 網站對應至 Azure 網路。</p> <p>網路對應可確保在相同來源 VM 網路中容錯移轉的所有虛擬機器，在容錯移轉之後仍可連接。此外，如果目標 Azure 網路上有網路閘道，則虛擬機器可以連接到內部部署虛擬機器。</p><p>如果未啟用對應，則只有相同復原計畫中容錯移轉的虛擬機器，才能在容錯移轉到 Azure 之後彼此連接。</p></td>
<td><p>將來源 VM 網路對應至目標 VM 網路。</p> <p>網路對應可用來將複寫的虛擬機器放在最佳的 Hyper-V 主機伺服器上，並確保與來源 VM 網路相關聯的虛擬機器，在容錯移轉之後會與對應的目標網路相關聯。</p><p>如果未啟用對應，則複寫的虛擬機器不會連接至網路。</p></td>
<td><p>將來源 VM 網路對應至目標 VM 網路。</p> <p>網路對應可確保與來源 VM 網路相關聯的虛擬機器，在容錯移轉之後會與對應的目標網路相關聯。</p><p>如果未啟用對應，則複寫的虛擬機器不會連接至網路。</p></td>
</tr>

<tr>
<td>儲存體對應</td>
<td>不適用</td>
<td><p>將來源 VM 伺服器上的儲存體分類對應至目標 VMM 伺服器上的儲存體分類。</p> <p>如果啟用對應，則來源儲存體分類中的虛擬機器硬碟在容錯移轉之後，將位於目標儲存體分類中。</p><p>如果未啟用儲存體對應，則複寫的虛擬硬碟會儲存在目標 Hyper-V 主機伺服器上的預設位置。</p></td>
<td><p>在主要和次要站台的存放裝置陣列與集區之間進行對應。</p></td>
</tr>

</table>

## 後續步驟

完成此概觀之後，請[讀取最佳作法](site-recovery-best-practices.md)以協助您開始進行部署規劃。
 

<!---HONumber=July15_HO2-->