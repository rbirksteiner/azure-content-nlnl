<properties 
	pageTitle="如何在 DocumentDB 中使用 .NET SDK 分割資料" 
	description="了解如何使用 Azure DocumentDB .NET SDK 在多個集合中分割 (分區) 資料和路由要求" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="arramac"/>

# 如何在 DocumentDB 中使用 .NET SDK 分割資料

Azure DocumentDB 是一項文件資料庫服務，可讓您在佈建集合期間，使用 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 和 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) (亦稱為**分區化**) 順利調整帳戶。為了讓您更輕鬆地開發資料分割應用程式，並減少分割工作所需的樣板程式碼數量，我們已在 .NET SDK 中加入功能，讓您可以輕鬆地建立跨多個資料分割向外延展的應用程式。

在本文中，我們將探討在 .NET SDK 中的類別和介面，以及如何使用它們來開發資料分割應用程式。

## 使用 DocumentDB SDK 資料分割

在深入探討資料分割之前，讓我們複習一下一些與資料分割相關的基本 DocumentDB 概念。每個 Azure DocumentDB 資料庫帳戶是由一組資料庫所組成，每個資料庫都包含多個集合，而集合可包含預存程序、觸發程序、UDF、文件和相關附件。您可以將集合視為 DocumentDB 中的資料分割，並具有下列屬性：

- 集合不只是邏輯容器，還是實體分割區。因此，在查詢或處理其位於相同集合內的文件時具有效能優勢。
- 集合是 ACID 交易 (亦即，預存程序和觸發程序) 的界限。
- 集合不會強制執行結構描述，因此可以用於相同或不同類型的 JSON 文件。

