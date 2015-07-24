<properties 
	pageTitle="建立和管理 SQL Database 彈性資料庫集區 (預覽)" 
	description="建立單一資源集區供 Azure SQL Database 的群組共用。" 
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


# 建立和管理 SQL Database 彈性集區 (預覽)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

本文將說明如何使用 [Azure 入口網站](https://portal.azure.com)建立彈性集區。

對於大量的資料庫而言，彈性集區可簡化建立、維護和管理流程的效能與成本。
 

> [AZURE.NOTE]彈性集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。

 


## 必要條件

若要建立彈性集區，您需要下列項目：

- Azure 訂用帳戶！ 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database V12 伺服器。如果沒有 V12 伺服器，請遵循以下文章中的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。



## 建立彈性集區

將新的集區加入至伺服器，以建立彈性集區。您可以將多個集區加入至伺服器，但可以與每個集區相關聯的伺服器只有一部。此外，可以將伺服器上的所有或部分資料庫加入至集區。


1.	選取 SQL Database V12 伺服器 (其中含有您想加入至集區的資料庫)。
2.	選取 [SQL Server] 刀鋒視窗頂端的 [新增集區]，以建立集區。

   ![建立彈性集區][1]

## 設定彈性集區

設定集區的方式為，設定定價層、加入資料庫，以及設定集區的效能特性。

*當您選取 [新增集區] 命令時，必須選取 [預覽版條款]，並完成 [預覽版條款] 刀鋒視窗中的項目，藉此接受預覽版的條款。各個訂用帳戶只需要接受條款一次。*

   ![設定彈性集區][2]


### 定價層

彈性集區的定價層有點類似 SQL Database 的服務層。定價層會判斷集區中彈性資料庫可用的功能，還有最大數目的 DTU (DTU MAX)，以及每個資料庫可用的儲存體 (GB)。

> [AZURE.NOTE]此預覽版目前僅限於「標準」定價層。

| 定價層 | 每個資料庫的最大 DTU |
| :--- | :--- |
| 標準 | 每個資料庫最多 100 個 DTU |

### 新增資料庫

不論何時，您都可以選取想要包含在集區中的特定資料庫。當您建立新的集區時，Azure 會建議可在集區中獲益的資料庫，並標示這些資料庫以供納入。您可以加入伺服器上所有可用的資料庫，或視需要選取或清除初始清單中的資料庫。

   ![新增資料庫][5]

當您選取要加入至集區的資料庫時，必須符合下列條件：

- 集區必須要有資料庫的放置空間 (已經包含的資料庫數目不得為最大數目)。更明確地說，集區必須要有足夠的可用 DTU，才能涵蓋每個資料庫的 DTU 保證。例如，如果群組的 DTU 保證是 400，而每個資料庫的 DTU 保證是 10，則集區中允許的資料庫數目上限為 40 (400 個 DTU 除以每個資料庫保證的 10 個 DTU = 最多 40 個資料庫)。
- 集區中必須要有資料庫目前所使用的功能。 


### 設定效能

您可以藉由設定集區和集區中彈性資料庫的效能參數，來設定集區的效能。請記住，彈性資料庫設定會套用到集區中的所有資料庫。

   ![設定彈性集區][3]

您可以設定三種參數以定義集區效能：集區的 DTU 保證、集區中彈性資料庫的最小 DTU (DTU MIN) 和最大 DTU (DTU MAX)。下表說明每個參數，並提供一些如何設定這些參數的指引。

| 效能參數 | 說明 |
| :--- | :--- |
| **POOL DTU/GB**：集區的 DTU 保證 | 集區的 DTU 保證是保證的可用 DTU 數目，且會由集區中的所有資料庫共用這些 DTU。<br> 目前，您可以將此參數設為 100、200、400、800 或 1200。<br>佈建特定大小的群組 DTU 保證時，應該考慮群組的 DTU 使用量記錄。或者，也可以根據每個資料庫所需的 DTU 保證，以及在同時作用中資料庫的使用量，來設定這個大小。集區的 DTU 保證也與集區的可用儲存體量相互關聯，您配置給集區的每個 DTU，都會成為 1 GB 的資料庫儲存體 (1 DTU = 1 GB 的儲存體)。<br> **我應該將集區的 DTU 保證設定為？** <br>您至少應該將集區的 DTU 保證設為 ([資料庫數目] x [每個資料庫的 DTU 平均使用量]) |
| **DTU MIN**：每個資料庫的 DTU 保證 | 每個資料庫的 DTU 保證是集區中單一資料庫能夠保證的 DTU 數。目前，您可以將這項保證設定為 0、10、20 或 50 個 DTU，或者您可以選擇不提供保證給群組中的資料庫 (DTU MIN = 0)。<br> **我應該將每個資料庫的 DTU 保證設定為？** <br> 您至少應該將每個資料庫的 DTU 保證 (DTU MIN) 設為 ([每個資料庫的平均使用量])。每個資料庫的 DTU 保證是全域設定，會設定集區中所有資料庫的 DTU 保證。 |
| **DTU MAX**：每個資料庫的 DTU 上限 | 每個資料庫的 DTU 上限是集區中單一資料庫可使用的最大 DTU 數。將每個資料庫的 DTU 上限設定得夠高，才能有效處理資料庫可能會遇到的高負載量或暴增量。您可以將此上限設為系統限制的最大值，這會取決於集區的定價層 (標準版為 100 個 DTU)。此上限的特定大小應該配合群組中資料庫的尖峰使用量模式。某種程度的群組過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。<br> **我應該將每個資料庫的 DTU 上限設定為？** <br> 請將每個資料庫的 DTU MAX 或 DTU 上限設為 ([資料庫尖峰使用量])。例如，假設每個資料庫的尖峰使用量是 50 個 DTU，且群組中的 100 個資料庫只有 20% 會同時暴增到尖峰。如果每個資料庫的 DTU 上限設為 50 個 DTU，則以 5 倍的量過量使用群組，並將群組的 DTU 保證設為 1,000 個 DTU 都是合理的。此外值得注意的是，DTU 上限不等於資料庫的資源保證，這只是情況許可時能達到的 DTU 上限而已。 |


## 將資料庫加入至彈性集區

建立集區後，您可以在集區內或在集區外新增或移除資料庫。


## 監視和管理彈性集區

建立彈性集區後，您可以瀏覽至現有集區的清單，然後選取所需的集區，藉此監視和管理入口網站中的集區。

建立集區後，您可以：

- 選取 [設定集區] 以變更集區 DTU 和每個資料庫的 DTU 設定。
- 選取 [建立工作]，然後建立彈性工作以管理集區中的資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。
- 選取 [管理工作] 以管理現有的彈性工作。



![監視彈性集區][8]




![監視彈性集區][4]

當您選取現有的集區時，會看見集區的資源使用率。按一下 [資源使用率] 圖表，開啟 [度量] 刀鋒視窗，便可在此處自訂圖表和設定警示。


![資源使用率][6]

按一下 [編輯圖表] 加入參數，以便輕鬆地檢視集區的遙測資料。


![編輯圖表][7]







## 後續步驟
建立彈性集區後，可以藉由建立彈性工作來管理集區中的資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。

如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。



## 相關參考

- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用 PowerShell 建立 SQL Database 彈性集區](sql-database-elastic-pool-powershell.md)
- [彈性資料庫參考](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png

<!---HONumber=62-->