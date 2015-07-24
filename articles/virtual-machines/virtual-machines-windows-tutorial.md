<properties
	pageTitle="在 Azure 預覽入口網站中建立執行 Windows 的虛擬機器"
	description="了解如何使用 Azure 預覽入口網站中的 Azure Marketplace，建立執行 Windows 的 Azure 虛擬機器 (VM)"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# 在 Azure 預覽入口網站中建立執行 Windows 的虛擬機器#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

本教學課程示範在 Azure Preview 入口網站中建立 Azure 虛擬機器 (VM) 有多麼容易。我們將使用 Windows Server 映像做為範例，這只是 Azure 提供眾多映像中的一種。請注意，您可以選擇何種映像取決於您的訂用帳戶。例如，桌面映像可能可供 MSDN 訂閱者使用。

您也可以使用[自己的映像](virtual-machines-create-upload-vhd-windows-server.md)來建立 VM。若要深入瞭解上述方法與其他方法，請參閱[建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## 建立虛擬機器

使用 Windows Server 2012 R2 Datacenter 做為範例，您可以利用幾分鐘的時間建立 VM 來試試。對於大部分的組態，您可以使用 Azure 的預設設定。

1. 登入 [Preview 入口網站](https://portal.azure.com)。

2. 在 [中心] 功能表上，按一下 [新增]。

3. 在 [新增] 刀鋒視窗中，按一下 [計算] **>** [Windows Server 2012 R2 Datacenter]。

	![從 Marketplace 選取 VM 映像](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. 在 [建立 VM] 分頁上，填入您想要的 VM [主機名稱]、系統管理 [使用者名稱，及強式 [密碼]。[使用者名稱] 是指要用來管理伺服器的系統管理帳戶。建立一個很難讓人猜到，但您可以記住的密碼。**您將需要使用者名稱和密碼才能登入虛擬機器**。如果您忘記密碼，可以使用[這些指示](virtual-machines-windows-reset-password.md)重設密碼

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. 檢閱預設設定，例如 [定價層] 和 [選擇性組態]。這些選項會影響 VM 的大小，以及如網域成員資格等的網路功能選項。例如，若要在虛擬機器上試用進階儲存體，您將必須挑選區域和支援它的大小。如果這是您第一個虛擬機器，預設值通常沒什麼問題。

	>[AZURE.NOTE]進階儲存體可供某些區域的 DS 系列虛擬機器使用。進階儲存體對於如資料庫這類資料密集的工作負載是最佳的儲存體選項。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal.md)。

6. 當您檢閱或更新完設定時，請按一下 [建立]。

7. 當 Azure 建立 VM 時，您可以在 [中心] 功能表的 [通知] 中持續追蹤進度。Azure 建立 VM 之後，除非您在 [建立 VM] 刀鋒視窗中清除 [釘選到開始面板]，否則您將會在開始面板上看到 VM。

## 登入虛擬機器

建立 VM 之後，您就可以登入 VM 以管理其設定和將在 VM 上執行的應用程式。

>[AZURE.NOTE]如需要求和疑難排解提示，請參閱[透過 RDP 或 SSH 連接至 Azure 虛擬機器](https://msdn.microsoft.com/library/azure/dn535788.aspx)。

1. 如果您尚未登入 [Preview 入口網站](https://portal.azure.com)，請先登入。

2. 在 [開始面板] 上按一下您的 VM。如果您要尋找 VM，請按一下 [瀏覽] > [虛擬機器]。然後從清單中選取您的 VM。

3. 在 VM 刀鋒視窗上，按一下 [連線]。

	![登入虛擬機器](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. 按一下 [開啟]，以使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。

5. 按一下 [連接]。

6. 輸入您建立虛擬機器時建立的使用者名稱和密碼，然後按一下 [確定]。

7. 按一下 [是] 以驗證虛擬機器的身分識別。

	您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

## 後續步驟

若要深入了解如何在 Azure 上設定 Windows 虛擬機器，請參閱下列文章：

[將虛擬機器與虛擬網路或雲端服務連線](cloud-services-connect-virtual-machine.md)

[將資料磁碟連接至虛擬機器](storage-windows-attach-disk.md)

[管理虛擬機器的可用性](../manage-availability-virtual-machines.md)

[關於 Azure VM 組態設定](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->