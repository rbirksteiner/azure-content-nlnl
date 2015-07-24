<properties 
	pageTitle="將資料匯入 DocumentDB | Azure" 
	description="深入了解如何使用開放原始碼 DocumentDB 資料移轉工具，將資料從各種來源 (包括 JSON 檔案、CSV 檔案、SQL、MongoDB、Azure 資料表儲存體，以及 DocumentDB 集合) 匯入到 DocumentDB。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="stbaro"/>

# 將資料匯入 DocumentDB #

此文章將說明如何使用開放原始碼 DocumentDB 資料移轉工具，將資料從各種來源 (包括 JSON 檔案、CSV 檔案、SQL、MongoDB、Azure 資料表儲存體，以及 DocumentDB 集合) 匯入到 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)。

閱讀本文後，您將能夠回答下列問題：

-	如何將 JSON 檔案資料匯入 DocumentDB？
-	如何將 CSV 檔案資料匯入 DocumentDB？
-	如何將 SQL Server 資料匯入 DocumentDB？
-	如何將 MongoDB 資料匯入 DocumentDB？
-	如何將資料從 Azure 資料表儲存體匯入 DocumentDB？
-	如何將資料在 DocumentDB 集合之間進行移轉？

##<a id="Prerequisites"></a>必要條件 ##

在依照本文中的指示進行之前，請確定已安裝下列項目：

