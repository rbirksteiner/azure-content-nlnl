<properties
   pageTitle="SQL 資料倉儲中使用者定義的結構描述 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用 Transact-SQL 結構描述開發解決方案的秘訣。"
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

# SQL 資料倉儲中使用者定義的結構描述

傳統資料倉儲通常使用不同的資料庫，根據工作負載、網域或安全性來建立應用程式界限。例如，傳統 SQL Server 資料倉儲可能包含臨時資料庫、資料倉儲資料庫和某些資料市集資料庫。在此拓撲中，每個資料庫均作為架構中的工作負載和安全性界限運作。

相反地，SQL 資料倉儲會在某個資料庫中執行整個資料倉儲工作負載。不允許跨資料庫聯結。因此，SQL 資料倉儲預期倉儲使用的所有資料表都會儲存在一個資料庫內。

> [AZURE.NOTE]SQL 資料倉儲不支援任何種類的跨資料庫查詢。因此，需要修改運用此模式的資料倉儲實作。

## 建議 

以下是使用使用者定義的結構描述合併工作負載、安全性、網域和功能界限時的一些建議

1. 使用 SQL 資料倉儲資料庫來執行整個資料倉儲工作負載
2. 合併現有的資料倉儲環境，以使用一個 SQL 資料倉儲資料庫
3. 運用**使用者定義的結構描述**來提供先前使用資料庫實作的界限。

如果先前尚未使用使用者定義的結構描述，您就沒有任何記錄。只需使用舊資料庫名稱作為 SQL 資料倉儲資料庫中使用者定義之結構描述的基礎。

如果已經使用結構描述，您有下列幾個選項可用：

1. 移除舊版結構描述名稱並重新開始
2. 在資料表名稱前面附加舊版結構描述名稱，以保留舊版結構描述名稱。
3. 在額外結構描述中的資料表上實作檢視來重建舊的結構描述結構，以保留舊版結構描述名稱。

> [AZURE.NOTE]在第一次檢查時，選項 3 似乎像是最吸引人的選項。不過，魔鬼就在細節裡。SQL 資料倉儲中的檢視為唯讀狀態。必須對基底資料表執行任何的資料表或資料修改。選項 3 還在您的系統中引進了一個檢視層。如果您已在架構中使用檢視，您可以再仔細思考一下這個選項。


### 範例：

1. 根據資料庫名稱實作使用者定義的結構描述

```
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

2. 在資料表名稱前面附加舊版結構描述名稱，以保留舊版結構描述名稱。使用工作負載界限的結構描述。

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

3. 使用檢視保留舊版結構描述名稱

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey	BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey	BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM	[edw].customer
;
```

> [AZURE.NOTE]結構描述策略如有任何變更，則需要檢閱資料庫的資訊安全模型。在許多情況下，您可以在結構描述層級指派權限，以簡化資訊安全模型。如果需要更細微的權限，您可以使用資料庫角色。

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->