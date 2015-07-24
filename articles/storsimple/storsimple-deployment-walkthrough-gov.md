<properties 
   pageTitle="在 Government 入口網站中部署您的內部部署 StorSimple 裝置"
   description="在 Azure Government 入口網站中部署 StorSimple Update 1 裝置和服務的步驟與最佳作法。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/12/2015"
   ms.author="v-sharos" />

# 在 Government 入口網站中部署您的內部部署 StorSimple 裝置

## 概觀

歡迎使用 Microsoft Azure StorSimple 裝置部署。

這些部署教學課程適用於 Azure Government 入口網站中執行 Update 1 軟體的 StorSimple 8000 系列。

這一系列的教學課程說明如何設定 StorSimple 裝置，並包含預先安裝檢查清單、設定必要條件以及詳細的設定步驟。

> [AZURE.NOTE]Microsoft Azure 網站上與 MSDN Library 中發行的 StorSimple 部署資訊只適用 StorSimple 8000 系列裝置。如需 7000 系列裝置的完整資訊，請移至： [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。如需 7000 系列部署資訊，請參閱 [StorSimple 系統快速入門指南](http://onlinehelp.storsimple.com/111_Appliance/)。

這些教學課程中的資訊均假設您已經檢閱安全性預防措施與設定檢查清單，並已打開 StorSimple 裝置包裝、裝上機架並接好纜線。如果您仍然需要執行這些工作，請適當地移至[安全性預防措施](https://msdn.microsoft.com/library/azure/dn772366.aspx)、[設定檢查清單](https://msdn.microsoft.com/library/azure/dn757787.aspx)以及[裝置硬體安裝](https://msdn.microsoft.com/library/azure/dn772375.aspx)。

您必須需要有系統管理員權限，才能完成安裝和設定程序。建議您開始之前，請檢閱預先安裝檢查清單。部署與設定程序可能需要一些時間才能完成。

## 預先安裝檢查清單

下列預先安裝檢查清單描述您在設定 StorSimple 裝置上的軟體之前需要收集的資訊。事先備妥此資訊可協助簡化在環境中部署 StorSimple 裝置的程序。

| | 需求 | 詳細資料 | 值 |
|---| --------------------- | ---------------------- | ------------- |
| 1 | 網路設定 <ol><li>網路介面、4x1 GbE、2x10 GbE</li><li>固定控制器 IP</li><li>子網路遮罩</li><li>閘道</li></ol> | 需要的 IP 總計：8 個<ol><li>每個啟用的網路介面一個，共 6 個</li><li>每個控制站一個，共 2 個，用於連線至網際網路進行服務更新</li><li>每個 IP 位址一個</li><li>每個裝置一個</li></ol> | |
| 2 | 序列存取 | 初始裝置組態 | 是/否 |
| 3 | DNS 伺服器 IP 位址 | 需要連接到 Microsoft Azure：總共需要 2 個以提供高可用性 | |
| 4 | NTP 伺服器 IP 位址 | 需要與 Azure 同步處理時間：1 必要、1 選擇性 | |
| 5 | Proxy 伺服器 (選擇性) | Proxy 伺服器的 IP 位址/完整網域名稱、要使用的連接埠 | |
| 6 | Azure 儲存體帳戶 | 存取認證，例如帳戶名稱與存取金鑰，根據儲存體帳戶 | |
| 7 | 雲端儲存體加密金鑰 (建議選項) | 根據磁碟區容器 | |
| 8 | 主機的 IQN | 根據主機 | |

## 部署必要條件

下列各節說明 StorSimple Manager 服務與 StorSimple 裝置的設定必要條件。

### 對於 StorSimple Manager 服務

在您開始前，請確定：

- 您擁有的 Microsoft 帳戶具有存取認證。

- 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。

- StorSimple Manager 服務已啟用您的 Microsoft Azure 訂用帳戶。您應該透過[企業合約](http://azure.microsoft.com/pricing/enterprise-agreement/)購買訂用帳戶。

- 您有權限可存取終端機模擬軟體，例如 PuTTY。

### 對於資料中心的裝置

在設定裝置前，請確認：

- 您已完全打開裝置的包裝，如[打開 8100 裝置包裝](https://msdn.microsoft.com/library/azure/dn772327.aspx)或[打開 8600 裝置包裝](https://msdn.microsoft.com/library/azure/dn772418.aspx)中所述。

- 您已在機架上掛接裝置，如[機架掛接 8100 裝置](https://msdn.microsoft.com/library/azure/dn757749.aspx)或[機架掛接 8600 裝置](https://msdn.microsoft.com/library/azure/dn757745.aspx)中所述。

- 您已完全連接裝置的電源、網路及序列存取纜線，如[連接 8100 裝置纜線](https://msdn.microsoft.com/library/azure/dn757738.aspx)或[連接 8600 裝置纜線](https://msdn.microsoft.com/library/azure/dn757762.aspx)中所述。

- 資料中心防火牆中的連接埠已開放，以允許 iSCSI 和雲端流量，如 [StorSimple 裝置的網路需求](https://msdn.microsoft.com/library/dn772371.aspx)中所述。

## 部署步驟

請遵循這些必要步驟來設定 StorSimple 裝置，並將它連接到 StorSimple Manager 服務：

- 步驟 1：建立新的服務 
- 步驟 2：取得服務註冊金鑰
- 步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置 
- 步驟 4：完成最小量裝置設定
- 步驟 5：建立磁碟區容器 
- 步驟 6：建立磁碟區
- 步驟 7：掛接、初始化及格式化磁碟區 
- 步驟 8：進行備份

在部署方案時，除了必要步驟，您可能還有幾個選擇性步驟需要完成。這些選擇性步驟說明如何：

- 針對服務設定新的儲存體帳戶
- 使用 PuTTY 連接到裝置序列主控台
- 取得 Windows Server 主機的 IQN
- 建立手動備份
- 設定 MPIO

逐步部署指出您應該執行各選擇性步驟的時機。

## 步驟 1：建立新的服務

StorSimple Manager 服務可以管理多個 StorSimple 裝置。請執行下列步驟以建立 StorSimple Manager 服務的新執行個體。

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT]如果您並未啟用服務自動建立儲存體帳戶，您將必須在成功建立服務後，至少建立一個儲存體帳戶。當您建立磁碟區容器時，將會使用此儲存體帳戶。
>
> * 如果您未自動建立儲存體帳戶，請移至[針對服務設定新的儲存體帳戶](#configure-a-new-storage-account-for-the-service)以取得詳細指示。 
> * 如果您已啟用自動建立儲存體帳戶，請移至[步驟 2：取得服務註冊金鑰](#step-2:-get-the-service-registration-key)。

## 步驟 2：取得服務註冊金鑰

當 StorSimple Manager 服務啟動後處於執行中時，您就必須取得服務註冊金鑰。這個金鑰是用來註冊和將 StorSimple 裝置與服務連接。

請在 Government 入口網站中執行下列步驟。

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## 步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置

您可以使用 Windows PowerShell for StorSimple 來完成 StorSimple 裝置的初始安裝，如下列程序所述。您必須使用終端機模擬軟體來完成這個步驟。如需詳細資訊，請參閱[使用 PuTTY 連接到裝置序列主控台](#use-putty-to-connect-to-the-device-serial-console)。

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## 步驟 4：完成最小量裝置設定

為完成 StorSimple 裝置的最小量裝置設定，您必須：

- 設定次要 DNS 伺服器。
- 至少在一個網路介面上啟用 iSCSI。
- 針對兩個控制器指派固定的 IP 位址。

請在 Government 入口網站中執行下列步驟，以完成最小量裝置設定。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 步驟 5：建立磁碟區容器

磁碟區容器具有其中所含之所有磁碟區的儲存體帳戶、頻寬及加密設定。您必須建立磁碟區容器，才能開始在 StorSimple 裝置上佈建磁碟區。

請在 Government 入口網站中執行下列步驟，以建立磁碟區容器。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 步驟 6：建立磁碟區

建立磁碟區容器之後，您就可以為伺服器在 StorSimple 裝置上佈建存放磁碟區。請在 Government 入口網站中執行下列步驟，以建立磁碟區。

> [AZURE.IMPORTANT]Azure StorSimple 只能建立精簡佈建的磁碟區。您無法在 Azure StorSimple 系統上建立完整佈建或部分佈建的磁碟區。

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 步驟 7：掛接、初始化及格式化磁碟區

請在 Windows Server 主機上執行這些步驟。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 步驟 8：進行備份

備份可提供磁碟區的時間點保護，並改善復原能力，同時讓還原時間降至最低。您可以在 StorSimple 裝置上進行兩種備份類型： 本機快照與雲端快照。每一種備份類型都可以是 [排程] 或 [手動]。

請在 Government 入口網站中執行下列步驟，以建立排程備份。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

您可以隨時進行手動備份。如需相關程序，請移至[建立手動備份](#create-a-manual-backup)。

## 針對服務設定新的儲存體帳戶

這是選擇性步驟，只有當您並未啟用服務自動建立儲存體帳戶時才需要執行。必須要有 Microsoft Azure 儲存體帳戶，才能建立 StorSimple 磁碟區容器。

如果您需要在不同區域建立 Azure 儲存體帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)以取得逐步指示。

請在 Government 入口網站上的 [StorSimple Manager 服務] 頁面，執行下列步驟。

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## 使用 PuTTY 連接到裝置序列主控台

若要連接到 Windows PowerShell for StorSimple，您需要使用終端機模擬軟體，例如 PuTTY。您可以在存取裝置時，直接透過序列主控台或從遠端電腦開啟 Telnet 工作階段來使用 PuTTY。

[AZURE.INCLUDE [使用 PuTTY 連接到裝置序列主控台](../../includes/storsimple-use-putty.md)]

## 取得 Windows Server 主機的 IQN

請執行下列步驟，以取得正在執行 Windows Server® 2012 之 Windows 主機的 iSCSI 限定名稱 (IQN)。

[AZURE.INCLUDE [建立手動備份](../../includes/storsimple-get-iqn.md)]

## 建立手動備份

請在 Government 入口網站中執行下列步驟，以針對 StorSimple 裝置上的單一磁碟區建立隨選手動備份。

[AZURE.INCLUDE [建立手動備份](../../includes/storsimple-create-manual-backup-gov.md)]

## 設定 MPIO

多重路徑 I/O (MPIO) 是 Windows Server 預設不會安裝的選擇性功能。您應該透過伺服器管理員將它安裝為功能。

> [AZURE.NOTE]StorSimple 虛擬裝置不支援 MPIO。

如需 MPIO 安裝指示，請移至[為 StorSimple 裝置設定 MPIO](storsimple-configure-mpio-windows-server.md)。

## 後續步驟

設定[虛擬裝置](storsimple-virtual-device.md)。

使用 [StorSimple Manager 服務](https://msdn.microsoft.com/library/azure/dn772396.aspx)以管理 StorSimple 裝置。
 

<!---HONumber=July15_HO2-->