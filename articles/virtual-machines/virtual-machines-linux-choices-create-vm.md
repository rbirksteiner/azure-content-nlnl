<properties 
	pageTitle="建立 Linux 虛擬機器的不同方式" 
	description="列出建立 Linux 虛擬機器的不同方式，並提供指示的連結。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services"
	ms.date="05/14/2015" 
	ms.author="kathydav"/>

# 建立 Linux 虛擬機器的不同方式

Azure 提供建立 VM 的不同方式，因為 VM 適用於不同的使用者和用途。這表示您必須進行一些關於 VM 的選擇，並選擇建立它的方式。本文提供這些選項及指示連結的摘要說明。

我們最近推出 Azure 資源管理員範本做為一種方式，用以建立虛擬機器，並將它與其不同資源當成一個邏輯部署單位來管理。以下提供這個可用方法的指示。若要深入了解 Azure 資源管理員，以及如何將資源當成一個單位來管理，請參閱[概觀][]。

## 工具選項

### GUI：Azure 入口網站或 Preview 入口網站 

Azure 入口網站的圖形化使用者介面是用來嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。使用 Azure 入口網站或 Azure Preview 入口網站來建立 VM。如需一般指示，請參閱[建立執行 Linux 的虛擬機器][]。

### 命令殼層：Azure CLI 或 Azure PowerShell

如果您偏好在命令殼層中工作，可選擇適用於 Mac 和 Linux 使用者的 Azure 命令列介面 (CLI) 或 Azure PowerShell (其中具有適用於 Azure 和自訂主控台的 Windows PowerShell Cmdlet)。

針對 Azure CLI，請參閱[在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 操作中的對等資源管理員和服務管理命令][]。若要使用範本，請參閱[使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器][]。

針對 Azure PowerShell，請參閱[使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器][]。若要使用範本，請參閱[使用 Azure 資源管理員範本和 PowerShell 部署和管理虛擬機器][]。

### 開發環境：Visual Studio

[使用 Visual Studio 建立網站的虛擬機器][]

[使用計算、網路和儲存體 .NET 程式庫部署 Azure 資源][]

## 作業系統和映像選項

根據您想要執行的作業系統來選擇映像。Azure 與其合作夥伴提供許多映像，其中有些包括應用程式和工具。或者，使用您自己的其中一個映像。

### Azure 映像

這些指示會示範如何使用 Azure 映像來建立虛擬機器，此虛擬機器是使用適用於網路功能、負載平衡，以及更多選項來自訂。請參閱[如何在 Azure 中建立執行 Linux 的自訂虛擬機器][]。

### 使用您自己的映像

「擷取」現有的 Azure 虛擬機器，根據該 VM 來使用映像，或者，上傳您自己的映像 (儲存於虛擬硬碟 (VHD) 中)：

- [如何使用 CLI 擷取 Linux 虛擬機器以做為範本][]
- [建立及上傳包含 Linux 作業系統的虛擬硬碟][]

## 後續步驟

[登入虛擬機器][]

[連接資料磁碟][]

## 其他資源
[關於 Azure VM 組態設定][]

[基本組態測試環境][]

[Azure 混合式雲端測試環境][]

<!-- LINKS -->
[概觀]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[建立執行 Linux 的虛擬機器]: virtual-machines-linux-tutorial.md

[在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 操作中的對等資源管理員和服務管理命令]: xplat-cli-azure-manage-vm-asm-arm.md
[使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器]: virtual-machines-deploy-rmtemplates-azure-cli.md
[使用 Azure 資源管理員範本和 PowerShell 部署和管理虛擬機器]: virtual-machines-deploy-rmtemplates-powershell.md
[使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器]: virtual-machines-ps-create-preconfigure-linux-vms.md

[如何在 Azure 中建立執行 Linux 的自訂虛擬機器]: virtual-machines-linux-create-custom.md
[如何使用 CLI 擷取 Linux 虛擬機器以做為範本]: virtual-machines-vm-capture-image-cli.md

[建立及上傳包含 Linux 作業系統的虛擬硬碟]: virtual-machines-linux-create-upload-vhd.md

[使用 Visual Studio 建立網站的虛擬機器]: virtual-machines-dotnet-create-visual-studio-powershell.md
[使用計算、網路和儲存體 .NET 程式庫部署 Azure 資源]: virtual-machines-arm-deployment.md

[登入虛擬機器]: virtual-machines-linux-how-to-log-on.md

[連接資料磁碟]: virtual-machines-linux-how-to-attach-disk.md

[關於 Azure VM 組態設定]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[基本組態測試環境]: virtual-machines-base-configuration-test-environment.md
[Azure 混合式雲端測試環境]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=July15_HO2-->