<properties 
	pageTitle="使用 MongoLab 附加元件在有 MongoDB 的 Azure 上建立 .NET Web 應用程式" 
	description="了解如何建立在 Azure App Service 建立 ASP.NET Web 應用程式，將資料儲存在 MongoLab 主控的 MongoDB 中。" 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# 使用 MongoLab 附加元件在有 MongoDB 的 Azure 上建立 .NET Web 應用程式

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>作者：MongoLab 的 Eric Sedor</em></p>

探險家們大家好！ 歡迎使用 MongoDB-as-a-Service。在本教學課程中，您將：

1. [佈建資料庫][provision] - Azure Marketplace 中的 [MongoLab](http://mongolab.com) (英文) 附加元件將提供您在 Azure 雲端主控並由 MongoLab 的雲端資料庫平台管理的 MangoDB 資料庫。
1. [建立應用程式][create] - 它將是用於記事的簡單 C# ASP.NET MVC 應用程式。
1. [部署應用程式][deploy] - 只要將幾個組態勾點連結在一起，將程式碼推播到 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 就可以變得輕鬆無比。
1. [管理資料庫][manage] - 最後，我們將展示 MongoLab 的網頁式資料庫管理入口網站，您可在此輕鬆執行資料的搜尋、虛擬化、修改。

在本教學課程的任何時間有任何問題，歡迎寄送電子郵件至 [support@mongolab.com](mailto:support@mongolab.com)。

## 快速入門
如果您已經有要使用的 Azure App Service，或者您比較熟悉 Azure Marketplace，請從本節著手快速開始。否則，請繼續前往到底下的[佈建資料庫][provision]。
 
1. 按一下 [新增] > [Markeplace] 來開啟 Azure Marketplace。  
	<!-- ![Store][button-store] -->

1. 購買 MongoLab 附加元件。  
	![MongoLab][entry-mongolab]

1. 在 [附加元件] 清單中按一下您的 MongoLab 附加元件，然後按一下 [連線資訊]。  
	![ConnectionInfoButton][button-connectioninfo]

1. 將 MONGOLAB_URI 複製到剪貼簿。  
	![ConnectionInfoScreen][screen-connectioninfo]
	**此 URI 包含您的資料庫使用者名稱和密碼。這是機密資訊，請勿洩露。**

1. 將這個值新增到 Azure Web 應用程式的 [組態] 功能表中的 [連接字串] 清單：
	![WebSiteConnectionStrings][focus-website-connectinfo]

1. 在 [名稱] 中，輸入 MONGOLAB_URI。

1. 在 [值] 中，貼上我們在上一節取得的連接字串。

1. 在 [類型] 下拉式清單中選取 [自訂] (而非預設的 **SQLAzure**)。

1. 在 Visual Studio 中，選取 [工具] > [Library Package Manager] > [Package Manager Console] 安裝 Mongo C# 驅動程式。在 PM 主控台上，輸入 **Install-Package mongocsharpdriver**，並按下 **Enter**。

1. 在程式碼中設置勾點，以便從環境變數取得 MongoLab 連線 URI：

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **注意：**Azure 會將前置詞 **CUSTOMCONNSTR_** 新增到原始宣告的連接字串前面，所以程式碼會參考 **CUSTOMCONNSTR_MONGOLAB_URI.** 而非 **MONGOLAB_URI**。

現在，回到教學課程...

<a name="provision"></a>
## 佈建資料庫

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## 建立應用程式

在本節中，您將了解如何建立 C# ASP.NET Visual Studio 專案，並逐步使用 C# MongoDB 驅動程式建立簡單 Note 應用程式。您會想要造訪您的Web 應用程式並寫下記事，然後檢視所有留下的記事。

您將使用 Visual Studio Express 2013 for Web 進行這項開發。

### 建立專案
範例應用程式將先使用 Visual Studio 範本。務必使用 .NET Framework 4.5。

1. 選取 [檔案] > [新增專案]。[新增專案] 對話方塊隨即顯示：  
	![NewProject][dialog-mongolab-csharp-newproject]

1. 選取 [已安裝] > [範本] > [Visual C#] > [Web]。

1. 從 .NET 版本下拉式功能表中，選取 [.NET Framework 4.5]。

1. 選擇 [MVC 應用程式]。

1. 輸入 _mongoNotes_ 做為**專案名稱**。如果您選擇不同的名稱，您需要修改整個教學課程中提供的程式碼。

1. 選取 [工具] > [Library Package Manager] > [Package Manager Console]。在 PM 主控台上，輸入 **Install-Package mongocsharpdriver**，並按下 **Enter**。  
	![PMConsole][focus-mongolab-csharp-pmconsole]
	MongoDB C# 驅動程式隨即整合到專案中，而且下列一行將自動新增至 _packages.config_ 檔案：

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### 新增記事模型
首先建立 Notes 的模型，其中只有簡單的日期和文字內容。

1. 在 [方案總管] 中以滑鼠右鍵按一下 [模型]，並選取 [新增] > [類別]。將這個新類別命名為 *Note.cs*。

1. 將對於此類別自動產生的程式碼改為以下內容：

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### 新增資料存取層
務必建立存取 MongoDB 的方法以擷取和儲存記事。您的資料存取層將使用 Note 模型，此後將繫結到 HomeController。

1. 在 [方案總管] 中以滑鼠右鍵按一下 [mongoNotes]，並選取 [新增] > [新增資料夾]。將資料夾命名為 **DAL**。

1. 在 [方案總管] 中以滑鼠右鍵按一下 [DAL]，並選取 [新增] > [類別]。將這個新類別命名為 *Dal.cs*。

1. 將對於此類別自動產生的程式碼改為以下內容：

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
                }
        
                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            if (mongoServer != null)
                            {
                                this.mongoServer.Disconnect();
                            }
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
            }
        }

