<properties
   pageTitle="SQL 資料倉儲中的迴圈 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用 Transact-SQL 迴圈及取代資料指標以開發解決方案的秘訣。"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 資料倉儲中的迴圈
SQL 資料倉儲支援 [WHILE][] 迴圈重複執行陳述式區塊。只要指定的條件都成立，或者在程式碼使用 `BREAK` 關鍵字特別終止迴圈之前，這個情況都會繼續下去。迴圈特別適用於取代 SQL 程式碼中定義的資料指標。幸運的是，幾乎所有以 SQL 程式碼撰寫的資料指標都是向前快轉，並且只讀取多樣性。因此，如果您發現自己必須將其取代，[WHILE] 迴圈是絕佳的替代方案。

## 運用迴圈和取代 SQL 資料倉儲中的資料指標
不過，在深入說明之前，您應該先自問下列問題：「這個資料指標是否能重新撰寫以使用集合型作業？」。在許多情況下，答案為「是」。通常最好的方法就是這麼做。集合型作業的執行速度通常會比反覆的逐列方法還要快。

向前快轉唯讀資料指標可以輕鬆地以迴圈建構取代。以下是一個傳遞此方法的簡單範例。程式碼範例會更新資料庫中每個資料表的統計資料。藉由反覆迴圈中的資料表，我們就能夠依序執行每個命令。

首先，建立暫存資料表，其中包含用來識別個別陳述式的唯一資料列數目：
  
```
CREATE TABLE #tbl 
WITH 
(   LOCATION     = USER_DB
,   DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) ) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

其次，初始化執行迴圈的必要變數：

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

現在每次對一個陳數式執行一次迴圈：

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後，將第一個步驟中建立的暫存資料表卸除

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=July15_HO1-->