<properties
   pageTitle="SQL 資料倉儲的開發解決方案 | Microsoft Azure"
   description="使用 Azure SQL 資料倉儲建置解決方案的開發技術和最佳做法。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 資料倉儲開發解決方案

查看開發文章，進一步了解 SQL 資料倉儲的開發準則、 最佳做法和編碼技術。

## 重要概念
下列文章的重點在於使用 SQL 資料倉儲開發分散式資料倉儲時，必須了瞭解的一些重要概念和設計決策：

- [連接][]
- [並行][]
- [交易][]
- [使用者定義的結構描述][]
- [資料表設計][]
- [雜湊散發索引鍵][]
- [資料表分割][]
- [CTAS][]
- [統計資料][]

## 開發技術
這些文章的重點在於使用 SQL 資料倉儲開發解決方案的特定程式碼撰寫編碼技術、祕訣和最佳做法：

- [預存程序][]
- [標籤][]
- [檢視][]
- [暫存資料表][]
- [動態 SQL][]
- [迴圈][]
- [重新命名物件][]
- [樞紐分析資料][]
- [依據選項分組][]
- [變數指派][]

## 後續步驟
仔細閱讀開發文章之後，請查看 [Transact-SQL 參考資料][]頁面，以取得 SQL 資料倉儲支援的語法的詳細資料。

<!--Image references-->

<!--Article references-->
[並行]: sql-data-warehouse-develop-concurrency.md
[連接]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[動態 SQL]: sql-data-warehouse-develop-dynamic-sql.md
[依據選項分組]: sql-data-warehouse-develop-group-by-options.md
[雜湊散發索引鍵]: sql-data-warehouse-develop-hash-distribution-key.md
[標籤]: sql-data-warehouse-develop-label.md
[迴圈]: sql-data-warehouse-develop-loops.md
[樞紐分析資料]: sql-data-warehouse-develop-pivot-unpivot.md
[重新命名物件]: sql-data-warehouse-develop-rename.md
[統計資料]: sql-data-warehouse-develop-statistics.md
[預存程序]: sql-data-warehouse-develop-stored-procedures.md
[資料表設計]: sql-data-warehouse-develop-table-design.md
[資料表分割]: sql-data-warehouse-develop-table-partitions.md
[暫存資料表]: sql-data-warehouse-develop-temporary-tables.md
[交易]: sql-data-warehouse-develop-transactions.md
[使用者定義的結構描述]: sql-data-warehouse-develop-user-defined-schemas.md
[變數指派]: sql-data-warehouse-develop-variable-assignment.md
[檢視]: sql-data-warehouse-develop-views.md

[Transact-SQL 參考資料]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->