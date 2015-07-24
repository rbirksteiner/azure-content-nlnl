<properties
   pageTitle="如何從 .NET 應用程式使用 Azure 搜尋服務 | Microsoft Azure"
   description="如何從 .NET 應用程式使用 Azure 搜尋服務"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/08/2015"
   ms.author="brjohnst"/>

# 如何從 .NET 應用程式使用 Azure 搜尋服務 #

本文會逐步指引您學會如何使用 [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)。您可以透過 .NET SDK，利用 Azure 搜尋服務在應用程式中實作豐富的搜尋經驗。

## Azure 搜尋服務 SDK 中有哪些內容 ##

SDK 包含用戶端程式庫 `Microsoft.Azure.Search`。該程式庫可讓您管理索引、資料來源及索引子，以及更新和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。

用戶端程式庫會定義類別，例如 `Index`、`Field` 及 `Document`，以及定義作業，例如 `SearchServiceClient` 和 `SearchIndexClient` 類別上的 `Indexes.Create` 和 `Documents.Search`。這些類別可編成以下命名空間：

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

目前的 Azure 搜尋服務 .NET SDK 版本是發行前版本。如果您想提供意見反應給我們，讓我們可以將您的意見併入第一個穩定版本中，請瀏覽我們[意見回應頁面](http://feedback.azure.com/forums/263029-azure-search)。

.NET SDK 支援 `2015-02-28` 版 Azure 搜尋服務 REST API，該項目已記載於 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)。新功能*不*在此版本中，例如支援 Microsoft 的自然語言處理器或 `moreLikeThis` 搜尋參數，都在[預覽版](search-api-2015-02-28-preview.md)中，SDK 還沒有提供這些功能。您可以查看[搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)或 [Azure 搜尋服務的最新更新](search-latest-updates.md)，了解任一功能的狀態更新。

