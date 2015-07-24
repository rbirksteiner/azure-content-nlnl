<properties 
	pageTitle="多分區查詢" 
	description="使用彈性資料庫用戶端程式跨分區執行查詢。" 
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

# 多分區查詢

## 概觀

**多分區查詢**用於資料收集/報告等工作，這些工作需要跨越數個分區執行查詢。(這與在單一分區上執行所有工作的[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)相反。)

彈性資料庫用戶端程式庫引進一個新的命名空間，稱為 **Microsoft.Azure.SqlDatabase.ElasticScale.Query**，可讓您使用單一查詢和結果來查詢多個分區。它提供一組分區的查詢抽象方法。它也提供替代執行原則，特別是部分結果，以處理查詢許多分區時的失敗。

多分區查詢的主要進入點是 **MultiShardConnection** 類別。如同資料相依路由一樣，API 的用法類似於 **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)** 類別和方法。使用 **SqlClient** 程式庫時，第一個步驟是建立 **SqlConnection**，再建立連接的 **SqlCommand**，然後透過其中一個 **Execute** 方法執行命令。最後，**SqlDataReader** 逐一查看從命令執行傳回的結果集。多分區查詢 API 的用法依照下列步驟：

1. 建立 **MultiShardConnection**。
2. 建立 **MultiShardConnection** 的 **MultiShardCommand**。
3. 執行命令。
4. 透過 **MultiShardDataReader** 來使用結果。 

主要差異是多分區連接的建構方式。其中，**SqlConnection** 在單一資料庫上運作，**MultiShardConnection** 接受 ***分區集合*** 做為輸入。您可以從分區分區來填入對應的集合。然後，使用 **UNION ALL** 語意在分區集合上執行查詢，以組成單一的整體結果。(選擇性) 在命令上使用**ExecutionOptions** 屬性，可將資料列的來源分區名稱加入至輸出。下列程式碼說明如何使用給定的 **ShardMap** (名為 *myShardMap*) 來使用多分區查詢。

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
            	{ 
                	while (sdr.Read())
                    	{ 
                        	var c1Field = sdr.GetString(0); 
                        	var c2Field = sdr.GetFieldValue<int>(1); 
                        	var c3Field = sdr.GetFieldValue<Int64>(2);
                    	} 
             	} 
           } 
    } 
 

請注意 **myShardMap.GetShards()** 的呼叫。這個方法會從分區對應擷取所有分區，並提供簡單的方式跨所有相關的資料庫執行查詢。在呼叫 **myShardMap.GetShards()** 所傳回的集合分區上執行 LINQ 查詢，可以進一步調整多分區查詢的分區集合。結合部分結果原則，多分區查詢目前的功能已設計成可適當處理數十個到數百個分區。多分區查詢目前的限制在於無法驗證所查詢的分區和 Shardlet。資料相依路由可在查詢時驗證給定的分區是屬於分區對應，但多分區查詢不會執行這項檢查。這可能會導致在已從分區對應中移除的資料庫上執行多分區查詢。

## 多分區查詢與分割合併作業

多分區查詢不會驗證所查詢資料庫上的 Shardlet 是否參與進行中的分割/合併作業。這可能會導致不一致的情況，亦即在相同的多分區查詢中，多個資料庫顯示相同 Shardlet 的資料列。請注意這些限制，在執行多分區查詢時，請考慮清空進行中的分割/合併作業和分區對應的變更。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->