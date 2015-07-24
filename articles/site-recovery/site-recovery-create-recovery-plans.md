<properties
	pageTitle="建立復原方案" 
	description="Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉及復原協調至 Azure 或次要資料中心。" 
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
	ms.date="05/14/2015" 
	ms.author="raynew"/>

# 建立復原方案

Site Recovery 服務提供健全的商務持續性和災害復原 (BCDR) 解決方案，可協調及自動化複寫並容錯移轉至 Azure，或是次要的內部部署資料中心，藉以保護您的內部部署實體伺服器和虛擬機器。如需 Site Recovery 部署案例的簡介，請參閱 [Site Recovery 概觀](site-recovery-overview.md)。

## 本文內容

本文提供建立和自訂復原方案的相關資訊。

如果您在閱讀本文後有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 概觀

復原方案包含一或多個已排序的群組，其中含有受保護的虛擬機器或複寫群組 (用於 SAN 複寫)。機器會根據所屬的群組執行容錯移轉。特定群組中的虛擬機器會以平行方式執行容錯移轉。復原方案可執行下列作業：

- 定義容錯移轉及同時啟動的機器群組。
- 將機器分到同一個復原方案群組，藉以建立機器間的相依性。例如，若要執行容錯移轉，並帶出特定的應用程式，應將該應用程式的虛擬機器分到相同的復原方案群組。
- 自動化並延伸容錯移轉。您可以透過復原方案執行測試，以及執行規劃或未規劃的容錯移轉。您可以使用指令碼、Azure 自動化和手動動作來自訂復原方案。

復原方案會顯示在 Site Recovery 入口網站的「復原方案」上。


## 建立復原方案

建立復原方案的方法取決於您的 Site Recovery 部署方式。

- Hyper-V 複寫 (VMM) — 如果使用 Hyper-V 複寫，從 VMM 站台複寫至次要的內部部署站台或 Azure，應將 VMM 雲端中受保護的 Hyper-V 虛擬機器新增至復原方案。
- Hyper-V 複寫 (Hyper-V 站台) — 如果從 Hyper-V 站台 (不含 VMM 伺服器) 複寫至 Azure，應將保護群組中受保護的 Hyper-V 虛擬機器新增至復原方案。
- SAN 複寫 — 如果使用 SAN 複寫，複寫至次要的內部部署站台，應將含有虛擬機器的複寫群組新增至復原方案。您應選取複寫群組，而不是特定的虛擬機器，因為複寫群組中的所有虛擬機器必須同時執行容錯移轉 (容錯移轉會先從儲存層開始)。
- VMware 複寫 — 如果將 VMware 虛擬機器複寫至 Azure，應將含有虛擬機器的複寫群組新增至復原方案。

建立復原方案，如下所示：

1. 在 [復原方案] 索引標籤上，按一下 [建立復原方案]。指定復原方案的名稱，以及來源和目標伺服器。來源伺服器必須有已啟用容錯移轉和復原功能的虛擬機器。

	- 如果從 VMM 複寫至 VMM，請選取 [來源類型] 中的 [VMM]，以及來源和目標 VMM 伺服器。按一下 [Hyper-V]，查看設為使用 Hyper-V 複本的雲端。 
	- 如果使用 SAN 從 VMM 複寫至 VMM，請選取 [來源類型] 中的 [VMM]，以及來源和目標 VMM 伺服器。按一下 [SAN]，查看設定用於 SAN 複寫的雲端。
	- 如果從 VMM 複寫至 Azure，請選取 [來源類型] 中的 [VMM]。選取來源 VMM 伺服器及 [Azure] 做為目標。
	- 如果從 Hyper-V 站台複寫，請選取 [來源類型] 中的 Hyper-V 站台。選取該站台做為來源，並選取 [Azure] 做為目標。- 如果從 VMware 或實體內部部署伺服器複寫至 Azure，請選取組態伺服器做為來源，並選取 [Azure] 做為目標

2.在 [選取虛擬機器] 中，選取您要在復原方案中新增至預設群組 (Group 1) 的虛擬機器 (或複寫群組)。

## 自訂復原方案

將受保護的虛擬機器或複寫群組新增至預設的復原方案群組，並且建立方案後，即可開始自訂：

- 新增群組 — 您可以新增額外的復原方案群組。新增的群組會依您加入的順序加以編號。最多可新增七個群組。您可以在這些新群組中加入更多機器或複寫群組。請注意，虛擬機器或複寫群組只能包含在一個復原方案群組中。
- ****新增指令碼 — 您可以新增指令碼，在建立復原方案群組之前或之後執行。新增指令碼後，群組中會加入新的動作集。例如，系統會為 Group 1 建立一組前置步驟，並取名為 Group 1: Pre-steps。所有前置步驟都會列於此動作集中。請注意，若您已部署 VMM 伺服器，則只能在主要站台上新增指令碼。- 新增手動動作 — 您可以新增手動動作，在建立復原方案群組之前或之後執行。執行復原方案時，會在您插入手動動作的位置停止，然後出現對話方塊，提示您指定手動動作已完成。

### 使用指令碼擴充復原方案

您可以將指令碼新增至復原方案：

