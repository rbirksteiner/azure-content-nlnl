<properties 
   pageTitle="StorSimple 元件有哪些？" 
   description="描述 StorSimple 裝置、服務與管理技術。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="06/11/2015"
   ms.author="v-sharos"/>


# StorSimple 元件有哪些？ 

歡迎使用 Microsoft Azure StorSimple，StorSimple 是一個整合式儲存體解決方案，可管理內部部署裝置與 Microsoft Azure 雲端儲存體之間的儲存體工作。StorSimple 的設計旨在降低儲存體成本、簡化儲存體管理、改善災害復原能力和效率，以及提供資料行動力。

下列章節將描述 Microsoft Azure StorSimple 元件，並說明解決方案如何排列資料、配置儲存體、加快儲存體管理速度以及資料保護。

> [AZURE.NOTE]發佈於 Microsoft Azure 網站上的 StorSimple 部署資訊僅適用於 StorSimple 8000 系列裝置。如需 7000 序列裝置的相關資訊，請移至：[StorSimple 說明](http://onlinehelp.storsimple.com/)。

## StorSimple 裝置

Microsoft Azure StorSimple 裝置是內部部署混合式儲存體陣列，可透過主要儲存體和 iSCSI 存取其中儲存的資料。它會管理雲端儲存體之間的通訊，並協助確保所有儲存在 Microsoft Azure StorSimple 方案的資料安全性和機密性。

StorSimple 裝置包括固態硬碟 (SSD) 和硬碟 (HDD)，並支援叢集和自動容錯移轉。它包含共用處理器、共用儲存體，以及兩個鏡像控制站。每個控制站都可提供下列功能：

- 連接到主機電腦
- 最多六個網路連接埠可連接到區域網路 (LAN)
- 硬體監控
- 即使電源中斷仍會保留資訊的靜態隨機存取記憶體 (NVRAM)
- 叢集感知更新可管理容錯移轉叢集中伺服器上的軟體更新，因此更新對服務可用性的影響會降到最低或不會有影響
- 叢集服務 (如後端叢集般運作) 可提供高可用性，並將 HDD 或 SSD 故障或離線時可能會發生的任何負面影響降到最低

不論在任何時間點，只會有一個作用中的控制器。如果作用中的控制器發生故障，則第二個控制站便會自動啟動。

如需詳細資訊，請參閱 [StorSimple 裝置](https://msdn.microsoft.com/library/azure/dn772363.aspx)。

## StorSimple 虛擬裝置

您可以使用 StorSimple 來建立可複寫實際混合式存放裝置之架構與功能的虛擬裝置。

StorSimple 虛擬裝置 (也稱為 StorSimple 虛擬應用裝置) 會在 Azure 虛擬機器中的單一節點上執行。(虛擬裝置只能建立在 Azure 虛擬機器上。您無法在 StorSimple 裝置或在內部部署伺服器上建立虛擬裝置)。 StorSimple 虛擬裝置與實體 StorSimple 裝置之間的差異如下：

- 虛擬裝置只有一個介面，而實體裝置則會有六個網路介面。 
- 您會在裝置設定期間 (而不是以個別工作的方式) 註冊虛擬裝置。
- 您無法在虛擬裝置中重新產生服務資料加密金鑰。金鑰變換期間，您會在實體裝置上重新產生金鑰，然後以新的金鑰更新虛擬裝置。
- 如果需要將更新套用到虛擬裝置，您將會遇到出現停機時間的問題。實體 StorSimple 裝置不會發生這個問題。

建議您在實體裝置無法使用的災害復原案例中使用 StorSimple 虛擬裝置，例如測試及小型試驗部署。

如需詳細資訊，請參閱 [StorSimple 虛擬裝置](https://msdn.microsoft.com/library/azure/dn772390.aspx)。


## 儲存體管理技術
 
除了專用的 StorSimple 裝置和虛擬裝置以外，Microsoft Azure StorSimple 還會使用下列軟體技術來提供資料的快速存取，並減少儲存體使用：

- 自動儲存體分層 
- 精簡佈建 
- 重複資料刪除和壓縮 

### 自動儲存體分層

Microsoft Azure StorSimple 會依據目前使用量、使用年限及與其他資料的關聯性，自動排列邏輯層中的資料。最常使用的資料會儲存在本機，而不常使用和非使用中資料會自動移轉至雲端。圖 1 說明此儲存方法。
 
![StorSimple 儲存層](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**圖 1：StorSimple 儲存體**

若要提供快速存取，StorSimple 會將經常使用的資料 (熱資料) 儲存在 StorSimple 裝置的 SSD 上。它會將偶爾使用的資料 (暖資料) 儲存在裝置的 HDD 上或儲存在資料中心的伺服器上。它會將非使用中的資料、備份資料，以及為封存或遵循法規而保留的資料移動至雲端。

StorSimple 會隨著使用模式變更而調整並重新排列資料和儲存體指派。例如，某些資訊在經過一段時間之後可能會變成不常使用。在資訊變成極少使用的情況下，系統會將它從 SSD 移轉至 HDD，然後移轉至雲端。如果相同資料再次變成使用中，系統會將它移轉回存放裝置。

### 精簡佈建

精簡佈建是一種虛擬化技術，精簡佈建中的可用儲存體會顯示超過實體資源。與其預先保留足夠的儲存空間，StorSimple 會使用精簡佈建來配置剛好符合目前需求的足夠空間。雲端儲存體的彈性本質正好支援這種方法，因為 StorSimple 可以增加或減少雲端儲存體以符合不斷變更的需求。

### 重複資料刪除和壓縮

Microsoft Azure StorSimple 會使用重複資料刪除和資料壓縮，來進一步降低儲存體需求。

重複資料刪除減少整體儲存資料量的方式是刪除儲存資料集中的重複資料。當資訊變更時，StorSimple 會忽略未變更的資料，而只擷取變更。此外，StorSimple 會透過識別並移除不必要資訊來減少儲存資料量。

## Windows PowerShell for StorSimple

Windows PowerShell for StorSimple 提供了可用來建立和管理 Microsoft Azure StorSimple 服務以及設定和監視 StorSimple 裝置的命令列介面。它會是 Windows PowerShell 型的命令列介面，其中包含可管理 StorSimple 裝置的專用 Cmdlet。Windows PowerShell for StorSimple 的功能可讓您：

- 註冊裝置。
- 在裝置上設定網路介面。
- 安裝特定類型的更新。
- 透過存取支援工作階段來疑難排解您的裝置。
- 變更裝置狀態。

您可以從序列主控台 (在與裝置直接連線的主機電腦上) 或使用 Windows PowerShell 遠端處理來遠端存取 Windows PowerShell for StorSimple。請注意，部分 Windows PowerShell for StorSimple 工作 (例如初始裝置註冊) 只能在序列主控台上執行。

如需詳細資訊，請參閱 [Windows PowerShell for StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx)。

## StorSimple Manager 服務

Microsoft Azure StorSimple 提供可讓您集中管理資料中心和雲端儲存體的 Web 架構使用者介面 (StorSimple Manager 服務)。您可以使用 StorSimple Manager 服務來執行下列工作：

- 進行 StorSimple 裝置的系統設定。
- 進行和管理 StorSimple 裝置的安全性設定。
- 設定雲端認證和屬性。
- 設定和管理伺服器上的磁碟區。
- 設定磁碟區群組。
- 備份和還原資料。
- 監視效能。
- 檢閱系統設定，並識別可能發生的問題。

您可以使用 StorSimple Manager 服務來執行所有管理工作，除了需要系統停機的工作 (例如初始安裝和更新安裝) 以外。

如需詳細資訊，請參閱 [StorSimple Manager 服務](https://msdn.microsoft.com/library/azure/dn772396.aspx)。

## StorSimple Snapshot Manager

StorSimple Snapshot Manager 是 Microsoft Management Console (MMC) 嵌入式管理單元，可用來建立本機和雲端資料的一致時間點備份複本。嵌入式管理單元會在 Windows Server 型的主機上執行。您可以使用 StorSimple Snapshot Manager 進行下列作業：

- 設定、備份及刪除磁碟區。
- 設定磁碟區群組，以確保是應用程式一致的備份資料。
- 管理備份原則，方便在預先決定的排程上備份資料並儲存在指定位置 (在本機或在雲端中)。
- 還原磁碟區和個別檔案。

系統會以快照集的方式擷取備份，快照集僅會記錄建立上次快照集之後所做的變更，而且需要的儲存空間遠少於完整備份。您可以建立備份排程或視需要進行立即備份。此外，您還可以使用 StorSimple Snapshot Manager 建立可控制要儲存多少快照集的保留原則。如果您之後需要將資料從備份還原，StorSimple Snapshot Manager 可讓您從本機或雲端快照集的類別中進行選取。

如果發生損毀，或如果因為其他原因而必須還原資料，StorSimple Snapshot Manager 會在需要時將它以累加方式還原。當您還原檔案、更換設備或將作業移到另一個站台時，資料還原不需要您將整個系統關閉。

如需詳細資訊，請參閱 [StorSimple Snapshot Manager](https://msdn.microsoft.com/library/azure/dn772365.aspx)。

## StorSimple Adapter for SharePoint

Microsoft Azure StorSimple 包括 StorSimple Adapter for SharePoint，其為可將 StorSimple 儲存體和資料保護功能明確延伸至 SharePoint 伺服器陣列的選擇性元件。將配接器與遠端 Blob 儲存體 (RBS) 提供者和 SQL Server RBS 功能搭配使用，可讓您將 Blob 移至由 Microsoft Azure StorSimple 系統備份的伺服器。Microsoft Azure StorSimple 接著可根據使用方式，在本機或在雲端中儲存 BLOB 資料。

您可以在 SharePoint 管理中心入口網站內管理 StorSimple Adapter for SharePoint。因此，SharePoint 管理仍然是集中式管理，而所有的儲存體會顯示在 SharePoint 伺服陣列中。

如需詳細資訊，請參閱 [StorSimple Adapter for SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx)。


## 後續步驟

檢閱 [StorSimple 版本資訊](https://msdn.microsoft.com/library/azure/dn772367.aspx)




 

<!---HONumber=July15_HO1-->