此 SDK 中其他不支援的功能包括：

  - [管理作業](https://msdn.microsoft.com/library/azure/dn832684.aspx)。管理作業包括佈建 Azure Search 服務和管理 API 金鑰。未來會以個別的 Azure 搜尋服務 .NET Management SDK 支援這些作業。

## SDK 的需求 ##

1. Visual Studio 2013 或更新版本。

2. 擁有 Azure Search 服務。為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。[在入口網站建立服務](search-create-service-portal.md)可協助您執行這些步驟。

3. 使用 Visual Studio 中的 [管理 NuGet 封裝] 下載 Azure 搜尋服務 .NET SDK [NuGet 封裝](http://www.nuget.org/packages/Microsoft.Azure.Search)。只要在 NuGet.org 上搜尋封裝名稱 `Microsoft.Azure.Search` 即可。務必選取 [包含發行前版本 (Include Prerelease)]，確保 SDK 的發行前版本會出現在搜尋結果中。

Azure 搜尋服務 .NET SDK 支援以 .NET Framework 4.0 或更新版為目標的應用程式，以及以 Windows 8.1 和 Windows Phone 8.1 為目標的 Windows 市集應用程式。不支援 Silverlight。

## 主要情節 ##

您必須在搜尋應用程式中執行一些作業。本教學課程中涵蓋了這些主要情節：

- 建立索引
- 透過文件填入索引
- 使用全文檢索搜尋和篩選來搜尋文件

這些情節的說明都隨附範例程式碼，歡迎在您的應用程式中使用這些程式碼片段。

### 概觀 ###

我們將探索的範例應用程式，會建立名為 "hotels" 的新索引，並透過一些文件填入索引，然後執行一些搜尋查詢。以下是主要程式，該程式顯示了整體流程：

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

我們會逐步說明，首先必須建立新的 `SearchServiceClient`。此物件可讓您管理索引。若要建構此物件，必須提供 Azure Search 服務名稱和系統管理 API 金鑰。

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE]如果提供不正確的金鑰 (例如，需要系統管理金鑰卻提供查詢金鑰)，則 `SearchServiceClient` 會在您第一次用它呼叫作業方法時 (例如 `Indexes.Create`) 擲回 `CloudException`，並附上「禁止」的錯誤訊息。如果遇到此情況，請按兩下我們的 API 金鑰。

以下幾行程式碼會呼叫建立名為 "hotels" 索引的方法，如果索引已存在，請加以刪除。稍後我們會逐項執行這些方法。

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

接著必須填入索引。若要這麼做，必須要有 `SearchIndexClient`。取得的方式有兩種：建構該項目，或用 `SearchServiceClient` 呼叫 `Indexes.GetClient`。為方便起見，我們使用後者。

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE]在一般搜尋應用程式中，索引的管理和填入是由搜尋查詢的個別元件所處理。使用 `Indexes.GetClient` 可以很輕易填入索引，因為可以節省提供其他 `SearchCredentials` 的時間。執行方法是將用於建立 `SearchServiceClient` 的系統管理金鑰傳遞至新的 `SearchIndexClient`。但在執行查詢的應用程式中，最好直接建立 `SearchIndexClient`，如此一來就可以傳遞查詢金鑰，而非系統管理金鑰。這不但符合最低權限的準則，也可以讓您的應用程式更安全。您可以[在這裡](https://msdn.microsoft.com/library/azure/dn798935.aspx)深入了解系統管理金鑰和查詢金鑰。

現在我們有 `SearchIndexClient`，可以開始填入索引。這要使用另一個方法來執行，稍後我們會逐項說明。

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

最後我們會執行一些搜尋查詢以顯示結果，然後再次使用 `SearchIndexClient`：

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

如果使用有效的服務名稱和 API 金鑰執行此應用程式，則輸出應該會看起來如下：

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []
    Complete.  Press any key to end application...

本文結尾會提供完整的應用程式原始程式碼。

接著，我們要進一步了解 `Main` 所呼叫的每個方法。

### 建立索引 ###

建立 `SearchServiceClient`後，`Main` 會接著刪除 "hotels" 索引 (如果已經存在)。刪除方法如下：

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

此方法使用指定的 `SearchServiceClient` 查看索引是否存在，如果存在，就加以刪除。

> [AZURE.NOTE]為簡單起見，本文的範例程式碼使用 Azure 搜尋服務 .NET SDK 的同步方法。我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。例如，您可以在以上方法中使用 `ExistsAsync` 和 `DeleteAsync`，而非 `Exists` 和 `Delete`。

接著，`Main` 會呼叫此方法建立新的 "hotels" 索引：

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

此方法會以定義新索引結構描述的 `Field` 物件清單，建立新的 `Index` 物件。每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。除了欄位之外，您還可以新增評分設定檔、建議工具或 CORS 選項到 Index (基於簡化目的，範例已省略這些選項)。如需 Index 物件以及其組成部分的詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) 中的 SDK 參考和 [Azure 搜尋服務 REST API 參考](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

### 填入索引 ###

`Main` 的下一步是填入新建立的索引。執行方法如下：

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

此方法分四個部分。第一個部分會建立 `Hotel` 物件的陣列，做為我們上傳到索引的輸入資料。為簡單起見，此資料採硬式編碼。在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二部分會為每個 `Hotel` 建立 `IndexAction`，然後將建立的項目群組在一起為新的 `IndexBatch`。接著以 `Documents.Index` 方法將該 Batch 上傳至 Azure 搜尋服務索引。

> [AZURE.NOTE]在此範例中，我們只上傳文件。如果您想將執行的變更合併至現有文件，或刪除文件，可以用對應的 `IndexActionType` 建立 `IndexAction`。在此範例中不需指定 `IndexActionType`，因為預設為 `Upload`。

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。如果您的 Azure Search 服務無法將 Batch 中的一些文件編制索引，則 `Documents.Index` 會擲回 `IndexBatchException`。如果您在服務負載過重時編制文件的索引，就會發生此情況。**我們強烈建議您在程式碼中明確處理此情況。** 您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

最後，方法會延遲兩秒。您的 Azure 搜尋服務中會發生非同步索引編製，因此範例應用程式必須稍待一會，才能確定文件已準備好可供搜尋。通常只有在示範、測試和範例應用程式中，才需要這類延遲。

#### .NET SDK 如何處理文件 ####

您可能想知道 Azure 搜尋服務 .NET SDK 如何能夠將使用者定義的類別執行個體 (例如 `Hotel` 上傳至索引。為了回答這問題，我們來看一下 `Hotel` 類別：

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

首先要注意的是，每個 `Hotel` 的公用屬性會對應索引定義中的欄位，但這之中有一項關鍵的差異：每個欄位的名稱會以小寫字母 (「駝峰式命名法」) 為開頭，而每個 `Hotel` 的公用屬性名稱會以大小字母 (「巴斯卡命名法」) 為開頭。這在執行資料繫結、而目標結構描述在應用程式開發人員控制範圍之外的 .NET 應用程式中很常見。與其違反 .NET 命名方針，使屬性名稱為駝峰式命名法，您可以改用 `[SerializePropertyNamesAsCamelCase]` 屬性，告訴 SDK 自動將屬性名稱對應至駝峰式命名法。

第二個要注意的是，`Hotel` 類別為公用屬性的資料類型。這些屬性的 .NET 類型會對應至索引定義中，與其相當的欄位類型。例如，`Category` 字串屬性會對應至 `category` 欄位 (此欄位屬於 `Edm.String` 類型)。`bool?` 與 `Edm.Boolean`、`DateTimeOffset?` 與 `Edm.DateTimeOffset` 等，它們之間也有類似的類型對應。類型對應的特定規則和 `Documents.Get` 方法已一起記載於 [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx)。請注意，如 `bool` 和 `int` 之類的實值類型，在 `Hotel` 類別中均為 Null，因為所有基本欄位類型在 Azure 搜尋服務中均為 Null。

這讓使用您的類別做為文件可雙向有效；您也可以擷取搜尋結果，然後讓 SDK 將結果自動還原序列化為您選擇的類型，我們會在下一節中看到這部分。

> [AZURE.NOTE]Azure 搜尋服務 .NET SDK 還支援使用 `Document` 類別的動態類型文件，也就是欄位名稱與欄位值的索引鍵/值對應。當您在設計階段卻不知道索引的結構描述時，這很實用，否則要繫結到特定模型類別會很麻煩。SDK 中所有處理文件的方法，都有可搭配 `Document` 類別使用的多載，以及使用泛型類型參數的強類型多載。本教學課程的範例程式碼只使用了後者。如需 `Document` 類別的詳細資訊，請參閱[這裡](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)。

### 搜尋索引中的文件 ###

此範例應用程式的最後一個步驟是搜尋索引中的一些文件。執行方法如下：

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response)
        {
            Console.WriteLine(result.Document);
        }
    }

