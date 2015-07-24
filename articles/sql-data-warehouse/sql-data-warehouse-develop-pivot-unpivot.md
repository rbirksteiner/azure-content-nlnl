<properties
   pageTitle="樞紐和取消樞紐 SQL 資料倉儲中的資料 | Microsoft Azure"
   description="樞紐和取消樞紐 Azure SQL 資料倉儲中的資料以開發解決方案的秘訣。"
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

# 樞紐和取消樞紐 SQL 資料倉儲中的資料

您可以使用 CASE 陳述式樞紐 SQL 資料倉儲中的資料。

本文包含兩個簡單範例，關於如何同時樞紐和取消樞紐資料表，而不需使用在 SQL Server 中找到的樞紐和取消樞紐語法。

## 樞紐

```
CREATE TABLE AnnualSales_pvt
WITH    (   DISTRIBUTION = ROUND_ROBIN
        )
AS
WITH SalesPVT 
AS
(   SELECT  [EnglishProductCategoryName]
    ,       CASE WHEN [CalendarYear] = 2001 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2001'
    ,       CASE WHEN [CalendarYear] = 2002 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2002'
    ,       CASE WHEN [CalendarYear] = 2003 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2003'
    ,       CASE WHEN [CalendarYear] = 2004 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2004'
    FROM    [dbo].[FactInternetSales] s
    JOIN    [dbo].[DimDate] d               ON s.[OrderDateKey]          = d.[DateKey]
    JOIN    [dbo].[DimProduct] p            ON s.[ProductKey]            = p.[ProductKey]
    JOIN    [dbo].[DimProductSubCategory] u ON p.[ProductSubcategoryKey] = u.[ProductSubcategoryKey]
    JOIN    [dbo].[DimProductCategory] c    ON u.[ProductCategoryKey]    = c.[ProductCategoryKey]
    GROUP BY 
            [EnglishProductCategoryName]
    ,       [CalendarYear]
)
SELECT  [EnglishProductCategoryName]
,       SUM([CY_2001])  AS 'CY_2001'
,       SUM([CY_2002])  AS 'CY_2002'
,       SUM([CY_2003])  AS 'CY_2003'
,       SUM([CY_2004])  AS 'CY_2004'
FROM    SalesPVT
GROUP BY 
        [EnglishProductCategoryName]
;
```

## 取消樞紐

取消樞紐有點複雜。不過，它仍然可以使用 `CASE` 達成。為此，您也必須先判斷要取消樞紐多少資料行。在上一個範例中，我們樞紐了四個資料行。讓我們維持原意。若要執行 Unpivot，我們必須暫時增強 4 倍的資料集。這會導致兩個步驟的程序：

首先，建立暫存資料表，其中包含四個資料列。我們將使用此資料表增強資料：

```
CREATE TABLE #Nmbr
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION     = USER_DB
)
AS
SELECT 1 AS 'Number'
UNION ALL
SELECT 2
UNION ALL
SELECT 3
UNION ALL
SELECT 4
OPTION (LABEL = 'CTAS : #Nmbr : CREATE')
;
```

第二步是使用 CASE 有條件地取消樞紐資料，將資料集轉換回資料列。若要達成此目的，我們必須藉由聯結第一個步驟中建立的暫存資料表 #Nmbr，以建立笛卡兒乘積：

```
SELECT  [EnglishProductCategoryName]
,       CASE    c.[Number]
                WHEN 1 THEN [CY_2001]
                WHEN 2 THEN [CY_2002]
                WHEN 3 THEN [CY_2003]
                WHEN 4 THEN [CY_2004]
        END as Sales
FROM AnnualSales_pvt
JOIN #Nmbr c ON 1=1
WHERE   CASE    c.[Number]
                WHEN 1 THEN CY_2001
                WHEN 2 THEN CY_2002
                WHEN 3 THEN CY_2003
                WHEN 4 THEN CY_2004
        END IS NOT NULL
OPTION (LABEL = 'Unpivot :  : SELECT')
;
```

最後，別忘了卸除暫存資料表將其加以清除。

```
DROP TABLE #Nmbr
;
```

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->