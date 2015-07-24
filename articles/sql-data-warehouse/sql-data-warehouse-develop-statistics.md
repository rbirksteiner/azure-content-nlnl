<properties
   pageTitle="管理 SQL 資料倉儲中的統計資料 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中管理統計資料以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 管理 SQL 資料倉儲中的統計資料
 SQL 資料倉儲使用統計資料來評估以不同方式執行分散式查詢的成本。如果統計資料很精確，查詢最佳化工具即可產生高品質查詢計劃，以改善查詢效能。

建立和更新統計資料極為重要，以便達到 SQL 資料倉儲預計提供的查詢效能。本指南提供統計資料的概觀，然後顯示如何：

- 在資料庫設計階段建立統計資料
- 在資料庫維護階段更新統計資料
- 利用系統檢視和函式檢視統計資料

## 統計資料簡介

單一資料行統計資料是含有單一資料行中值範圍和次數相關資訊的物件。查詢最佳化工具會使用此長條圖來估計查詢結果中的資料列數目。這會直接影響如何最佳化查詢的相關決策。

多重資料行統計資料是對一份資料行清單建立的統計資料。其中包含清單中第一個資料行的單一資料行統計資料，再加上一些跨資料行關聯性資訊 (稱為密度)。多重資料行統計資料可以改善某些作業 (例如複合 joins 和 group by) 的查詢效能。

如需詳細資訊，請參閱 MSDN 上的 [DBCC SHOW_STATISTICS][]。

## 為何需要統計資料？
若無正確的統計資料，您將無法獲得 SQL 資料倉儲預計提供的效能。資料表和資料行都沒有 SQL 資料倉儲自動產生的統計資料，所以您需要自行建立。在建立資料表時建立統計資料，然後在您填入統計資料後予以更新是個不錯的主意。

> [AZURE.NOTE]如果您使用 SQL Server，您可能會視需要依賴 SQL Server 為您建立和更新單一資料行統計資料。SQL 資料倉儲在這方面有所不同。由於資料已分散，所以 SQL 資料倉儲不會自動彙總所有分散式資料的統計資料。它只會在您建立和更新統計資料時產生彙總統計資料。

## 何時建立統計資料
一組一致的最新統計資料是 SQL 資料倉儲的重要部分。因此，務必在設計資料表階段建立統計資料。

建立每個資料行的單一資料行統計資料是開始使用統計資料的簡單方式。不過，建立和更新統計資料的效能與成本之間總有一些取捨。如果您建立所有資料行的單一資料行統計資料，而後發現更新所有統計資料花費太長的時間，您可以捨棄一些統計資料，或較常更新其中一些統計資料。

只有在資料行位於複合 joins 或 group by 子句時，查詢最佳化工具才會使用多重資料行統計資料。複合篩選條件目前並未受益於多重資料行統計資料。

開始進行 SQL 資料倉儲開發時，實作下列模式是個不錯的主意：- 對每個資料表上的每個資料行建立單一資料行統計資料 - 對 joins 和 group by 子句中查詢所用的資料行，建立多重資料行統計資料。

