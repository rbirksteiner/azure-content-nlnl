<properties 
	pageTitle="在 Windows 上使用 Node.js 連接到 SQL Database" 
	description="提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。這個範例會在 Windows 用戶端電腦上執行。"
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


# 在 Windows 上使用 Node.js 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。Node.js 程式會在 Windows 用戶端電腦上執行。若要管理連接，請使用 msnodesql 驅動程式。


## 需求


用戶端開發電腦上必須有下列軟體項目。


-  Node.js - [0.8.9 版 (32 位元版本)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/)。捲動並按一下適用於 32 位元 x86 的 Window Installer 下載連結，而不是 Windows x64 Installer 64 位元。
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)，適用於 x86 或 x64 的安裝程式。 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) - Microsoft 免費提供的 Express 版。
- SQL Server Native Client 11.0 - 以 [SQL Server 2012 功能套件](http://www.microsoft.com/download/details.aspx?id=29065)中的 Microsoft SQL Server 2012 Native Client 提供。


### 安裝必要的模組


在 **cmd.exe** 命令列視窗中，巡覽至 msnodesql 所在的目錄。請依照顯示的順序輸入下列命令。


	npm install msnodesql
	npm install -g node-gyp


安裝 node-gyp 之後，請巡覽至您的目錄 *您的專案目錄*，再巡覽至 **node_modules\\msnodesql**。然後在您的 **cmd.exe** 視窗中發出下列命令。


	node-gyp configure 
	node-gyp build


接下來，巡覽至目錄 **build\\release**。複製 **sqlserver.node** 檔案並貼到 **msnodesql\\lib** 目錄中。如有必要，請取代舊的檔案。

請查看我們的[小組部落格](http://blogs.msdn.com/b/sqlphp/archive/2015/05/12/getting-started-with-node-js-and-microsoft-sql-server-and-azure-sql-database.aspx)和[影片](https://www.youtube.com/watch?v=kQo_L-D_zk8)，以了解如何安裝及設定上述需求。


### 建立資料庫並擷取您的連接字串
 
請參閱[快速入門主題](sql-database-get-started.md)，以了解如何建立範例資料庫及擷取連接字串。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 連接到您的 SQL Database


- 將下列程式碼複製到您的專案目錄中的 .js 檔案。


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- 現在發出下列命令來執行您的 .js 檔案。


		node index.js


## 執行 SQL SELECT 陳述式


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## 插入資料列、傳遞參數及擷取產生的主索引鍵


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## 交易


**conn.beginTransactions** 方法無法在 Azure SQL Database 中運作。相反地，請遵循下列程式碼範例，以在 SQL Database 中執行交易。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## 預存程序


您必須先擁有或建立不輸入任何參數的預存程序，這個程式碼範例才能運作。您可以使用 SQL Server Management Studio (SSMS.exe) 等工具建立預存程序。


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

 

<!---HONumber=July15_HO2-->