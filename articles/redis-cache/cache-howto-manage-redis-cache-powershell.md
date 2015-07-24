<properties 
 pageTitle="使用 Azure PowerShell 管理 Azure Redis 快取" 
 description="了解如何使用 Azure PowerShell 執行 Azure Redis 快取的管理工作。" 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   writer="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="04/23/2015"
   ms.author="riande"/>

# 使用 Azure PowerShell 管理 Azure Redis 快取

本教學課程提供建立、更新和刪除 Azure Redis 快取的快速入門。

## 必要條件 ##

在將 Windows PowerShell 與資源管理員搭配使用之前，您必須具備下列項目：

- Windows PowerShell 3.0 或 4.0 版本。若要找出 Windows PowerShell 的版本，請輸入：`$PSVersionTable`，並確認 `PSVersion` 的值是 3.0 還是 4.0。若要安裝相容版本，請參閱 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。
	
- Azure PowerShell 0.8.0 或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

本教學課程是為 Windows PowerShell 初學者所設計。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) (英文)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

	Get-Help <cmdlet-name> -Detailed

例如，如需取得 Add-AzureAccount Cmdlet 的說明，請輸入：

	Get-Help Add-AzureAccount -Detailed

## Redis 快取的簡單 Azure PowerShell 指令碼  ##

下列指令碼示範如何建立、更新和刪除 Azure Redis 快取。

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## 後續步驟
深入了解如何使用 Windows Azure PowerShell：
 
- [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409)：深入了解在 AzureResourceManager 模組中使用 Cmdlet。
- [使用資源群組以管理您的 Azure 資源](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)：深入了解在 Azure 管理入口網站中建立和管理資源群組。
- [Azure 部落格](http://blogs.msdn.com/windowsazure)：深入了解 Azure 的新功能。
- [Windows PowerShell 部落格](http://blogs.msdn.com/powershell)：深入了解 Windows PowerShell 的新功能。
- ["Hey, Scripting Guy!" 部落格](http://blogs.technet.com/b/heyscriptingguy/)：從 Windows PowerShell 社群中取得實際的秘訣及訣竅。

<!---HONumber=July15_HO1-->