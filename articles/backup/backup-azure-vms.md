<properties
	pageTitle="Azure 虛擬機器備份 - 備份"
	description="了解如何在註冊之後備份 Azure 虛擬機器"
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
	ms.topic="hero-article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>


# 備份 Azure 虛擬機器

本文是備份 Azure 虛擬機器的基本指南。繼續之前，請確定已符合所有[必要條件](backup-azure-vms-introduction.md#prerequisites)。

備份 Azure 虛擬機器需要三個主要步驟：

![備份 Azure 虛擬機器的三個步驟](./media/backup-azure-vms/3-steps-for-backup.png)

## 探索 Azure 虛擬機器
探索程序會在 Azure 中查詢訂用帳戶中的虛擬機器清單，以及其他資訊，例如雲端服務名稱、地區等。

> [AZURE.NOTE]第一個步驟一定都是執行探索程序。這是為了確保識別任何加入至訂用帳戶的新虛擬機器。

若要觸發探索程序，請執行下列步驟：

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。

2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [**選取**] 按鈕。![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下頁面底部的 [**探索**] 按鈕。![探索按鈕](./media/backup-azure-vms/discover-button.png)

4. 在列表顯示虛擬機器時，探索程序可能會執行幾分鐘。探索程序執行時，畫面底部會出現快顯通知。![探索 VM](./media/backup-azure-vms/discovering-vms.png)

5. 探索程序完成時會出現快顯通知。![探索完成](./media/backup-azure-vms/discovery-complete.png)


## 註冊 Azure 虛擬機器
虛擬機器必須先向 Azure 備份服務註冊，才能受到保護。註冊程序有兩個主要目標：

1. 將備份擴充功能外掛到虛擬機器中的 VM 代理程式

2. 將虛擬機器與 Azure 備份服務相關聯。

註冊通常是一次性活動。Azure 備份服務會自動處理備份擴充功能的升級和修補，完全不會干擾使用者。如此可減輕備份產品經常對使用者造成的「代理程式管理負擔」。

### 註冊虛擬機器

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。

2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [選取] 按鈕。![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下頁面底部的 [**註冊**] 按鈕。![註冊按鈕](./media/backup-azure-vms/register-button.png)

4. 在 [**註冊的項目**] 快顯功能表中，選擇您想要註冊的虛擬機器。如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。

    **註冊**作業可以大規模進行，也就是可以一次選取多個要註冊的虛擬機器。如此可大幅減少為了準備虛擬機器來備份而投入的一次性工作量。

    >[AZURE.NOTE]只會顯示未註冊且與備份保存庫位於相同區域的虛擬機器。

5. 每一個應該註冊的虛擬機器都會建立一項工作。快顯通知會顯示此活動的狀態。按一下 [**檢視工作**] 以移至 [**工作**] 頁面。![註冊工作](./media/backup-azure-vms/register-create-job.png)

6. 虛擬機器也會出現在註冊的項目清單中，還會顯示註冊作業的狀態。![註冊狀態 1](./media/backup-azure-vms/register-status01.png)

7. 作業完成時，入口網站中的狀態會改變來反映已註冊的狀態。![註冊狀態 2](./media/backup-azure-vms/register-status02.png)

## 備份 Azure 虛擬機器
此步驟涉及到設定虛擬機器的備份和保留原則。若要保護虛擬機器，請執行下列步驟：

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。
2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [**選取**] 按鈕。![在入口網站中選取工作負載](./media/backup-azure-vms/select-workload.png)

3. 按一下頁面底部的 [**保護**] 按鈕。

4. 這樣會開啟 [**保護項目**] 精靈，供您選取要保護的虛擬機器。如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。

    **保護**作業可以大規模進行，也就是可以一次選取多個要註冊的虛擬機器。如此可大幅減少為了保護虛擬機器而投入的工作量。

    >[AZURE.NOTE]這裡只會顯示已向 Azure 備份服務正確註冊且與備份保存庫位於相同區域的虛擬機器。

5. 在 [**保護項目**] 精靈的第二個畫面，選擇備份和保留原則來備份已選取的虛擬機器。從現有的一組原則中挑選，或定義新的原則。

    >[AZURE.NOTE]在預覽版本中，支援最多 30 天的保留期和每天最多備份一次。

    在每個備份保存庫中，您可以有多個備份原則。原則反映應該如何排程和保留備份的詳細資料。例如，一個備份原則可能是每天上午 10:00 備份，另一個備份原則可能是每週上午 6:00 備份。多個備份原則可讓虛擬機器基礎結構在排定備份時更有彈性。

    每一個備份原則可以有多個相關聯的虛擬機器。無論何時，虛擬機器只能與一個原則相關聯。

6. 每個虛擬機器會建立一項工作，以設定保護原則並將虛擬機器與原則相關聯。按一下 [**工作**] 索引標籤，選擇正確的篩選器來檢視 [**設定保護**] 工作清單。![設定保護工作](./media/backup-azure-vms/protect-configureprotection.png)

7. 完成之後，虛擬機器就受到原則保護，必須等到排程的備份時間讓初始備份完成為止。現在，虛擬機器出現在 [**受保護的項目**] 索引標籤下，且 [保護狀態] 為 [*受保護*] (擱置中的初始備份)。
    >[AZURE.NOTE]目前還無法在設定保護之後立即啟動初始備份。

8. 在排程時間，Azure 備份服務會為每個需要備份的虛擬機器建立備份工作。按一下 [**工作**] 索引標籤來檢視 [**備份**] 工作清單。在備份工作進行時，Azure 備份服務會發出命令給每個虛擬機器中的備份擴充功能，以排清所有寫入並取得一致的快照。![備份進行中](./media/backup-azure-vms/protect-inprogress.png)

9. 完成之後，[**受保護的項目**] 索引標籤中的虛擬機器 [保護狀態] 會顯示成 [*受保護*]。![搭配復原點備份虛擬機器](./media/backup-azure-vms/protect-backedupvm.png)

## 檢視備份狀態和詳細資料
虛擬機器受到保護後，[**儀表板**] 頁面摘要中的虛擬機器計數也會遞增。此外，[儀表板] 頁面還會顯示過去 24 小時內成功、失敗和仍在進行中的工作數目。按一下任何一個類別可在 [**工作**] 頁面中深入查看該類別。![儀表板頁面中的備份狀態](./media/backup-azure-vms/dashboard-protectedvms.png)

## 錯誤疑難排解
您可以疑難排解將 Azure 備份使用於下表所列資訊時發生的錯誤。

| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 探索 | 無法探索新的項目 - Microsoft Azure 備份發生內部錯誤。等候幾分鐘的時間，然後再次嘗試操作。 | 在 15 分鐘之後重試探索程式。
| 探索 | 無法探索新的項目 – 另一個探索作業正在進行中。請等待目前的探索作業完成。 | None |
| 註冊 | Azure VM 角色未處於可安裝擴充功能的狀態 - 請檢查 VM 是否處於「執行中」狀態。Azure 探索服務擴充功能需要 VM 處於「執行中」。 | 啟動虛擬機器，當它處於「執行中」狀態時，重試註冊作業。|
| 註冊 | 連接至虛擬機器的資料磁碟數目超過支援的限制 - 請卸離此虛擬機器上的某些資料磁碟，然後重試作業。Azure 備份最多支援 5 個連接至 Azure 虛擬機器的資料磁碟以供備份。 | None |
| 註冊 | Microsoft Azure 備份遇到內部錯誤 - 等候幾分鐘的時間，然後再次嘗試操作。如果問題持續發生，請連絡 Microsoft 支援服務。 | 您可能會因為不支援下列其中一個組態而發生此錯誤：<ol><li>Premium LRS <li>多重 NIC <li>負載平衡器 </ol> |
| 註冊 | 找不到 VM 客體代理程式憑證 | 請遵循下列指示以解決此錯誤：<ol><li>從 [這裡](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)下載最新版的 VM 代理程式。請確定下載的代理程式版本為 2.6.1198.718 或更高版本。<li>在虛擬機器中安裝 VM 代理程式。</ol> [學習](#validating-vm-agent-installation)如何檢查 VM 代理程式的版本。 |
| 註冊 | 因為安裝代理程式作業逾時而註冊失敗 | 請檢查是否支援虛擬機器的作業系統版本。 |
| 註冊 | 命令執行失敗 - 另一項作業正在此項目上進行。請等到前一項作業完成 | None |
| 備份 | 從備份保存庫複製 VHD 已逾時 - 請在幾分鐘內重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務。 | 要複製的資料太多時會發生此問題。請檢查您擁有的資料磁碟是否小於 6 個。 |
| 備份 | 快照 VM 子工作已逾時 - 請在幾分鐘內重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務。 | 如果 VM 代理程式發生問題，或以某種方式封鎖對 Azure 基礎結構的網路存取，則會擲回這個錯誤。<ul><li>深入了解 [VM 代理程式問題偵錯](#Troubleshooting-vm-agent-related-issues) <li>深入了解 [網路問題偵錯](#troubleshooting-networking-issues) </ul> |
| 備份 | 備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務。 | 發生此錯誤的原因有 2 個：<ol><li> 要複製的資料太多。請檢查您擁有的磁碟是否小於 6 個。<li>已刪除原始的 VM，因此無法進行備份。若要保留已刪除 VM 的備份資料但阻止備份錯誤，請取消保護 VM 並選擇保留資料。這樣會停止備份排程以及週期性的錯誤訊息。 |
| 備份 | 無法在選取的項目上安裝 Azure 復原服務延伸模組 - VM 代理程式是 Azure 復原服務延伸模組的必要條件。請安裝 Azure VM 代理程式並重新啟動註冊作業 | <ol> <li>檢查是否已正確安裝 VM 代理程式。<li>確定已正確設定 VM 組態中的旗標。</ol> [深入了解](#validating-vm-agent-installation) VM 代理程式安裝，以及如何驗證 VM 代理程式安裝。 |
| 備份 | 命令執行失敗 - 另一項作業目前正在此項目上進行。請等到前一項作業完成，然後重試 | VM 的現有備份或還原作業正在執行中，而當現有作業正在執行時，無法啟動新的作業。<br><br>如果您希望此選項可取消進行中的作業，請在 [Azure 意見反應論壇](http://feedback.azure.com/forums/258995-azure-backup-and-scdpm/suggestions/7941501-add-feature-to-allow-cancellation-of-backup-restor)上投票。 |

### 疑難排解 VM 代理程式相關問題

#### 設定 VM 代理程式
一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。不過，從內部部署資料中心移轉的虛擬機器不會安裝 VM 代理程式。對於這類 VM，必須明確安裝 VM 代理程式。深入了解[在現有 VM 上安裝 VM 代理程式](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)。

若為 Windows VM：

- 下載並安裝[代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。您需要有系統管理員權限，才能完成安裝。
- [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)，表示已安裝代理程式。


#### 更新 VM 代理程式
更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。不過，您需要確定在更新 VM 代理程式時，沒有任何執行中的備份作業。


#### 驗證 VM 代理程式安裝
如何檢查 Windows VM 上的 VM 代理程式版本：

- 登入 Azure 虛擬機器並巡覽至 *C:\WindowsAzure\Packages* 資料夾。
- 您應該會發現 WaAppAgent.exe 檔案已存在。
- 以滑鼠右鍵按一下檔案，移至 [**屬性**]，然後選取 [**詳細資料**] 索引標籤。
- [產品版本] 欄位應為 2.6.1198.718 或更高版本

### 疑難排解網路問題
如同所有的延伸模組，備份延伸模組需要存取公用網際網路才能運作。沒有公用網際網路的存取權可能會以各種方式顯現：

- 延伸模組安裝可能會失敗
- 備份作業 (如磁碟快照) 可能會失敗
- 顯示備份作業狀態可能會失敗

解析網際網路的公用位址的需求已在[這裡](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)說明。您必須檢查 VNET 的 DNS 設定，並確定可以解析 Azure URI。

正確完成名稱解析後，也必須提供 Azure IP 的存取權。若要解除封鎖對 Azure 基礎結構的存取，請遵循下列步驟：

1. 取得要列入允許清單的 [Azure 資料中心 IP](https://msdn.microsoft.com/library/azure/dn175718.aspx)。
2. 使用 [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) Cmdlet 解除封鎖 IP。在 Azure VM 中提升權限的 PowerShell 視窗中執行這個 Cmdlet (以系統管理員身分執行)。


## 復原點的一致性
處理備份資料時，客戶會擔心 VM 還原之後的行為。客戶常見的問題包括：

- 虛擬機器會啟動嗎？
- 資料會存在磁碟上嗎？會遺失任何資料嗎？
- 應用程式能夠讀取資料嗎？資料會損毀嗎？
- 應用程式還可辨識資料嗎？應用程式讀取的資料仍保有自我一致性嗎？

下表說明 Azure VM 備份和還原期間發生的一致性類型：

| 一致性 | VSS 型 | 說明和詳細資料 |
|-------------|-----------|---------|
| 應用程式一致性 | 是 | 這是 Microsoft 工作負載的理想位置，因為可確保：<ol><li>VM *啟動*、<li>*不會損毀*、<li>不會*遺失資料*，以及<li>對於使用資料的應用程式，資料維持一致，亦即備份時將應用程式納入考量 - 使用 VSS。</ol> 磁碟區快照服務 (VSS) 確保資料正確寫入儲存體。大部分 Microsoft 工作負載都有 VSS 寫入器，負責執行與資料一致性有關的工作負載特定動作。例如，Microsoft SQL Server 的 VSS 寫入器可確保正確寫入交易記錄檔和資料庫。<br><br> 對於 Azure VM 備份，取得應用程式一致復原點表示備份擴充功能可以叫用 VSS 工作流程，並在取得 VM 快照之前*正確*完成。當然，這表示也都已叫用 Azure VM 中所有應用程式的 VSS 寫入器。<br><br>了解 [VSS 的基本知識](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx)深入[運作方式](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)的詳細資料。 |
| 檔案系統一致性 | 是 - 適用於 Windows 電腦 | 有兩種情況，復原點具有檔案系統一致性：<ul><li>在 Azure 中備份 Linux VM，因為 Linux 沒有相當於 VSS 的平台。<li>在 Azure 中備份 Windows VM 期間 VSS 失敗。</li></ul> 在這兩種情況下，最佳做法是確保：<ol><li>VM *啟動*、<li>不會*損毀*和<li>不會*遺失資料*。</ol> 應用程式需要在還原的資料上實作自己的「修正」機制。|
| 損毀一致性 | 否 | 此狀況相當於電腦「當機」(經由軟體重設或硬體重設)。無法保證儲存媒體上的資料一致性。只有備份時已存在磁碟上的資料才會擷取並備份。<ol><li>在大多數情況下，OS 會啟動，但不保證一定如此。<li>這通常會接著進行磁碟檢查程序，例如 chkdsk，以檢查任何損毀錯誤。<li> 記憶體中任何未完全排清到磁碟的資料或寫入將會遺失。<li> 如果需要進行資料復原，應用程式通常會接著進行其本身的驗證機制。</ol>對於 Azure VM 備份，取得損毀一致復原點表示 Azure 備份不保證儲存體上的資料一致性 - 無論從 OS 還是應用程式的觀點來說都一樣。這通常發生在備份時 Azure VM 關閉。<br><br>例如，如果交易記錄中有項目不存在資料庫中，則資料庫軟體會執行復原，直到資料變成一致為止。在處理跨多個虛擬磁碟的資料時 (例如跨距磁碟區)，損毀一致復原點不保證資料的正確性。|

## 後續步驟
若要深入了解開始使用 Azure 備份，請參閱：

- [還原虛擬機器](backup-azure-restore-vms.md)
- [管理虛擬機器](backup-azure-manage-vms)
 

<!---HONumber=62-->