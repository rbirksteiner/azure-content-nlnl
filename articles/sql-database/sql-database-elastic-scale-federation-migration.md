<properties 
    pageTitle="同盟移轉" 
    description="概述將使用同盟功能建立的現有應用程式移轉至彈性資料庫模型的步驟。" 
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
    ms.date="05/21/2015" 
    ms.author="sidneyh"/>

# 同盟移轉 

Azure SQL 資料庫同盟功能即將於 2015 年 9 月的 Web/企業版中淘汰。屆時，採用同盟功能的應用程式將停止執行。為了確保成功移轉，強烈建議儘快開始移轉，以便充分的規劃和執行。本文件提供同盟移轉公用程式的內容、範例和簡介，以說明如何成功地將目前的同盟應用程式順暢地移轉至[彈性資料庫用戶端程式庫](http://go.microsoft.com/?linkid=9862592) API 以進行分區化。本文件的目的是引導您進行建議的步驟來移轉同盟應用程式，完全不需要任何資料移動。

將現有同盟應用程式移轉至使用彈性資料庫工具的應用程式時有三個主要步驟。

1. [從同盟根建立分區對應管理員](#create-a-shard-map-manager-from-a-federation-root) 
2. [修改現有的應用程式](#modify-the-existing-application)
3. [切換移出現有的同盟成員](#switch-out-existing-federation-members)
    

### 移轉範例工具
為了協助完成此程序，已建立[同盟移轉公用程式](http://go.microsoft.com/?linkid=9862613)。此公用程式將完成步驟 1 和 3。

## 從同盟根建立分區對應管理員
移轉同盟應用程式的第一個步驟是將同盟根資料庫的中繼資料移轉到分區對應管理員的建構。

![將同盟根複製到分區對應管理員][1]
 
在測試環境中從現有的同盟應用程式開始。
 
使用 [同盟移轉公用程式]，將同盟根中繼資料複製到彈性資料庫用戶端程式庫的[分區對應管理員](http://go.microsoft.com/?linkid=9862595)的建構中。類似於同盟根，分區對應管理員資料庫是獨立的資料庫，其中包含分區對應 (亦即，同盟)、分區 (亦即，同盟成員) 的參考和個別的範圍對應。

將同盟根資料庫複製到分區對應管理員就是複製和轉譯中繼資料。不會更改同盟根上的中繼資料。請注意，使用同盟移轉公用程式來複製同盟根是時間點作業，對同盟根或分區對應所做的任何變更，將不會反映在其他各自的資料存放區。如果在測試新的 API 時變更同盟根，同盟移轉公用程式可用來整理分區對應以呈現目前的狀態。

![移轉現有的應用程式以使用彈性資料庫工具 API][2]

## 修改現有的應用程式 

準備好分區對應管理員，並將同盟成員和範圍註冊到分區對應管理員之後 (透過移轉公用程式完成)，就可以修改現有的同盟應用程式來使用彈性資料庫用戶端程式庫。如上圖所示，透過這些 API 連線的應用程式連線將透過分區對應管理員，遞送至適當的同盟成員 (現在也是分區)。將同盟成員對應至分區對應管理員可讓應用程式的兩個版本 (一個使用同盟，另一個使用彈性資料庫用戶端程式庫) 並行執行以驗證功能。

在移轉應用程式期間，現有的應用程式需要進行兩項重大的修改。


#### 變更 1：具現化分區對應管理員物件： 

不同於同盟，彈性資料庫工具 API 會透過 **ShardMapManager** 類別與分區對應管理員互動。**ShardMapManager** 物件和分區對應的個體化可透過以下方式完成：
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 變更 2：將連接遞送到適當的分區 

使用同盟後，即可透過 USE FEDERATION 命令連線到特別的同盟成員，如下所示：

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

使用彈性資料庫工具 API 時，透過[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)並搭配 **RangeShardMap** 類別的 **OpenConnectionForKey** 方法，以建立特定分區的連接。

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

本小節的步驟是必要的步驟，不過可能無法解決所有發生的移轉情況。如需詳細資訊，請參閱[彈性資料庫工具的概念式概觀](sql-database-elastic-scale-introduction.md)和 [API 參考資料](http://go.microsoft.com/?linkid=9862604) (英文)。

## 切換移出現有的同盟成員 

![切換移出分區的同盟成員][3]

一旦加入彈性資料庫工具 API 來修改應用程式之後，移轉同盟應用程式的最後一個步驟就是 **SWITCH OUT** (切換移出) 同盟成員 (如需詳細資訊，請參閱 MSDN 參考的 [ALTER FEDERATION (Azure SQL Database](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx))。對於特定同盟成員發出 **SWITCH OUT** 陳述式的最終結果是移除呈現同盟成員成員一般 Azure SQL 資料庫的所有同盟建構和中繼資料，這和其他任何 Azure SQL 資料庫並無不同。

請注意，對於同盟成員發出 **SWITCH OUT** 陳述式是單向作業，無法復原。一旦執行，產生的資料庫就不能加回到同盟，而且 USE FEDERATION 命令將不再適用於此資料庫。

為了執行切換，已經對 ALTER FEDERATION 命令加入另一個引數，以便切換移出同盟成員。雖然可以針對個別的同盟成員發出此命令，但同盟移轉公用程式能夠以程式設計的方式逐一查看每個同盟成員，並執行切換作業。

對於所有現有同盟成員執行切換後，應用程式的移轉隨即完成。當所有同盟成員都已切換移出時，建議在根目錄執行 DROP FEDERATION 作業。這不會影響到任何先前的成員，但會允許根資料庫能輕易地自 Web 和 Business Edition 移轉。
  
同盟移轉公用程式能夠：

1.    將同盟根資料庫複製到分區對應管理員。使用者可以選擇將現有分區對應管理員放置在新的 Azure SQL 資料庫 (建立) 或現有的同盟根資料庫。
2.    針對同盟中的所有同盟成員發出 SWITCH OUT。


## 功能比較

雖然彈性資料庫工具提供許多其他功能 (例如，[多分區查詢](sql-database-elastic-scale-multishard-querying.md)、[分割及合併分區](sql-database-elastic-scale-overview-split-and-merge.md)、[分區彈性](sql-database-elastic-scale-elasticity.md)、[用戶端快取](sql-database-elastic-scale-shard-map-management.md)等)，但有一些在彈性資料庫工具中不支援但值得注意的同盟功能。
  
- 使用 **FILTERING=ON**。相反地，建議您針對資料列篩選使用資料列層級安全性 (RLS)。就像在同盟中篩選一樣，RLS 會自動新增一個述詞到分區資料表上的所有查詢。如需詳細資訊，請參閱[使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)。 
 
 或者，您可以在針對分區發出的查詢中建立篩選邏輯。例如：

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 產生的結果同於：

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- 彈性資料庫工具「分割」功能並未完全上線。在分割作業期間，各個小分區都會在移動期間離線。
- 分割功能需要手動佈建資料庫和管理結構描述。

## 其他考量

* Web 和企業版及同盟將於 2015 年秋季淘汰。在同盟應用程式的移轉中，強烈建議對於 Basic、Standard 和 Premium 版執行效能測試。 

* 在同盟成員上執行 SWITCH OUT 陳述式，可讓產生的資料庫利用所有 Azure SQL 資料庫功能 (亦即，新版本、備份、PITR、稽核等。)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=62-->