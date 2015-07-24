<properties 
	pageTitle="在 Mac OS X 上搭配 Tedious 使用 Node.js 連接到 SQL Database" 
	description="提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。這個範例使用 Tedious 驅動程式進行連接。"
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# 在 Mac OS X 上搭配 Tedious 使用 Node.js 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可在 Mac OS X 上執行的 Node.js 程式碼範例。這個範例使用 Tedious 驅動程式連接到 Azure SQL Database。


## 必要的軟體項目


安裝 **node**，除非它已在電腦上安裝。


若要在 OSX 10.10 Yosemite 上安裝 node.js，您可以下載預先編譯的二進位封裝，如此可讓您順利並輕鬆安裝。[前往 nodejs.org](http://nodejs.org/)，按一下 [安裝] 按鈕，以下載最新的封裝。

遵循將會安裝 **node** 和 **npm** 的安裝精靈透過 .dmg 安裝套件，npm 是「節點封裝管理員」，有助於安裝 node.js 的其他封裝。


將您的電腦設定為使用**節點** 和 **npm** 之後，請巡覽至您打算建立 Node.js 專案的目錄，並輸入下列命令。


	npm init
	npm install tedious


**npm init** 會建立節點專案。若要在專案建立期間保留預設值，請按 Enter 鍵直到專案建立為止。現在您會在專案目錄中看到 **package.json** 檔案。


### 建立 AdventureWorks 資料庫


本主題中的程式碼範例必須使用 **AdventureWorks** 測試資料庫。如果還沒有這個資料庫，請參閱[開始使用 SQL Database](sql-database-get-started.md)。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 連接到您的 SQL Database

[new Connection](http://pekim.github.io/tedious/api-connection.html) 函式可用來連接到 SQL Database。

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## 執行 SQL SELECT


所有 SQL 陳述式都會使用 [new Request()](http://pekim.github.io/tedious/api-request.html) 函式來執行。如果陳述式傳回資料列 (例如 SELECT 陳述式)，您可以使用 [request.on()](http://pekim.github.io/tedious/api-request.html) 函式擷取這些資料列。如果沒有資料列，[request.on()](http://pekim.github.io/tedious/api-request.html) 函式會傳回空白清單。


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## 插入資料列、套用參數及擷取產生的主索引鍵


在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。在這個範例中，您將了解如何執行 INSERT 陳述式、安全地傳遞透過 SQL 插入保護的參數，以及擷取自動產生的主索引鍵值。


本節中的程式碼範例會將參數套用至 SQL INSERT 陳述式。所產生的主索引鍵值會透過程式來擷取。


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

<!---HONumber=July15_HO2-->