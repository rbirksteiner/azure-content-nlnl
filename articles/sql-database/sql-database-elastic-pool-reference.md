<properties 
	pageTitle="Azure SQL 彈性資料庫集區參考" 
	description="這份參考提供了彈性資料庫集區文章和可程式性資訊的連結與詳細資料。" 
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


# SQL Database 彈性資料庫集區參考 (預覽)

對於擁有數十、數百或甚至數千個資料庫的 SaaS 開發人員而言，彈性資料庫集區可以簡化在整個資料庫群組中，進行建立、維護及管理流程的效能和成本。

這份參考提供了彈性集區文章和可程式性資訊的連結與詳細資料。

## 概觀

彈性集區是資料庫輸送量單位 (DTU) 的集合，也是由多個資料庫共用的儲存體 (GB)。您可以隨時在集區中加入和移除彈性資料庫。集區中的彈性資料庫只會利用集區中它們需要的資源，而集區也只會針對需要這些資源的作用中資料庫釋出可用資源。



## 建立和管理彈性集區的必要條件


- 只有 Azure SQL Database V12 伺服器才可以使用彈性集區。   
- 只有 Azure 資源管理員 (ARM) 支援彈性集區的 PowerShell 和 REST API；不支援服務管理命令 (RDFE)。 
- 只有 [Microsoft Azure 入口網站](https:portal.azure.com)中支援建立和管理彈性集區。 


## 目前的預覽版本限制

- 目前預覽版本中彈性集區的定價層為標準版。  
- 不支援直接將資料庫匯入彈性集區。您可以匯入獨立資料庫，然後將資料庫移到集區。支援從集區將資料庫匯出。


## 文章清單

以下文章可協助您開始使用彈性資料庫和彈性工作：

| 文章 | 說明 |
| :-- | :-- |
| [SQL Database 彈性集區](sql-database-elastic-pool.md) | 彈性集區概觀 |
| [使用 Azure 入口網站建立和管理 SQL Database 彈性集區](sql-database-elastic-pool-portal.md) | 如何使用 Azure 入口網站建立和管理彈性集區 |
| [使用 PowerShell 建立和管理 SQL Database 彈性集區](sql-database-elastic-pool-powershell.md) | 如何使用 PowerShell Cmdlet 建立和管理彈性集區 |
| [彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md) | 彈性工作服務的概觀，此服務可在集區中的所有彈性資料庫內執行 T-SQL 指令碼 |
| [安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md) | 如何安裝彈性資料庫工作服務 |
| [建立彈性工作服務所需的使用者](sql-database-elastic-jobs-add-logins-to-dbs.md) | 若要執行彈性資料庫工作指令碼，必須將具有適當權限的使用者加入至集區中的每個資料庫。 |
| [如何解除安裝彈性資料庫工作元件](sql-database-elastic-jobs-uninstall.md) | 嘗試安裝彈性資料庫工作服務失敗時加以復原 |



## 命名空間和端點詳細資料
彈性集區是 Microsoft Azure SQL Database 中 "ElasticPool" 類型的 ARM 資源。

- **namespace**：Microsoft.Sql/ElasticPool
- 用於 REST API 呼叫的 **secondary-endpoint** (Azure 資源管理員)：https://management.azure.com



## 彈性資料庫集區屬性

| 屬性 | 說明 |
| :-- | :-- |
| creationDate | 建立集區的日期。 |
| databaseDtuMax | 集區中單一資料庫可使用的最大 DTU 數。資料庫最大 DTU 不等於資源保證。最大 DTU 會套用至集區中的所有資料庫。 |
| databaseDtuMin | 集區中單一資料庫能夠保證的最小 DTU 數。資料庫最小 DTU 可設定為 0。最小 DTU 會套用至集區中的所有資料庫。請注意，集區中的資料庫數目和資料庫最小 DTU 的乘積不能超過集區本身的 DTU。 |
| Dtu | 集區中所有資料庫共用的 DTU 數。 |
| edition | 集區的服務層。集區中的每個資料庫均有此版本。 |
| elasticPoolId | 集區執行個體的 GUID。 |
| elasticPoolName | 集區的名稱。此名稱是其父伺服器的唯一相對名稱。 |
| location | 建立集區的資料中心位置。 |
| state | 如果訂閱的帳單付款有拖欠，則狀態為「已停用」，反之為「就緒」。 |
| storageMB | 集區的儲存體限制 (MB)。集區中的任何單一資料庫可以使用的上限為標準版的儲存體限制 (250 GB)，但集區中的所有資料庫使用的儲存體總計不能超過此集區限制。 |