首先，此方法會建立新的 `SearchParameters` 物件。該物件用於指定查詢的其他選項，例如排序、篩選、分頁及 Facet。在此範例中，我們只設定 `Filter` 屬性。

下一步是實際執行搜尋查詢，我們透過 `Documents.Search` 方法完成此步驟。在此方法中，我們傳遞搜尋文字做為字串，並再加上先前建立的搜尋參數。我們也指定了 `Hotel` 做為 `Documents.Search` 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 `Hotel` 類型的物件。

最後，此方法會反覆查詢搜尋結果中的所有相符項，然後將每個文件列印至主控台。

我們來進一步了解如何呼叫此方法：

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

在第一個呼叫中，我們要尋找含有 "fancy" 或 "wifi" 查詢字詞的所有文件。在第二個呼叫中，搜尋字詞設為 "*"，這表示「全部尋找」。如需搜尋查詢運算式語法的詳細資訊，請參閱[這裡](https://msdn.microsoft.com/library/azure/dn798920.aspx)。

第二個呼叫使用 OData `$filter` 運算式 `category eq 'Luxury'`。這會將搜尋限制為只傳回 `category` 欄位完全符合 "Luxury" 字串的文件。您可以在[這裡](https://msdn.microsoft.com/library/azure/dn798921.aspx)找到更多 Azure 搜尋服務支援的 OData 語法。

現在您已經知道這兩個呼叫的用途，應該可以輕易了解為什麼它們的輸出會看起來如下：

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []

第一個搜尋結果傳回兩個文件。第一個文件在名稱裡有 "Fancy"，而第二個在 `tags` 欄位裡有 "wifi"。第二個搜尋傳回兩個文件，它們剛好是索引中 `category` 欄位設為 "Luxury" 的唯一兩個文件。

此步驟已完成本教學課程，但別就此結束。**後續步驟**會提供可深入了解 Azure 搜尋服務的其他資源。

## 後續步驟 ##

- 使用[影片與其他範例和教學課程](https://msdn.microsoft.com/library/azure/dn818681.aspx)加深知識。
- 了解這一版 Azure 搜尋服務 SDK 的功能：[Azure 搜尋服務概觀](https://msdn.microsoft.com/library/azure/dn798933.aspx)。
- 檢閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)，了解命名各種物件的規則。
- 檢閱 Azure 搜尋服務[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。


## 範例應用程式的原始程式碼 ##

以下是本逐步解說課程中所使用的完整範例應用程式原始程式碼。請注意，如果想要建置並執行範例，您必須在 Program.cs 中，以您的值取代服務名稱和 API 金鑰預留位置。

Program.cs：

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        { 
                            HotelId = "1058-441", 
                            HotelName = "Fancy Stay", 
                            BaseRate = 199.0, 
                            Category = "Luxury", 
                            Tags = new[] { "pool", "view", "concierge" }, 
                            ParkingIncluded = false, 
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                            Rating = 5, 
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "666-437", 
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel() 
                        { 
                            HotelId = "970-501", 
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "956-532", 
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel() 
                    {
                            HotelId = "566-518", 
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };

                try
                {
                    indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs：

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }
 

<!---HONumber=July15_HO2-->