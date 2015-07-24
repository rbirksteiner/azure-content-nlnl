<properties
   pageTitle="SQL 資料倉儲中的 PolyBase 教學課程 | Microsoft Azure"
   description="瞭解 PolyBase 是什麼及如何用於資料倉儲案例。"
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
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# 使用 PolyBase 載入資料
PolyBase 技術可讓您查詢和聯結多個來源的資料，且完全使用 Transact-SQL 命令。

使用 PolyBase 時，您可以執行下列步驟來查詢儲存在 Azure blob 儲存體中的資料，再將資料載入 SQL 資料倉儲資料庫：

- 建立資料庫主要金鑰和認證。
- 建立 PolyBase 物件：外部資料來源、外部檔案格式和外部資料表。 
- 查詢儲存在 Azure blob 儲存體中的資料。
- 從 Azure blob 儲存體將資料載入 SQL 資料倉儲。


## 必要條件
若要逐步執行本教學課程，您需要：

- Azure 儲存體帳戶
- 在 Azure blob 儲存體中儲存為分隔符號文字檔的資料

首先，您將建立當 PolyBase 連接到 Azure blob 儲存體和查詢資料時所需要的物件。

## 建立資料庫主要金鑰
連接到伺服器上的主要資料庫來建立資料庫主要金鑰。這個金鑰在下一步用來加密認證密碼。

```
-- Creating master key
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
```

參考主題：[CREATE MASTER KEY (Transact-SQL)][]。

## 建立資料庫範圍認證
若要存取 Azure blob 儲存體，您需要建立資料庫範圍認證，其中儲存 Azure 儲存體帳戶的驗證資訊。連接到資料倉儲資料庫，並為您想要存取的每個 Azure 儲存體帳戶建立資料庫範圍認證。指定身分識別名稱和 Azure 儲存體帳戶金鑰做為密碼。身分識別名稱並不會影響到 Azure 儲存體的驗證。

```
-- Creating credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

參考主題：[CREATE CREDENTIAL (Transact-SQL)][]。

輪換 Azure 儲存體帳戶金鑰時，您必須捨棄認證，再使用新的金鑰做為密碼來重新建立認證。

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

參考主題：[DROP CREDENTIAL (Transact-SQL)][]。


## 建立外部資料來源
外部資料來源是儲存 Azure blob 儲存體資料位置及存取資訊的資料庫物件。對於您想要存取的每個 Azure 儲存體容器，您需要定義外部資料來源。

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

參考主題：[CREATE EXTERNAL DATA SOURCE (Transact-SQL)][]。

## 建立外部檔案格式
外部檔案格式是指定外部資料格式的資料庫物件。在此範例中，我們在文字檔中有未壓縮的資料，且欄位以縱線字元 ('|') 分隔。

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase 可以處理壓縮和未壓縮的資料，包括分隔的文字、Hive RCFILE 和 HIVE ORC 格式。

參考主題：[CREATE EXTERNAL FILE FORMAT (Transact-SQL)][]。

## 建立外部資料表

外部資料表定義類似於關聯式資料表定義。主要的差異在於資料的位置和格式。外部資料表定義儲存在 SQL 資料倉儲資料庫。資料儲存在資料來源所指定的位置。

LOCATION 選項指定從資料來源根目錄到資料的路徑。在此範例中，資料位於 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'。

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]請注意，您目前無法在外部資料表上建立統計資料。

相同資料表的所有檔案都必須在 Azure BLOB 中相同的邏輯資料夾底下。最佳做法是將 Azure 儲存體資料分成不超過 1 GB 的檔案，以利於 SQL 資料倉儲進行平行處理。

參考主題：[CREATE EXTERNAL TABLE (Transact-SQL)][]。

您剛才建立的物件會儲存在 SQL 資料倉儲資料庫。您可以在 SQL Server Data Tools (SSDT) 物件總管中檢視它們。


## 查詢 Azure blob 儲存體資料
針對外部資料表的查詢只使用資料表名稱，如同關聯式資料表一樣。

這是臨機操作查詢，聯結 SQL 資料倉儲中儲存的保險客戶資料，與 Azure 儲存體 blob 中儲存的汽車感應器資料。結果顯示開車速度超過其他人的駕駛。

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## 從 Azure blob 儲存體載入資料
此範例將 Azure blob 儲存體中的資料載入至 SQL 資料倉儲資料庫。

直接儲存資料可免除查詢時的資料傳輸時間。搭配 columnstore 索引儲存資料可讓分析查詢的查詢效能提升 10 倍。

此範例使用 CREATE TABLE AS SELECT 陳述式來載入資料。新的資料表繼承查詢中指名的資料行。它會從外部資料表定義繼承這些資料行的資料型別。

CREATE TABLE AS SELECT 是效能很高的 Transact-SQL 陳述式，可替代 INSERT...SELECT。它原本是針對分析平台系統中的大量平行處理 (MPP) 引擎所開發，現在已納入 SQL 資料倉儲中。

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

請參閱 [CREATE TABLE AS SELECT (Transact-SQL)][]。


## 限制
使用 PolyBase 載入時僅支援 UTF-8 編碼樣式。至於其他編碼樣式，例如 UTF-16，請考慮使用 bcp 公用程式、SSIS 或 Azure Data Factory，將資料載入 SQL 資料倉儲資料庫。

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/zh-tw/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/zh-tw/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/zh-tw/library/ms189450.aspx

<!---HONumber=July15_HO1-->