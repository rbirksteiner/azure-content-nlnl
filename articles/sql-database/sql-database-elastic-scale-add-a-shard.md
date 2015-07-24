<properties 
	pageTitle="使用彈性資料庫工具加入分區" 
	description="如何使用 Elastic Scale API 將新的分區新增至分區集。" 
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
	ms.date="04/15/2015" 
	ms.author="sidneyh"/>

# 使用彈性資料庫工具加入分區

## 為新的範圍或索引鍵加入分區  

對於已經存在的分區對應，應用程式通常只需要加入新的分區，以處理預期來自新的索引鍵或索引鍵範圍的資料。例如，以租用戶識別碼分區化的應用程式，可能需要為新的租用戶佈建新的分區，或者，每月分區化的資料可能需要在每個新月份開始之前佈建新的分區。

如果索引鍵值的新範圍尚不屬於現有的對應，則新增分區並將新的索引鍵或範圍與該分區產生關聯就非常簡單。

### 範例：將分區及其範圍加入至現有的分區對應
在下列範例中，已建立名為 **sample_shard_2** 的資料庫及其中的所有必要結構描述物件，以保存範圍 [300, 400)。

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


## 為現有範圍的空白部分加入分區  

在某些情況下，您可能已將某個範圍對應至分區，並在其某些部分填入資料，但現在您想讓後續的資料導向至不同的分區。例如，假設您依日期範圍分區，並且已配置 50 天給某個分區，但在第 24 天，您想要將後續資料分配到不同的分區。彈性資料庫[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)可以執行這項作業，但如果不需要移動資料 (例如，尚不存在日期範圍 [25, 50) 的資料，亦即第 25 天 (含) 到第 50 天 (不含)，您可以直接使用分區對應管理 API 完全執行此作業。

### 範例：分割範圍並將空白部分指派給新增的分區

已建立名為 "sample_shard_2" 的資料庫，及其中所有必要的結構描述物件。

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**重要**：只有在您確定更新對應的範圍是空白時，才可使用這項技術。上述方法並不會檢查要移動的資料範圍，因此您最好在程式碼中納入檢查。如果資料列存在於要移動的範圍中，則實際的資料分佈將不會符合更新的分區對應。在這些情況下，請改用[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)執行作業。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO2-->