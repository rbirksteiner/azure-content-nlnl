<properties
	pageTitle="服務管理中針對虛擬機器的文章 | Microsoft Azure"
	description="列出的文章有助於您在 Azure 服務管理中建立並管理虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# 服務管理中針對虛擬機器的文章
這是在 Azure 服務管理中使用虛擬機器的文章清單。針對新的部署，Microsoft 建議使用 Azure 資源管理員為基礎的虛擬機器和其他資源。如需資源管理員優點的說明，請參閱 [Azure 資源管理員底下的 Azure 運算、網路和儲存體提供者](virtual-machines-azurerm-versus-azuresm.md)。

## 開始使用

[在 Azure 管理入口網站中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial-classic-portal.md)

[如何建立一個執行 Windows 的自訂虛擬機器](virtual-machines-windows-create-custom.md)

[如何建立一個執行 Linux 的自訂虛擬機器](virtual-machines-linux-create-custom.md)

[教學課程：在 Azure 中建立純雲端虛擬網路](create-virtual-network.md)

## 自動化

[使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-linux-vms.md)

[使用 Azure PowerShell 建立和預先設定 Windows 型虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)

[利用 PowerShell 和 Azure 服務管理建立和管理 Windows 虛擬機器](virtual-machines-create-windows-powershell-service-manager.md)

[使用 Azure 命令列介面 (Azure CLI) 建立 VM](virtual-machines-xplat-getting-started.md)

[使用 Azure CLI 建立多個 VM 的部署](virtual-machines-create-multi-vm-deployment-xplat-cli.md)

## 規劃

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[關於 VM 組態設定](https://msdn.microsoft.com/library/azure/dn763935.aspx)

[我需要虛擬網路嗎？](https://msdn.microsoft.com/library/azure/jj156007.aspx)

## 建立

[建立 Windows Server VHD 並上傳至 Azure](virtual-machines-create-upload-vhd-windows-server.md)

[建立及上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-create-upload-vhd.md)

[如何擷取 Windows 虛擬機器以做為範本](virtual-machines-capture-image-windows-server.md)


[如何擷取 Linux 虛擬機器作為範本使用](virtual-machines-linux-capture-image.md)

[如何使用 CLI 擷取 Linux 虛擬機器以做為範本](virtual-machines-vm-capture-image-cli.md)


## 管理

[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)

[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)

[如何將資料磁碟連接至 Windows 虛擬機器](storage-windows-attach-disk.md)

[如何從 Windows 虛擬機器中斷連接資料磁碟](storage-windows-detach-disk.md)

[如何將資料磁碟連接至 Linux 虛擬機器](virtual-machines-linux-how-to-attach-disk.md)

[如何從 Linux 虛擬機器中斷連接資料磁碟](virtual-machines-linux-how-to-detach-disk.md)

[如何將虛擬機器與雲端服務或虛擬網路連接](cloud-services-connect-virtual-machine.md)

[如何設定虛擬機器的可用性設定組](virtual-machines-how-to-configure-availability.md)

[開始設定內部負載平衡器](../load-balancer/load-balancer-internal-getstarted.md)

[開始設定網際網路面向的負載平衡器](../load-balancer/load-balancer-internet-getstarted.md)

[如何設定您的虛擬機器的端點](virtual-machines-set-up-endpoints.md)

## 建立工作負載

[Active Directory](https://msdn.microsoft.com/library/azure/jj156090.aspx)

[Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)

[Docker](virtual-machines-docker-with-xplat-cli.md)

[Dynamics NAV](https://msdn.microsoft.com/library/azure/dn168977.aspx)

[HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

[LAMP Stack](virtual-machines-linux-install-lamp-stack.md)

[在 Windows Server 上的 MongoDB](virtual-machines-install-mongodb-windows-server.md)

[在 Windows Server 上的 MySQL](virtual-machines-mysql-windows-server-2008r2.md)

[Oracle](virtual-machines-oracle-azure-virtual-machines.md)

[SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

[SQL Server](virtual-machines-sql-server-infrastructure-services.md)

## 監視

[了解 Azure 中的監視警示和通知](https://msdn.microsoft.com/library/azure/dn306639.aspx)

[啟用診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)

[在 Visual Studio 中進行雲端服務或虛擬機器的偵錯](https://msdn.microsoft.com/library/azure/ff683670.aspx)

[使用 Windows PowerShell 指令碼來發行至開發和測試環境](https://msdn.microsoft.com/library/azure/dn642480.aspx)

## 疑難排解

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

## 參考

[適用服務管理的 PowerShell Cmdlet](https://msdn.microsoft.com/library/azure/dn708504.aspx)

[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[適用服務管理的 Azure CLI 命令](virtual-machines-command-line-tools.md)

<!---HONumber=July15_HO2-->