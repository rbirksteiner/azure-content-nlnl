<properties
	pageTitle="從 Windows 連接到 SQL DB 的 PHP| Microsoft Azure"
	description="提供可從 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。"
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# 在 Windows 上使用 PHP 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題說明如何從以 PHP 撰寫並在 Windows 上執行的用戶端應用程式，連接到 Azure SQL Database。


## 必要條件


若要執行本主題提供的 PHP 程式碼範例，您的用戶端電腦必須安裝下列軟體項目：


- [適用於 Microsoft SQL Server 的 Microsoft PHP 驅動程式](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE 包含最新位元)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Microsoft ODBC 驅動程式](https://www.microsoft.com/zh-tw/download/details.aspx?id=36434)
- IIS Express
- [適用於 IIS Express 的 PHP 5.6](http://www.microsoft.com/web/downloads/platform.aspx)：使用平台安裝程式下載。請確定您是使用 Internet Explorer 下載平台安裝程式

請查看我們的[小組部落格](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx)和[影片](https://www.youtube.com/watch?v=0oCjiRK_tUk)，以了解如何安裝及設定上述需求。


## 建立資料庫並擷取您的連接字串


請參閱[快速入門主題](sql-database-get-started.md)，以了解如何建立範例資料庫及擷取連接字串。請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 連接到您的 SQL Database 資料庫


這個 **OpenConnection** 函式會在所有後續函式的頂端附近呼叫。


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 執行查詢並擷取結果集

[sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) 函式可用來擷取對 SQL Database 查詢的結果集。這個函式基本上會接受任何查詢與連線物件並傳回結果集，您可以使用 [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) 反覆查詢結果集。

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## 插入資料列、傳遞參數及擷取產生的主索引鍵


在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";	
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## 交易


這個程式碼範例示範如何使用交易，您將：

-開始交易

-插入一列資料，更新另一列資料

-確認您的交易中插入與更新是否成功，如果其中一項沒有成功就復原交易


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## 進階閱讀


如需 PHP 安裝和使用方式的詳細資訊，請參閱[使用 PHP 存取 SQL Server Database](http://technet.microsoft.com/library/cc793139.aspx)。

 

<!---HONumber=July15_HO2-->