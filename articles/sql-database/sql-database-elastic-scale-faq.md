<properties 
	pageTitle="Azure SQL 彈性延展常見問題集" 
	description="有關 Azure SQL Database 彈性延展的常見問題集。" 
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

# 彈性資料庫工具常見問題集 

#### 如果我的每一分區各有單一租用戶，卻沒有分區化索引鍵，我要如何填入結構描述資訊的分區化索引鍵？
結構描述資訊物件只是用來分割合併案例。如果應用程式原本就是單一租用戶，則其並不需要分割合併工具，因此就不需要填入結構描述資訊物件。

#### 我已佈建資料庫並已擁有分區對應管理員，要如何將這個新資料庫註冊為分區？
請參閱[使用彈性資料庫用戶端程式庫新增分區到應用程式](sql-database-elastic-scale-add-a-shard.md)。

#### 使用彈性資料庫的成本要多少？
使用彈性資料庫用戶端程式庫不會產生任何成本。只有您使用於分區和分區對應管理員的 Azure SQL Database 和您為分割合併工具佈建的 Web/背景工作角色才會產生成本。

#### 為什麼從其他伺服器新增分區時我的認證無法使用？
請勿使用 “User ID=username@servername” 格式的認證，只需使用 “User ID = username”。此外，請確認 "username" 登入擁有分區的權限。

#### 每次啟動應用程式，都必須建立分區對應管理員和填入分區嗎？
否，建立分區對應管理員 (例如，**[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) 是一次性作業。您的應用程式應在應用程式啟動時使用 **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** 呼叫。每一應用程式網域應只有一個這類呼叫。

#### 我有關於使用彈性資料庫工具的疑問，要如何尋求解答？ 
請在 [Azure SQL Database 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)上與我們聯繫。

#### 當我使用分區化索引鍵連接資料庫時，我仍然可以在相同的分區上查詢其他分區化索引鍵的資料。這是原先的設計嗎？
彈性延展 API 可讓您針對分區化索引鍵連接至正確的資料庫，但是不提供分區化索引鍵篩選。如有必要，請新增 **WHERE** 子句到您的查詢，以將範圍限制在提供的分區化索引鍵。

#### 我可以在分區集中針對個別的分區使用不同的 Azure Database 版本嗎？
可以，分區是個別的資料庫，因此可以有一個分區是「高階」版，另一個是「標準」版。此外，在分區存留期間，分區的版本可以多次相應增加或相應減少。

#### 分割合併工具會在分割或合併作業期間佈建 (或刪除) 資料庫嗎？ 
不會。如果是「分割」作業，目標資料庫必須有適當的結構描述，而且必須向分區對應管理員登錄。如果是「合併」作業，您必須從分區對應管理員刪除分區，然後再刪除資料庫。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=62-->