<properties
	pageTitle="Azure 機器虛擬備份的簡介"
	description="使用 Azure 備份服務來備份在 Azure 中虛擬機器的簡介"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>

# Azure 虛擬機器備份 - 簡介

本章節提供使用 Microsoft Azure 備份來保護 Azure 虛擬機器的簡介。在閱讀本文後，您將了解：

- Azure 虛擬機器備份的運作方式
- 備份 Azure 虛擬機器的程序
- 使備份流暢的必要條件
- 遇到的一般錯誤以及如何排除
- 不支援的狀況清單以及如何影響產品的變更

若要快速了解有關 Azure 虛擬機器的詳細資訊，請參閱＜[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)＞。

## 為什麼要備份 Azure 虛擬機器？
雲端計算讓應用程式在可調整和高可用性的環境中執行，這正是 Microsoft 開發 Azure 虛擬機器的原因。從這些 Azure 虛擬機器產生的資料很重要，並需要備份以妥善保存。一般需要還原備份資料的狀況包括：

- 意外或惡意刪除檔案
- 虛擬機器在修補更新期間損毀
- 意外或惡意刪除整台虛擬機器

這些虛擬機器資料可以透過兩種不同方式進行備份：

- 備份來自虛擬機器裡的個別資料來源
- 備份整台虛擬機器

備份整台虛擬機器是很熱門的方式，因為管理更容易，並可以協助您輕鬆還原整個應用程式和作業系統。Azure 備份可用於客體資料備份或完整的虛擬機器備份。

使用 Azure 備份為虛擬機器備份的商業利益為：

- 為您的虛擬機器進行自動化備份與復原工作流程
- 透過應用程式一致性的備份，確保資料是在一致的狀態下進行還原。
- 虛擬機器備份期間不需停機。
- Windows 或 Linux 虛擬機器皆可進行備份。
- 可使用 Azure 備份保存庫中的復原點輕鬆還原。
- 自動剪除及記憶體回收較舊的復原點。

## Azure 虛擬機器備份如何運作？
若要備份虛擬機器，首先需要資料的時間點快照集。Azure 備份服務在排定的時間初始備份作業，並觸發備份延伸模組以建立快照集。備份延伸模組與客體的 VSS 服務進行協調以達到一致性，達到一致性後將叫用 Azure 儲存體服務的 blob 快照集 API。這可以讓虛擬機器不需關機即可獲取一致性的磁碟快照集。

在擷取快照集之後，Azure 備份服務會將資料傳送到備份保存庫。服務會負責識別並傳送僅上次備份變更的區塊，讓備份儲存體更有效率。資料傳送完畢時將移除快照集，並建立復原點。Azure 管理入口網站中可以查看此復原點。

