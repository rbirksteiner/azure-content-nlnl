<properties 
	pageTitle="在 Windows 上使用 Python 連接到 SQL Database" 
	description="提供可用來從 Windows 用戶端連接到 Azure SQL Database 的 Python 程式碼範例。這個範例使用 pymssql 驅動程式。"
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# 在 Windows 上使用 Python 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供以 Python 撰寫的程式碼範例。這個範例會在 Windows 電腦上執行。這個範例使用 **pymssql** 驅動程式連接到 Azure SQL Database。


## 需求


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### 安裝必要的模組


安裝 [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)。

確定您選擇正確的 whl 檔案。

例如：如果您在 64 位元電腦上使用 Python 2.7，請選擇：pymssql‑2.1.1‑cp27‑none‑win_amd64.whl。下載 .whl 檔案之後，請將它放在 C:/Python27 資料夾中。

現在，從命令列 .cd 使用 pip 將 pymssql 驅動程式安裝到 C:/Python27 中，並執行下列程式碼：
	
	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

您可以在[這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到如何啟用以使用 pip 的指示。


## 建立資料庫並擷取您的連接字串


請參閱[快速入門主題](sql-database-get-started.md)，以了解如何建立範例資料庫及擷取連接字串。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 連接到您的 SQL Database


[pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 函式可用來連接到 SQL Database。

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## 執行 SQL SELECT 陳述式

[cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 函式可用來擷取對 SQL Database 查詢的結果集。這個函式基本上會接受任何查詢並傳回結果集，您可以使用 [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) 反覆查詢結果集。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## 插入資料列、傳遞參數及擷取產生的主索引鍵

在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## 交易


這個程式碼範例示範如何使用交易，您將：


-開始交易

-插入一列資料

-回復您的交易以復原插入


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

 

<!---HONumber=July15_HO2-->