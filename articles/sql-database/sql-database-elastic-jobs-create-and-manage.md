<properties
	pageTitle="建立和管理彈性資料庫工作"
	description="逐步解說如何建立和管理彈性資料庫工作。"
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="sidneyh"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="sidneyh"/>

# 建立和管理彈性資料庫工作

**彈性資料庫集區**提供一個用於部署大量資料庫的可預測模型。您可以在固定成本下，為每個資料庫設定最小資料輸送量單位 (DTU)。管理這些資料庫中之通用物件的最簡單方式，是透過**彈性資料庫工作**。該服務可讓您對單一作業之集區中的所有資料庫執行 T-SQL 指令碼。例如，您可以在每個資料庫上設定原則，僅允許具有正確認證的人員檢視機密資料。

## 必要條件

* Azure 訂閱。如需免費試用，請參閱[免費試用一個月](http://azure.microsoft.com/pricing/free-trial/)。
* 彈性資料庫集區。請參閱[關於彈性資料庫集區](sql-database-elastic-pool.md)。
* 安裝彈性資料庫工作服務元件。請參閱[安裝彈性資料庫工作服務](sql-database-elastic-jobs-service-installation.md)。

## 建立工作 (Job)

1. 在 [彈性資料庫工作集區] 刀鋒視窗中，按一下 [建立工作]。
2. 輸入工作控制資料庫 (工作的中繼資料儲存體) 之資料庫系統管理員 (在安裝作業時建立) 的使用者名稱與密碼。

	![為工作命名，輸入或貼上程式碼，然後按一下 [執行]][1]
2. 在 [建立工作] 刀鋒視窗中，輸入工作的名稱。
3. 輸入使用者名稱與密碼來連線至目標資料庫，以取得足夠權限來成功執行指令碼。
4. 貼上或輸入 T-SQL 指令碼。
5. 按一下 [儲存]，然後按一下 [執行]。

	![建立工作並執行][5]

## 執行等冪工作

當您對一組資料庫執行指令碼時，您必須確定指令碼是等冪。換句話說，指令碼必須能夠重複執行，即使之前在未完成狀態失敗亦然。例如，當指令碼失敗時，系統會自動重試工作，直到成功為止 (在限制內，因為重試邏輯最終將會停止重試)。其作法是使用 "IF EXISTS" 子句，並刪除任何找到的執行個體，再建立新的物件。範例如下所示：

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

或者，使用 "IF NOT EXISTS" 子句，再建立新的執行個體：

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	 CREATE TABLE TestTable(
	  TestTableId INT PRIMARY KEY IDENTITY,
	  InsertionTime DATETIME2
	 );
	END
	GO

	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO

這個指令碼會接著更新之前建立的資料表。

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN

	ALTER TABLE TestTable

	ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO


## 檢查工作狀態

工作開始之後，您可以檢查它的進度。

1. 從 [彈性資料庫集區] 頁面，按一下 [管理工作]。

	![按一下 [管理工作]][2]

2. 按一下工作的名稱 (a)。[狀態] 可以是 [已完成] 或 [失敗]。 工作的詳細資料隨即出現 (b)，並提供建立和執行的日期和時間。其下方的清單 (c) 顯示對集區中每個資料庫執行指令碼的進度，並提供其日期和時間詳細資料。

	![檢查完成的工作][3]


## 檢查失敗的工作

如果工作失敗，您可以找到其執行記錄檔。按一下失敗工作的名稱，以查看其詳細資料。

![查看失敗的工作][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 

<!---HONumber=62-->