<properties
	pageTitle="使用 C# 連接及查詢您的 SQL Database"
	description="這個 C# 用戶端程式碼範例使用 ADO.NET 連接到 Azure SQL Database 雲端服務上的 AdventureWorks 資料庫，並與其互動。"
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/14/2015"
	ms.author="cakarst"/>


# 使用 C&#x23; 連接及查詢您的 SQL Database


本主題提供 C# 程式碼範例，示範如何使用 ADO.NET 連接到現有的 AdventureWorks SQL Database。這個範例會編譯成可查詢資料庫並顯示結果集的主控台應用程式。


## 必要條件


- Azure SQL Database 上的現有 AdventureWorks 資料庫。[只需幾分鐘即可建立](sql-database-get-started.md)。
- [Visual Studio 和 .NET Framework](https://www.visualstudio.com/zh-tw/visual-studio-homepage-vs.aspx)


## 步驟 1：主控台應用程式


1. 使用 Visual Studio 建立 C# 主控台應用程式。


![連接和查詢](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## 步驟 2：SQL 程式碼範例


1. 將下面的程式碼範例複製並貼到您的主控台應用程式中。


> [AZURE.WARNING]為方便了解，這個程式碼範例盡可能設計得很簡短。這個範例的目的不是在生產環境中使用。


這個程式碼不適用於生產環境。如果您想要實作可供生產環境使用的程式碼，以下是業界最佳作法：


- 例外狀況處理。
- 暫時性錯誤的重試邏輯。
- 在組態檔中安全儲存密碼。



### C# 範例的原始程式碼


將這個原始程式碼到貼到您的 **Program.cs** 檔案中。


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## 步驟 3：尋找資料庫的連接字串


1. 開啟 [Azure Preview 入口網站](http://portal.azure.com/)。
2. 按一下 [瀏覽] > [SQL Database] > [Adventure Works 資料庫] > [屬性] > [顯示資料庫連接字串]。


![入口網站](./media/sql-database-connect-query/ConnectandQuery_portal.png)


在 [資料庫連接字串] 刀鋒視窗上，您可以找到 ADO.NET、ODBC、PHP 和 JDBC 的適當連接字串。


## 步驟 4：以真正的連接資訊取代


- 在您貼上的原始程式碼中，以連接字串取代 *[Your_Connection_String]* 預留位置，並請務必以您的實際密碼取代該字串中的 *your_password_here*。


## 步驟 5：執行應用程式


1. 若要建置並執行您的應用程式，請按一下 [偵錯] > [開始偵錯]
2. 程式會將查詢結果列印至主控台視窗。
 

<!---HONumber=July15_HO2-->