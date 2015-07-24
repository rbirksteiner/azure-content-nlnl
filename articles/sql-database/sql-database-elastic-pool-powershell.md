<properties 
   pageTitle="使用 PowerShell 建立和管理 SQL Database 彈性資料庫集區" 
   description="使用 PowerShell 建立和管理 Azure SQL Database 彈性資料庫集區" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="06/24/2015"
   ms.author="adamkr; sstein"/>

# 使用 PowerShell 建立和管理 SQL Database 彈性集區 (預覽)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## 概觀

本文將說明如何使用 PowerShell 建立和管理 SQL Database 彈性集區。


為了清楚起見，我們已將使用 Azure PowerShell 建立彈性集區的流程細分為以下步驟，並逐一說明。如果您只需要簡單扼要的命令清單，請參閱本文底部的＜總整理＞一節。

本文將說明如何建立所需一切以建立和設定彈性集區，但建立 Azure 訂用帳戶除外。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。

> [AZURE.NOTE]彈性集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。


## 必要條件

若要使用 PowerShell 建立彈性集區，您必須安裝 Azure PowerShell 並加以執行，然後將其切換為資源管理員模式，才能存取 Azure 資源管理員 PowerShell Cmdlet。

您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

建立和管理 Azure SQL Database 與彈性集區時所需的 Cmdlet，都位於 Azure 資源管理員模組中。當您開始使用 Azure PowerShell 時，系統會依預設匯入 Azure 模組中的 Cmdlet。若要切換至 Azure 資源管理員模組，請使用 Switch-AzureMode Cmdlet。

	PS C:\>Switch-AzureMode -Name AzureResourceManager

如需詳細資訊，請參閱[將 Windows PowerShell 與資源管理員搭配使用](powershell-azure-resource-manager.md)。


## 設定您的認證並選取您的訂用帳戶

既然您已在執行 Azure 資源管理員模組，便可以存取建立和設定彈性集區所需的所有必要 Cmdlet。首先必須建立對您 Azure 帳戶的存取權。執行以下項目，然後您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	PS C:\>Add-AzureAccount

成功登入後，您應該會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中的資訊，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊。當您的訂用帳戶執行了以下 Cmdlet 之後：

	PS C:\>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## 建立資源群組、伺服器和防火牆規則

現在您有權在您的 Azure 訂用帳戶下執行 Cmdlet，因此下一步是建立含有伺服器的資源群組，以在伺服器中建立彈性集區。為了使用您選擇的任何有效位置，您可以編輯下一個命令。執行 **(Get-AzureLocation | where-object {$_.Name -eq "Microsoft.Sql/servers" }).Locations** 以取得有效位置的清單。

如果您已經有資源群組，可以前往下一個步驟，或執行以下命令來建立新的資源群組：

	PS C:\>New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### 建立伺服器 

彈性集區會建立在 Azure SQL Database 伺服器內。如果您已經有此伺服器，可以前往下一個步驟，或執行以下命令來建立新的 V12 伺服器：用您的伺服器名稱取代 ServerName。這必須是 Azure SQL Server 的唯一名稱，因此伺服器名稱如果被佔用，您就可能會收到錯誤訊息。另外值得注意的是，此命令可能需要數分鐘才能完成。成功建立伺服器後，會出現伺服器詳細資料和 PowerShell 提示字元。為了使用您選擇的任何有效位置，您可以編輯此命令。

	PS C:\>New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

當您執行此命令時，會隨即開啟向您詢問 [使用者名稱] 和 [密碼] 的視窗。這不是您的 Azure 認證，請輸入要用於新伺服器之系統管理員認證的使用者名稱和密碼。


### 設定伺服器防火牆規則以允許存取伺服器

建立可存取伺服器的防火牆規則。執行以下命令，用對您電腦有效的值，取代開頭和結尾 IP 位址。