- [Microsoft.NET Framework 4.51](http://www.microsoft.com/net/downloads) 或更高版本。

##<a id="Overviewl"></a>DocumentDB 資料移轉工具概觀 ##

DocumentDB 資料移轉工具是一個開放原始碼解決方案，可將資料從各種來源匯入 DocumentDB，來源包括：

- JSON 檔案
- MongoDB
- SQL Server
- CSV 檔案
- Azure 資料表儲存體
- DocumentDB 集合

雖然匯入工具包括圖形化使用者介面 (dtui.exe)，您也可以從命令列 (dt.exe) 驅動此工具。事實上，在透過 UI 設定匯入之後，有一個選項可以輸出相關聯的命令。表格式來源資料 (例如 SQL Server 或 CSV 檔案) 可以進行轉換，以致可以在匯入期間建立階層式關聯性 (子文件)。繼續閱讀以深入了解來源選項、從每個來源匯入的範例命令列、目標選項，以及檢視匯入結果。


##<a id="Install"></a>安裝 DocumentDB 資料移轉工具 ##

您可以在 GitHub 上的[此儲存機制](https://github.com/azure/azure-documentdb-datamigrationtool)中找到移轉工具的原始程式碼，並可以從 [Microsoft 下載中心](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)取得編譯版本。您可以編譯解決方案，或直接下載編譯版本並將它解壓縮至選擇的目錄。然後執行：

- Dtui.exe：此工具的圖形化介面版本
- Dt.exe：此工具的命令列版本

##<a id="JSON"></a>匯入 JSON 檔案 ##

JSON 檔案來源匯入工具選項可讓您匯入一或多個單一文件 JSON 檔案或包含 JSON 文件陣列的每個 JSON 檔案。新增包含要匯入之 JSON 檔案的資料夾時，您可以選擇是否要以遞迴方式搜尋子資料夾中的檔案。

![JSON 檔案來源選項的螢幕擷取畫面](./media/documentdb-import-data/jsonsource.png)

以下是匯入 JSON 檔案的一些命令列範例：

	#Import a single JSON file	
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\CompanyData\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>從 MongoDB 匯入 ##

MongoDB 來源匯入工具選項可讓您從個別的 MongoDB 集合匯入，並使用查詢來選擇性地篩選文件，及/或使用投影來修改文件結構。

![MongoDB 來源選項的螢幕擷取畫面](./media/documentdb-import-data/mongodbsource.png)

連接字串會採用標準的 MongoDB 格式：

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 MongoDB 執行個體，請使用 Verify 命令。

輸入要匯出資料的集合名稱。您可以選擇性地對這兩個篩選器指定或提供檔案進行查詢 (例如 {pop: {$gt:5000}}) 及/或投射 (例如 {loc:0})，並使資料適合匯入作業。

以下是從 MongoDB 匯入的一些命令列範例：

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>匯入 MongoDB 匯出檔案 ##

MongoDB 匯出 JSON 檔案來源匯入工具選項可讓您匯入從 mongoexport 公用程式產生的一或多個 JSON 檔案。

![MongoDB 匯出來源選項的螢幕擷取畫面](./media/documentdb-import-data/mongodbexportsource.png)

新增包含要匯入之 MongoDB 匯出 JSON 檔案的資料夾，您可以選擇是否要以遞迴方式搜尋子資料夾中的檔案。

以下是從 MongoDB 匯出 JSON 檔案匯入的命令列範例：

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>從 SQL Server 匯入 ##

SQL 來源匯入工具選項可讓您從個別的 SQL Server 資料庫匯入，並使用查詢來選擇性地篩選要匯入的記錄。此外，您可以藉由指定巢狀分隔符號 (稍後將有更詳細的說明) 來修改文件結構。

![SQL 來源選項的螢幕擷取畫面](./media/documentdb-import-data/sqlexportsource.png)

連接字串的格式會採用標準的 SQL 連接字串格式。

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 SQL Server 執行個體，請使用 Verify 命令。

巢狀分隔符號屬性可用來在匯入期間建立階層式關聯性 (子文件)。請考慮下列 SQL 查詢：

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

它會傳回下列 (部分) 結果：

![SQL 查詢結果的螢幕擷取畫面](./media/documentdb-import-data/sqlqueryresults.png)

注意別名，例如 Address.AddressType 和 Address.Location.StateProvinceName。藉由指定巢狀分隔符號 ‘.’，匯入工具會在匯入期間建立 Address 和 Address.Location 子文件。在 DocumentDB 中產生的文件範例如下：

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "\#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*
 
以下是從 SQL Server 匯入的一些命令列範例：

	#Import records from SQL which match a query	
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>匯入 CSV 檔案 ##

CSV 檔案來源匯入工具選項可讓您匯入一或多個 CSV 檔案。新增包含要匯入之 CSV 檔案的資料夾時，您可以選擇是否要以遞迴方式搜尋子資料夾中的檔案。

![CSV 來源選項的螢幕擷取畫面](media/documentdb-import-data/csvsource.png)

與 SQL 來源類似，巢狀分隔符號屬性可用來在匯入期間建立階層式關聯性 (子文件)。請考慮下列 CSV 標頭資料列和資料資料列：

![CSV 範例記錄的螢幕擷取畫面](./media/documentdb-import-data/csvsample.png)

注意別名，例如 DomainInfo.Domain_Name 和 RedirectInfo.Redirecting。藉由指定巢狀分隔符號 ‘.’，匯入工具將會在匯入期間建立 DomainInfo 和 RedirectInfo 子文件。在 DocumentDB 中產生的文件範例如下：

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

匯入工具將嘗試推斷 CSV 檔案中不具引號之值的類型資訊 (加上引號的值永遠會被視為字串)。系統會依照下列順序識別類型：數字、日期時間、布林值。

下面提供兩個其他有關 CSV 匯入的注意事項：

1.	根據預設，不具引號的值一律會針對定位點和空格進行修剪，而具有引號的值則會以原樣方式加以保留。您可以使用 [修剪具有引號的值] 核取方塊或 /s.TrimQuoted 命令列選項，來覆寫這個行為。

2.	根據預設，不具引號的 Null 會被視為 Null 值。您可以使用 [將不具引號的 NULL 視為字串] 核取方塊或 /s.NoUnquotedNulls 命令列選項，來覆寫這個行為 (例如，將不具引號的 Null 視為 “null” 字串)。


以下是 CSV 匯入的命令列範例：

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>從 Azure 資料表儲存體匯入 ##

Azure 資料表儲存體來源匯入工具選項可讓您從個別的 Azure 資料表儲存體資料表匯入，並選擇性地篩選要匯入的資料表實體。

![Azure 資料表儲存體來源選項的螢幕擷取畫面](./media/documentdb-import-data/azuretablesource.png)

Azure 資料表儲存體連接字串的格式如下：

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 Azure 資料表儲存體執行個體，請使用 Verify 命令。

輸入要匯出資料的 Azure 資料表名稱。您可以選擇性地指定[篩選器](https://msdn.microsoft.com/library/azure/ff683669.aspx)。

Azure 資料表儲存體來源匯入工具選項具有下列其他選項：

1. 包含內部欄位 
	2. 全部 - 包括所有內部欄位 (PartitionKey、RowKey 和 Timestamp)
	3. 無 - 排除所有內部欄位
	4. RowKey - 僅包含 RowKey 欄位
3. 選取資料行
	1. Azure 資料表儲存體篩選器不支援投影。如果您只想匯入特定的 Azure 資料表實體屬性，請將它們加入 [選取資料行] 清單。其他所有實體屬性都會被忽略。

以下是從 Azure 資料表儲存體匯入的命令列範例：

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DocumentDBSource"></a>從 DocumentDB 匯入 ##

DocumentDB 來源匯入工具選項可讓您從一或多個 DocumentDB 集合匯入資料，並選擇性地使用查詢來篩選文件。

![DocumentDB 來源選項的螢幕擷取畫面](./media/documentdb-import-data/documentdbsource.png)

DocumentDB 連接字串的格式如下：

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 DocumentDB 執行個體，請使用 Verify 命令。

若要從單一 DocumentDB 集合匯入，請輸入要從中匯入資料的來源集合名稱。若要從多個 DocumentDB 集合匯入，可提供規則運算式來比對一或多個集合名稱 (例如 collection01 | collection02 | collection03)。您可以選擇性地對這兩個篩選器指定或提供檔案進行查詢，並使資料適合匯入作業。

> [AZURE.NOTE]因為集合欄位接受規則運算式，所以，若您要從其名稱包含規則運算式字元的單一集合進行匯入，則必須對應地將那些字元逸出。

DocumentDB 來源匯入工具選項具有下列進階選項：

1. 包括內部欄位：指定匯出中是否包含 DocumentDB 文件系統屬性 (例如 _rid、_ts)。2. 失敗時的重試次數：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時的重試次數。
3. 重試間隔：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時兩次重試之間要等候的時間。
4. 連線模式：指定要與 DocumentDB 搭配使用的連線模式。可用的選項包括：DirectTcp、DirectHttps 和閘道器。直接連線模式會比較快，但閘道器模式比較支援防火牆，因為它只會使用連接埠 443。

![DocumentDB 來源進階選項的螢幕擷取畫面](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP]匯入工具會預設 DirectTcp 連線模式。如果您遇到防火牆問題，請切換到閘道器連線模式，因為它只需要連接埠 443。


以下是從 DocumentDB 匯入的一些命令列範例：

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="DocumentDBBulkTarget"></a>匯入到 DocumentDB (大量匯入) ##

為了方便，DocumentDB 大量匯入工具可讓您使用 DocumentDB 預存程序，從任何可用的來源選項匯入。此工具支援匯入到單一 DocumentDB 集合，以及跨多個 DocumentDB 集合分割資料的分區化匯入。您可以在[這裡](documentdb-partition-data.md)深入了解如何在 DocumentDB 中分割資料。此工具將建立並執行預存程序，然後從目標集合中將它刪除。

![DocumentDB 大量選項的螢幕擷取畫面](./media/documentdb-import-data/documentdbbulk.png)

DocumentDB 連接字串的格式如下：

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 DocumentDB 執行個體，請使用 Verify 命令。

若要匯入到單一集合，請輸入要匯入資料的目標集合名稱，然後按一下 [新增] 按鈕。若要匯入到多個集合，請分別輸入每個集合的名稱，或使用下列語法來指定多個集合： *collection_prefix*[開始索引 - 結束索引]。透過上述語法指定多個集合時，請記住下列事項：

1. 僅支援整數範圍的名稱模式。例如，指定 collection[0-3] 將產生下列集合：collection0、collection1、collection2、collection3。
2. 您可以使用縮寫的語法：collection[3] 將發出一組與步驟 1 中所述相同的集合。
3. 您可以提供一個以上的替代項目。例如，collection[0-1] [0-9] 將產生 20 個開頭為零的集合名稱 (collection01、..02、..03)。

指定集合名稱之後，請選擇所需的集合定價層 (S1、S2 或 S3)。為了達到最佳的匯入效能，請選擇 S3。您可以在[這裡](documentdb-performance-levels.md)深入了解 DocumentDB 效能等級。

> [AZURE.NOTE]效能層設定僅適用於建立集合。如果指定的集合已經存在，將不會修改其定價層。

匯入到多個集合時，匯入工具支援以雜湊為基礎的分區化。在此案例中，指定您想要用來做為資料分割索引鍵的文件屬性 (如果資料分割索引鍵是空白的，文件就會跨目標集合隨機進行分區化)。

您可以選擇性地指定在匯入期間，匯入來源中的哪些欄位應該做為 DocumentDB 文件識別碼屬性使用 (請注意，如果文件不包含這個屬性，則匯入工具將會產生 GUID 做為識別碼屬性值)。

匯入期間有數個可用的進階選項。首先，雖然此工具包含預設的大量匯入預存程序 (BulkInsert.js)，但您可以選擇指定自己的匯入預存程序：

 ![DocumentDB 大量插入 sproc 選項的螢幕擷取畫面](./media/documentdb-import-data/bulkinsertsp.png)

此外，匯入日期類型時 (例如從 SQL Server 或 MongoDB)，有三種匯入選項可供選擇：

 ![DocumentDB 日期時間匯入選項的螢幕擷取畫面](./media/documentdb-import-data/datetimeoptions.png)

-	字串：保存為字串值
-	Epoch：保存為 Epoch 數值
-	兩者：保存字串和 Epoch 數值。這個選項會建立子文件，例如："date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 } 


DocumentDB 大量匯入工具具有下列其他進階選項：

1. 批次大小：此工具會將批次大小預設為 50。如果要匯入很大的文件，請考慮降低批次大小。相反地，如果要匯入很小的文件，請考慮提高批次大小。
2. 指令碼大小上限 (位元組)：此工具會將指令碼大小上限預設為 960 KB
3. 停用自動化識別碼產生作業：如果要匯入的每個文件都包含識別碼欄位，則選取此選項可以提升效能。遺漏唯一識別碼欄位的文件將不會被匯入。
4. 失敗時的重試次數：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時的重試次數。
5. 重試間隔：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時兩次重試之間要等候的時間。
6. 連線模式：指定要與 DocumentDB 搭配使用的連線模式。可用的選項包括：DirectTcp、DirectHttps 和閘道器。直接連線模式會比較快，但閘道器模式比較支援防火牆，因為它只會使用連接埠 443。

![DocumentDB 大量匯入進階選項的螢幕擷取畫面](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP]匯入工具會預設 DirectTcp 連線模式。如果您遇到防火牆問題，請切換到閘道器連線模式，因為它只需要連接埠 443。

##<a id="DocumentDBSeqTarget"></a>匯入到 DocumentDB (循序記錄匯入) ##

為了方便，DocumentDB 循序記錄匯入工具可讓您從任何可用的來源選項逐筆匯入記錄。如果您打算匯入至已達到預存程序配額的現有集合，您可以選擇此選項。此工具支援匯入到單一 DocumentDB 集合，以及跨多個 DocumentDB 集合分割資料的分區化匯入。您可以在[這裡](documentdb-partition-data.md)深入了解如何在 DocumentDB 中分割資料。

![DocumentDB 循序記錄匯入選項的螢幕擷取畫面](./media/documentdb-import-data/documentdbsequential.png)

DocumentDB 連接字串的格式如下：

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]若要確定可以存取連接字串欄位中指定的 DocumentDB 執行個體，請使用 Verify 命令。

若要匯入到單一集合，請輸入要匯入資料的目標集合名稱，然後按一下 [新增] 按鈕。若要匯入到多個集合，請分別輸入每個集合的名稱，或使用下列語法來指定多個集合： *collection_prefix*[開始索引 - 結束索引]。透過上述語法指定多個集合時，請記住下列事項：

1. 僅支援整數範圍的名稱模式。例如，指定 collection[0-3] 將產生下列集合：collection0、collection1、collection2、collection3。
2. 您可以使用縮寫的語法：collection[3] 將發出一組與步驟 1 中所述相同的集合。
3. 您可以提供一個以上的替代項目。例如，collection[0-1] [0-9] 將產生 20 個開頭為零的集合名稱 (collection01、..02、..03)。

指定集合名稱之後，請選擇所需的集合定價層 (S1、S2 或 S3)。為了達到最佳的匯入效能，請選擇 S3。您可以在[這裡](documentdb-performance-levels.md)深入了解 DocumentDB 效能等級。

> [AZURE.NOTE]效能層設定僅適用於建立集合。如果指定的集合已經存在，將不會修改其定價層。

匯入到多個集合時，匯入工具支援以雜湊為基礎的分區化。在此案例中，指定您想要用來做為資料分割索引鍵的文件屬性 (如果資料分割索引鍵是空白的，文件就會跨目標集合隨機進行分區化)。

您可以選擇性地指定在匯入期間，匯入來源中的哪些欄位應該做為 DocumentDB 文件識別碼屬性使用 (請注意，如果文件不包含這個屬性，則匯入工具將會產生 GUID 做為識別碼屬性值)。

匯入期間有數個可用的進階選項。首先，匯入日期類型時 (例如從 SQL Server 或 MongoDB)，有三種匯入選項可供選擇：

 ![DocumentDB 日期時間匯入選項的螢幕擷取畫面](./media/documentdb-import-data/datetimeoptions.png)

-	字串：保存為字串值
-	Epoch：保存為 Epoch 數值
-	兩者：保存字串和 Epoch 數值。這個選項會建立子文件，例如："date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 } 

DocumentDB 循序記錄匯入工具具有下列其他進階選項：

1. 平行要求數目：此工具會預設為 2 個平行要求。如果要匯入很小的文件，請考慮提高平行要求數目。請注意，如果這個數字提高太多，則匯入可能會發生節流。
2. 停用自動化識別碼產生作業：如果要匯入的每個文件都包含識別碼欄位，則選取此選項可以提升效能。遺漏唯一識別碼欄位的文件將不會被匯入。
3. 失敗時的重試次數：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時的重試次數。
4. 重試間隔：指定與 DocumentDB 的連線發生暫時性失敗 (例如網路連線中斷) 時兩次重試之間要等候的時間。
5. 連線模式：指定要與 DocumentDB 搭配使用的連線模式。可用的選項包括：DirectTcp、DirectHttps 和閘道器。直接連線模式會比較快，但閘道器模式比較支援防火牆，因為它只會使用連接埠 443。

![DocumentDB 循序記錄匯入進階選項的螢幕擷取畫面](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP]匯入工具會預設 DirectTcp 連線模式。如果您遇到防火牆問題，請切換到閘道器連線模式，因為它只需要連接埠 443。

## 匯出至 JSON 檔案

DocumentDB JSON 匯出工具可讓您將任何可用的來源選項匯出至包含 JSON 文件陣列的 JSON 檔案。此工具將會為您處理匯出作業，或者您可以選擇檢視產生的移轉命令，然後自己執行命令。

![DocumentDB JSON 匯出選項的螢幕擷取畫面](./media/documentdb-import-data/jsontarget.png)

您可以選擇性地選擇美化產生的 JSON，這會增加產生文件的大小，但會使內容更容易閱讀。

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
	
	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]
	
## 進階組態

在 [進階組態] 畫面中，指定您想要寫入所有錯誤的記錄檔位置。下列規則會套用到此頁面：

1.	如果未提供檔案名稱，則會在 [結果] 頁面上傳回所有錯誤。
2.	如果提供不含目錄的檔案名稱，則會在目前的環境目錄中建立 (或覆寫) 該檔案。
3.	如果選取了現有的檔案，則將會覆寫該檔案，沒有任何附加選項。

	![進階組態畫面的螢幕擷取畫面](./media/documentdb-import-data/AdvancedConfiguration.png)

## 確認匯入設定和檢視命令列

1. 指定來源資訊、目標資訊與進階組態之後，請檢閱移轉摘要，並選擇性地檢視或複製產生的移轉命令 (複製命令對於自動匯入作業非常有用)：

	![摘要畫面的螢幕擷取畫面](./media/documentdb-import-data/summary.png)

	![摘要畫面的螢幕擷取畫面](./media/documentdb-import-data/summarycommand.png)

2. 在您滿意來源和目標選項之後，請按一下 [**匯入**]。隨著匯入的處理，已耗用時間、傳送的計數，以及失敗的資訊 (如果您未在 [進階組態] 中提供檔案名稱) 都將隨之更新。完成後，您可以匯出結果 (例如處理任何匯入失敗)。

	![DocumentDB JSON 匯出選項的螢幕擷取畫面](./media/documentdb-import-data/viewresults.png)

3. 您也可以啟動新的匯入，您可以保留現有設定 (例如連接字串資訊、來源和目標選擇等)，或重設所有值。

	![DocumentDB JSON 匯出選項的螢幕擷取畫面](./media/documentdb-import-data/newimport.png)

## 後續步驟

- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。


 

<!---HONumber=58_postMigration-->