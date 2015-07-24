<properties 
	pageTitle="在 Ubuntu 上搭配 pymssql 使用 Python 連接到 SQL Database" 
	description="提供可用來連接到 Azure SQL Database 的 Python 程式碼範例。這個範例會在 Ubuntu Linux 用戶端電腦上執行。"
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mebha"/>


# 在 Ubuntu Linux 上使用 Python 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可在 Ubuntu Linux 用戶端電腦上執行，以連接到 Azure SQL Database 資料庫的 Python 程式碼範例。


## 需求


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)。


### 安裝必要的模組


開啟您的終端機，並巡覽至您打算用來建立 Python 指令碼的目錄。輸入下列命令，以安裝 **FreeTDS** 和 **pymssql**。pymssql 使用 FreeTDS 連接到 SQL Database。

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### 建立資料庫並擷取您的連接字串


請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫及取得連接字串。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


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