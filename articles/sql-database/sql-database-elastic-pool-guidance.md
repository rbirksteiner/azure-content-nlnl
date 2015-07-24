<properties 
	pageTitle="彈性資料庫集區的價格和效能考量 |Azure SQL Database 彈性資料庫集區" 
	description="彈性資料庫集區是一組彈性資料庫共用的可用資源集合。本文件提供指引，幫助您評估對一組資料庫使用彈性資料庫集區的合適性。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# 彈性資料庫集區的價格和效能考量


本文件提供指引，幫助您根據資料庫使用模式和彈性集區與單一資料庫之間的價格差異，評估對一組資料庫使用彈性資料庫集區是否具成本效益。也提供其他指引，以協助判斷一組現有的 SQL 資料庫所需的目前集區大小。

- 如需彈性資料庫集區的概觀，請參閱 [SQL Database 彈性資料庫集區](sql-database-elastic-pool.md)。
- 如需彈性資料庫集區的詳細資訊，請參閱 [SQL Database 彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。


> [AZURE.NOTE]彈性集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。

## 彈性資料庫集區

SaaS ISV 會開發建置在由多個資料庫組成的大規模資料層上的應用程式。常見的應用程式模式是每個資料庫有不同的客戶，但有獨特的且無法預期的使用模式。因此讓 ISV 難以個別預測每個資料庫的資源需求。在這些情況下，ISV 可能以可觀的費用過度佈建資源，以確保所有資料庫能有最佳輸送量和回應時間。或者，ISV 可能用較少的費用，並且讓客戶承擔效能不佳體驗的風險。

Azure SQL Database 中的彈性資料庫集區可讓 SaaS ISV 將一組資料庫的價格效能最佳化在規定的預算內，同時為每個資料庫提供效能彈性。彈性集區可讓 ISV 為由多個資料庫共用的彈性集區購買資料庫輸送量單位 (DTU)，以容納個別資料庫無法預期的使用量期間。彈性集區的 DTU 需求取決於其資料庫的彙總使用量。彈性集區可用的 DTU 數量是由 ISV 預算控制。彈性集區可讓 ISV 輕鬆為其集區推斷預算對效能的影響，反之亦然。ISV 只要將資料庫加入至彈性集區、設定資料庫需要任何 DTU 保證或容量，並根據其預算設定集區的 DTU。藉由使用彈性集區，ISV 可以順暢地擴大其服務，以漸增的規模從精簡的新創公司到成熟的企業。
  


## 考慮使用彈性資料庫集區的時機

彈性集區很適合具備特定使用模式的大量資料庫。針對指定的資料庫，此模式的特徵是低平均使用量與相對不頻繁的使用量高峰。

您可以加入集區的資料庫愈多，您可以獲得的節約愈高，但視您的應用程式使用量模式而定，可能會發現與使用 4 個 S3 資料庫一樣少的節約。

下列各節將協助您了解如何評估您特定的資料庫集是否將因使用彈性集區受益。

### 評估資料庫使用量模式

下圖顯示資料庫的範例，該資料庫花費太多時間閒置，但也定期因活動達到尖峰。這是非常適合彈性集區的使用量模式：
 
   ![一個資料庫][1]

針對如上所示的一小時期間，DB1 尖峰最高達 90 個 DTU，但其整體平均使用量 < 5 個 DTU。需要 S3 效能層級，才能在單一資料庫中執行此工作負載。不過，這會在活動較少的期間保留大多數的資源未使用。

彈性集區可讓這些未使用的 DTU 跨多個資料庫共用，並因此減少需要的 DTU 總數量和整體成本。

以上一個範例為建置基礎，假設有其他資料庫具有與 DB1 類似的使用量模式。在接下來的兩個圖形中，4 個資料庫和 20 個資料庫的使用量分層放在相同的圖形，來說明經過一段時間其使用量非重疊的本質：

   ![四個資料庫][2]

   ![二十個資料庫][3]

在上圖中，黑色線條說明跨所有 20 個資料庫的彙總 DTU 使用量。這顯示彙總的 DTU 使用量永遠不會超過 100 個 DTU，並指出 20 個資料庫可以在這段期間共用 100 個 DTU。相較於將每個資料庫放在單一資料庫的 S3 效能層級，這會導致 DTU 減少 20 倍和價格降低 6 倍。


由於以下原因，此範例很理想：

- 每一資料庫之間的尖峰使用量和平均使用量有相當大的差異。  
- 每個資料庫的尖峰使用量會在不同時間點發生。 
- DTU 會在大量資料庫之間共用。


彈性集區的價格是集區 DTU 與在其中的資料庫數目的函數。雖然 GA 定價的集區的 DTU 單位價格較單一資料庫的 DTU 的單價位價格大 3 倍，集區的 DTU 可由多個資料庫共用，因而在許多情況下需要的總 DTU 會較少。定價方面和 DTU 共用的這些差異是集區可以提供價格節約潛力的基礎。

<br>

下列資料庫計數和資料庫使用量相關規則的經驗法則，可協助確保彈性集區可提供相較於使用單一資料庫的效能層級降低的成本。本指南是基於公開上市 (GA) 價格。請注意，GA 價格在預覽期間提供 50% 折扣，因此這些經驗法則應視為相當保守。


### 資料庫的最小數目

利用 GA 定價，如果 1 個 DTU 可由 3 個以上資料庫共用，彈性集區會成為更具成本效益的效能選擇。這表示單一資料庫的效能層級的 DTU 總和是集區的 DTU 的 3 倍。如需可用的大小，請參閱[彈性集區和彈性資料庫的 DTU 和儲存體限制](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases)。


***範例***<br>100 個 DTU 彈性集區需要至少 4 個 S3 資料庫或至少 36 個 S0 資料庫，才能較使用單一資料庫的效能層級更具成本效益。(請注意，利用預覽價格，基於資料庫計數的價格平衡點可降低到 2 個 S3 資料庫或 17 個 S0 資料庫。)



### 並行尖峰資料庫的最大數目

藉由共用 DTU，並非集區中的所有資料庫都能同時使用 DTU 達到使用單一資料庫的效能層級時的最大限制。同時尖峰的資料庫愈少，可以設定的集區 DTU 愈低，就能實現更多的成本效益。一般而言，集區中的資料庫不能超過 1/3 應該同時尖峰達到其 DTU 的限制。

***範例***<br>為了降低 200 DTU 集區中 4 個 S3 資料庫的成本，在其使用量中最多可以有 2 個資料庫可以同時處於尖峰。否則，如果 4 個 S3 資料庫中超過 2 個同時尖峰，則必須將集區調整為超過 200 個 DTU。而且，如果將集區調整大小為超過 200 個 DTU，則需要加入更多的 S3 資料庫至集區，以使成本保持低於單一資料庫的效能層級。


請注意，此範例不考慮集區中其他資料庫的使用量。如果在任何給定時間點，所有資料庫都有一些使用量，則可以同時處於尖峰的資料庫少於 1/3。


### 每個資料庫的 DTU 使用量

資料庫的尖峰和平均使用量之間的差異為，長時間的低使用量和短時間的高使用量。這個使用量模式非常適合在資料庫之間共用資源。當資料庫的尖峰使用量為平均使用量的 3 倍大時，應該將資料庫視為集區。

    
***範例***<br>尖峰為 100 個 DTU 且平均使用 30 個 DTU 或更少的 S3 資料庫是在彈性集區中共用 DTU 的良好候選項目。或者，尖峰為 20 個 DTU 且平均使用 7 個 DTU 或更少的 S1 資料庫是彈性集區的良好候選項目。
    

## 比較的彈性集區和單一資料庫的價格差異的啟發學習法 

下面啟發學習法可以協助您預估彈性集區是否比使用個別的單一資料庫更符合成本效益。

1. 透過下列項目來估計集區所需的 DTU：
    
    MAX(*資料庫的總數目* * *每一資料庫的平均 DTU 使用量*、*同時尖峰資料庫的數目* * *每一資料庫的尖峰 DTU 使用量*)

2. 選取大於步驟 1 估計值的集區的最小可用 DTU 值。如需可用的 DTU 選擇，請參閱此處所列的 DTU 有效值：[彈性集區和彈性資料庫的 DTU 和儲存體限制](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases)。

    


3. 計算集區的價格，如下所示：

    集區價格 = (*集區 DTU* * *集區 DTU 單價*) + (*資料庫數目總計* * *集區資料庫單價*)

    如需定價資訊，請參閱 [SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/)。


4. 將步驟 3 的集區價格與單一資料庫適當效能層級的價格相比較。



## 為現有的 SQL 資料庫決定最佳的集區 DTU 大小 

彈性集區的最佳大小取決於彙總的 DTU 和集區中所有資料庫所需的儲存體資源。這牽涉到決定下兩個數量的較大值：

* 集區中所有資料庫使用的最大 DTU。
* 集區中所有資料庫使用的最大儲存體位元組。 

請注意，針對 Standard 服務層，對集區設定的每 1 個 DTU 允許有 1 GB 的儲存體。例如，如果集區是設定為 200 個 DTU，則儲存體限制為 200 GB。

### 使用 Service Tier Advisor (STA) 和動態管理檢視 (DMV) 來調整大小的建議   

STA 和 DMV 為調整彈性集區的大小提供不同的工具選項和功能。無論使用何種工具選項，大小估計值應該僅用於彈性集區的初步評估與建立。一旦建立彈性集區，則應該準確地監視其資源使用狀況，並視需要增加和減少進行調整集區的效能設定。

**STA**<br>STA 是 Azure 入口網站中的內建工具，它會自動評估現有的 SQL Database 伺服器中資料庫的歷程記錄資源使用量，並且建議適當的彈性集區設定。

**DMV 調整大小工具**<br>DMV 調整大小工具以 PowerShell 指令碼的形式提供，並可讓您自訂伺服器中現有資料庫彈性集區的調整大小估計值。

### STA 和 DMV 工具之間的選擇 

選取適用於分析特定應用程式的工具。下表摘要說明這些 2 個調整大小工具之間的差異：

| 功能 | STA | DMV |
| :--- | :--- | :--- |
| 分析中使用的資料範例的粒度。 | 15 秒 | 15 秒 |
| 考慮單一資料庫的集區和效能層級之間的定價差異。 | 是 | 否 |
| 允許自訂伺服器內所分析資料庫的清單。 | 否 | 是 |
| 允許自訂分析中使用的時間期間。 | 否 | 是 |


### 使用 STA 估計彈性集區大小  

STA 會評估資料庫的使用量歷程記錄，並在比使用單一資料庫的效能層級更符合成本效益時，建議您使用彈性集區。如果建議使用彈性集區，工具會提供建議的資料庫的清單，以及建議的集區 DTU 數量和每個彈性資料庫的最小/最大 DTU 設定。為了讓資料庫被視為彈性集區的候選項目，它必須存在至少 14 天。彈性資料庫集區公用預覽僅限於 Standard，因此，Premium 資料庫尚不能被視為彈性集區的候選項目。

STA 是在加入彈性集區至現有的伺服器時，於 Azure 入口網站中提供。如果有該伺服器彈性集區的建議，它們會顯示在 [彈性資料庫集區] 建立刀鋒視窗中。客戶可以隨時變更建議的設定，以建立自己的彈性集區群組。

### 使用動態管理檢視 (DMV) 預估彈性集區大小 

[Sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV 會測量個別資料庫的資源使用量。此 DMV 會提供 CPU、IO、記錄與資料庫的記錄檔使用量，做為資料庫的效能層級限制的百分比。此資料可以用來計算在任何指定 15 秒間隔中，資料庫的 DTU 使用量。

可以估計彈性集區在 15 秒的間隔中彙總的 DTU 使用量，方法是彙總在該時段的所有候選資料庫的 DTU 使用量。視特定的效能目標而定，捨棄少數百分比的範例資料亦合適。例如，可以套用跨所有時間間隔的彙總 DTU 第 99 個百分位值，以排除極端值並提供彈性集區 DTU 以符合 99% 的取樣時間間隔。

## 用於預估您的資料庫彙總 DTU 使用量的 PowerShell 指令碼

此處提供範例 PowerShell 指令碼，以估計 SQL Database 伺服器中使用者資料庫的彙總 DTU 值。

指令碼只會在執行時收集資料。針對一般的實際執行工作負載，您應該至少執行指令碼一天，但一週或甚至更長將會提供更精確的估計值。對可代表您資料庫的一般工作負載的時間期間執行指令碼。

> [AZURE.IMPORTANT]執行指令碼時，您必須保持 PowerShell 視窗開啟。除非您已執行指令碼一段足夠的時間量，並擷取足夠的資料來代表您橫跨一般和尖峰使用時間的一般工作負載，否則請勿關閉 PowerShell 視窗。

### 指令碼必要條件 

執行指令碼之前安裝下列：

- 最新的 [PowerShell 命令列工具](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。
- [SQL Server 2014 功能套件](https://www.microsoft.com/download/details.aspx?id=42295)。


### 指令碼詳細資料


您可以從本機電腦或雲端上的 VM 上執行指令碼。從本機機器上執行時，因為指令碼需要從您的目標資料庫下載資料，可能會產生資料輸出費用。以下顯示根據目標資料庫的數目和執行指令碼持續時間的資料磁碟區估計。針對 Azure 資料傳輸成本，請參閱[資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/)。
       
 -     1 個資料庫每小時 = 38 KB
 -     1 個資料庫每日 = 900 KB
 -     1 個資料庫每週 = 6 MB
 -     100 個資料庫每日 = 90 MB
 -     500 個資料庫每週 = 3 GB

指令碼會排除不適合做為 Standard 彈性集區層的目前公用預覽提供項目的特定資料庫。如果您需要從目標伺服器排除其他資料庫，您可以變更指令碼以符合您的準則。根據預設，指令碼不會編譯下列項目的資訊：

* V12 伺服器上的所有 Premium 資料庫。
* V11 伺服器上的 P2 和 P3 資料庫。
* 彈性資料庫 (資料庫已在彈性集區中)。
* 伺服器的主要資料庫。

指令碼需要輸出資料庫來儲存中繼資料以供分析。您可以使用新的或現有的資料庫。雖然執行工具不需要太多技術，輸出資料庫應該在不同的伺服器，以避免影響分析結果。建議輸出資料庫的效能層級至少為 S0 或更高。長時間收集大量資料庫的資料時 ，您可以考慮將輸出資料庫升級至較高的效能層級。

指令碼需要您提供認證，才能連接到目標伺服器 (彈性資料庫集區候選項目) 與完整伺服器的名稱，如 “abcdef.database.windows.net”。指令碼目前不支援一次分析多部伺服器。



提交一組初始參數的值之後，會提示您登入您的 Azure 帳戶。這是用於登入您的目標伺服器，而不是輸出資料庫伺服器。
	
如果您在執行指令碼遇到下列警告，可加以忽略：

- 警告：Switch-AzureMode Cmdlet 已經過時。
- 警告：無法取得 SQL Server 服務資訊。嘗試連接到 'Microsoft.Azure.Commands.Sql.dll' 上的 WMI 失敗，並出現下列錯誤：RPC 伺服器無法使用。

指令碼完成時，它會輸出要包含目標伺服器中所有候選資料庫，彈性集區所需的 DTU 的估計數目。此估計的 DTU 可以用於建立及設定彈性集區，以保存這些資料庫。一旦建立集區並將資料庫移到集區，應該對它密切監控數日，並且視需要對集區 DTU 設定進行任何調整。


若要選取整個指令碼以進行複製，請在指令碼中的任何文字 3 次 (按三下)。

    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
     foreach ($db in $ListOfDBs)
     {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
     }
    
     $start_time = $start_time.AddMinutes($Waittime)
     $end_time = $end_time.AddMinutes($Waittime)
     Write-Host $start_time
     Write-Host $end_time
     do {
    Start-Sleep 1
    }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## 摘要

並非所有單一資料庫都是彈性資料庫集區的最佳候選項目。使用模式的特徵是低平均使用量與相對不頻繁的使用量高峰的資料庫，是彈性集區的絕佳候選項目。應用程式使用模式是動態的，因此，請使用這篇文章中所述的資訊及工具來進行初始評估，以查看彈性資料庫集區是否是部分或所有資料庫的良好選擇。這篇文章只是協助您決定彈性資料庫集區是否合適的起點。請記住，您應該持續監視歷程記錄資源使用量 (使用 STA 或 DMV)，並不斷重新評估所有的資料庫的效能層級。請記得，您可以輕鬆地將資料庫移入和移出彈性集區，而且如果您有非常大量的資料庫，則可以將資料庫分割，以具備不同大小的多個集區。



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=July15_HO2-->