當您了解要如何查詢您的資料時，您可能想要修改此模型 - 尤其在資料表的範圍很廣時。如需更進階的方法，請參閱 [實作統計資料管理] (## 實作統計資料管理) 一節。

## 何時更新統計資料
請務必在您的資料庫管理例行工作中納入更新統計資料。資料庫中的資料散發情況改變時，就需要更新統計資料。否則，您可看見次佳的查詢效能，而進一步排解查詢疑難所做的付出可能不值得。

因此，進行查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」

這個問題不是可依存留期回答的問題。最新的統計資料物件有可能非常舊。當資料列數目或特定資料行的值散發有實質變更時，您*就* 需要更新統計資料。

例如，資料倉儲中的日期資料行通常需要經常更新統計資料。每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。這些會改變資料散發情況並使統計資料過時。

相反地，客戶資料表上性別資料行的統計資料可能永遠不需要更新。假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。不過，如果資料倉儲只包含一種性別，而新的需求導致多種性別，您肯定需要更新性別資料行的統計資料。

如需進一步說明，請參閱 MSDN 上的[統計資料][]。

## 實作統計資料管理

擴充您的資料載入程序通常是個不錯的主意，可確保在載入結束時更新統計資料。當資料表變更其大小和/或其值散發時，資料載入最為頻繁。因此，這是實作某些管理程序的合理位置。

以下提供一些指導原則，以便在載入過程中更新您的統計資料：

- 確保每個載入的資料表至少都更新一個統計資料物件。這會在統計資料更新過程中更新資料表大小 (資料列計數和頁面計數) 資訊。
- 將焦點放在參與 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 考慮較頻繁更新「遞增索引鍵」資料行 (例如交易日期)，因為這些值不會包含在統計資料長條圖中。
- 考慮較不常更新靜態散發資料行。
- 請記得，每個統計資料物件會依序更新。僅只實作 `UPDATE STATISTICS <TABLE_NAME>` 可能不太理想 - 尤其是對具有許多統計資料物件的寬型資料表而言。

> [AZURE.NOTE]如需 [遞增索引鍵] 的詳細資訊，請參閱 SQL Server 2014 基數估計模型白皮書。

如需進一步說明，請參閱 MSDN 上的[基數估計][]。

## 範例：建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

### 答：使用預設選項建立單一資料行統計資料

若要建立資料行的統計資料，只需提供統計資料物件的名稱和資料行的名稱。

此語法會使用所有預設選項。根據預設，SQL 資料倉儲在建立統計資料時會取樣 20% 的資料表。

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

例如：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B.檢查每個資料列以建立單一資料行統計資料

20% 的預設取樣率足以應付大部分的情況。不過，您可以調整取樣率。

若要取樣整個資料表，請使用此語法：

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

例如：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C.指定取樣大小以建立單一資料行統計資料

或者，您可以以百分比指定取樣大小：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D.只對某些資料列建立單一資料行統計資料

另一個選項是，您可以對資料表中部分的資料列建立統計資料。這稱為篩選的統計資料。

例如，當您計劃查詢大型分割資料表的特定分割時，可以使用篩選的統計資料。只對分割值建立統計資料，統計資料的精確度將會改善，並因而改善查詢效能。

這個範例會建立某個值範圍的統計資料。您可以輕鬆地定義這些值以符合分割中的值範圍。

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]若要讓查詢最佳化工具在選擇分散式查詢計劃時考慮使用篩選的統計資料，查詢必須符合統計資料物件的定義。使用上述範例，查詢的 where 子句需要指定介於 2000101 和 20001231 之間的 col1 值。

### E.使用所有選項建立單一資料行統計資料

您當然可以將選項合在一起。以下範例會使用自訂樣本大小建立篩選的統計資料物件：

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

如需完整參考，請參閱 MSDN 上的 [CREATE STATISTICS][]。

### F.建立多重資料行統計資料

若要建立多重資料行統計資料，只需利用上述範例，但要指定更多資料行。

> [AZURE.NOTE]用來估計查詢結果中資料列數目的長條圖，只適用於統計資料物件定義中所列的第一個資料行。

在此範例中，長條圖位於 *product_category*。跨資料行統計資料會依據 *product_category* 和 *product_sub_category* 計算：

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

因為 *product_category* 和 *product_sub_category* 之間有關聯性，所以多重資料行統計資料在同時存取這些資料行時相當實用。

### G.對資料表中的所有資料行建立統計資料

