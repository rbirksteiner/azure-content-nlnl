<properties 
   pageTitle="什麼是 StorSimple？" 
   description="說明 StorSimple 功能和架構，並介紹 StorSimple 元件。" 
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
   ms.date="05/27/2015"
   ms.author="v-sharos@microsoft.com"/>

# 什麼是 StorSimple？ 

Microsoft Azure StorSimple 是一個有效率、符合成本效益且易管理的解決方案，可減少許多與企業儲存體和資料保護相關聯的問題和支出。它會使用專屬裝置 (Microsoft Azure StorSimple 裝置) 和整合式管理工具，提供所有企業儲存體 (包括雲端儲存體) 的整體檢視。

## 為何要使用 StorSimple？

Microsoft Azure StorSimple 提供下列優點：

- **透明整合** – Microsoft Azure StorSimple 使用 Internet Small Computer System Interface (iSCSI) 通訊協定，以無形的方式連結資料儲存設備。這可確保儲存在雲端、在資料中心或在遠端伺服器上的資料會看似儲存在單一位置。
- **降低儲存體成本** – Microsoft Azure StorSimple 會配置足夠符合目前需求的本機或雲端儲存體，且只在必要時才擴充雲端儲存體。它可以進一步地降低儲存體需求和支出，方法是消除相同資料的備援版本 (重複資料刪除)，並使用壓縮。
- **簡化儲存體管理** – Microsoft Azure StorSimple 提供系統管理工具，可用來設定和管理在內部部署、在遠端伺服器上和在雲端中儲存的資料。此外，您可以從 Microsoft Management Console (MMC) 嵌入式管理單元來管理備份和還原功能。StorSimple 提供一個單獨的選用介面，可用來將 StorSimple 管理和資料保護服務延伸到儲存在 SharePoint 伺服器上的內容。 
- **改進災害復原和提高合規性** – Microsoft Azure StorSimple 不需要延長的回復時間。相反地，它會在需要時還原資料。這表示正常作業可以在最少干擾的情況下繼續執行。此外，您還可以設定原則以指定備份排程和資料保留。
- **資料行動性** – 您可以從其他站台存取上傳至 Microsoft Azure 雲端服務的資料，以供復原和移轉使用。此外，您可以使用 StorSimple，在 Microsoft Azure 中執行的虛擬機器 (VM) 上設定 StorSimple 虛擬裝置。VM 然後可以使用虛擬裝置來存取儲存的資料，以供測試或復原使用。 

下圖提供 Microsoft Azure StorSimple 解決方案的高階檢視。

![StorSimple 架構](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple 架構**

## StorSimple 元件

Microsoft Azure StorSimple 解決方案包括下列元件：

- **Microsoft Azure StorSimple 裝置** - 包含固態硬碟 (SSD) 和硬碟 (HDD) 的內部部署混合式存放裝置陣列，提供備援控制器和自動容錯移轉功能。控制器可管理儲存體分層、將目前使用的 (或熱) 資料放在本機儲存體 (在裝置或在內部部署伺服器中)，而將不常使用的資料移到雲端。
- **StorSimple 虛擬裝置** – 也就是 StorSimple 虛擬應用裝置，這是 StorSimple 裝置的軟體版本，可複寫實體混合式存放裝置的架構和功能。StorSimple 虛擬裝置會在 Azure 虛擬機器中的單一節點上執行。虛擬裝置適用於測試和小型試驗案例。您無法在 StorSimple 裝置或在內部部署伺服器上建立 StorSimple 虛擬裝置。
- **Windows PowerShell for StorSimple** – 可讓您管理 StorSimple 裝置的命令列介面。Windows PowerShell for StorSimple 的功能包括：可讓您註冊您的 StorSimple 裝置、在您的裝置上設定網路介面、安裝特定類型的更新，以及透過存取支援工作階段及變更裝置狀態來疑難排解您的裝置。藉由連接至序列主控台或使用 Windows PowerShell 遠端處理，您可以存取 Windows PowerShell for StorSimple。
- **Azure PowerShell StorSimple Cmdlet** – 一組 Windows PowerShell Cmdlet，可讓您從命令列將服務層級和移轉工作自動化。如需適用於 StorSimple 的 Azure PowerShell Cmdlet 的詳細資訊，請移至 [Cmdlet 參考](https://msdn.microsoft.com/library/dn920427.aspx)。
- **StorSimple Manager 服務** – Azure 管理入口網站的延伸模組，可讓您從單一 Web 介面管理 StorSimple 裝置或 StorSimple 虛擬裝置。您可以使用 StorSimple Manager 服務來建立和管理服務、檢視和管理裝置、檢視警示、管理磁碟區，以及檢視和管理備份原則與備份類別。
- **StorSimple Snapshot Manager** – MMC 嵌入式管理單元，可使用磁碟區群組和 Windows 磁碟區陰影複製服務產生應用程式一致備份。此外，您可以使用 StorSimple Snapshot Manager 建立備份排程及複製或還原磁碟區。 
- **StorSimple Adapter for SharePoint** – 此工具可將 Microsoft Azure StorSimple 儲存體和資料保護明確延伸至 SharePoint 伺服器陣列，並可讓您從 SharePoint 管理入口網站檢視和管理 StorSimple 儲存體。

## 後續步驟

請閱讀 [StorSimple 元件](https://technet.microsoft.com/library/cc754482.aspx)並檢閱 [StorSimple 版本資訊](https://msdn.microsoft.com/library/azure/dn772367.aspx)



 

<!---HONumber=July15_HO1-->