<properties
   pageTitle="在 SQL 資料倉儲中的 Create table as select (CTAS) | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中利用 create table as select (CTAS) 陳述式撰寫程式碼做為開發解決方案的提示。"
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

# 在 SQL 資料倉儲中的 Create Table As Select (CTAS)
Create table as select 或 CTAS 是最重要的可用 T-SQL 功能之一。這是根據 Select 陳述式的輸出建立新資料表的完全平行化作業。您可以根據意願將它視為 SELECT..INTO 的增強版本。

CTAS 也可以用來解決上面所列的數個不支援的功能。這通常可以證明是雙贏的情況，因為您的程式碼不但能夠相容，而且通常可以在 SQL 資料倉儲上更快速執行。這是完全平行化設計的結果。

> [AZURE.NOTE]嘗試考慮「CTAS 優先」。如果您認為您可以使用 CTAS 解決問題，即使您正在撰寫更多資料做為結果，這通常是最佳的解決方法。

可以使用 CTAS 解決的案例包括：

- SELECT..INTO
- ANSI JOINS on UPDATEs 
- ANSI JOINs on DELETEs
- MERGE 陳述式

這份文件也包含利用 CTAS 撰寫程式碼時的一些一些最佳作法。

## SELECT..INTO
您可能會發現 SELECT...INTO 會出現在解決方案中的幾個地方。

以下是 SELECT..INTO 的一個範例：

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

將其轉換為 CTAS 相當簡單：

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

使用 CTAS 表示您也可以指定資料散發喜好設定和選擇性索引以及資料表。

## update 陳述式的 ANSI 聯結取代

您可能會發現有複雜的更新，使用 ANSI 聯結語法執行 UPDATE 或 DELETE 以將兩個以上的資料表聯結在一起。

假設您必須更新此資料表：

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

原始的查詢看起來可能像這樣：

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

因為 SQL 資料倉儲不支援 ANSI 聯結，所以您無法在毫不變更此程式碼的情形下將其複製。

您可以使用 CTAS 和隱含聯結的組合來取代此程式碼：

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
;

-- Use an implicit join to perform the update 
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## delete 陳述式的 ANSI 聯結取代
有時候刪除資料的最佳方法是使用 CTAS。除了刪除資料以外，可以只選取您想要保留的資料。這對於使用 ansi 聯結語法的 DELETE 陳述式尤其適用，因為 SQL 資料倉儲上並不支援此方式。

已轉換之 DELETE 陳述式的範例如下所示：

```
CREATE TABLE dbo.DimProduct_upsert
WITH 
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p 
RIGHT JOIN dbo.stg_DimProduct s 
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## 取代 merge 陳述式
Merge 陳述式可以取代，至少有部分可使用 CTAS 取代。您可以將 `INSERT` 和 `UPDATE` 合併成單一陳述式。任何已刪除的記錄都必須在第二個陳述式中關閉。

`UPSERT` 的範例如下所示：

```
CREATE TABLE dbo.[DimProduct_upsert]
WITH 
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS 
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## CTAS 建議：明確陳述資料類型和輸出可為 null

移轉程式碼時，您可能會發現自己正在跨這種類型的程式碼撰寫模式執行：

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f 
;
```

您可能會自動認為您應該將此程式碼移轉到 CTAS，就會是正確的。不過，還是會有隱藏的問題。

下列程式碼不會產生相同的結果：

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

請注意，資料行 "result" 沿用運算式的資料類型和可為 null 值。如果不小心處理，這可能會導致細微差異值。

請嘗試下列內容做為範例：

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

儲存的結果值是不同的。因為結果資料行中保留的值用於其他運算式，錯誤變得更加顯著。

![][1]

這對資料移轉特別重要。即使第二個查詢已經更精確，仍然有一個問題。相較於來源系統，此資料有所不同，這會在移轉中產生完整性的問題。這是「錯誤」答案其實是正確答案的少數原因之一！

我們會看到這兩個結果之間有差異，追根究柢的原因是隱含的類型轉型。在第一個範例中，資料表會定義資料行。插入資料列時，就會發生隱含類型轉換。在第二個範例中，沒有隱含類型轉換，因為此運算式會定義資料行的資料類型。請注意，第二個範例中的資料行已定義為可為 Null 的資料行，而在第一個範例中還沒有定義。在第一個範例中建立資料表時，尚未明確定義資料行可為 null。在第二個範例中，它只留給了運算式，根據預設，這會導致 NULL 定義。

若要解決這些問題，您必須在 CTAS 陳述式的 SELECT 部分中明確設定類型轉換和可為 null 屬性。您無法在建立資料表的部分中設定這些屬性。

下列範例示範如何修正程式碼：

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

請注意下列事項：- CAST 或 CONVERT 可能已經使用 - ISNULL 用來強制可為 Null 屬性而非 COALESCE - ISNULL 是最外層的函數 - ISNULL 的第二個部分是常數，也就是 0

> [AZURE.NOTE]若要正確地設定可為 null 屬性，必須使用 ISNULL 而不是 COALESCE。COALESCE 不是具決定性的函數，因此運算式的結果一律可為 Null。ISNULL 則不同。它是具決定性的。因此當 ISNULL 函數的第二個部分是常數或常值，則結果值將會是 NOT NULL。

本秘訣不只適用於確保計算的完整性。它對資料表分割切換也很重要。假設您根據事實定義此資料表：

```
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH 
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
) 
;
```

不過，[值] 欄位是已計算的運算式，它不是來源資料的一部分。

若要建立資料分割資料集，您可能會想要這麼做：

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

執行最適合的查詢。當您嘗試執行資料分割切換時會出現問題。資料表定義不相符。若要讓資料表定義相符，必須修改 CTAS。

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

因此，您可以查看類型一致性，且維護 CTAS 上的可為 null 屬性是很好的工程最佳作法。它有助於維護計算的完整性，並且也可確保資料分割切換的可能性。

如需使用 [CTAS][] 的詳細資訊，請參閱 MSDN。它是 Azure SQL 資料倉儲中最重要的陳述式之一。請確定您已徹底了解。

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdnstage.redmond.corp.microsoft.com/zh-tw/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->