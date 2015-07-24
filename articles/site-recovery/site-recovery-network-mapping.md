<properties
	pageTitle="Site Recovery 網路對應"
	description="Azure Site Recovery 可將內部部署上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要內部部署站台。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>


# Site Recovery 網路對應


Azure Site Recovery 可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原做出貢獻。了解 [Site Recovery 概觀](site-recovery-overview.md)中可能的部署案例。


## 本文內容

網路對應是部署 VMM 和 Site Recovery 時的重要元素。它會以最佳方式將複寫的虛擬機器放在目標 Hyper-V 主機伺服器上，並確保複寫的虛擬機器會在容錯移轉後連線到適當的網路。本文說明網路對應並提供幾個範例，以協助您了解網路對應的運作方式。


若有任何問題，請造訪 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文)。

## 概觀

您設定網路對應的方式取決於您的 Site Recovery 部署案例。



- **內部部署至內部部署 VMM 伺服器** — 網路對應會對應來源 VMM 伺服器上的 VM 網路與目標 VMM 伺服器上的 VM 網路，以便：

	- **在容錯移轉之後連線虛擬機器** — 可確保虛擬機器將在容錯移轉之後，連線到適當的網路。複本虛擬機器將會連線至對應到來源網路的目標網路。
	- **將複本虛擬機器放在主機伺服器上** — 以最佳方式將複本虛擬機器放在 Hyper-V 主機伺服器上。複本虛擬機器將會放在可以存取對應的 VM 網路的主機上。
	- **無網路對應** — 如果您沒有設定網路對應，複本虛擬機器將不會在容錯移轉後連線到 VM 網路。

- **內部部署 VMM 伺服器至 Azure** — 網路對應會對應來源 VMM 伺服器上的 VM 網路與目標 VMM 伺服器上的 VM 網路，以便：
	- **在容錯移轉之後連線虛擬機器** — 在相同網路上容錯移轉的所有機器都可以彼此連線，無論它們隸屬於哪個復原計畫都一樣。
	- **網路閘道** — 如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連線到其他內部部署虛擬機器。
	- **無網路對應** — 如果您未設定網路對應，則只有在相同復原計畫中容錯移轉的虛擬機器才能在容錯移轉到 Azure 之後彼此連線。

## VM 網路

VMM 邏輯網路提供實體網路基礎結構的抽象檢視。VM 網路提供網路介面，讓虛擬機器可以連線到邏輯網路。邏輯網路至少需要一個 VM 網路。當您將虛擬機器放在雲端中保護時，必須連線至連結到與雲端相關聯的邏輯網路的 VM 網路。深入了解：

- [邏輯網路 (第 1 部分)](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)
- [VMM 2012 SP1 中的虛擬網路](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx)

## 範例

您可以在兩部 VMM 伺服器的 VM 網路之間設定網路對應，如果兩個站台由相同的伺服器管理，則可以在單一 VMM 伺服器上設定網路對應。正確設定對應並啟用複寫時，位於主要位置的虛擬機器將會連線至網路，且其位於目標位置的複本將會連線到其對應的網路。

如果網路已在 VMM 中正確設定，當您在網路對應期間選取目標 VM 網路時，將會顯示使用來源 VM 網路的 VMM 來源雲端，以及用於保護的目標雲端上的可用目標 VM 網路。

以下是說明這項機制的範例。讓我們以具有兩個位置 (紐約和芝加哥) 的組織為例。

**位置** | **VMM 伺服器** | **VM 網路** | **對應至**
---|---|---|---
紐約 | VMM-NewYork| VMNetwork1-NewYork | 對應至 VMNetwork1-Chicago
 | | VMNetwork2-NewYork | 未對應
芝加哥 | VMM-Chicago| VMNetwork1-Chicago | 對應至 VMNetwork1-NewYork
 | | VMNetwork1-Chicago | 未對應

在此範例中：

- 為連線至 VMNetwork1-NewYork 的任何虛擬機器建立複本虛擬機器時，它將會連線至 VMNetwork1-Chicago。
- 為 VMNetwork2-NewYork 或 VMNetwork2-Chicago 建立複本虛擬機器時，它將不會連線至任何網路。

以下是如何在我們的範例組織中設定 VMM 雲端，以及與雲端相關聯的邏輯網路。

### 雲端保護設定

**受保護的雲端** | **保護雲端** | **邏輯網路 (紐約)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### 邏輯和 VM 網路設定

**位置** | **邏輯網路** | **相關聯的 VM 網路**
---|---|---
紐約 | LogicalNetwork1-NewYork | VMNetwork1-NewYork
芝加哥 | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### 目標網路

根據這些設定，當您選取目標 VM 網路時，下表會顯示可用的選擇。

**選取** | **受保護的雲端** | **保護雲端** | **可用的目標網路**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 可用
 | GoldCloud1 | GoldCloud2 | 可用
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 尚未提供
 | GoldCloud1 | GoldCloud2 | 可用



## 多重子網路

如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。


### 容錯回復

若要了解容錯回復 (反向複寫) 時發生的狀況，讓我們假設 VMNetwork1-NewYork 對應到 VMNetwork1-Chicago，並包含下列設定。


**虛擬機器** | **已連線至 VM 網路**
---|---
VM1 | VMNetwork1-Network
VM2 (VM1 的複本) | VMNetwork1-Chicago

讓我們使用這些設定檢閱幾個可能的案例中發生的情況。

**案例** | **結果**
---|---
在容錯移轉之後，VM-2 的網路內容沒有變更 | VM 1 仍然連線至來源網路。
在容錯移轉並中斷連線之後，VM-2 的網路內容變更 | VM-1 已中斷連線
在容錯移轉並連線至 VMNetwork2-Chicago 之後，VM-2 的網路內容變更 | 如果未對應 VMNetwork2-Chicago，將會中斷 VM-1 連線
VMNetwork1-Chicago 的網路對應已變更 | VM-1 現在將會連線到對應至 VMNetwork1-Chicago 的網路


## 後續步驟

既然您已經更了解網路對應，請開始讀取[最佳作法](site-recovery-best-practices.md)以準備部署。
 

<!---HONumber=July15_HO2-->