如果您有 VMM 來源站台，可在 VMM 伺服器上建立指令碼，並將其納入您的復原方案。如果複寫至 Azure，您可以將 Azure 自動化 Runbook 整合至您的復原方案

#### 建立 VMM 指令碼

開始之前，請注意下列事項：

- 請使用 Windows PowerShell 撰寫指令碼。
- 會在 Windows PowerShell 模組中傳遞 VMM Cmdlet。安裝 VMM 主控台時，會一併安裝 VMM Windows PowerShell 模組。系統可能使用指令碼的下列命令將 VMM 模組載入您的指令碼：Import-Module -Name virtualmachinemanager。[取得詳細資訊](hhttps://technet.microsoft.com/library/hh875013.aspx)。
- 確認您的 VMM 部署中至少有一部程式庫伺服器。根據預設，VMM 伺服器的程式庫共用路徑位於本機的 VMM 伺服器上，資料夾名稱為 MSCVMMLibrary。
- 如果您的程式庫共用路徑位於遠端 (或是位於本機上，但未與 MSCVMMLibrary 共用)，請依以下所示設為共用 (以 \\libserver2.contoso.com\\share\\ 為例)：
	- 開啟 [登錄編輯器]。
	- 瀏覽至 HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft System Center Virtual Machine Manager Server\\DRAdapter\\Registration。
	- 編輯 ScriptLibraryPath 的值。
	- 輸入值 \\libserver2.contoso.com\\share。指定完整的 FQDN。
	- 提供共用位置的權限。

- 復原方案中的指令碼會依據 VMM 服務帳戶中的內容執行。請確認此帳戶具有指令碼所處位置之遠端共用讀取權限，並在 VMM 服務帳戶的權限層級測試要執行的指令碼。
- 	請確認您用來測試指令碼的使用者帳戶與 VMM 服務帳戶具有相同權限，以確保獨立測試的指令碼與其在復原方案中執行的方式相同。
- 	在 VMM 伺服器上，設定要略過的執行原則，如下所示：
	- 以較高的權限開啟 64 位元 Windows PowerShell 主控台。
	- 類型：**Set-executionpolicy bypass**。[取得詳細資訊](https://technet.microsoft.com/library/ee176961.aspx)。
- 請確認您使用的是 try catch 區塊，以便適當地處理例外狀況。如果在指令碼中出現例外狀況，指令碼會隨即停止執行，而工作也會顯示為失敗。一旦發生錯誤，指令碼的剩餘部分將不會執行。如果在執行未規劃的容錯移轉時發生這種情況，復原方案將會繼續執行。如果在執行規劃的容錯移轉時發生這種情況，復原方案將會停止執行。若發生這種情況，請修正指令碼，確認其能如預期執行，然後再次執行復原方案。
- Write-Host 命令不會在復原方案指令碼中正常運作，指令碼將會失敗。如果要建立輸出，請建立 Proxy 指令碼，使其依序執行主要指令碼，並確保全部使用 >> 命令經由管道輸出。
- 如果指令碼未在 600 秒內傳回則會逾時。
- 如果有任何資料寫入至 STDERR，則該指令碼將分類為失敗。此資訊會顯示在指令碼的執行詳細資料中。

建立指令碼，如下所示：

1. 在程式庫共用中建立新的資料夾，例如 <VMMServerName>\\MSSCVMMLibrary\\RPScripts。將資料夾放在來源和目標 VMM 伺服器上。
2. 建立指令碼 (例如 RPScript)，並檢查其能否如預期運作。
3. 將指令碼放在來源和目標 VMM 伺服器的 <VMMServerName>\\MSSCVMMLibrary 位置。

#### 建立 Azure 自動化 Runbook

您可以將 Azure 自動化 Runbook 納入方案中執行，藉以擴充您的復原方案。[閱讀更多資訊](site-recovery-runbook-automation.md)。


### 將自訂設定新增至復原方案

1. 開啟您要自訂的復原方案。
2. 按一下以新增虛擬機器或新的群組。
3. 若要新增指令碼或手動動作，請按一下 [步驟] 清單中的任意項目，然後按一下 [指令碼] 或 [手動動作]。指定要在已選取項目之前或之後新增指令碼或動作。使用 [上移] 和 [下移] 命令按鈕，上下移動指令碼的位置。
4. 如果是新增 VMM 指令碼，請選取 [容錯移轉至 VMM 指令碼]，然後在 [指令碼路徑] 中輸入共用的相對路徑。這樣一來，範例的共用位置為 \<VMMServerName>\\MSSCVMMLibrary\\RPScripts，指定路徑：\\RPScripts\\RPScript.PS1。
5. 如果是新增 Azure 自動化 Runbook，請指定 Runbook 所在的 [Azure 自動化帳戶]，並選取適當的 [Azure Runbook 指令碼]。
5. 執行復原方案容錯移轉，以確保指令碼可以正常運作。


## 執行容錯移轉

您可以執行不同類型的容錯移轉復原方案，包括可檢查環境的容錯移轉測試，以及規劃或未規劃的容錯移轉。您可以在[這裡](site-recovery-failover.md)進一步了解容錯移轉，並取得有關執行不同類型容錯移轉的指示。


 

<!---HONumber=July15_HO2-->