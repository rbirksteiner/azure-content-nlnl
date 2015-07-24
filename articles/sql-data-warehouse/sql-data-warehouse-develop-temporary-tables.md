<properties
   pageTitle="SQL 資料倉儲中的暫存資料表 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用暫存資料表開發解決方案的秘訣。"
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

# SQL 資料倉儲中的暫存資料表
暫存資料表存在於 SQL 資料倉儲中的工作階段層級。這類資料表會定義為本機暫存資料表，而與 SQL Server 資料表不同的是，它們可以從工作階段內的任何位置存取。

暫存資料表在處理資料時非常有用 - 尤其是具有暫時性中繼結果的轉換期間。

本文特別強調一些運用暫存資料表的特定方法，協助您將程式碼模組化。

## 模組化程式碼

在使用者工作階段中的任何位置均可看見暫存資料表的這項事實，可用於協助您將應用程式程式碼模組化。

讓我們舉個可行的範例。

下列預存程序會產生更新資料庫中每個資料行的統計資料所需的 DDL：

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
,   @sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION = HASH([seq_nmbr])
        ,   LOCATION     = USER_DB
        )
WITH t1
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
```

請注意，此資料實際上未做任何處理。此程序只產生了 DDL 並將其儲存在暫存資料表中。

不過，在 SQL 資料倉儲中有可能將該暫存資料表使用於建立它的程序之外。這與 SQL Server 有所不同。事實上，暫存資料表可以使用於工作階段內的**任何位置**。

這可能會導致更多模組化和可管理的程式碼。

```
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

在某些情況下，也可以使用這項技術取代內嵌和多重陳述式的函式。

> [AZURE.NOTE]您也可以擴充此解決方案。如果您只想要更新單一資料表，您只需要篩選 #stats_ddl 資料表

## 暫存資料表限制
SQL 資料倉儲在實作暫存資料表時的確有一些限制。

主要限制如下：

- 不支援全域暫存資料表
- 無法在暫存資料表上建立檢視


## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->