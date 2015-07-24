<properties
	pageTitle="在 Azure 中建立執行 Windows 的虛擬機器"
	description="了解如何在 Azure 管理入口網站中建立 Windows 虛擬機器 (VM)。"
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
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# 在 Azure 管理入口網站中建立執行 Windows 的虛擬機器

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)


本教學課程示範在 Azure 管理入口網站中建立 Azure 虛擬機器 (VM) 有多麼容易。我們將使用 Windows Server 映像做為範例，這只是 Azure 提供眾多映像中的一種。請注意，您可以選擇何種映像取決於您的訂用帳戶。例如，桌面映像可能可供 MSDN 訂閱者使用。

您也可以使用[自己的映像](virtual-machines-create-upload-vhd-windows-server.md)來建立 VM。若要深入瞭解上述方法與其他方法，請參閱[建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>如何建立虛擬機器

本節說明如何在 Azure 管理入口網站中使用 [從組件庫] 選項建立虛擬機器。此選項提供的組態選擇比 [快速建立] 選項還多。例如，如果您要將虛擬機器加入虛擬網路中，您必須使用 [從組件庫] 選項。

> [AZURE.NOTE]您也可以使用更豐富而可自訂的 [Azure Preview 入口網站](https://portal.azure.com)建立虛擬機器、將多機器應用程式範本的部署自動化、使用增強的 VM 監控和診斷功能，並且執行其他作業。兩個入口網站中的可用 VM 組態選項有許多重疊之處，但並不完全相同。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## 後續步驟

- 登入虛擬機器。如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

- 附加磁碟來儲存資料。您可以附加空的磁碟和含有資料的磁碟。如需指示，請參閱[附加資料磁碟教學課程](storage-windows-attach-disk.md)。

## 其他資源

若要深入了解您可以為 VM 設定什麼功能以及何時可以設定，請參閱[有關 Azure VM 設定](http://msdn.microsoft.com/library/azure/dn763935.aspx)。
 

<!---HONumber=July15_HO2-->