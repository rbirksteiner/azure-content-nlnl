<properties
	pageTitle="Azure DPM 備份簡介 | Microsoft Azure"
	description="使用 Azure 備份服務來備份 DPM 伺服器的簡介"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Azure DPM 備份簡介

本文簡介如何使用 Microsoft Azure 備份來保護 System Center Data Protection Manager (DPM) 伺服器和工作負載。閱讀本文您將可了解：

- Azure DPM 伺服器備份的運作方式
- 使備份流暢的必要條件
- 遇到的一般錯誤以及如何排除
- 支援的案例

System Center DPM 會備份檔案和應用程式資料。備份到 DPM 的資料可以儲存在磁帶、磁碟上，或使用 Microsoft Azure 備份來備份到 Azure。DPM 與 Azure 備份的互動方式如下：

- **DPM 部署為實體伺服器或內部部署虛擬機器** — 如果 DPM 部署為實體伺服器或內部部署 HYPER-V 虛擬機器，除了備份到磁碟和磁帶上，您還可以將資料備份到 Azure 備份保存庫。
- **DPM 部署為 Azure 虛擬機器** — 從 System Center 2012 R2 Update 3 開始，DPM 可以部署為 Azure 虛擬機器。如果 DPM 部署為 Azure 虛擬機器，您可以將資料備份到連接至 DPM Azure 虛擬機器的 Azure 磁碟，或者您也可以將資料備份到 Azure 備份保存庫以卸載資料儲存體。

## 為何要備份 DPM 伺服器？

使用 Azure 備份來備份 DPM 伺服器的商業利益包括：

- 在內部部署 DPM 部署中，您可以使用 Azure 備份來替代長期的磁帶部署。
- 在 Azure 的 DPM 部署中，Azure 備份可讓您卸載 Azure 磁碟中的儲存體，進而透過將較舊的資料儲存在 Azure 備份上而將較新的資料儲存在磁碟上來進行擴充。

## DPM 伺服器備份的運作方式
若要備份虛擬機器，首先需要資料的時間點快照集。Azure 備份服務在排定的時間初始備份作業，並觸發備份延伸模組以建立快照集。備份延伸模組與客體的 VSS 服務進行協調以達到一致性，達到一致性後將叫用 Azure 儲存體服務的 blob 快照集 API。這可以讓虛擬機器不需關機即可獲取一致性的磁碟快照集。

在擷取快照集之後，Azure 備份服務會將資料傳送到備份保存庫。服務會負責識別上次備份後有所變更的區塊並只傳送這些區塊，讓備份儲存體和網路更有效率。資料傳送完畢時將移除快照集，並建立復原點。Azure 管理入口網站中可以查看此復原點。

>[AZURE.NOTE]Linux 虛擬機器只能進行檔案一致性的備份。

## 必要條件
如下所示讓 Azure 備份做好備份 DPM 資料的準備：

1. **建立備份保存庫** — 在 Azure 備份主控台中建立保存庫。
2. **下載保存庫認證** — 在 Azure 備份中，將您建立的管理憑證上傳到保存庫。
3. **安裝 Azure 備份代理程式並註冊伺服器** — 從 Azure 備份，在每一部 DPM 伺服器上安裝代理程式，並在備份保存庫中註冊 DPM 伺服器。

### 建立備份保存庫
若要開始備份您的 Azure 虛擬機器，您必須先建立備份保存庫。保存庫是一實體，儲存隨著時間建立的所有備份和復原點。保存庫也包含備份虛擬機器時將套用的備份原則。

