<properties 
	pageTitle="如何解除安裝彈性資料庫工作工具" 
	description="如何解除安裝彈性資料庫工作工具" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh"/>

# 如何解除安裝彈性資料庫工作元件

如果嘗試安裝彈性資料庫工作服務時發生失敗，請刪除該服務的資源群組。

## 解除安裝服務元件

1. 開啟 [Azure Preview 入口網站](https://ms.portal.azure.com/)。
2. 巡覽至包含彈性工作的訂閱。
3. 按一下 [瀏覽]，然後按一下 [資源群組]。
4. 選取名為 "__ElasticDatabaseJob" 的資源群組。
5. 刪除資源群組。

或者，使用這個 PowerShell 指令碼：

1. 啟動 [Microsoft Azure PowerShell 視窗](../powershell-install-configure.md)。 
2. 確定使用 PowerShell SDK 0.8.10 或更新版本。
3. 執行指令碼：

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## 後續步驟

若要重新安裝彈性資料庫工作，請參閱[安裝彈性資料庫工作服務](sql-database-elastic-jobs-service-installation.md)。

如需彈性工作服務的概觀，請參閱[彈性工作概觀](sql-database-elastic-jobs-overview.md)。

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=62-->