## 彈性集區和彈性資料庫的 DTU 和儲存體限制

集區的儲存體限制取決於集區的 DTU 量；每個 DTU = 1 GB 的儲存體。例如，200 DTU 集區的儲存體限制為 200 GB。

| 屬性 | 預設值 | 有效值 |
| :-- | :-- | :-- |
| Dtu | 100 | 100、200、400、800、1200 |
| databaseDtuMax | 100 | 10、20、50、100 |
| databaseDtuMin | 0 | 0、10、20、50 |
| storageMB | 100 GB* | 100 GB、200 GB、400 GB、800 GB、1200 GB |

*API 中單位為 MB，而非 GB

## 背景工作和工作階段限制

支援彈性集區中所有資料庫的並行背景工作數上限和並行工作階段數上限，是取決於集區的 DTU 設定：

| DTU | 並行背景工作數上限 | 並行工作階段數上限 |
| :-- | :-- | :-- |
| 100 | 200 | 2,400 |
| 200 | 400 | 4,800 |
| 400 | 800 | 9,600 |
| 800 | 1,600 | 19,200 |
| 1,200 | 2,400 | 28,800 |


## Azure 資源管理員限制

使用彈性集區需要 Azure SQL Database V12 伺服器。伺服器均位於資源群組內。

- 每個資源群組最多可以有 800 部伺服器。
- 每部伺服器最多可以有 800 個彈性集區。
- 每個彈性集區最多可以有 100 個資料庫。


## 彈性集區的作業延遲

- 每個資料庫的保證 DTU 數 (databaseDtuMin) 或每個資料庫的最大 DTU 數 (databaseDtuMax) 變更作業通常在 5 分鐘內即可完成。 
- 集區之 DTU/儲存體限制 (storageMB) 的變更作業，則需視集區中所有資料庫使用的總空間量而定。變更作業平均每 100 GB 會在 90 分鐘以內完成。舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則集區 DTU/儲存體限制變更作業的預期延遲時間會少於 3 小時。 



## 彈性資料庫集區 PowerShell Cmdlet 和 REST API 命令 (僅限 Azure 資源管理員)

下列 PowerShell Cmdlet 和 REST API 命令可用來建立及管理彈性集區：