![Azure 虛擬機器備份架構](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Linux 虛擬機器只能進行檔案一致性的備份。

## 計算受保護的執行個體
使用 Azure 備份進行備份的 Azure 虛擬機器將受 [Azure 備份定價](http://azure.microsoft.com/pricing/details/backup/)拘束。受保護的執行個體是根據虛擬機器的*實際*大小計算，也就是不包含「資源磁碟」，虛擬機器裡所有資料的總和。我們*不會*以每個連接到虛擬機器可支援的資料磁碟之大小上限計費，而是根據實際儲存在資料磁碟的資料。同樣地，備份儲存體帳單也是根據 Azure 備份所儲存的資料計費，也就是每個復原點所儲存的實際資料總和。

例如，A2 標準型虛擬機器擁有兩個額外資料磁碟，每個大小上限為 1 TB。下表提供上述每個磁碟實際儲存的資料：

|磁碟類型|大小上限|實際儲存的資料|
|---------|--------|------|
| 作業系統磁碟 | 1023 GB | 17 GB |
| 本機磁碟/資源磁碟 | 135 GB | 5 GB (未包含備份) |
| 資料磁碟 1 |	1023 GB | 30GB |
| 資料磁碟 2 | 1023 GB | 0GB |

在此案例中，虛擬機器的*實際*容量為 17 GB + 30 GB + 0 GB = 47 GB。這將是受保護的執行個體大小，每月帳單的計費基礎。當虛擬機器的資料量增加時，用於計費之受保護的執行個體大小也會適當地變更。

完成第一次成功備份後才會計費。儲存體和受保護的執行個體也會在此同時開始計費。只要虛擬機器*使用 Azure 備份儲存任何的備份資料*，就會持續計費。如果保留備份資料，執行 [停止保護] 作業將不會停止計費。指定的虛擬機器只有在停止保護*和*刪除全部備份資料後才會停止計費。無使用中之備份工作時 (已停止保護)﹐受保護的執行個體大小將根據上一次成功備份時的虛擬機器大小，進行每月計費。

## 必要條件
### 1.備份保存庫
若要開始備份您的 Azure 虛擬機器，您必須先建立備份保存庫。保存庫是一實體，儲存隨著時間建立的所有備份和復原點。保存庫也包含備份虛擬機器時將套用的備份原則。

下圖顯示各種 Azure 備份實體之間的關係：![Azure 備份實體和其關聯性](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### 若要建立備份保存庫

1. 登入[管理入口網站](http://manage.windowsazure.com/)。

2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > [**快速建立**]。如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。每個 Azure 訂用帳戶皆能擁有多重備份保存庫，用以組合管理受保護的虛擬機器。

3. 在 [**名稱**] 中，輸入保存庫的易記識別名稱。每個訂用帳戶皆需為唯一名稱。

4. 在 [**區域**] 中，選取保存庫的地理區域。請注意，保存庫必須與您想要保護的虛擬機器位於相同區域。如果您的虛擬機器在不同區域，每個區域皆需建立保存庫。儲存備份資料不需指定儲存體帳戶，備份保存庫和 Azure 備份服務將自動處理。![建立備份保存庫](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]Azure 備份服務只支援選取區域的虛擬機器備份。[支援區域](http://azure.microsoft.com/regions/#services)的檢查清單。如果您尋找的區域目前不受支援，就不會於建立保存庫期間出現在下拉式清單中。

5. 按一下 [**建立保存庫**]。要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。![建立保存庫快顯通知](./media/backup-azure-vms-introduction/creating-vault.png)

6. 將有一則訊息確認保存庫已成功建立，並且該保存庫會在「復原服務」頁面中列為 [使用中] 狀態。![備份保存庫的清單](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. 按一下 [備份保存庫] 前往 [**快速入門**] 頁面，上面將會顯示備份 Azure 虛擬機器的指示。![虛擬機器的備分指示在 [儀表板] 頁面上](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]請確定建立保存庫之後，立即選擇適當的儲存體備援選項。進一步了解 [在備份保存庫中設定儲存體備援選項][vault-storage-redundancy]。

### 2.VM 代理程式
在您開始備份 Azure 虛擬機器之前，請先確定 Azure VM 代理程式已正確安裝在虛擬機器上。為了備份虛擬機器，Azure 備份服務將在 VM 代理程式上安裝延伸模組。由於 VM 代理程式在建立虛擬機器時為選擇性元件，您必須確定在佈建虛擬機器前，已選取 VM 代理程式的核取方塊。

深入了解 [VM 代理程式](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)和[如何安裝](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

>[AZURE.NOTE]如果您打算將您的虛擬機器從內部部署資料中心移轉至 Azure，請確定您已下載並安裝 VM 代理程式 MSI，再開始移轉程序。這也適用於 Azure 中使用 Azure Site Recovery 所保護的虛擬機器。

## 在預覽期間的限制

- 不支援具有 5 個以上磁碟的虛擬機器備份。
- 不支援使用進階儲存體的虛擬機器備份。
- 不支援使用多重 NIC 或正在負載平衡組態的虛擬機器備份。
- 不支援在還原期間取代現有的虛擬機器。先刪除現有的虛擬機器及任何相關聯的磁碟，然後從備份還原資料。
- 不支援使用 Azure Site Recovery 還原的虛擬機器備份。
- 不支援跨區域備份和還原。
- Azure 備份服務只支援選取區域的虛擬機器備份。[支援區域](http://azure.microsoft.com/regions/#services)的檢查清單。如果您尋找的區域目前不受支援，就不會於建立保存庫期間出現在下拉式清單中。
- 只有選取的作業系統版本能支援使用 Azure 備份服務為虛擬機器備份：
  - **Linux**：由 Azure 背書的散發套件清單可以在[這裡](../virtual-machines-linux-endorsed-distributions.md)取得。只要 VM 代理程式可以在虛擬機器上使用，其他「攜帶您自己的 Linux」散發套件也應該可以運作。
  - **Windows Server**：不支援比 Windows Server 2008 R2 更舊的版本。

如果您想要查看任何所包含功能，[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## 後續步驟
若要開始使用虛擬機器備份，了解如何：

- [探索、註冊及保護虛擬機器](backup-azure-vms.md)

- [還原虛擬機器](backup-azure-restore-vms.md)

+ 監視備份工作


 

<!---HONumber=62-->