1. 登入[管理入口網站](http://manage.windowsazure.com/)。
2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > [**快速建立**]。如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。每個 Azure 訂用帳戶皆能擁有多個備份保存庫，用以組織受保護的資料。
3. 在 [名稱] 中，輸入保存庫的易記識別名稱。每個訂用帳戶皆需為唯一名稱。
4. 在 [**區域**] 中，選取保存庫的地理區域。請注意，保存庫必須與您想要保護的虛擬機器位於相同區域。如果您的虛擬機器在不同區域，每個區域皆需建立保存庫。儲存備份資料不需指定儲存體帳戶，備份保存庫和 Azure 備份服務將自動處理。
    > [AZURE.NOTE] 「使用 Azure 備份服務進行的虛擬機器備份僅在部份地區支援。如果您正在尋找的區域現在不[支援](http://azure.microsoft.com/regions/#services)，則建立保存庫時不會出現在下拉式清單中。」

5. 在 [訂閱] 中，輸入要與備份保存庫搭配使用的 Azure 訂閱。
6. 按一下 [**建立保存庫**]。![建立備份保存庫](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。![建立保存庫快顯通知](./media/backup-azure-dpm-introduction/creating-vault.png)

    將有一則訊息確認保存庫已成功建立，並且該保存庫會在 [復原服務] 頁面中列為 [**使用中**] 狀態。

    ![備份保存庫的清單](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. 按一下備份保存庫以前往 [**快速入門**] 頁面，上面將會顯示 DPM 伺服器的備份指示。![虛擬機器的備分指示在 [儀表板] 頁面上](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]請確定建立保存庫之後，立即選擇適當的儲存體備援選項。進一步了解[在備份保存庫中設定儲存體備援選項](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options)。

### 下載保存庫認證
1. 按一下 [**復原服務**]，然後按一下備份保存庫。在 [**快速入門**] 頁面上，按一下 [**下載保存庫認證**] 以下載認證檔案，並儲存到安全的位置。您無法編輯認證，因此您不需要開啟位置。基於安全性理由，檔案中的金鑰會在 48 小時之後到期。

2. 將檔案複製到安全的位置，並且要可供您想要在 Azure 備份保存庫中註冊 DPM 伺服器輕鬆地存取。安裝 Azure 備份代理程式時會需要選取此檔案。

### 安裝 Azure 備份代理程式和註冊伺服器
您將下載代理程式安裝檔案，並在包含您想要備份之資料的每一部 DPM 伺服器上執行它。代理程式儲存在 **Azure 下載中心**，且有自己的安裝程序。當您執行安裝程式時，就會安裝代理程式並向保存庫註冊 DPM 伺服器。請注意：

- 要有 DPM 伺服器的系統管理權限才能安裝代理程式。
- 若要安裝在多部 DPM 伺服器上，您可以將安裝程式檔案放在共用網路資源上，或是使用群組原則或管理產品 (例如 System Center Configuration Manager) 來安裝代理程式。
- 安裝後不需要重新啟動 DPM 伺服器。

#### 安裝備份代理程式和註冊伺服器

1. 在 Azure 備份保存庫的 [**快速入門**] 頁面上，於 [**下載 Azure 備份代理程式**] 中選取 [**適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端**]。將應用程式下載到要用來執行應用程式的 DPM 伺服器。
2. 執行安裝程式檔案 **MARSAgentInstaller.exe**。接受服務條款，然後選取安裝任何遺漏的必備軟體。
3. 在 [**安裝設定**] 頁面上選取 [**安裝資料夾**] 和 [**快取位置**]。

    預設快取位置資料夾為 <system drive>:\Program Files\Azure Backup Agent。在快取位置中，安裝程序會在 **Azure Backup Agent** 資料夾建立名為 **Scratch** 的資料夾。快取位置必須至少有 2.5 GB (或將要備份至 Azure 之資料大小的 10%) 的可用空間。只有本機系統管理員和 Administrators 群組的成員可以存取快取目錄，以避免遭受阻絕服務攻擊。

4. 在 [**Proxy 組態**] 頁面上，設定代理程式用來連線到 Azure 的自訂 Proxy 設定。如果未設定任何設定，將會使用 DPM 伺服器上預設的網際網路存取設定。請注意，若您使用的 Proxy 伺服器需要驗證，則應該在此頁面上輸入詳細資料。
5. 在 [**選擇加入 Microsoft Update**] 頁面上，建議您啟用更新。如果伺服器已啟用自動更新，則會略過此步驟。請注意，Microsoft Update 設定適用於所有 Microsoft 產品的更新，而非只適用於 Azure 備份代理程式。
6. [**安裝**] 頁面隨即顯示。安裝過程中會檢查是否已安裝所需軟體，並完成安裝程式。安裝完成後，您會收到一則訊息，指出已成功安裝 Azure 備份代理程式。此時，您可以選擇檢查是否有更新。建議您允許進行更新檢查。
7. 按一下 [**前往註冊**]，在保存庫中註冊伺服器。
8. 在 [**保存庫識別**] 頁面中，選取您在 Azure 備份保存庫中產生的保存庫註冊檔案。
9. 在 [**加密設定**] 頁面中，指定複雜密碼的詳細資料，或自動產生複雜密碼。
10. 按一下 [產生複雜密碼後複製到剪貼簿]。您會收到一則訊息，指出您的複雜密碼已複製到剪貼簿。此時很適合開啟記事本，貼上剪貼簿中的複雜密碼並儲存檔案，同時再列印檔案然後將檔案保存起來。按一下 [註冊] 在備份保存庫中註冊 DPM 伺服器。

    > [AZURE.TIP] 在 [加密設定] 步驟中，請記得將複雜密碼複製到剪貼簿。
11. 按一下 [註冊]。

    完成註冊之後，DPM 主控台會顯示 Azure 備份的可用性。

    Azure 備份一律會以指定的或自動產生的複雜密碼 (英數字元字串) 自動加密位於來源的資料。
    >[AZURE.NOTE]Azure 備份永遠不會保存複雜密碼，若您遺失密碼，就無法還原或復原資料。強烈建議您將金鑰儲存到外部位置。

當您指定複雜密碼，並按一下 [**完成**]，代理程式需要幾秒鐘的時間來將實際執行伺服器註冊到備份保存庫。在保存庫中註冊完成後，就會立即出現 [**伺服器註冊**] 摘要頁面。

## 需求 (和限制)

- DPM 可以做為實體伺服器或 System Center 2012 SP1 或 System Center 2012 R2 上安裝的 HYPER-V 虛擬機器來執行。它也可以做為 System Center 2012 R2 (至少含 DPM 2012 R2 更新彙總套件 3) 上執行的 Azure 虛擬機器，或 System Center 2012 R2 (至少含更新彙總套件 5) 上執行之 VMWare 中的 Windows 虛擬機器來執行。
- 如果您使用 System Center 2012 SP1 來執行 DPM，則應該安裝 System Center Data Protection Manager SP1 的更新彙總套件 2。要有此軟體才能安裝 Azure 備份代理程式。
- DPM 伺服器應該已安裝 Windows PowerShell 和 .Net Framework 4.5。
- DPM 可以將大部分的工作負載備份至 Azure 備份。如需所支援項目的完整清單，請參閱下面的 Azure 備份支援項目。
- 使用 [複製到磁帶] 選項無法復原 Azure 備份中儲存的資料。
- 您需要已啟用 Azure 備份功能的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。請閱讀 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。
- 要使用 Azure 備份，就必須在您想要備份的伺服器上安裝 Azure 備份代理程式。每個伺服器至少必須有 2.5 GB 的本機可用儲存體可供快取位置使用，但建議準備 15 GB 的可用本機儲存體空間供快取位置使用。
- 資料會儲存在 Azure 保存庫儲存體中。可以備份至 Azure 備份保存庫的資料數量沒有限制，但是資料來源 (例如虛擬機器或資料庫) 的大小不應超過 1.65 TB。

下列檔案類型可支援備份至 Azure：

- 加密 (僅限完整備份)
- 壓縮 (支援增量備份)
- 疏鬆 (支援增量備份)
- 壓縮和疏鬆 (視為疏鬆來處理)

下列為不支援的類型：

- 不支援區分大小寫的檔案系統上的伺服器。
- 硬式連結 (略過)
- 重新分析點 (略過)
- 加密和壓縮 (略過)
- 加密和疏鬆 (略過)
- 壓縮資料流
- 疏鬆資料流

>[AZURE.NOTE]從 System Center 2012 DPM SP1 開始，您可以使用 Microsoft Azure 備份將受到 DPM 保護的工作負載備份至 Azure。

<!---HONumber=62-->