| [PowerShell Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx) | [REST API 命令](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Get Azure SQL database |
| Get-AzureSqLElasticPool | Get Azure SQL Database elastic database pool |
| Get-AzureSqlElasticPoolActivity | Get Azure SQL Database elastic database pool operations |
| Get-AzureSqlElasticPoolDatabase | Get Azure SQL Database elastic database |
| Get-AzureSqlElasticPoolDatabaseActivity | Get Azure SQL Database elastic database operations |
| Get-AzureSqlServer | Get Azure SQL Database server |
| Get-AzureSqlServerFirewallRule | Get Azure SQL Database server firewall rule |
| Get-AzureSqlServerServiceObjective | Get Azure SQL Database server service objective |
| New-AzureSqlDatabase | Create Azure SQL database |
| New-AzureSqlElasticPool | Create Azure SQL Database elastic database pool |
| New-AzureSqlServer | Create Azure SQL Database server |
| New-AzureSqlServerFirewallRule | Create Azure SQL Database server firewall rule |
| Remove-AzureSqlDatabase | Remove Azure SQL database |
| Remove-AzureSqlElasticPool | Remove Azure SQL Database elastic database pool |
| Remove-AzureSqlServer | Remove Azure SQL Database server |
| Set-AzureSqlDatabase | Set Azure SQL database |
| Set-AzureSqlElasticPool | Set Azure SQL Database elastic database pool |
| Set-AzureSqlServer | Set Azure SQL Database server |
| Set-AzureSqlServerFirewallRule | Set Azure SQL Database server firewall rule |
| Get-Metrics | Get Metrics |


## 計費和定價資訊

彈性資料庫集區會依據下列特性計費：

- 彈性集區在建立時就會開始計費，即使集區中沒有資料庫也一樣。 
- 彈性集區會以每小時計費。這和獨立資料庫效能等級的計量頻率相同。 
- 如果彈性集區調整為新的 DTU 量，則在調整作業完成之前，不會根據新的 DTU 量來計算集區費用。這會遵循與變更獨立資料庫的效能等級相同的模式。 


- 彈性集區的價格是以集區的 DTU 數和集區中的資料庫數目為計算基礎。
- 價格的計算方式為 (集區的 DTU 數) x (每 DTU 的單價) + (資料庫數目) x (每個資料庫的單價)

在同一個服務層中，彈性集區的 DTU 單價大於獨立資料庫的 DTU 單價。如需詳細資訊，請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。

## 彈性資料庫集區錯誤

| 錯誤號碼 | 錯誤嚴重性 | 錯誤格式 | 錯誤說明 | 錯誤原因 | 錯誤修正動作 |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | 彈性集區已達到其儲存體限制。彈性集區的儲存體使用量不能超過 (%d) MB。 | 彈性集區空間限制 (MB)。 | 當彈性集區達到儲存體限制時，嘗試將資料寫入資料庫。 | 請考慮盡可能增加彈性集區的 DTU 數以提高其儲存體限制、減少彈性集區中個別資料庫所使用的儲存體量，或是從彈性集區移除資料庫。 |
| 10929 | EX_USER | %s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。請參閱 [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) 以取得協助。或者，請稍後再試一次。 | 每個資料庫的最小 DTU、每個資料庫的最大 DTU | 彈性集區中的所有資料庫並行背景工作 (要求) 總數試圖超過集區限制。 | 請考慮盡可能增加彈性集區的 DTU 數以提高其背景工作數的限制，或是從彈性集區移除資料庫。 |
| 40844 | EX_USER | 伺服器 '%ls' 上的資料庫 '%ls' 是彈性集區中的 '%ls' 版資料庫，且無法有連續複製關聯性。 | 資料庫名稱、資料庫版本、伺服器名稱 | 針對彈性集區中的非高階資料庫發出了 StartDatabaseCopy 命令。 | 敬請期待 |
| 40857 | EX_USER | 找不到伺服器 '%ls' 的彈性集區，彈性集區名稱: '%ls'。 | 伺服器名稱、彈性集區名稱 | 指定的彈性集區不存在於指定的伺服器中。 | 請提供有效的彈性集區名稱。 |
| 40858 | EX_USER | 彈性集區 '%ls' 已存在於伺服器 '%ls' 中 | 彈性集區名稱、伺服器名稱 | 指定的彈性集區已存在於指定的邏輯伺服器中。 | 請提供新的彈性集區名稱。 |
| 40859 | EX_USER | 彈性集區不支援服務層 '%ls'。 | 彈性集區服務層 | 指定的服務層不支援彈性集區佈建。 | 請提供正確的版本，或者將服務層保留空白，以使用預設的服務層。 |
| 40860 | EX_USER | 彈性集區 '%ls' 和服務目標 '%ls' 的組合無效。 | 彈性集區名稱、服務層級目標名稱 | 如果服務目標指定為 'ElasticPool'，才能同時指定彈性集區和服務目標。 | 請指定正確的彈性集區和服務目標組合。 |
| 40861 | EX_USER | 資料庫版本 '%.*ls' 不能和彈性集區服務層 ('%.*ls') 不同。 | 資料庫版本、彈性集區服務層 | 資料庫版本和彈性集區服務層不同。 | 請勿指定和彈性集區服務層不同的資料庫版本。請注意，不需要指定資料庫版本。 |
| 40862 | EX_USER | 指定彈性集區服務目標時，必須指定彈性集區名稱。 | None | 彈性集區服務目標不是彈性集區的唯一識別依據。 | 使用彈性集區服務目標時，請指定彈性集區名稱。 |
| 40864 | EX_USER | 服務層 '%.*ls' 的彈性集區 DTU 數必須至少為 (%d) 個 DTU。 | 彈性集區的 DTU 數、彈性集區服務層。 | 試圖將彈性集區的 DTU 數設為低於最小限制。 | 請嘗試將彈性集區的 DTU 數至少設為等於最小限制。 |
| 40865 | EX_USER | 服務層 '%.*ls' 的彈性集區 DTU 數不可超過 (%d) 個 DTU。 | 彈性集區的 DTU 數、彈性集區服務層。 | 試圖將彈性集區的 DTU 數設為高於最大限制。 | 請嘗試將彈性集區的 DTU 數設為低於最大限制。 |
| 40867 | EX_USER | 服務層 '%.*ls' 的每個資料庫最大 DTU 必須至少為 (%d)。 | 每個資料庫的最大 DTU、彈性集區服務層。 | 試圖將每個資料庫的最大 DTU 設為低於支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40868 | EX_USER | 服務層 '%.*ls' 的每個資料庫最大 DTU 不可超過 (%d)。 | 每個資料庫的最大 DTU、彈性集區服務層。 | 試圖將每個資料庫的最大 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40870 | EX_USER | 服務層 '%.*ls' 的每個資料庫最小 DTU 不可超過 (%d)。 | 每個資料庫的最小 DTU、彈性集區服務層。 | 試圖將每個資料庫的最小 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層。 |
| 40873 | EX_USER | 資料庫數目 (%d) 和每個資料庫的最小 DTU (%d) 不能超過彈性集區的 DTU 數 (%d)。 | 彈性集區中的資料庫數目、每個資料庫的最小 DTU、彈性集區的 DTU 數。 | 試圖針對彈性集區中的資料庫指定最小 DTU 而超過彈性集區的 DTU 數。 | 請考慮增加彈性集區的 DTU 數，或減少每個資料庫的最小 DTU，或是減少彈性集區中的資料庫數目。 |
| 40877 | EX_USER | 除非不包含任何資料庫，否則無法刪除彈性集區。 | None | 彈性集區包含一或多個資料庫，因此無法刪除。 | 若要刪除彈性集區，請移除其中的資料庫。 |
| 40881 | EX_USER | 彈性集區 '%.*ls' 已達到其資料庫計數上限。如果彈性集區的 DTU 為 (%d) 個，則彈性集區的資料庫計數上限不能超過 (%d)。 | 彈性集區名稱、彈性集區的資料庫計數上限、資源集區的 DTU 數。 | 當達到彈性集區的資料庫計數上限時，試圖建立資料庫，或將資料庫加入至彈性集區。 | 請考慮盡可能增加彈性集區的 DTU 數以提高其資料庫限制，或是從彈性集區移除資料庫。 |
| 40889 | EX_USER | 不能減少彈性集區 '%.*ls' 的 DTU 數或儲存體限制，因為這麼做會無法提供足夠的儲存空間給位於其中的資料庫。 | 彈性集區名稱。 | 試圖將彈性集區的儲存體限制減少為低於其儲存體使用量。 | 請考慮減少彈性集區中之個別資料庫的儲存體使用量，或從集區中移除資料庫，以便減少集區的 DTU 數或儲存體限制。 |
| 40891 | EX_USER | 每個資料庫的最小 DTU (%d) 不能超過每個資料庫的最大 DTU (%d)。 | 每個資料庫的最小 DTU、每個資料庫的最大 DTU。 | 試圖將每個資料庫的最小 DTU 設為超過每個資料庫的最大 DTU。 | 請確定每個資料庫的最小 DTU 並未超過每個資料庫的最大 DTU。 |
| TBD | EX_USER | 彈性集區中個別資料庫的儲存體大小，不能超過 '%.*ls' 服務層彈性集區所允許的大小上限。 | 彈性集區服務層 | 資料庫的大小上限超過彈性集區服務層所允許的大小上限。 | 請將資料庫的大小上限設定在彈性集區服務層所允許的大小上限內。 |

<!---HONumber=62-->