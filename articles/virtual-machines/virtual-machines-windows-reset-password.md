<properties 
	pageTitle="如何重設 Windows 虛擬機器的密碼或遠端桌面服務" 
	description="使用 Azure Preview 入口網站或 PowerShell 命令，快速重設 Windows 虛擬機器的本機系統管理員密碼或遠端桌面服務。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="josephd"/>

# 如何重設 Windows 虛擬機器的密碼或遠端桌面服務

如果您因為忘記密碼或遠端桌面服務組態有問題，而無法連線至 Windows 虛擬機器，請使用 Azure Preview 入口網站或 VMAccess 延伸模組，來重設本機系統管理員密碼或重設遠端桌面服務組態。

> [AZURE.NOTE]本文不適用於以 Azure 資源管理員為基礎的虛擬機器。

## Azure Preview 入口網站

若要在 [Azure Preview 入口網站](https://portal.azure.com)中重設遠端桌面服務，可依序按一下 [瀏覽] > [虛擬機器] > *您的 Windows 虛擬機器* > [重設遠端存取]。範例如下。


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

若要在 [Azure Preview 入口網站](https://portal.azure.com)中重設本機系統管理員帳戶的名稱和密碼，可依序按一下 [瀏覽] > [虛擬機器] > *您的 Windows 虛擬機器* > [所有設定] > [密碼重設]。範例如下。

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)

 
## VMAccess 延伸模組和 PowerShell

在開始之前，您將需要下列項目：

- Azure PowerShell 模組 0.8.5 版或更新版本。您可以使用 **Get-Module azure | format-table version** 命令，來檢查已安裝的 Azure PowerShell 版本。如需最新版本的指示與連結，請參閱[如何安裝和設定 Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409)。 
- 新的本機系統管理員帳戶密碼。如果您想要重設遠端桌面服務組態，則無需此資訊。 
- VM 代理程式。 

不需要先安裝 VMAccess 延伸項目即可使用。只要在虛擬機器上安裝 VM 代理程式，此延伸模組便會在您執行使用 **Set-AzureVMExtension** Cmdlet 的 Azure PowerShell 命令時自動載入。
 
首先，確認已安裝 VM 代理程式。填寫雲端服務名稱和虛擬機器名稱，然後在系統管理員層級 Azure PowerShell 命令提示字元上執行下列命令。取代括弧內的所有項目，包括 < and > 字元。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

如果您不知道雲端服務和虛擬機器名稱，請執行 **Get-AzureVM**，來顯示目前訂用帳戶中所有虛擬機器的該項資訊。

如果 **write-host** 命令顯示 **True**，則會安裝 VM 代理程式。如果顯示 **False**，請參閱 Azure 部落格文章 [VM 代理程式與延伸模組 - 第 2 部分](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)中的指示和下載連結。

如果您已經使用 Azure 管理入口網站建立虛擬機器，請執行下列額外的命令：

	$vm.GetInstance().ProvisionGuestAgent = $true

此命令將避免在下列各節執行 Set AzureVMExtension 命令時發生「必須先在 VM 物件啟用佈建客體代理程式，才能設定 IaaS VM 存取延伸項目」錯誤。

現在，您可以執行下列工作：

- 重設本機系統管理員帳戶密碼
- 重設遠端桌面服務組態

## 重設本機系統管理員帳戶密碼

填入目前的本機系統管理員帳戶名稱和新的密碼，然後執行下列命令。

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- 如果您輸入與目前帳戶不同的名稱，則 VMAccess 延伸項目會重新命名本機系統管理員帳戶、將密碼指派給該帳戶，以及發出遠端桌面登出。
- 如果本機系統管理員帳戶已停用，則 VMAccess 延伸項目會將它啟用。
 
這些命令也會重設遠端桌面服務組態。

## 重設遠端桌面服務組態

若要重設遠端桌面服務組態，請執行此命令。

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 延伸項目會在 VM 上執行這兩個命令：

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	此命令會啟用允許連入遠端桌面流量 (使用 TCP 連接埠 3389) 的內建 Windows 防火牆群組。

- **Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -Value 0**

	此命令會將 fDenyTSConnections 登錄值設為 0，以啟用遠端桌面連線。

如果這樣仍未解決您的遠端桌面存取問題，請執行 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)。

1.	按一下此頁面上的 **Microsoft Azure IaaS (Windows) 診斷封裝**，即可建立新的診斷工作階段。
2.	在 [您的 Azure VM 遇到下列哪些問題？] 頁面上，選取 [RDP 連線至 Azure VM (需要重新開機)] 問題 

如需詳細資訊，請參閱 [Microsoft Azure IaaS (Windows) 診斷封裝知識庫文件](http://support.microsoft.com/kb/2976864)。

如果您無法執行 Azure IaaS (Windows) 診斷封裝或執行後仍未解決您的問題，請參閱[疑難排解遠端桌面連線至 Windows 架構 Azure 虛擬機器](virtual-machines-troubleshoot-remote-desktop-connections.md)。


## 其他資源

[Azure VM 延伸模組與功能](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[透過 RDP 或 SSH 連接至 Azure 虛擬機器](http://msdn.microsoft.com/library/azure/dn535788.aspx)

 

<!---HONumber=July15_HO2-->