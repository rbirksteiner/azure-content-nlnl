<properties 
	pageTitle="從 .NET (C#) 使用 SQL Database" 
	description="使用這個快速入門中的範例程式碼建置現代應用程式，這個應用程式使用 C#，並受到具有 Azure SQL Database 的雲端中之強大關聯式資料庫的支援。"
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="tobiast"/>


# 從 .NET (C#) 使用 SQL Database 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## 需求

### .NET Framework

.NET Framework 會隨 Windows 預先安裝。若是 Linux 和 Mac OS X，您可以從 [Mono Project](http://www.mono-project.com/) 下載 .NET Framework。

### SQL Database

請參閱[快速入門頁面](sql-database-get-started.md)，以了解如何建立範例資料庫及取得連接字串。

## 連接到您的 SQL Database

[System.Data.SqlClient.SqlConnection 類別](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)可用來連接到 SQL Database。
	
	```
	using System.Data.SqlClient;
	
	class Sample
	{
	  static void Main()
	  {
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		  {
			  conn.Open();	
		  }
	  }
	}	
	```

## 執行查詢並擷取結果集 

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) 和 [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) 類別可用來擷取對 SQL Database 查詢的結果集。請注意，System.Data.SqlClient 也支援將資料擷取到離線 [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) 中。
	
	```
	using System;
	using System.Data.SqlClient;
	
	class Sample
	{
		static void Main()
		{
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
			{
				var cmd = conn.CreateCommand();
				cmd.CommandText = @"
						SELECT 
							c.CustomerID
							,c.CompanyName
							,COUNT(soh.SalesOrderID) AS OrderCount
						FROM SalesLT.Customer AS c
						LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
						GROUP BY c.CustomerID, c.CompanyName
						ORDER BY OrderCount DESC;";
	
				conn.Open();	
			
				using(var reader = cmd.ExecuteReader())
				{
					while(reader.Read())
					{
						Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
					}
				}					
			}
		}
	}
	
	```

## 插入資料列、傳遞參數及擷取產生的主索引鍵值 

在 SQL Database 中，[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 屬性和 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 物件可用來自動產生[主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)值。在這個範例中，您將了解如何執行 [INSERT 陳述式](https://msdn.microsoft.com/library/ms174335.aspx)、安全地傳遞透過 [SQL 插入](https://msdn.microsoft.com/magazine/cc163917.aspx)保護的參數，以及擷取自動產生的主索引鍵值。

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) 類別中的 [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) 方法可用來執行陳述式，並擷取這個陳述式所傳回的第一個資料行和資料列。INSERT 陳述式的 [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) 子句可用來將插入值當做結果集傳回給呼叫應用程式。請注意，OUTPUT 也受到 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)、[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) 和 [MERGE](https://msdn.microsoft.com/library/bb510625.aspx) 陳述式的支援。如果插入多個資料列，則應該使用 [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) 方法為所有資料列擷取插入值。
	
	```
	class Sample
	{
	    static void Main()
	    {
			using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	        {
	            var cmd = conn.CreateCommand();
	            cmd.CommandText = @"
	                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
	                OUTPUT INSERTED.ProductID
	                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";
	
	            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
	            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
	            cmd.Parameters.AddWithValue("@Cost", 0);
	            cmd.Parameters.AddWithValue("@Price", 0);
	
	            conn.Open();
	
	            int insertedProductId = (int)cmd.ExecuteScalar();
	
	            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
	        }
	    }
	}
	```

 

<!---HONumber=July15_HO2-->