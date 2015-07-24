<properties 
	pageTitle="在彈性資料庫用戶端程式庫中管理認證" 
	description="如何設定彈性資料庫應用程式的正確認證層級 (管理員到唯讀)。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# 在彈性資料庫用戶端程式庫中管理認證

[彈性資料庫用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)使用認證來執行不同類型的作業 - 特別是建立或操作[分區對應管理員](sql-database-elastic-scale-shard-map-management.md)、參考現有的分區對應管理員以取得分區的相關資訊，以及連接到分區。以下將討論這些作業類型的認證。


* **分區對應存取的管理認證**：對於操作分區對應的應用程式具現化 **ShardMapManager** 物件時會使用管理認證。彈性擴縮用戶端程式庫的使用者必須建立必要的 SQL 使用者和 SQL 登入，也要確定將全域分區對應資料庫及所有分區資料庫的讀取/寫入權限授與他們。在分區對應上執行變更時，這些認證用來維護全域分區對應和本機分區對應。比方說，使用管理認證來具現化分區對應管理員物件，如下列程式碼所示： 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     變數 **smmAdminConnectionString** 是包含的管理認證的連接字串。使用者識別碼和密碼提供分區對應資料庫以及個別分區的讀取/寫入存取權。管理連接字串也包含伺服器名稱和資料庫名稱，以識別全域分區對應資料庫。以下是針對該用途的一般連接字串：

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

     請勿使用 "username@server" 格式的 [使用者識別碼] 值 -- 而只是使用 "username"。這是因為認證必須同時適用於入分區對應管理員資料庫和個別分區 (可能位於不同的伺服器上)。
     
* **分區對應管理員存取的使用者認證**：在不管理分區對應的應用程式中具現化分區對應管理員時，請使用在全域分區對應上具有唯讀權限的認證。在這些認證下，從全域分區對應所擷取的資訊用於[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)，以及填入用戶端的分區對應快取。認證是透過 **GetSqlShardMapManager** 的相同呼叫模式來提供，如上所示：
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     請注意，我們使用 **smmReadOnlyConnectionString**，以反映使用不同的認證以代表**非管理員**使用者進行此存取 - 這些認證不應提供全域分區對應上的寫入權限。

* **使用者資料存取的使用者認證**：使用 **OpenConnectionForKey** 方法來存取索引鍵相關聯的分區時，需要額外的認證。這些認證必須提供唯讀權限來存取分區上的本機分區對應資料表。在分區上執行資料相依路由的連線驗證時，這是必要的。下列程式碼片段說明如何在資料相依路由的情況下以使用者認證進行使用者資料存取：
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    在此範例中，**smmUserConnectionString** 保留使用者認證的連接字串。在 Azure SQL DB 中，以下是使用者認證的一般連接字串：

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

    在系統管理員認證中，請勿使用 "username@server" 格式的 [使用者識別碼] 值 -- 而只使用 "username"。另請注意，連接字串不包含伺服器名稱和資料庫名稱。這是因為 **OpenConnectionForKey** 呼叫會根據索引鍵自動將連接導向至正確的分區。因此，不可提供資料庫名稱和伺服器名稱。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->