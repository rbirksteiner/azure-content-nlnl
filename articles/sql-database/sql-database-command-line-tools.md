<properties 
	pageTitle="使用 PowerShell 管理 Azure SQL Database 資源" 
	description="使用 PowerShell 管理 Azure SQL Database。" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="vinsonyu"/>

# 使用 PowerShell 管理 Azure SQL Database 資源


本主題提供 PowerShell 命令，使用 Azure 資源管理員 Cmdlet 來執行許多 Azure SQL Database 工作。


## 必要條件

若要執行 PowerShell Cmdlet，您必須安裝 Azure PowerShell 並加以執行，然後視您可能需要的版本將其切換為資源管理員模式，才能存取 Azure 資源管理員 PowerShell Cmdlet。

您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

建立和管理 Azure SQL Database 時所需的 Cmdlet，都位於 Azure 資源管理員模組中。當您開始使用 Azure PowerShell 時，系統會依預設匯入 Azure 模組中的 Cmdlet。若要切換至 Azure 資源管理員模組，請使用 **Switch-AzureMode** Cmdlet。

	PS C:>Switch-AzureMode -Name AzureResourceManager

如果您收到警告，說明「Switch-AzureMode cmdlet 已經過時，未來版本將移除」。 您可以略過它並移至下一節。

如需詳細資訊，請參閱[將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager.md)。



## 設定您的認證

若要針對 Azure 訂用帳戶執行 PowerShell Cmdlet，您必須先建立至 Azure 帳戶的存取權。執行以下項目，然後您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	PS C:>Add-AzureAccount

成功登入後，您應該會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


## 選取您的 Azure 訂用帳戶

若要選取使用的訂用帳戶，您必須提供訂用帳戶識別碼 (**-SubscriptionId**) 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中的資訊，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊。

使用您的訂用帳戶資訊執行下列 Cmdlet，以設定您目前的訂用帳戶：

	PS C:>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

會針對您在上方剛選取的訂用帳戶執行下列命令。

## 建立資源群組

建立會包含伺服器的資源群組。您可以編輯下一個命令，以使用任何有效位置。

如需有效的 Azure SQL Database 伺服器位置清單，請執行下列 Cmdlet：

		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

如果您已經有資源群組，您可以跳過以建立伺服器，或可以編輯或執行以下命令來建立新的資源群組：

	PS C:>New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## 建立伺服器 

若要建立新的 V12 伺服器，請使用 [New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx) 命令。將 server12 取代為您伺服器的名稱。這必須是 Azure SQL Server 的唯一名稱，因此伺服器名稱如果被佔用，您就可能會收到錯誤訊息。另外值得注意的是，此命令可能需要數分鐘才能完成。成功建立伺服器後，會出現伺服器詳細資料和 PowerShell 提示字元。您可以編輯命令以使用任何有效位置。

	PS C:>New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

當您執行此命令時，會隨即開啟向您詢問 [使用者名稱] 和 [密碼] 的視窗。這不是您的 Azure 認證，請輸入要用於新伺服器之系統管理員認證的使用者名稱和密碼。

## 建立伺服器防火牆規則

若要建立防火牆規則以存取伺服器，請使用 [New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx) 命令。執行以下命令，用對您用戶端有效的值，取代開頭和結尾 IP 位址。

如果您的伺服器需要允許存取其他 Azure 服務，請加入 **-AllowAllAzureIPs** 參數，藉此加入特殊的防火牆規則，並允許所有 Azure 流量存取伺服器。

	PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。

## 建立 SQL 資料庫

若要建立資料庫，請使用 [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125915.aspx) 命令。您需要伺服器才能建立資料庫。下列範例會建立名為 TestDB12 的 SQL 資料庫。資料庫會建立為標準 S1 資料庫。

	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## 變更 SQL 資料庫的效能層級

您可以使用 [Set-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125814.aspx) 命令向上或向下調整您的資料庫。下列範例會將名為 TestDB12 的 SQL 資料庫，從其目前的效能層級擴充至標準 S3 層級。

	PS C:>Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## 刪除 SQL 資料庫

您可使用 [Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx) 命令刪除 SQL 資料庫。下列範例會刪除名為 TestDB12 的 SQL 資料庫。

	PS C:>Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## 刪除伺服器

您也可以使用 [Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx) 命令刪除伺服器。下列範例會刪除名為 server12 的伺服器。

	PS C:>Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



如果您將再次建立這些 Azure SQL 資源或類似的資源，您可以：

- 將這個命令集儲存為 PowerShell 指令碼檔案 (*.ps1)
- 在 Azure 管理入口網站的 [自動化] 區段中，將這個命令集儲存為 Azure 自動化 Runbook 

## 後續步驟

結合命令和自動化。例如，以您的值取代引號中的任何內容 (包括 < and > 字元) 以建立伺服器、防火牆規則和資料庫：


    PS C:>New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    PS C:>New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    PS C:>New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## 相關資訊

- [Azure SQL Database 資源管理員 Cmdlet](https://msdn.microsoft.com/library/mt163521.aspx)
- [Azure SQL Database 服務管理 Cmdlet](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=July15_HO2-->