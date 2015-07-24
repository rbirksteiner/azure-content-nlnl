<properties
	urlDisplayName="How to connect to an Azure SQL database using SSMS"
	pageTitle="如何使用 SSMS 連接到 Azure SQL Database" metaKeywords=""
	description="了解如何使用 SSMS 連接到 Azure SQL Database。"
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	title="How to connect to an Azure SQL database using SSMS"
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/02/2015"
	ms.author="sidneyh" />

# 如何使用 SSMS 連接到 Azure SQL Database

以下是使用 SQL Server Management Studio 連接到 Microsoft Azure SQL Database 的步驟。

## 必要條件
* 已佈建且在執行中的 Azure SQL Database。若要建立新的 SQL Database，請參閱[開始使用 Microsoft Azure SQL Database](sql-database-get-started.md)。
* SQL Database 的系統管理員名稱和密碼。
* SQL Server Management Studio 2014。若要取得工具，請參閱[下載 SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx)。

## 連接到 SQL Database 的執行個體
1. 登入 [Azure 管理入口網站](https://portal.azure.com)。
2. 按一下 [瀏覽] 按鈕，然後按一下 [SQL Database] (b)。

	![按一下 [瀏覽] 和 [SQL Database]][1]
3. 在選取 [SQL Database] 時 (a)，按一下伺服器上您要連接的資料庫名稱 (b)。

	![按一下資料庫的名稱][2]
4. 在選取名稱時 (a)，按一下 [屬性] (b)。複製伺服器的名稱 (c) 和系統管理員的名稱 (d)。系統管理員名稱和密碼是在建立 SQL Database 時所建立。您必須有密碼才能繼續進行下一個步驟。

	![按一下 [SQL Server]、[設定] 和 [屬性]][3]
5. 開啟 SQL Server Management Studio 2014。
6. 在 [連接到伺服器] 對話方塊的 [伺服器名稱] 方塊 (a) 中貼上伺服器的名稱。將 [驗證] 設定為 [SQL Server 驗證] (b)。在 [登入] 方塊 (c) 中貼上伺服器系統管理員的名稱，然後輸入其密碼 (d)。然後按一下 [選項] (e)。

	![SSMS 登入對話方塊][4]
7. 在 [連接屬性] 索引標籤中，將 [連接到資料庫] 方塊設定為 [主要] (或您要連接的任何其他資料庫)。然後按一下 [連接]。

	![設定為 [主要] 並按一下 [連接]][5]

## 連接問題的疑難排解

如果發生問題，請參閱 [Azure SQL Database 連接問題的疑難排解](https://support.microsoft.com/kb/2980233/)。如需可能的問題和解答清單，請參閱 [Microsoft Azure SQL Database 連接的疑難排解](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1)。


## 後續步驟
您可以使用 Transact-SQL 陳述式來建立或管理資料庫。請參閱 [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 和[使用 SQL Server Management Studio 管理 Azure SQL Database](sql-database-manage-azure-ssms.md)。您也可以將事件記錄到 Azure 儲存體。請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)。

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=58_postMigration-->