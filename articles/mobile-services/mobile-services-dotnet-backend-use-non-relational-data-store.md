<properties 
	pageTitle="使用非關聯式資料存放區來建置服務 | Azure 行動服務" 
	description="了解如何對於 .NET 型行動服務使用非關聯式資料存放區，例如 MongoDB 或 Azure Table Storage。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# 建置使用 MongoDB 而非 SQL Database 進行儲存的 .NET 後端行動服務

本主題說明如何在 .NET 後端行動服務中使用非關聯式資料存放區。在本教學課程中，您將修改行動服務快速入門專案，以使用 MongoDB 而非預設 Azure SQL Database 資料存放區。

使用本教學課程之前，必須先完成[開始使用行動服務]或[將行動服務新增至現有應用程式]教學課程。您也必須將 MongoLab 服務加入至您的訂用帳戶。

## <a name="create-store"></a>建立 MongoLab 非關聯式存放區

1. 在 [Azure 管理入口網站]中，按一下 [**新增**]，然後按一下 [**Marketplace**]。

2. 按一下 **MongoLab** 附加元件，並完成精靈以註冊 MongoLab 帳戶。

	如需 MongoLab 的詳細資訊，請參閱 [MongoLab 附加元件頁面]。

2. 帳戶設定完成後，請按一下 [**連接資訊**]，然後複製連接字串。

3. 在您的行動服務中，按一下 [**設定**] 索引標籤，向下捲動至 [**連接字串**] 並輸入新連接字串 (其 [**名稱**] 為 `MongoConnectionString`，其 [**值**] 為您的 MongoDB 連接)，然後按一下 [**儲存**]。

	![新增 MongoDB 連接字串](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	儲存體帳戶連接字串會以加密方式儲存在應用程式設定中。您可以在執行階段在任何資料表控制器中存取此字串。

8. 在 Visual Studio 的 [方案總管] 中，為行動服務專案開啟 Web.config 檔案，並新增下列新的連接字串：

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. 以 MongoDB 連接字串取代 `<MONGODB_CONNECTION_STRING>` 預留位置。

	在本機電腦上執行行動服務時，該服務會使用此連接字串，讓您先測試程式碼再發佈。在 Azure 中執行時，行動服務會改用入口網站中設定的連接字串值，並忽略專案中的連接字串。

## <a name="modify-service"></a>修改資料類型和資料表控制器

1. 安裝 **WindowsAzure.MobileServices.Backend.Mongo** NuGet 封裝。

2. 將 **TodoItem** 修改為衍生自 **DocumentData**，而不是 **EntityData**。

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. 在 **TodoItemController** 中，使用下列程式碼取代 **Initialize** 方法：

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. 在上述 **Initialize** 方法的程式碼中，使用您在佈建 MongoLab 附加元件時選擇的名稱來取代 `<YOUR-DATABASE-NAME>`。

您現在已可測試應用程式。

## <a name="test-application"></a>測試應用程式

1. (選用) 重新發佈您的行動服務 .NET 後端專案。

	您也可先在本機測試您的行動服務，再將 .NET 後端專案發佈至 Azure。不論在本機或在 Azure 中測試，行動服務都會使用 MongoDB 進行儲存。

4. 和之前一樣使用啟動頁面上的 [**立即試用**] 按鈕，或使用連線到行動應用程式的用戶端應用程式，查詢資料庫中的項目。
 
	請注意，您在快速入門教學課程中不會看見任何先前儲存在 SQL Database 中的項目。

	>[AZURE.NOTE]當您使用 [**立即試用**] 按鈕來啟動說明 API 頁面時，請記得提供您的應用程式金鑰來做為密碼 (使用者名稱則為空白)。

3. 建立新項目。

	除了您的資料現在儲存在非關聯式存放區而不是 SQL Database 中，應用程式和行動服務的行為應如同以往。

##後續步驟

您現在已看到搭配使用資料表儲存體與 .NET 後端是多麼簡單，請考慮瀏覽某些其他後端儲存體選項：

+ [建置使用資料表儲存體而非 SQL Database 的 .NET 後端行動服務](mobile-services-dotnet-backend-store-data-table-storage.md)</br>如同您剛完成的教學課程，本主題說明如何將非關聯式資料存放區使用於您的行動服務。在本教學課程中，您將修改行動服務快速入門專案，以使用 Azure 儲存體而非 SQL Database 作為資料存放區。
 
+ [使用混合式連接連接到內部部署 SQL Server](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>混合式連接可讓您的行動服務安全地連接到內部部署資產。因此，您可以藉由混合式連線讓行動用戶端使用 Azure 存取內部部署資料。支援的資產包括任何可在靜態 TCP 連接埠上執行的資源，例如 Microsoft SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

+ [使用行動服務將影像上傳至 Azure 儲存體](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>示範如何擴充 TodoList 範例專案，讓您將影像從您的應用程式上傳至 Azure Blob 儲存體。


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[開始使用行動服務]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[將行動服務新增至現有應用程式]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab 附加元件頁面]: /gallery/store/mongolab/mongolab
 

<!---HONumber=July15_HO2-->