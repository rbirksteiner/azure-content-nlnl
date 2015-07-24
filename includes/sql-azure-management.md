
# 使用 SQL Server Management Studio 管理 Azure SQL Database 

您可以使用 SQL Server Management Studio (SSMS) 來管理 Azure SQL Database 邏輯伺服器與資料庫。本主題會引導您使用 SSMS 的一般工作。在您開始之前，您應該已經有 Azure SQL Database 中建立的邏輯伺服器與資料庫。若要開始使用，請閱讀[建立您的第一個 Azure SQL Database](sql-database-get-started.md)，然後再回來。

每當您使用 Azure SQL Database 時，建議您使用最新版本的 SSMS。請造訪[下載 SQL Server Management Studio](https://msdn.microsoft.com/zh-tw/library/mt238290.aspx) 取得。


## 連線到 SQL Database 邏輯伺服器

若要連線到 SQL Database，您需要知道 Azure 上的伺服器名稱。您可能需要登入入口網站才能取得此資訊。

1.  登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在左窗格中，按一下 [SQL Database]。

3.  在 SQL Databases 首頁上，按一下頁面頂端的 [伺服器]，以列出與訂閱相關的所有伺服器。尋找想要連線的伺服器名稱，並將它複製到剪貼簿上。

	接著，設定您的 SQL Database 防火牆，以允許來自本機電腦的連線。若要這麼做，請將本機電腦 IP 位址加入防火牆例外清單。

1.  在 SQL Databases 首頁上，按一下 [伺服器]，然後按一下想要與其連線的伺服器。

2.  按一下頁面頂端的 [設定]。

3.  複製 [目前用戶端 IP 位址] 中的 IP 位址。

4.  在 [設定] 頁面中，[允許的 IP 位址] 包括三個方塊，您可以在此處指定規則名稱與作為起始和結束值的 IP 位址範圍。在規則名稱中，您可以輸入您的電腦名稱。在起始範圍和結束範圍中，將您的電腦 IP 位址貼入這兩個方塊，然後按一下出現的核取方塊。

	規則名稱必須是唯一的。如果這是您的開發電腦，您可以在 IP 範圍起始方塊和 IP 範圍結束方塊中輸入 IP 位址。否則，您可能需要輸入較廣泛的 IP 位址範圍，以容納來自組織中其他電腦的連線。
 
5. 按一下頁面底部的 [儲存]。

    **請注意：**防火牆設定的變更最長可能延遲五分鐘才會生效。

	您現在可以使用 Management Studio 來連線到 SQL Database。

1.  按一下工作列上的 [開始]，依序指向 [所有程式]、[Microsoft SQL Server 2014]，然後按一下 [SQL Server Management Studio]。

2.  在 [連線到伺服器] 中，將完整伺服器名稱指定為 *serverName*.database.windows.net。在 Azure 上，伺服器名稱是由英數字元組成的自動產生字串。

3.  選取 [SQL Server Authentication]。

4.  在 [登入] 方塊中，輸入您建立伺服器時，在入口網站中指定的 SQL Server 系統管理員登入。

5.  在 [密碼] 方塊中，輸入建立伺服器時，在入口網站中指定的密碼。

8.  按一下 [連線] 以建立連線。

具備最新更新的 SQL Server 2014 SSMS 提供工作的擴大支援，例如建立和修改 Azure SQL databases。此外，您也可以使用 Transact-SQL 陳述式來完成這些工作。以下步驟將提供這些陳述式的範例。如需有關使用 Transact-SQL 搭配 SQL Database 的詳細資訊 (包括支援的命令詳細資料)，請參閱 [Transact-SQL 參考 (SQL Database)](http://msdn.microsoft.com/library/bb510741.aspx)。

## 建立和管理 Azure SQL 資料庫

連線到 [master] 資料庫時，您可以在伺服器上建立新資料庫，並且可以修改或卸除現有的資料庫。下列步驟將說明如何透過 Management Studio 來完成數個常見的資料庫管理工作。若要執行這些工作，請確保您已使用在設定伺服器時所建立的伺服器層級主體登入，來連線到 [master] 資料庫。

若要在 Management Studio 中開啟查詢視窗，您可以開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 [master] 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

-   使用 **CREATE DATABASE** 陳述式來建立新的資料庫。如需詳細資訊，請參閱 [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)。以下陳述式將建立名為 **myTestDB** 的新資料庫，並指定它是預設大小上限為 250 GB 的「標準 S0 Edition」資料庫。

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

按一下 [執行] 來執行查詢。

-   使用 **ALTER DATABASE** 陳述式來修改現有資料庫，例如，如果您要變更資料庫的名稱或版本。如需詳細資訊，請參閱 [ALTER DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms174269.aspx)。以下陳述式將修改您在先前步驟中所建立的資料庫，將版本變更為「標準 S1」。

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   使用 **the DROP DATABASE** 陳述式來刪除現有的資料庫。如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。以下陳述式將刪除 **myTestDB** 資料庫，但不會立即卸除此資料庫，因為您將會在下一個步驟中用它來建立登入。

        DROP DATABASE myTestBase;

-   master 資料庫具有 **sys.databases** 檢視，您可用它來檢視有關所有資料庫的詳細資料。若要檢視所有現有的資料庫，請執行下列陳述式：

        SELECT * FROM sys.databases;

-   在 SQL Database 中，**USE** 陳述式不支援資料庫之間的切換。相反地，您需要建立與目標資料庫的直接連線。

>[AZURE.NOTE]許多可建立或修改資料庫的 Transact-SQL 陳述式必須在自己的批次中執行，且無法與其他 Transact-SQL 陳述式群組在一起。如需詳細資訊，請參閱陳述式特定資訊 (可透過上面列出的連結取得)。

## 建立和管理登入

**master** 資料庫會持續追蹤登入，以及哪些登入具有可建立資料庫或其他登入的權限。使用在設定伺服器時所建立的伺服器層級主體登入，連線到 [master] 資料庫以管理登入。您可以使用 **CREATE LOGIN**、**ALTER LOGIN** 或 **DROP LOGIN** 陳述式，來針對管理整個伺服器登入的 master 資料庫執行查詢。如需詳細資訊，請參閱[管理 SQL Database 中的資料庫和登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)。


-   使用 **CREATE LOGIN** 陳述式來建立新的伺服器層級登入。如需詳細資訊，請參閱 [CREATE LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189751.aspx)。以下的陳列式將建立一個名為 **login1** 的新登入。使用您選擇的密碼來取代 **password1**。

        CREATE LOGIN login1 WITH password='password1';

-   使用 **CREATE USER** 陳述式來授與資料庫層級的權限。所有的登入都必須在 [master] 資料庫中建立，但若是要連線到不同資料庫的登入，您必須在該資料庫中使用 **CREATE USER** 陳述式來對它授與資料庫層級的權限。如需詳細資訊，請參閱 [CREATE USER (SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx)。

-   若要對名為 **myTestDB** 的資料庫提供 login1 權限，請完成下列步驟：

 1.  若要重新整理 [物件總管] 以檢視剛剛建立的 **myTestDB** 資料庫，請在 [物件總管] 中使用滑鼠右鍵按一下伺服器名稱，然後按一下 [重新整理]。  

     如果連線已關閉，您可以透過在 [檔案] 功能表上選取 [連接物件總管] 來重新連線。

 2. 在 **myTestDB** 資料庫上按一下滑鼠右鍵，並選取 [新增查詢]。

    3.  針對 myTestDB 資料庫執行下列陳述式，建立名為 **login1User** (對應到伺服器層級登入 **login1**) 的資料庫使用者。

            CREATE USER login1User FROM LOGIN login1;

-   使用 **sp_addrolemember** 預存程序，對使用者帳戶提供資料庫上適當層級的權限。如需詳細資訊，請參閱 [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)。以下陳述式將會對 **login1User** 提供資料庫的唯讀權限，方法是將 **login1User** 加入 **db_datareader** 角色。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   使用 **ALTER LOGIN** 陳述式來修改現有的登入，例如，如果您想要變更登入的密碼。如需詳細資訊，請參閱 [ALTER LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189828.aspx)。**ALTER LOGIN** 陳述式的執行對象應是 [master] 資料庫。切換回連線到該資料庫的查詢視窗。

    以下陳述式將修改 **login1** 登入以重設密碼。使用您選擇的密碼來取代 **newPassword**，並使用目前的登入密碼來取代 **oldPassword**。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   使用 **the DROP LOGIN** 陳述式來刪除現有的登入。刪除伺服器層級的登入也會同時刪除任何相關資料庫使用者帳戶。如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。**DROP LOGIN** 陳述式的執行對象應是 [master] 資料庫。以下陳述式將刪除 **login1** 登入。

        DROP LOGIN login1;

-   master 資料庫具有 **sys.sql_logins** 檢視，您可用它來檢視登入。若要檢視所有現有的登入，請執行下列陳述式：

        SELECT * FROM sys.sql_logins;

## 使用動態管理檢視監視 SQL Database</h2>

SQL Database 支援數種動態管理檢視，您可用他們來監視個別資料庫。以下是透過這些檢視擷取的一些監視資料類型範例。如需完整的詳細資料和更多使用方式範例，請參閱[使用動態管理檢視監視 SQL Database](https://msdn.microsoft.com/library/azure/ff394114.aspx)。

-   查詢動態管理檢視需要 **VIEW DATABASE STATE** 權限。若要對特定的資料庫使用者授與 **VIEW DATABASE STATE** 權限，請連線到您想要使用伺服器層級主體登入來管理的資料庫，並針對資料庫執行下列陳述式：

        GRANT VIEW DATABASE STATE TO login1User;

-   使用 **sys.dm_db_partition_stats** 檢視來計算資料庫大小。**sys.dm_db_partition_stats** 檢視會傳回資料庫中每一個資料分割的頁面和資料列計數資訊，您可用這些資訊來計算資料庫大小。下列查詢會傳回資料庫的大小 (以 MB 為單位)：

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   使用 **sys.dm_exec_connections** 和 **sys.dm_exec_sessions** 檢視，來擷取目前使用者連線和資料庫相關內部工作等相關資訊。下列查詢會傳回目前連接的相關資訊：

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   使用 **sys.dm_exec_query_stats** 檢視，來擷取快取查詢計劃的彙總效能統計資料。下列查詢會傳回平均 CPU 時間排名之前五項查詢的相關資訊。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<!---HONumber=July15_HO2-->