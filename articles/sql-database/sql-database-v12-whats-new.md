<properties 
	pageTitle="SQL Database V12 的新功能 | Microsoft Azure" 
	description="說明在雲端使用 Azure SQL Database 的業務系統為何可在立即升級至 V12 版本之後受益。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="jeffreyg"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/10/2015" 
	ms.author="genemi"/>


# SQL Database V12 新功能


本主題說明許多 Azure SQL Database V12 版本優於 V11 版本的新優點


我們持續新增 V12 的功能。因此我們鼓勵您造訪我們的 Azure 服務更新網頁，並使用它的篩選條件：


- 篩選出 [SQL Database 服務](http://azure.microsoft.com/updates/?service=sql-database)。
- 篩選出 SQL 資料庫功能的「正式運作 [(GA)」公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。


## 提升與 SQL Server 的應用程式相容性


SQL Database V12 的一個主要目標就是要提高與 Microsoft SQL Server 2014 的相容性。在其他區域中，V12 在可程式性的重要區域中已達成 SQL Server 同位檢查例如：


- [Common Language Runtime (CLR) 組件](http://msdn.microsoft.com/library/ms189524.aspx)
- [Window 函數](https://msdn.microsoft.com/library/bb934097.aspx) (包含 [OVER](http://msdn.microsoft.com/library/ms189461.aspx)) 
- [XML 索引](https://msdn.microsoft.com/library/bb934097.aspx)和[選擇性 XML 索引](http://msdn.microsoft.com/library/jj670104.aspx)
- [變更追蹤](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [全文檢索搜尋](http://msdn.microsoft.com/library/ms142571.aspx)


如需了解 SQL Database 中尚未支援的少部分功能，請參閱[這裡](http://msdn.microsoft.com/library/azure/ee336281.aspx)。


## 更多高階效能，新的效能層級


在 V12 中，我們將分配給所有高階效能層級的資料庫輸送量單位 (DTU) 提高了 25% 且不需要額外成本更可透過新功能進一步提升效能，例如：


- 支援記憶體內部[資料行存放區索引](http://msdn.microsoft.com/library/gg492153.aspx)。
- 利用 [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx) 相關增強功能，[依據資料列進行資料表分割](http://msdn.microsoft.com/library/ms187802.aspx)
- 可使用動態管理檢視 [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) 和擴充的事件 [(XEvents)](https://msdn.microsoft.com/library/bb630282.aspx) 來協助監視和調整效能。


## 更有效地支援雲端 SaaS 廠商


我們只在 V12 中發佈新的 Standard 效能層級 S3 與[彈性資料庫集區](sql-database-elastic-pool.md)公用預覽這是專為雲端 SaaS 廠商所設計的解決方案。使用彈性資料庫集區，您可以：


- 在資料庫之間共用 DTU，以降低大量資料庫的成本。
- 執行[彈性資料庫工作](sql-database-elastic-jobs-overview.md)以大規模管理資料庫。


## 安全性增強功能


對於在雲端營運業務的任何人來說，安全性是一個主要考量。V12 中發行的最新安全性功能包括：


- [資料列層級安全性](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)
- [自主資料庫](http://msdn.microsoft.com/library/azure/ff394108.aspx)
- 使用 GRANT、DENY、REVOKE 管理的[應用程式角色](http://msdn.microsoft.com/library/ms190998.aspx)
- [透明資料加密](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)


## 當需要復原時提升業務持續性


V12 提供大幅改進的復原點目標 (RPO) 與預估復原時間 (ERT)：


| 業務續航力功能 | 較早版本 | V12 |
| :-- | :-- | :-- |
| 異地還原 | • RPO < 24 小時。<br/>• ERT < 12 小時。 | • RPO < 1 小時。<br/>• ERT < 12 小時。 |
| 標準異地複寫 | • RPO < 30 分鐘。<br/>• ERT < 2 小時。 | • RPO < 5 秒。<br/>• ERT < 30 秒。 |
| 主動式異地複寫 | • RPO < 5 分鐘。<br/>• ERT < 1 小時。 | • RPO < 5 秒。<br/>• ERT < 30 秒。 |


如需詳細資訊，請參閱 [SQL Database 業務持續性](https://msdn.microsoft.com/library/azure/hh852669.aspx)。


## 更多需要立即升級的理由


客戶有許多很好的理由應該立即從 V11 升級到 Azure SQL Database V12：


- SQL Database V12 新增了許多 V11 沒有的新功能。
- 我們持續新增 V12 的功能，但是 V11 不會新增功能。
- 大部分的新功能在發佈給 Microsoft SQL Server 之前，都已在 SQL Database V12 上發佈。


## 您已經在使用 V12 了嗎？


查看您是否已經具備執行較早版本 SQL Database 服務的資料庫或邏輯伺服器的一個簡單方法是執行下列動作：


1. 移至 [Azure Preview 入口網站](http://portal.azure.com/)。
2. 按一下 [瀏覽]。
3. 按一下 [SQL Server]。
4. 您的伺服器或資料庫旁邊的圖示會告知詳情：
 - ![V12 伺服器圖示](./media/sql-database-v12-whats-new/v12_icon.png) **V12 邏輯伺服器**
 - ![較早版本的伺服器圖示](./media/sql-database-v12-whats-new/earlier_icon.png) **較早版本的邏輯伺服器**


確定版本的另一個方法是在資料庫中執行 `SELECT @@version;` 陳述式，並檢視類似於下面所示的結果：


- **12**.0.2000.10 &nbsp; *(版本 V12)*
- **11**.0.9228.18 &nbsp; *(版本 V11)*


V12 資料庫只能裝載在 V12 邏輯伺服器上。而 V12 只可以裝載 V12 資料庫。


如果您尚未於 V12 上執行，您可以依照下列[就地升級至 SQL Database V12](sql-database-v12-upgrade.md) 中的步驟，升級您的邏輯伺服器。


## <a name="V12AzureSqlDbPreviewGaTable"></a>預覽區域


V12 已於 2014 年 12 月發行，但是僅以[預覽](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態提供。到了 2015 年 4 月，V12 幾乎在所有的地理區域中都已「公開上市 (GA)」。


V12 在下列區域，僅提供「預覽」：


| Azure 區域 | V12 的目前<br/>版本狀態 | 升級至 GA<br/> 的日期 |
| :--- | :--- | :--- |
| 澳洲東部 | **預覽** | 2015 年第二季 (估計) |
| 澳洲東南部 | **預覽** | 2015 年第二季 (估計) |

 

<!---HONumber=July15_HO2-->