從 [Azure DocumentDB .NET SDK 1.1.0](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 版起，您可以直接在資料庫中執行文件作業。在內部，[DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 會使用您已為資料庫指定的 PartitionResolver，將要求路由至適當的集合。

每個 PartitionResolver 類別是 [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) 介面的具體實作，方法共有三種 - [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx)、[ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) 和 [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx)。LINQ 查詢和 ReadFeed 迭代器會在內部使用 ResolveForRead 方法，來逐一查看所有符合要求之資料分割索引鍵的集合。同樣地，建立作業會使用 ResolveForCreate 方法，將建立項目路由到正確的資料分割。變更、刪除和讀取等作業不需要變更，因為它們會使用已經包含對應集合參考的文件。

SDK 還包含兩種透過 [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 和 [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) 支援兩種標準資料分割技術 (雜湊和範圍查閱) 的類別。您可以使用這些類別輕鬆地在您的應用程式中加入資料分割邏輯。

## 加入資料分割邏輯，並註冊 PartitionResolver 

以下的程式碼片段將說明如何建立 [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)，並向資料庫註冊 DocumentClient。

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## 建立資料分割中的文件  

註冊 PartitionResolver 之後，您可以直接針對資料庫執行建立和查詢作業，如下所示。在此範例中，SDK 會使用 PartitionResolver 來擷取 UserId、將它雜湊化，並接著使用該值來將建立作業路由至正確的集合。

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## 針對資料分割建立查詢  

您可以透過在資料庫和資料分割索引鍵中傳遞，使用 [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) 方法進行查詢。查詢會傳回資料庫內對應至資料分割索引鍵的所有集合的單一結果集。

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## 在資料庫中針對所有集合建立查詢 

您也可以查詢資料庫內的所有集合，並藉由略過資料分割索引鍵引數來列舉結果，如下所示。

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## 雜湊分割解析程式
有了雜湊分割，分割是根據雜湊函式的值來指派，讓您在一些分割上平均分配要求和資料。這個方法通常用於分割從大量不同用戶端產生或取用的資料，適合用來儲存使用者設定檔、類別目錄項目，以及 IoT ("Internet of Things") 遙測資料。

**雜湊分割：**![說明雜湊分割如何在資料分割中平均分配要求的圖表](media/documentdb-sharding/partition-hash.png "雜湊分割")

跨 *N* 集合的簡易雜湊資料分割配置就是對任何文件計算 *hash(d) mod N* 來判斷它會放置在哪一個集合內。但這個簡單的技術有一個問題，那就是在新增集合或移除集合時無法正常運作，因為這會需要將幾乎所有的資料重新編組。[一致的雜湊](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738)是一種已知的演算法，可透過實作雜湊配置，在新增或移除集合期間將所需的資料移動降至最低加以解說。

[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 類別會實作邏輯，在 [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx) 介面中指定的雜湊函式上建立一致的雜湊環。根據預設，HashPartitionResolver 會使用 MD5 雜湊函式，但是您可以在自己的雜湊實作中更換函式。為達到在不同集合中平均分配文件的目的，HashPartitionResolver 會在內部的雜湊環中為每個集合建立 16 個雜湊或「虛擬節點」，但是您可以變更這個數字，以與用戶端計算數量的資料偏態加以權衡。

**一致的雜湊與 HashPartitionResolver：**![說明 HashPartitionResolver 如何建立雜湊環的圖表](media/documentdb-sharding/HashPartitionResolver.JPG "一致的雜湊")

## 範圍分割解析程式

在定界分割中，分割是根據分割索引鍵是否在特定範圍內所指派。這常用於分割時間戳記屬性 (例如 eventTime 介於 2015 年 4 月 1 日與 2015 年 4 月 14 日之間)。[RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) 類別可協助您維護 Range<T> 與集合自我連結之間的對應。

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) 是個簡單的類別，可針對實作 IComparable<T> 和 IEquatable<T> 的任何類型範圍進行管理，如字串或數字。在讀取和建立作業中，您可以傳入任何任意範圍，解析程式會透過找出與要求範圍交集的資料分割範圍，來找到所有候選集合。在針對時間序列資料執行範圍查詢時，這個功能會很有用。

**定界分割：**

![說明定界分割如何在資料分割中平均分配要求的圖表](media/documentdb-sharding/partition-range.png "定界分割")

一個有關定界分割的特殊案例是，當範圍只是單一離散值時，有時也稱為「查閱分割」。這常用於依區域分割 (例如：斯堪地那維亞的分割包含挪威、丹麥和瑞典)，或用於在多租用戶應用程式中分割租用戶。

## 範例 

看看 [DocumentDB 分割範例 Github 專案](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)，其中的程式碼片段包括如何使用這些 PartitionResolvers，並將它們延伸到實作自己的解析程式以符合特定使用案例，如下所示：

* 如何為 GetPartitionKey 指定任意的 lambda 運算式，並用它來實作複合資料分割索引鍵，或用它來以不同的方式分割不同類型的物件。
* 如何建立簡單的 [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs)，以便使用手動查閱資料表來執行資料分割。此模式通常用於以離散值 (例如區域、租用戶識別碼或應用程式名稱) 為基礎的資料分割。
* 如何建立 [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs)，以定義命名配置、IndexingPolicy 和必須向新集合註冊之預存程序的範本為基礎來自動建立集合。
* 如何建立無配置的 [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs)，可在舊的集合用完之後直接建立新的集合。
* 如何將 PartitionResolver 狀態序列化和還原序列化為 JSON，這樣您便可以在處理之間及在關閉期間共用。您可以在組態檔中 (或甚至在 DocumentDB 集合中) 加以保存。
* [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) 類別可用於將分割動態新增及移除至已根據一致的雜湊進行資料分割的資料庫。在內部，它會使用 [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs)，使用下列四種的其中一種模式，在移轉期間路由讀取和寫入作業：從舊的分割配置中讀取 (ReadCurrent)、讀取新的項目 (ReadNext)、合併兩者的結果 (ReadBoth)，或移轉期間無法使用 (無)。

這些範例是開放原始碼，我們鼓勵您提交提取要求，並附上可幫助其他 DocumentDB 開發人員的貢獻。請參閱[貢獻指導方針](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)，以取得有關如何貢獻的指引。

>[AZURE.NOTE]DocumentDB 會限制建立集合的速率，因此此處顯示的部分範例方法可能需要幾分鐘才能完成。

##常見問題集
**DocumentDB 為什麼支援用戶端與伺服器端資料分割？**

DocumentDB 支援用戶端資料分割的幾個原因如下：

- 真的很難從開發人員中抽走集合的概念，而不會影響到下列其中任何一項：一致性索引/查詢、高可用性和 ACID 交易保證。 
- 文件資料庫通常在定義資料分割策略方面需要彈性，而伺服器端方法可能無法容納。 

**為什麼其他平台 (Node.js、Java 或 Python) 不支援資料分割？**

我們將根據 .NET SDK 客戶的意見反映，逐漸推出其他平台的資料分割支援。

**如何將集合新增或移除至我的資料分割配置？**

如需如何實作重新分割的範例，請查看範例專案中的 DocumentClientHashPartitioningManager 實作。

**如何保存或與其他用戶端共用我的資料分割組態？**

您可以將分割器的狀態序列化為 JSON，並儲存在組態檔中，或甚至 DocumentDB 集合內。如需範例，請查看範例專案中的 RunSerializeDeserializeSample 方法。

**如何鏈結各種不同的資料分割技術？**

您可以藉由實作自己的 IPartitionResolver，在內部使用一或多個現有的解析程式來鏈結 PartitionResolvers。如需範例，請查看範例專案中的 TransitionHashPartitionResolver。

##參考
* [Github 上的資料分割程式碼範例](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [使用 DocumentDB 分割資料的概念](documentdb-partition-data.md)
* [DocumentDB 集合和效能等級](documentdb-performance-levels.md)
* [在 MSDN 的 DocumentDB .NET SDK 文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET 範例](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB 限制](documentdb-limits.md)
* [有關效能秘訣的 DocumentDB 部落格](http://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=58_postMigration-->