建立統計資料的其中一個方法是在建立資料表後發出 CREATE STATISTICS 命令。

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1;
CREATE STATISTICS stats_col2 on dbo.table2;
CREATE STATISTICS stats_col3 on dbo.table3;
```

### H.使用預存程序對資料庫中的所有資料行建立統計資料

SQL 資料倉儲沒有相當於 SQL Server 中 [sp_create_stats][] 的系統預存程序。此預存程序會對資料庫中還沒有統計資料的每個資料行建立單一資料行統計資料物件。

這會協助您開始進行資料庫設計。請放心地依照您的需求進行調整。

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
WHERE       l.[object_id] IS NULL
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')'
        WHEN 2
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN'
        WHEN 3
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT'
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

若要利用此程序對資料表中的所有資料行建立統計資料，只需呼叫程序即可。

```
prc_sqldw_create_stats;
```

## 範例：更新統計資料

若要更新統計資料，您可以：

1. 更新一個統計資料物件。指定您要更新的統計資料物件名稱。
2. 更新資料表上的所有統計資料物件。指定資料表名稱，而不是一個特定統計資料物件。


### 答：更新一個特定統計資料物件 ###
使用下列語法來更新特定統計資料物件：

```
UPDATE STATISTICS ON [schema_name].[table_name]([stat_name]);
```

例如：

```
UPDATE STATISTICS ON [dbo].[table1] ([stats_col1]);
```

藉由更新特定統計資料物件，即可減少管理統計資料所需的時間和資源。這需要經過思考，才能選擇要更新的最佳統計資料物件。


### B.更新資料表上的所有統計資料。 ###
這顯示一個簡單的方法來更新資料表上的所有統計資料物件。

```
UPDATE STATISTICS ON [schema_name].[table_name];
```

例如：

```
UPDATE STATISTICS ON dbo.table1;
```

此陳述式很容易使用。只要記住這會更新資料表上的所有統計資料，因此可能會執行超出所需的更多工作。如果效能不成問題，這絕對是保證擁有最新統計資料的最簡單且最完整的方式。

> [AZURE.NOTE]更新資料表上的所有統計資料時，SQL 資料倉儲會進行掃描，以針對每個統計資料進行資料表取樣。如果資料表很大、有許多資料行以及許多統計資料，則根據需求來更新個別統計資料可能比較有效率。

如需 `UPDATE STATISTICS` 程序的實作，請參閱[暫存資料表]一文。實作方法與上述的 `CREATE STATISTICS` 程序有點不同，但最終結果相同。

如需完整語法，請參閱 MSDN 上的[更新統計資料][]。

## 統計資料中繼資料
您可利用數個系統檢視和函式來尋找統計資料相關資訊。例如，使用 stats-date 函式來查看最後建立或更新統計資料的時間，即可查看統計資料物件是否可能過期。

### 統計資料的目錄檢視
這些系統檢視提供統計資料的相關資訊：

| 目錄檢視 | 說明 |
| :----------- | :---------- |
| [sys.columns][] | 每個資料行有一個資料列。 |
| [sys.objects][] | 資料庫中每個物件有一個資料列。 | |
| [sys.schemas][] | 資料庫中每個結構描述有一個資料列。 | |
| [sys.stats][] | 每個統計資料物件有一個資料列。 |
| [sys.stats_columns][] | 統計資料物件中每個資料行有一個資料列。連結回到 sys.columns。 |
| [sys.tables][] | 每個資料表 (包括外部資料表) 有一個資料列。 |
| [sys.table_types][] | 每個資料類型有一個資料列。 |


### 統計資料的系統函式
這些系統函式很適合用於處理統計資料：

| 系統函式 | 說明 |
| :-------------- | :---------- |
| [STATS_DATE][] | 上次更新統計資料物件的日期。 |
| [DBCC SHOW_STATISTICS][] | 提供有關統計資料物件所理解之值散發的摘要層級和詳細資訊。 |

### 將統計資料資料行和函式結合成一個檢視

此檢視會一起顯示與統計資料相關的資料行，以及 [STATS_DATE()][] 函式的結果。

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     sts.[user_created] = 1
;
```

## DBCC SHOW_STATISTICS() 範例

DBCC SHOW_STATISTICS() 顯示統計資料物件中保存的資料。此資料來自三個部分。

1. 標頭
2. 密度向量
3. 長條圖

有關統計資料的標頭中繼資料。此長條圖會顯示統計資料物件的第一個索引鍵資料行中的值散發。密度向量可測量跨資料行關聯性。SQLDW 可使用統計資料物件中的任何資料來計算基數估計值。

### 顯示標頭、密度和長條圖

這個簡單範例顯示統計資料物件的所有三個部分。

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

例如：

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### 顯示 DBCC SHOW_STATISTICS(); 的一或多個部分

如果您只想要檢視特定部分，請使用 `WITH` 子句並指定您要查看哪些部分：

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

例如：

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## DBCC SHOW_STATISTICS() 差異
相較於 SQL Server，DBCC SHOW_STATISTICS() 在 SQL 資料倉儲中會更嚴格地實作。

1. 不支援未記載的功能
- 無法使用 Stats_stream
- 無法聯結特定統計資料子集的結果，例如 (STAT_HEADER JOIN DENSITY_VECTOR)
2. 無法針對訊息隱藏項目設定 NO_INFOMSGS
3. 無法使用統計資料名稱前後的方括號
4. 無法使用資料行名稱來識別統計資料物件
5. 不支援自訂錯誤 2767


## 後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop/
[暫存資料表]: ./sql-data-warehouse-develop-temporary-tables/

<!-- External Links -->
[基數估計]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[統計資料]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[更新統計資料]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=July15_HO1-->