1. 請注意下列程式碼：
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
您將在此存取稍後將佈建的環境變數。如果您基於開發目的而執行本機 mongo 實例，則您可能會想要暫時將此值設定為「localhost」。
  
  另外，也設定資料庫名稱。具體而言，將 **dbName** 值設定為在佈建 MongoLab 附加元件時輸入的名稱。

1. 最後，檢查 **GetNotesCollection()** 中的下列程式碼：  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  這不需要任何變更；只要了解這只是呈現如何以 MongoCollection 物件執行插入、更新和查詢，如 **GetAllNotes()** 中的以下程式碼所示：

        collection.FindAll().ToList<Note>();

如需利用 MongoDB C# 驅動程式的詳細資訊，請參閱 mongodb.org 的 [CSharp 驅動程式快速入門](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp 驅動程式快速入門")。

### 新增建立檢視
您現在將新增建立新記事的檢視。

1. 在 [方案總管] 中以滑鼠右鍵按一下 [檢視] > [首頁] 項目，並選取 [新增] > [檢視]。將這個新檢視命名為 **Create**，並且按一下 [新增]。

1. 將針對此檢視 (**Create.cshtml**) 自動產生的程式碼改為以下內容：

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### 修改 index.cshtml
接著，在您的 Web 應用程式上放置一個用於檢視和建立記事的簡單配置。

1. 在 [檢視] > [首頁] 下開啟 **Index.cshtml**，然後將其中的內容改為以下內容：  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### 修改 HomeController.cs
最後，HomeController 需要將您的資料存取層具現化，然後將它應用於您的檢視。

1. 在 [方案總管] 的 [控制器] 下開啟 **HomeController.cs**，然後將其中的內容改為以下內容：  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
                        return RedirectToAction("Index");
                    }
                    catch
                    {
                        return View();
                    }
                }
        
                public ActionResult About()
                {
                    return View();
                }
        
                # region IDisposable
        
                new protected void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                new protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            this.dal.Dispose();
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
        
            }
        }
    
<a name="deploy"></a>
## 部署應用程式。

現在應用程式已經開發好，該開始以 Azure App Service 建立用以主控它的 Web 應用程式、設定該 Web 應用程式及部署程式碼。本節的重點是 MongoDB 連接字串 (URI) 的使用。您即將在 We 應用程式中以此 URI 設定環境變數，使 URI 和您的程式碼分開。您應該將 URI 當做敏感資訊，因為它包含連線至您的資料庫的認證。

### 建立新 Web 應用程式並取得發行設定檔案
在 Azure 中建立 Web 應用程式非常簡便，尤其是 Azure 會為 Visual Studio 自動產生發行設定檔。

1. 在 Azure 入口網站，按一下 [新增]。  
	![新增][button-new]

1. 選取 [運算] > [Web 應用程式] > [快速建立]。  
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. 輸入 URL 前置詞。選取所需的名稱，但是請注意，這必須是唯一的名稱 (可能無法使用「mongoNotes」)。

1. 按一下 [建立 Web 應用程式]。

1. 當 Web 應用程式建立完成，按一下 [Web 應用程式] 清單中的 Web 應用程式名稱。Web 應用程式儀表板隨即顯示。  
	![WebAppDashboard][screen-mongolab-websitedashboard]

1. 按一下 [快速概覽] 下的 [下載發行設定檔]，然後將 .PublishSettings 檔案儲存到所選的目錄。  
	![DownloadPublishProfile][button-website-downloadpublishprofile]

或者，您也可以直接從 Visual Studio 設定 Web 應用程式。當您將 Azure 帳戶連結至 Visual Studio 時，請依照提示從該處設定 Web 應用程式。完成此作業後，您即可在方案總管中以滑鼠右鍵按一下專案名稱，將其部署至 Azure。您仍需要設定 MongoLab 連接字串，如下方的步驟中所詳述。

### 取得 MongoLab 連接字串

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### 將連接字串加入 Web 應用程式的環境變數

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### 發行 Web 應用程式
1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下 **mongoNotes** 專案，並選取 [發行]。[發行] 對話方塊隨即顯示：  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. 按一下 [匯入]，並且從所選的下載目錄中選取 .PublishSettings 檔案。此檔案將自動填入 [發行] 對話方塊中的值。

1. 按一下 [驗證連線] 測試檔案。

1. 驗證成功後，按一下[發行]。發行完成後，將開啟新的瀏覽器索引標籤，其中顯示該 Web 應用程式。

1. 輸入一些記事文字，並按一下 [建立]，然後查看結果！  
	![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## 管理資料庫

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

恭喜！ 您已經啟動由 MongoLab 代管的 MongoDB 資料庫支援的 C# ASP.NET 應用程式！ 現在，您擁有 MongoLab 資料庫，如果對於資料庫有任何問題或疑慮，或者要取得 MongoDB 或 C# 驅動程式的協助，可連絡 [support@mongolab.com](mailto:support@mongolab.com)。祝您好運！

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

 

<!----HONumber=62-->