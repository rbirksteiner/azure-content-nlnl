<properties
   pageTitle="連線到 SQL 資料倉儲 | Microsoft Azure"
   description="連線到 SQL 資料倉儲以開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 連線到 SQL 資料倉儲 
若要連線到 SQL 資料倉儲，您必須傳入安全性認證進行驗證用途。建立連線時，您也會看到特定連線設定都設定為建立查詢工作階段的一部分。

本文詳細說明連線到 SQL 資料倉儲的下列層面：

- 驗證
- 連線設定
- 工作階段和要求識別碼


## 驗證
若要連線到 SQL 資料倉儲，您必須提供下列資訊：

- 完整伺服器名稱 
- 指定 SQL 驗證
- 使用者名稱 
- 密碼
- 預設資料庫 (選擇性)

請務必注意，使用者必須使用 SQL 驗證進行驗證。此時不支援受信任的驗證。

根據預設，您的連線會連線至主要資料庫，而非您的使用者資料庫。若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

1. 使用伺服器註冊 SSDT 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。例如，藉由包含 ODBC 連線的 InitialCatalog 參數。
2. 在 SSDT 中建立工作階段之前先反白顯示使用者資料庫。

> [AZURE.NOTE]如需連線到 SQL 資料倉儲與 SSDT 的指引，請參閱回[連線及查詢][]入門文章。

也請務必注意，變更連線的資料庫時，Transact-SQL 陳述式 **USE <your DB>** 不受支援

## 應用程式連線通訊協定
您可以使用下列任合通訊協定連線到 SQL 資料倉儲：

- ADO.NET
- ODBC
- PHP
- JDBC

### 範例 ADO.NET 連接字串

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### 範例 ODBC 連接字串

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### 範例 PHP 連接字串

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### 範例 JDBC 連接字串

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 連線設定
SQL 資料倉儲會在連線和建立物件期間標準化一些設定。這些不會被覆寫。

| 資料庫設定 | 值 |
| :----------------- | :--------------------------- |
| ANSI_NULLS | 開啟 |
| QUOTED_IDENTIFIERS | 開啟 |
| NO_COUNT | 關閉 |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| 資料庫定序 | SQL_Latin1_General_CP1_CI_AS |

## 工作階段和要求
建立連線和工作階段之後，您就可以撰寫查詢並將其提交至 SQL 資料倉儲。

每個查詢都會由一或多個要求識別碼表示。在該連線上提交的所有查詢都是單一工作階段的一部分，因此無法由單一工作階段識別碼表示。

不過，SQL 資料倉儲是散發式的 MPP 系統，相較於 SQL Server，工作階段和要求識別碼的公開方式都有點不同。

工作階段和要求皆由其各自的識別碼以邏輯方式表示。
	
| 識別碼 | 範例值 |
| :--------- | :------------ |
| 工作階段識別碼 | SID123456 |
| 要求 ID | QID123456 |

請注意，工作階段識別碼的前置詞為 SID - 工作階段識別碼的速記格式 - 而要求的前置詞為 QID，此為查詢識別碼的速記格式。

您將需要此資訊，幫助您在監視查詢效能時識別您的查詢。您可以使用 [Azure 入口網站] 和動態管理檢視來監視查詢效能。

若要識別您目前正在使用哪一個工作階段，請使用下列函數：

```
SELECT SESSION_ID()
;
```

若要檢視目前正在執行或近期曾針對資料倉儲執行的所有查詢，您可以使用類似下列的查詢：

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

請注意，這項查詢已封裝在檢視中，所以您可以將其併入您的解決方案。不過，若要查看結果，您必須建立檢視並加以執行。

## 後續步驟
一但連線之後，您就可以開始設計您的資料表。請參閱[表格設計]一文以取得詳細資料。

<!--Image references-->

<!--Azure.com references-->
[連線及查詢]: sql-data-warehouse-connect-query.md
[表格設計]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=July15_HO1-->