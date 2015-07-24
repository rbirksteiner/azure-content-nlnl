<properties 
	pageTitle="SQL Database 的用戶端快速入門程式碼範例 | Microsoft Azure" 
	description="提供適用於 Linux 上的 Node.js、Mac OS 上的 Python、Java 與 Windows，以及 Azure SQL Database 用戶端的程式碼範例與驅動程式。"
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="genemi"/>


# SQL Database 的用戶端快速入門程式碼範例


本主題提供可用來連接到 Azure SQL Database 之快速入門程式碼範例的連結。這些範例涵蓋：


- 各種程式設計語言。
- 您的用戶端程式可在其上執行的 Windows、Linux 及 Mac OS 作業系統。
- 簡短的快速入門程式碼範例。
- 處理自動重試邏輯之暫時性錯誤的較長範例。
- 將關聯式結果集轉換成物件導向格式的程式碼範例。


> [AZURE.NOTE]截至 2015 年 6 月 10 日為止，將會準備更多語言版本的程式碼範例，並將在本主題中新增這些範例的連結。


## Linux 上的用戶端


本節提供在 Linux 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短 | Retry | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | 。 | 。 |
| Python | [FreeTDS、pymssql](sql-database-develop-python-simple-unbutu-linux.md) | 。 | 。 |


## Mac OS 上的用戶端


本節提供在 Mac OS 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短 | Retry | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | 。 | 。 |


## Windows 上的用戶端


本節提供在 Windows 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短 | Retry | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET 與企業程式庫](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [ODBC 驅動程式](http://msdn.microsoft.com/library/azure/hh974312.aspx) | 。 | 。 |
| Java | [Java。JDBC、JDK。Insert、Transaction、Select。](sql-database-develop-java-simple-windows.md)<br/><br/>[Java。Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java。JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | 。 | 。 |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | 。 | 。 |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | 。 | 。 |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | 。 | 。 |


## 另請參閱


- [適用於多種語言和平台的 SDK 與工具的下載項目](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [SQL Database 和 SQL Server 的連接庫](sql-database-libraries.md)
- [暫時性錯誤之數字代碼的清單](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)<br/>&nbsp;
- [Azure SQL Database 開發：使用說明主題](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [連接到 SQL Database：連結、最佳作法和設計方針](sql-database-connect-central-recommendations.md)
- [建立您的第一個 Azure SQL Database](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->