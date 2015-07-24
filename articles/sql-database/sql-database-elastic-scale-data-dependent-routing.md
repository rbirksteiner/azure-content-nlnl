<properties 
	pageTitle="資料相依路由" 
	description="如何將 ShardMapManager 用於 Azure SQL DB 功能之一的資料相依路由。" 
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
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

#資料相依路由

在分區化環境中，**ShardMapManager** 類別可讓 ADO.NET 應用程式輕鬆地將資料庫查詢和命令指向適當的實體資料庫。這稱為**資料相依路由**，也是使用分區化資料庫時的一種基本模式。在使用資料相依路由的應用程式中，每個特定的查詢或交易會限制每個要求只能存取單一資料庫。

使用資料相依路由時，應用程式在分區化環境中不需要追蹤不同的連接字串，或與不同資料片段相關聯的 DB 位置。相反地，[分區對應管理員](sql-database-elastic-scale-shard-map-management.md)會根據分區對應中的資料和分區化索引鍵的值 (應用程式要求的目標)，負責在必要時提供正確資料庫的開啟連接。(此索引鍵通常是 *customer_id*、*tenant_id*、*date_key*，或作為資料庫要求基本參數的其他一些特定的識別項)。

## 在資料相依路由應用程式中使用 ShardMapManager 

對於使用資料相依路由的應用程式，每個應用程式定義域在初始化期間，都應該使用 Factory 呼叫 **GetSQLShardMapManager** 來具現化一次 **ShardMapManager**。

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

此範例中會初始化 **ShardMapManager** 及其包含的特定 **ShardMap**。

對於不操作分區對應本身的應用程式，在用來取得 **ShardMapManager** 的 Factory 方法中 (上述範例中的 *smmConnectionString*)，使用的認證在連接字串所參考的**全域分區對應**資料庫上應該只具有唯讀權限。這些認證通常不同於用來對分區對應管理員開啟連接的認證。請參閱[在彈性資料庫用戶端程式庫中使用認證](sql-database-elastic-scale-manage-credentials.md)。

## 叫用資料相依路由 

**ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** 方法傳回的 ADO.Net 連接可根據 **key** 參數的值，對適當的資料庫發出命令。**ShardMapManager** 會將分區資訊快取在應用程式中，因此這些要求通常不需要針對**全域分區對應**資料庫進行資料庫尋查。

* **key** 參數做為分區對應中的查閱索引鍵，以決定要求的適當資料庫。 

* **connectionString** 只用來傳遞使用者認證給所需的連接。此 *connectionString* 中不含任何資料庫名稱或伺服器名稱，因為此方法會使用 **ShardMap** 來決定資料庫和伺服器。

* **connectionOptions** 列舉用來指出傳遞開啟的連接時是否驗證。建議使用 **ConnectionOptions.Validate**。在可能因為分割或合併作業而變更分區對應和將資料列移至其他資料庫的環境中，驗證可確保根據索引鍵值的資而快取的資料庫查閱仍然正確。驗證包括在將連接傳遞至應用程式之前，對目標資料庫上的本機分區對應 (不是全域分區對應) 執行簡短查詢。

如果本機分區對應驗證失敗 (表示快取不正確)，分區對應管理員會查詢全域分區對應來取得查閱的新正確值、更新快取，然後取得並傳回適當的資料庫連接。

唯有當應用程式在線上而不預期分區對應變更時，才接受 **ConnectionOptions.None** (不驗證)。在此情況下，快取的值可假定為永遠正確，可放心地略過對於目標資料庫的額外往返驗證呼叫。這樣可減少交易延遲和資料庫流量。也可透過組態檔中的值來設定 **connectionOptions**，以指出在一段時間內是否預期有分區化變更。

下列程式碼範例使用分區對應管理員，根據整數索引鍵 **CustomerID** 的值，並使用名為 **customerShardMap** 的 **ShardMap** 物件，以執行資料相依路由。

## 範例：資料相依路由 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

請注意，範例中使用 **OpenConnectionForKey** 方法，將新的已開啟連接傳遞至正確的資料庫，而不是使用 **SqlConnection** 的建構函式，後面接著對連接物件的 **Open()** 呼叫。以這種方式使用連接仍可充分利用 ADO.Net 連接共用。只要一次一個分區就可滿足交易和要求，則已經使用 ADO.Net 的應用程式中只需要如此修改。

如果您的應用程式將非同步程式設計與 ADO.Net 搭配使用，則也可以使用 **OpenConnectionForKeyAsync** 方法。其行為相當於 ADO.Net **Connection.OpenAsync** 方法的資料相依路由。

## 與暫時性錯誤處理整合 

在雲端中開發資料存取應用程式時，最佳做法就是確保應用程式會攔截在連接至或查詢資料庫時的暫時性錯誤，並且將作業重試數次之後才會擲回錯誤。[暫時性錯誤處理](http://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx)中討論雲端應用程式的暫時性錯誤處理。
 
暫時性錯誤處理可以自然地與資料相依路由模式並存。主要需求是重試整個資料存取要求，包括用以取得資料相依路由連接的 **using** 區塊。上述範例可以改寫如下 (請注意反白顯示的變更)。

### 範例 – 資料相依路由與暫時性錯誤處理 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // 該客戶識別碼的分區連接 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // 執行簡單的命令 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Update completed"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


當您建置彈性資料庫範例應用程式時，自動會下載實作暫時性錯誤處理所需的封裝。另外也可以從[企業程式庫 - 暫時性錯誤處理應用程式區塊](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/) (英文) 取得封裝。請使用 6.0 版或更新版本。

## 交易一致性 

對於分區範圍內的所有作業，都保證交易式屬性。例如，透過資料相依路由提交的交易，都在連接的目標分區範圍內執行。目前無法將多個連接編列到交易中，因此對於跨分區執行的作業，不提供交易式保證。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->