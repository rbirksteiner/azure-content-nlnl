<properties
   pageTitle="SQL 資料倉儲中的檢視 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用 Transact-SQL 檢視開發解決方案的秘訣。"
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

 
# SQL 資料倉儲中的檢視

在 SQL 資料倉儲中檢視特別有用。以許多不同的方式使用，可以提升您的方案品質。

本文特別強調幾個範例，說明如何藉由實作檢視來豐富您的解決方案。此外，也必須考量一些限制。

## 架構抽象概念
極為常見的應用程式模式就是在載入資料時，使用後面接著物件重新命名模式的 CREATE TABLE AS SELECT (CTAS) 來重建資料表。

下列範例會將新的日期記錄加入至日期維度。請注意，如何第一次建立新的物件 DimDate_New，然後予以重新命名來取代原始物件版本。``` CREATE TABLE dbo.DimDate_New WITH (DISTRIBUTION = REPLICATE , CLUSTERED INDEX (DateKey ASC) ) AS SELECT * FROM dbo.DimDate AS prod UNION ALL SELECT * FROM dbo.DimDate_stg AS stg ;

RENAME OBJECT DimDate TO DimDate_Old; RENAME OBJECT DimDate_New TO DimDate;

``` 不過，這可能會導致資料表物件從 [SSDT SQL Server 物件總管] 的使用者檢視中出現與消失。檢視可為倉儲資料取用者提供一致的展示層，然而基礎物件已重新命名。透過檢視提供資料存取權，意味著使用者不需要基礎資料表的可見性。這會提供一致的使用者經驗，同時確保資料倉儲設計人員可以發展資料模型，也可在資料載入過程中使用 CTAS 充分發揮效能。

## 效能最佳化
檢視是在資料表之間強制執行效能最佳化聯結的明智方式。例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。另一個原因可能是強制執行特定查詢或聯結提示。這可確保聯結一律以最佳方式執行，而且與記得正確建構聯結的使用者無關。

## 限制
SQL 資料倉儲中的檢視僅限中繼資料使用。

因此無法使用下列選項：- 沒有結構描述繫結選項 - 無法透過檢視更新基底資料表 - 無法在暫存資料表上建立檢視 - 不支援 EXPAND / NOEXPAND 提示 - SQL 資料倉儲中沒有已編製索引的檢視


## 後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->

<!--Article references-->
[SQL 資料倉儲開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->