如果您的伺服器需要允許存取其他 Azure 服務，請加入 **-AllowAllAzureIPs** 參數，藉此加入特殊的防火牆規則，並允許所有 Azure 流量存取伺服器。

	PS C:\>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。


## 建立彈性集區和彈性資料庫

現在您已擁有資源群組、伺服器和設定完成的防火牆規則，便可以存取伺服器。以下命令會建立彈性集區。此命令建立的集區會共用總共 400 個 DTU。集區中的每個資料庫保證一律都有 10 個 DTU 可用 (DatabaseDtuMin)。集區中的個別資料庫可耗用最多 100 個 DTU (DatabaseDtuMax)。如需參數的詳細說明，請參閱 [Azure SQL Database 彈性集區](sql-database-elastic-pool.md)。


	PS C:\>New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### 建立彈性資料庫或將其加入至集區

在上一步中建立的彈性集區是空的，其中沒有資料庫存在。以下各節說明如何在彈性集區內建立新的資料庫，以及如何將現有的資料庫加入至集區。


### 在彈性集區內建立新的彈性資料庫

若要直接在彈性集區內建立新的資料庫，請使用 **New-AzureSqlDatabase** Cmdlet，並設定 **ElasticPoolName** 參數。


	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### 將現有資料庫移入彈性集區

若要將現有資料庫移入彈性集區，請使用 **Set-AzurSqlDatabase** Cmdlet，並設定 **ElasticPoolName** 參數。


這裡為了示範，建立了不在彈性集區中的資料庫。

	PS C:\>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

將這個現有資料庫移入彈性集區。

	PS C:\>Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 監視彈性資料庫和彈性集區

### 取得彈性集區作業的狀態

您可以追蹤彈性集區作業的狀態，包括建立和更新作業。

	PS C:\> Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### 取得將彈性資料庫移入和移出彈性集區的狀態

	PS C:\>Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### 取得彈性集區的資源消耗度量

可以用資源集區限制的百分比來擷取的度量：

* 平均 CPU 使用率：cpu_percent 
* 平均 IO 使用率：data_io_percent 
* 平均記錄檔使用率：log_write_percent 
* 平均記憶體使用率：memory_percent 
* 平均 DTU 使用量 (做為 CPU/IO/記錄檔使用率的最大值)：DTU_percent 
* 並行使用者要求 (背景工作) 的數目上限：max_concurrent_requests 
* 並行使用者工作階段的數目上限：max_concurrent_sessions 
* 彈性集區的總儲存體大小：storage_in_megabytes 


度量資料粒度/保留期限：

* 系統會以 5 分鐘的資料粒度傳回資料。  
* 資料會保留 14 天。  


此 Cmdlet 和 API 會將一次呼叫中可擷取的資料列限制為 1,000 個 (大約是 3 天份且資料粒度為 5 分鐘的資料)。但可以用不同的開始/結束時間間隔來多次呼叫此命令，以擷取更多資料。


擷取度量：

	PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

重複呼叫並附加資料來取得其他日期：

	PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
格式化資料表：

    PS C:\> $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### 取得彈性資料庫的資源消耗度量

除了以下的語意差異外，這些 API 與目前用於監視獨立資料庫之資源使用率的 (V12) API 相同。

* 這個擷取的 API 度量，會以針對該彈性集區所設定之每個 databaseDtuMax (或是 CPU、IO 等基礎度量的相等上限) 的百分比來表示。例如，這些度量有其中一項的使用率為 50%，則表示特定資源的消耗量佔該資源在父彈性集區中，每個資料庫上限限制的 50%。 

取得度量：PS C:\> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

視需要重複呼叫並附加資料，來取得其他日期：

    PS C:\> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

格式化資料表：

    PS C:\> $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    PS C:\> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## 總整理


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 後續步驟

建立彈性集區後，可以藉由建立彈性工作來管理集區中的彈性資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。

如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。


## 彈性資料庫參考

如需關於彈性資料庫和彈性資料庫集區的詳細資訊，包括 API 和錯誤詳細資料，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

<!---HONumber=62-->