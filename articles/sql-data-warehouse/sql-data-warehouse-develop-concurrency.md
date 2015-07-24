<properties
   pageTitle="SQL 資料倉儲中的並行存取和工作負載管理 | Microsoft Azure"
   description="了解 Azure SQL 資料倉儲中的並行存取和工作負載管理以開發解決方案。"
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

# SQL 資料倉儲中的並行存取和工作負載管理
若要在大型 SQL 資料倉儲提供可預測的效能，請實作機制以管理工作負載並行存取和運算資源指派。

本文介紹並行存取和工作負載管理的概念；說明如何實作這兩種功能，以及如何在資料倉儲中控制它們。

## 並行
請務必了解 SQL 資料倉儲中的並行存取由兩個概念控制；**並行查詢** 和 **並行存取插槽**。

並行查詢相當於在同一時間執行的查詢數目。SQL 資料倉儲支援高達 32 個**並行查詢**。無論是序列查詢 (單一執行緒) 或平行查詢 (多執行緒)，每個查詢執行都會視為單一查詢。這是固定的上限，並可套用至所有服務層級。

並行存取插槽是更為動態的概念，相對於資料倉儲的資料倉儲單位 (DWU) 服務等級目標。當您增加配置給 SQL 資料倉儲的 DWU 數目，就會指派更多計算資源。不過，增加 DWU 也會增加可用**並行存取插槽**的數目。

SQL 資料倉儲必須存在於兩個臨界值內。如果有 32 個以上的並行查詢，或超過並行存取插槽的數目，查詢就會排入佇列直到可以滿足兩個臨界值。

每個同時執行的查詢都會取用一或多個並行存取插槽。確切的插槽數目取決於兩個因素：

1. SQL 資料倉儲的 DWU 設定
2. 使用者隸屬的**資源類別** 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| 並行存取插槽耗用量 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

資源類別是 SQL 資料倉儲工作負載管理中不可或缺的一部分，因為它們也會控制配置給查詢的運算資源。這些會涵蓋在下列的工作負載管理一節中。

## 工作負載管理

SQL 資料倉儲會以**資料庫角色**的形式公開四個不同的資源類別，做為其工作負載管理實作的一部分。

角色如下：

- smallrc
- mediumrc
- largerc
- xlargerc

您可以利用下列查詢查看本身的角色：

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

根據預設，每位使用者都是小型資源類別 - smallrc 的成員。不過，任何使用者都可以加入至一或多個較高的資源類別。SQL 資料倉儲將採用最高的角色成員資格來執行查詢。將使用者加入至較高的資源類別會增加該使用者的資源，但它也會耗用更大的並行存取插槽；可能會限制您的並行存取。這是因為多個資源配置給一個查詢時，系統必須限制其他查詢所耗用的資源。天下沒有免費的午餐。

較高的資源類別所控制最重要的資源就是記憶體。大部分有意義大小的資料倉儲資料表都會使用叢集資料行存放區索引。這通常會提供資料倉儲工作負載的最佳效能，而維護工作負載是一項記憶體密集的作業。使用較高的資源類別通常相當有助於索引重建等資料管理作業。

若要增加您的記憶體，只需要將資料庫使用者加入至先前所述的其中一個角色。

您可以使用 `sp_addrolemember` 和 `sp_droprolemember` 程序，將自己新增和移除至工作負載管理資料庫角色。請注意，您需要 `ALTER ROLE` 權限才能執行此動作。您不能使用 ALTER ROLE DDL 語法。您必須使用上述的預存程序。

> [AZURE.NOTE]相較於在工作負載管理群組內外新增使用者，通常更容易的做法是透過永久指派給較高資源類別的個別登入/使用者，起始更密集的作業。

下表詳細說明每個查詢可用的記憶體增加；取決於套用至執行查詢之始用者的資源類別：：

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->
| 可用記憶體 (每個 dist) | 優先順序 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 中型 | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 中型 | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 高 | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 高 | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |


此外，如上所述，指派給使用者的資源類別愈高，並行存取插槽的耗用量就愈大。下表記錄查詢在指定資源類別中的並行存取插槽耗用量。

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| 並行存取插槽耗用量 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

請務必記得，使用中的查詢工作負載必須在並行查詢和並行存取插槽這兩個臨界值內。一旦超越任何一個臨界值，查訊就會開始排入佇列。排入佇列的查詢將根據提交時間的優先順序解決。

## 排入佇列的查詢偵測
若要識別保留在並行存取佇列中的查詢，您可以一律參考 `sys.dm_pdw_exec_requests` DMV。

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
FROM    sys.dm_pdw_exec_requests r
;
```

SQL 資料倉儲具有特定的等候類型以測量並行存取。

如下：
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType 指的是並行存取插槽架構之外的查詢。DMV 查詢和 SELECT @@VERSION 等系統函數都是 localqueries 的範例。

UserConcurrencyResourceType 指的是並行存取插槽架構之內的查詢。針對使用者資料表的查詢代表會使用此資源類型的範例。

DmsConcurrencyResourceType 指的是資料移動作業所產生的等候。

備份資料庫時，可以看到 BackupConcurrencyResourceType。此資源類型的最大值為 1。如果在同一時間要求多個備份，其他備份會排入佇列。


若要執行目前以排入佇列之要求的分析，以找出要求正在等候哪些資源，請參考 `sys.dm_pdw_waits` DMV。

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

若只檢視由指定查詢取用的資源等候，您可以參考 `sys.dm_pdw_resource_waits` DMV。資源等候時間只會測量等候提供資源的時間，與訊號等候時間相反，後者是基礎 SQL Server 將查詢排程到 CPU 所需的時間。

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID()
;
```

最後，SQL 資料倉儲會提供 `sys.dm_pdw_wait_stats` DMV 給等候的歷史趨勢分析。

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->