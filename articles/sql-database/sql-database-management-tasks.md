<properties 
	pageTitle="如何管理 SQL Database" 
	description="了解如何管理 Azure SQL 資料庫。" 
	headerExpose="" 
	footerExpose="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


## 概觀

本文將說明如何在 Azure SQL Database 中執行簡單的管理工作。 

## 作法：使用 Management Studio 連接到 Azure 中的 SQL Database

Management Studio 是一項管理工具，可讓您在單一工作區中管理多個 SQL Server 執行個體和伺服器。如果您已有內部部署 SQL Server 執行個體，您可以在 Azure 上同時建立對內部部署執行個體和邏輯伺服器的連線，以並行方式執行工作。

Management Studio 具有目前無法在管理入口網站中使用的功能，例如語法檢查程式，以及儲存指令碼和具名查詢以供重複使用的功能。SQL Database 是表格式資料流 (TDS) 端點。任何可用於 TDS 的工具 (包括 Management Studio)，都適用於 SQL Database 作業。您為內部部署伺服器開發的指令碼，將會在 SQL Database 邏輯伺服器上執行。 

在下列步驟中，您將使用 Management Studio 連接到 Azure 上的邏輯伺服器。要執行此步驟，您必須具備 SQL Server Management Studio 2008 R2 或 2012 版。如果您需要下載或連接到 Management Studio 方面的協助，請參閱此網站上的[使用 Management Studio 管理 SQL Database][]。

在某些情況下，您必須先建立防火牆例外狀況，使本機系統的連接埠 1433 允許輸出要求，您才能進行連接。依預設受到保護的電腦通常不會開放連接埠 1433。 

## 設定內部部署伺服器的防火牆

1. 在具備進階安全性的 Windows 防火牆中，建立新的輸出規則。

2. 選擇 [**連接埠**]、指定 TCP 1433、指定 [**允許連線**]，並確定已選取 [**公用**] 設定檔。

3. 提供有意義的名稱，例如 *WindowsAzureSQLDatabase (tcp-out) port 1433*。 


## 連接到邏輯伺服器

1. 在 Management Studio 的 [連接到伺服器] 中，確定已選取 Database Engine，然後輸入下列格式的邏輯伺服器名稱： *servername*.database.widnows.net

	您也可以在管理入口網站中、伺服器儀表板上或 [管理 URL] 中取得完整的伺服器名稱。

2. 在 [驗證] 中選擇 [**SQL Server 驗證**]，然後輸入您在設定邏輯伺服器時建立的系統管理員登入。

3. 按一下 [**選項**]。 

4. 在 [連接到資料庫] 中，指定 [**master**]。


## 連接到內部部署伺服器

1. 在 Management Studio 的 [連接到伺服器] 中，確定已選取 Database Engine，然後輸入下列格式的邏輯執行個體名稱： *servername**instancename*。如果伺服器是本機的預設執行個體，請輸入  *localhost*。

2. 在 [驗證] 中選擇 [**Windows 驗證**]，然後輸入屬於 sysadmin 角色之成員的 Windows 帳戶。


## 作法：將登入和使用者新增至 Azure SQL Database

部署資料庫後，您必須設定登入並指派權限。在下一個步驟中，您將執行兩個指令碼。

執行第一個指令碼時，您將會連接到 master，並執行會建立登入的指令碼。登入將用來支援讀取與寫入作業，以及委派營運工作，例如在不具 'sa' 權限的情況下執行系統查詢的功能。

您所建立的登入必須是 SQL Server 驗證登入。如果您已有使用 Windows 使用者身分識別或宣告身分識別的現成指令碼，該指令碼將無法在 SQL Database 上運作。

第二個指令碼會指派資料庫使用者權限。執行此指令碼時，您會連接到已在 Azure 上載入的資料庫。

## 建立登入

1. 在 Management Studio 中連接到 Azure 上的邏輯伺服器、展開 [資料庫] 資料夾、以滑鼠右鍵按一下 [**master**]，然後選取 [**新增查詢**]。

2. 使用下列 Transact-SQL 陳述式建立登入。請將密碼取代為有效密碼。請逐一選取每個密碼，然後按一下 [**執行**]。為其餘登入重複此步驟。

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## 建立資料庫使用者

1. 展開 [資料庫] 資料夾、以滑鼠右鍵按一下 **school**，然後選取 [**新增查詢**]。

2. 使用下列 Transact-SQL 陳述式新增資料庫使用者。請將密碼取代為有效密碼。 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## 檢視並測試登入

1. 在新的查詢視窗中連接到 [**master**]，然後執行下列陳述式： 

        SELECT * from sys.sql_logins;

2. 在 Management Studio 中，以滑鼠右鍵按一下 **school** 資料庫，然後選取 [**新增查詢**]。

3. 在 [查詢] 功能表中指向 [**連線**]，然後按一下 [**變更連線**]。

4. 以 *sqlreader* 身分登入。

5. 複製下列陳述式，並嘗試加以執行。此時應會顯示錯誤，指出物件不存在。

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. 開啟第二個查詢視窗，並將連線內容變更為 *sqlwriter*。此時，相同的查詢應可成功執行。

現在，您已建立並測試數個登入。如需詳細資訊，請參閱 [管理 SQL Database 中的資料庫和登入][] 和 [使用動態管理檢視監視 SQL Database][]。

[管理 SQL Database 中的資料庫和登入]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[使用動態管理檢視監視 SQL Database]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[使用 Management Studio 管理 SQL Database]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/






<!--HONumber=47-->
 