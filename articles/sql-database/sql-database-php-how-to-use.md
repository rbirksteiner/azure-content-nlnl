<properties 
	pageTitle="如何使用 SQL Database (PHP) - Azure 功能指南" 
	description="了解如何從 PHP 建立和連線到 Azure SQL Database。" 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#如何從 PHP 存取 Azure SQL Database 

## 概觀

本指南將示範從 PHP 使用 SQL Database 的基本概念。相關範例是以 PHP 撰寫的。涵蓋的案例包括**建立 SQL Database** 和**連接到 SQL Database**。本指南涵蓋從[管理入口網站][management-portal]建立 SQL Database。如需從生產入口網站執行這些工作的相關資訊，請參閱[開始使用 PHP 和 SQL Azure][prod-portal-instructions] (英文)。如需詳細資訊，請參閱[後續步驟](#NextSteps)一節。

##什麼是 SQL Database

SQL Database 可為 Azure 提供關聯式資料庫管理系統，並以 SQL Server 技術為基礎。在 SQL Database 中，您可以輕鬆地將關聯式資料庫解決方案佈建及部署到雲端，並充分利用分散式資料中心，而該資料中心可提供企業級的可用性、延展性和安全性，以及內建資料防護和自我修復功能。

##<a id="Concepts"></a>概念
由於 SQL Database 是以 SQL Server 技術為基礎組建的，因此從 PHP 存取 SQL Database 與從 PHP 存取 SQL Server 非常類似。您可以在本機開發應用程式 (使用 SQL Server)，然後藉由只變更連線字串來連接 SQL Database。然而，SQL Database 和 SQL Server 之間存在某些可能會影響應用程式的差異。如需詳細資訊，請參閱 [Azure SQL Database 指導方針和限制][limitations]。

從 PHP 存取 SQL Database 的建議方法是使用 [Microsoft Drivers for PHP for SQL Server][download-drivers]。(本文中的範例將使用這些驅動程式。) The Microsoft Drivers for PHP for SQL Server 僅適用於 Windows。

##<a id="Setup"></a>作法：設定環境

設定開發環境的建議方法是使用 [Microsoft Web Platform Installer][wpi-installer]。Web Platform Installer 可讓您選擇 Web 開發平台的元素，並且會自動安裝及設定這些元素。只要下載 Web Platform Installer 並選擇安裝 WebMatrix、PHP for WebMatrix 及 SQL Server Express，我們將會為您設定完整的開發環境。

您也可以手動設定環境：

* 安裝 PHP 和設定 IIS：[http://php.net/manual/en/install.windows.iis7.php][manual-config]。
* 下載並安裝 SQL Server Express：[http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* 下載並安裝 [Microsoft Drivers for PHP for SQL Server][download-drivers]。

##<a id="CreateServer"></a>作法：建立 SQL 資料庫

遵循下列步驟以建立 Azure SQL Database：

1. 登入[管理入口網站][management-portal]。
2. 按一下入口網站左下方的 [新增]。

	![Create New Azure Web Site][new-website]

3. 依序按一下 [資料服務]、[SQL DATABASE] 和 [快速建立]。提供資料庫的名稱、是否使用現有或新的資料庫伺服器、區域，以及系統管理員名稱和密碼。

	![Custom Create a new SQL Database][quick-create]


若要查看伺服器和資料庫資訊，請按一下管理入口網站中的 [SQL Database]。您可以接著按一下 [資料庫] 或 [伺服器]，查看相關資訊。

![View server and database information][sql-dbs-servers]

##<a id="ConnectionInfo"></a>作法：取得 SQL Database 連線資訊

若要取得 SQL Database 連線資訊，請按一下入口網站中的 [SQL DATABASE]，然後按一下資料庫的名稱。

![View database information][go-to-db-info]

接著按一下 [View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC]。

![Show connection strings][show-connection-string]

在結果視窗的 PHP 區段中，記下 **SERVER**、**DATABASE** 及 **USERNAME** 的值。您的密碼會是建立 SQL Database 時所用的密碼。

##<a id="Connect"></a>作法：連接到 SQL 資料庫執行個體

以下範例示範如何使用 **SQLSRV** 和 **PDO_SQLSRV** 擴充功能連接名為 `testdb` 的 SQL Database。如需 **SQLSRV** 和 **PDO_SQLSRV** API 的相關資訊，請參閱 [Microsoft Drivers for PHP for SQL Server 文件][driver-docs] (英文)。您將需要從上述區段取得的資訊。將 `SERVER_ID` 取代為 10 位數的伺服器 ID (自上述區段取得之 SERVER 值的前 10 個字元)，然後將正確的值 (您的使用者名稱和密碼) 指派給 `$user` 和 `$pwd` 等變數。

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>接續步驟
如前文提及，SQL Database 的使用方法與 SQL Server 非常類似。一旦建立 SQL Database 連線 (如前文所示) 後，您便可以使用 **SQLSRV** 或 **PDO_SQLSRV** API 來插入、擷取、更新及刪除資料。然而，SQL Database 和 SQL Server 之間存在某些可能會影響應用程式的差異。如需詳細資訊，請參閱 [Azure SQL Database 指導方針和限制][limitations]。

示範如何在 Azure 上搭配使用 SQL 資料庫和 PHP 的範例，位於 <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>。

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=62-->