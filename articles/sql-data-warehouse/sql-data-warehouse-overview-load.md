<properties
   pageTitle="將資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="了解 SQL 資料倉儲的常見資料載入情況"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# 將資料載入 SQL 資料倉儲
SQL 資料倉儲提供許多選項，供載入各種案例的資料。例如，您可以：

- 一天一次載入許多大型批次、
- 全天分批載入較小的批次，或
- 對較小的 (檔案大小) 資料表進行簡單的更新

由於載入 SQL 資料倉儲的資料類型不同，所以每個案例的需求也不同。在本文中，我們會列出一些載入資料的選項。

## 技術
SQL 資料倉儲支援這些標準的資料載入工具：

- Azure Data Factory
- bcp 命令列公用程式
- PolyBase
- SQL Server Integration Services (SSIS)
- 協力廠商資料載入工具

### Azure Data Factory (ADF)
ADF 是完全受管理的服務，它能將資料儲存體、資料處理及資料移動服務組成簡化、可調整又可靠的資料生產管線。SQL 資料倉儲是 ADF [複製活動][]的[受支援來源/接收][]。

### bcp 命令列公用程式
**Bcp** 命令列可執行檔是一個 Microsoft 公用程式，可從 SQL Server、SQL Database 及 SQL 資料倉儲載入和擷取資料。如要開始使用，請依照[使用 bcp 載入資料][]教學課程中的說明執行。

### PolyBase
PolyBase 這項 Microsoft 技術可提供方法查詢 Hadoop 和 Azure 儲存體 blob 儲存體 (全都使用標準 Transact-SQL，而不使用 MapReduce)，進而簡化資料分析。PolyBase 也可從 Azure blob 儲存體將資料載入 SQL 資料倉儲。如要開始使用，請依照[使用 PolyBase 載入][]教學課程中的說明執行。

### SQL Server Integration Services (SSIS)
[SSIS][] 是建置企業級資料整合和轉換解決方案的平台。如要建置可連接 SQL 資料倉儲的封裝，請使用標準的 [SQL Server 目的地配接器][]。

### 協力廠商工具
SQL 資料倉儲可支援領導業界的資料載入解決方案。如需詳細資訊，請參閱我們的[解決方案合作夥伴][]清單。

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[使用 bcp 載入資料]: sql-data-warehouse-load-with-bcp.md
[使用 PolyBase 載入]: sql-data-warehouse-load-with-polybase.md
[解決方案合作夥伴]: sql-data-warehouse-solution-partners.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[受支援來源/接收]: https://msdn.microsoft.com/library/dn894007.aspx
[複製活動]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server 目的地配接器]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO1-->