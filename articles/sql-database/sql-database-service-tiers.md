<properties
   pageTitle="SQL Database 服務層"
   description="比較 Azure SQL Database 各個服務層的效能和商務持續性功能，根據停機時間的需求，在成本和功能之間找出適當的平衡。"
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="04/15/2015"
   ms.author="shkurhek"/>

# 服務層

「基本」、「標準」和「高階」服務層皆提供 [SQL Database](sql-database-technical-overview.md)。這所有三個服務層都具備 99.99% 的執行時間 [SLA](http://azure.microsoft.com/support/legal/sla/)，並且提供可預測的效能、彈性的商務持續性選項、安全性功能，以及小時計費。每個服務層內有多個效能層級，您可以彈性選擇最符合您工作負載需求的層級。如果您需要相應增加或相應減少，可以很輕鬆地在 Azure 入口網站中變更資料庫層，您的應用程式完全不需要停機。如需詳細資訊，請參閱[變更資料庫服務層和效能層級](https://msdn.microsoft.com/library/azure/dn369872.aspx)。

> [AZURE.IMPORTANT]Web 和商務版本正逐漸遭到淘汰。了解如何[升級 Web 和商務版本](sql-database-upgrade-new-service-tiers.md)。如果您打算繼續使用 Web 和商務版本，請閱讀[終止常見問題集](http://azure.microsoft.com/pricing/details/sql-database/web-business/)。

## 基本

基本層專為少量交易工作負載的應用程式而設計。典型的使用案例是輕量級應用程式，只需要任何時間點進行單一作業的小型資料庫。

**效能、大小和功能**


| 服務層 | 基本 |
| :-- | :-- |
| 資料庫輸送量單位 (DTU) | 5 |
| 資料庫大小上限 | 2 GB |
| 時間點還原 (PITR) | 最遠到過去 7 天內的毫秒 |
| 災害復原 | 異地還原，還原到任何 Azure 區域 |
| 效能目標 | 每小時交易率 |


## 標準

標準層適合開始使用交易式工作負載。相較於基本層，標準層提供更好的效能和更好的內建商務持續性功能。典型的使用案例是具有多個並行交易的應用程式。

**效能和大小**


| 服務層 | 標準 S0 | 標準 S1 | 標準 S2 | 標準 S3 |
| :-- | :-- | :-- | :-- | :-- |
| 資料庫輸送量單位 (DTU) | 10 | 20 | 50 | 100 |
| 資料庫大小上限 | 250 GB | 250 GB | 250 GB | 250 GB |


**功能**


| 服務層 | 標準 (S0、S1、S2、S3) |
| :-- | :-- |
| 時間點還原 (PITR) | 最遠到過去 14 天內的毫秒 |
| 災害復原 | 標準異地複寫，1 個離線次要複本 |
| 效能目標 | 每分鐘交易率 |


## 高級

高階層專為任務關鍵應用程式而設計。不僅提供最佳的效能層級，還能夠存取進階的商務持續性功能，包括最多 4 個自選 Azure 區域的作用中異地複寫。典型的使用案例是具有高交易量和多位並行使用者的關鍵任務應用程式。

**效能和大小**


| 服務層 | 高階 P1 | 高階 P2 | 高階 P3 |
| :-- | :-- | :-- | :-- |
| 資料庫輸送量單位 (DTU) | 125 | 250 | 1000 |
| 資料庫大小上限 | 500 GB | 500 GB | 500 GB |


**功能**


| 服務層 | 高階 (P1、P2、P3) |
| :-- | :-- |
| 時間點還原 (PITR) | 最遠到過去 35 天內的毫秒 |
| 災害復原 | 作用中異地複寫，最多 4 個線上可讀取的次要複本 |
| 效能目標 | 每秒交易率 |


請在 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)進一步了解這些層的定價。

如果您認識了 SQL Database 各個層，可以[免費試用](http://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started.md)！
 

<!---HONumber=July15_HO2-->