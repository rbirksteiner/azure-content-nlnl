<properties 
	pageTitle="Azure 虛擬機器上的 SQL Server" 
	description="描述 SQL Server 代理程式延伸模組，可讓在 Azure 上的雲端中執行 SQL Server 的虛擬機器，使用自動化功能，並描述如果尚未自動安裝代理程式，如何加以安裝。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# SQL Server IaaS 代理程式延伸模組

這個延伸模組可讓 Azure 虛擬機器中的 SQL Server 使用這篇文章中列出的某些服務，這些服務只有在安裝此延伸模組時才能使用。此延伸模組會為 Azure Preview 入口網站中的 SQL Server 組件庫映像自動安裝。它可以安裝在 Azure 中的任何 SQL Server VM，其已安裝 Azure VM 客體代理程式。
 
## 必要條件
使用 Powershell cmdlet 的需求：

- 最新的 Azure 命令列 SDK [可在這裡取得](http://azure.microsoft.com/downloads/)

在您的 VM 上使用延伸模組的需求：

- Azure VM 客體代理程式
- Windows Server 2012、Windows Server 2012 R2 或更新版本
- SQL Server 2012、SQL Server 2014 或更新版本
 
## 延伸模組可用的服務

- **SQL 自動化備份**：這項服務會針對 VM 中 SQL Server 的預設執行個體，自動化所有資料庫的備份排程。若要查看此服務的詳細資訊，請參閱[Azure 虛擬機器中 SQL Server 的自動化備份](https://msdn.microsoft.com/library/azure/dn906091.aspx)。
- **SQL 自動化修補**：這項服務可讓您設定維護視窗 (在此期間會進行 VM 的更新)，您就可以在工作負載的尖峰時段避免更新。若要查看此服務的詳細資訊，請參閱[Azure 虛擬機器中 SQL Server 的自動化修補](https://msdn.microsoft.com/library/azure/dn961166.aspx)。

## 利用 Powershell 新增延伸模組
如果您使用 [Azure Preview 入口網站](https://portal.azure.com/)佈建 SQL Server VM，將會自動安裝延伸模組。對於利用 [Azure 管理入口網站](https://manage.windowsazure.com)佈建的 SQL Server VM，或對於您授與自己 SQL 授權的目標 VM，您都可以使用下列 Azure PowerShell cmdlet 將此延伸模組新增至現有的 VM。

**Set-AzureVMSqlServerExtension**

### 語法

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE]建議省略 –Version 參數。若沒有該參數，預設值為最新版本的延伸模組。

### 範例
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## 檢查延伸模組的狀態
如果您想要檢查這個延伸模組以及與其相關聯之服務的狀態，您可以使用任一入口網站。在您現有 VM 的詳細資料中，找出**設定**下的**延伸模組**。

您也可以使用下列 Azure PowerShell cmdlet。

**Get-AzureVMSqlServerExtension**

### 語法

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<CommonParameters>]

> [AZURE.NOTE]您可以省略 –Version 參數。若沒有該參數，預設值為最新版本的延伸模組。

### 範例
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 利用 Powershell 移除延伸模組   
如果您想要從您的 VM 中移除此延伸模組，您可以使用下列 Azure Powershell cmdlet。

**Remove-AzureVMSqlServerExtension**

### 語法
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=July15_HO2-->