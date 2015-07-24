<properties 
	pageTitle="在連線至在虛擬機器上執行之 MongoDB 的 Azure 中建立 Web 應用程式" 
	description="指導您如何使用 Git 將 ASP.NET 應用程式部署至與 Azure Virtual Machine 上的 MongoDB 連線的 Azure App Service 的教學課程。"
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="cephalin"/>


# 在連線至在虛擬機器上執行之 MongoDB 的 Azure 中建立 Web 應用程式

使用 Git，即可將 ASP.NET 應用程式部署至 Azure App Service Web Apps。在本教學課程中，您將建置簡易的前端 ASP.NET MVC 工作清單應用程式，以連接至在 Azure 的虛擬機器上執行的 MongoDB 資料庫。[MongoDB][MongoDB] (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。在部署電腦上執行和測試 ASP.NET 應用程式之後，您將使用 Git 將此應用程式上傳至 App Service Web Apps。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。


## 背景知識 ##

下列知識雖然並非必要，但是對本教學課程很實用：

* MongoDB 的 C# 驅動程式。如需針對 MongoDB 開發 C# 應用程式的詳細資訊，請參閱 MongoDB＜[CSharp Language Center][MongoC#LangCenter]＞(英文)。 
* ASP .NET Web 應用程式架構。您可以在 [ASP.net 網站][ASP.NET]了解此架構。
* ASP .NET MVC Web 應用程式架構。您可以在 [ASP.NET MVC 網站][MVCWebSite]了解此架構。
* Azure。您可以在 [Azure][WindowsAzure] 開始進行讀取。

## 必要條件 ##

- [Visual Studio Express 2013 for Web][VSEWeb] 或 [Visual Studio 2013][VSUlt]
- [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- 使用中的 Microsoft Azure 訂用帳戶

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## 建立虛擬機器和安裝 MongoDB ##

本教學課程假設您已在 Azure 中建立虛擬機器。建立虛擬機器之後，您必須在這部虛擬機器上安裝 MongoDB：

* 若要建立 Windows 虛擬機器和安裝 MongoDB，請參閱＜[在 Azure 中執行 Windows Server 的虛擬機器上安裝 MongoDB][InstallMongoOnWindowsVM]＞。
* 此外，若要建立 Linux 虛擬機器和安裝 MongoDB，請參閱＜[在 Azure 中執行 CentOS Linux 的虛擬機器上安裝 MongoDB][InstallMongoOnCentOSLinuxVM]＞。

在 Azure 中建立虛擬機器並安裝 MongoDB 之後，請務必記住虛擬機器的 DNS 名稱 (例如，"testlinuxvm.cloudapp.net") 以及您在端點中指定之 MongoDB 的外部連接埠。稍後在教學課程中需要此資訊。

<a id="createapp"></a>
## 建立應用程式 ##

在這一節中，您將使用 Visual Studio 建立名為 "My Task List" 的 ASP.NET 應用程式，並執行初始部署至 Azure App Service Web Apps。您會在本機執行此應用程式，但該應用程式將會連接至 Azure 上的虛擬機器並使用您在那裡建立的 MongoDB 執行個體。

1. 在 Visual Studio 中按一下 [新增專案]。

	![Start Page New Project][StartPageNewProject]

1. 在 [新增專案] 視窗的左窗格中，選取 [Visual C#]，然後選取 [Web]。在中間窗格中，選取 [ASP.NET Web 應用程式]。在底部將您的專案命名為 "MyTaskListApp"，然後按一下 [確定]。

	![New Project Dialog][NewProjectMyTaskListApp]

1. 在 [新增 ASP.NET 專案] 對話方塊中，選取 [MVC]，然後按一下 [確定]。

	![Select MVC Template][VS2013SelectMVCTemplate]

1. 如果您尚未登入 Microsoft Azure，系統將提示您登入。請依照提示登入 Azure。
2. 當您登入之後，即可開始設定您的 App Service Web 應用程式。指定 **Web 應用程式名稱**、**App Service 計劃**、**資源群組**，以及**區域**，然後按一下 [確定]。

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. 當專案建立完成之後，如 [Azure App Service 活動] 視窗所示，等待 Web 應用程式在 Azure App Service 中建立。然後，按一下 [立即將 MyTaskListApp 發佈至此 Web 應用程式]。

1. 按一下 [發行]。

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	當您預設的 ASP.NET 應用程式發佈至 Azure App Service Web Apps 之後，就會在瀏覽器中啟動。

## 安裝 MongoDB C# 驅動程式

MongoDB 透過驅動程式提供 C# 應用程式的用戶端支援，您必須將該驅動程式安裝在本機開發電腦上。C# 驅動程式可透過 NuGet 取得。

若要安裝 MongoDB C# 驅動程式：

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **MyTaskListApp** 專案並選取 [管理 NuGet 封裝]。

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. 在 [Manage NuGet Packages] 視窗的左窗格中，按一下 [線上]。在右邊的 [線上搜尋] 方塊中，輸入 "mongocsharpdriver"。按一下 [安裝] 以安裝驅動程式。

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. 按一下 [我接受] 以接受 10gen, Inc. 授權條款。

4. 在安裝驅動程式後，按一下 [關閉]。![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


MongoDB C# 驅動程式現已安裝。**MongoDB.Driver.dll** 和 **MongoDB.Bson.dll** 程式庫的參考便已新增至專案。

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

## 新增模型 ##
在 [方案總管] 中，於 *Models* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個新 [類別]，將其命名為 *TaskModel.cs*。在 *TaskModel.cs*，以下列程式碼取代現有程式碼：

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

## 新增資料存取層 ##
在 [方案總管] 中，於 *MyTaskListApp* 專案上按一下滑鼠右鍵，然後 [新增] 一個名為 **DAL** 的 [新資料夾]。於 *DAL* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個新 [類別]。將類名命名為 *Dal.cs*。在 *Dal.cs*，以下列程式碼取代現有程式碼：

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
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

## 新增控制器 ##
在 [方案總管] 中開啟 **Controllers\HomeController.cs** 檔案，然後以下列程式碼取代現有程式碼：

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
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

## 設定樣式 ##
若要變更頁面最上方的標題，請在 [方案總管] 中開啟 **Views\Shared_Layout.cshtml** 檔案，並以 "My Task List Application" 取代導覽列中的 "Application name"，使其看起來如下：

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

若要設定 Task List 功能表，請開啟 *\Views\Home\Index.cshtml* 檔案並以下列程式碼取代現有程式碼：
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


若要新增建立新工作的功能，請在 *Views\Home* 資料夾上按一下滑鼠右鍵，然後 [新增] 一個 [檢視]。將檢視命名為 *Create*。使用下列程式碼來取代此程式碼：

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

[方案總管] 看起來如下所示：

![Solution Explorer][SolutionExplorerMyTaskListApp]

## 設定 MongoDB 連接字串 ##
在 [方案總管] 中開啟 *DAL/Dal.cs* 檔案。尋找下列程式碼行：

	private string connectionString = "mongodb://<vm-dns-name>";

以執行 MongoDB 之虛擬機器的 DNS 名稱來取代 `<vm-dns-name>`，該 MongoDB 是您在本教學課程的[建立虛擬機器和安裝 MongoDB][] 步驟中所建立的資料庫。若要尋找虛擬機器的 DNS 名稱，請移至 Azure 入口網站，選取 [虛擬機器]，然後尋找 [DNS 名稱]。

如果虛擬機器的 DNS 名稱為 "testlinuxvm.cloudapp.net"，且 MongoDB 是在預設連接埠 27017 上接聽，則連接字串程式碼行如下所示：

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

如果虛擬機器端點為 MongoDB 指定不同的外部連接埠，您可以在連接字串中指定連接埠：

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

如需 MongoDB 連接字串的詳細資訊，請參閱＜[連接][MongoConnectionStrings]＞(英文)。

## 測試本機部署 ##

若要在部署電腦上執行您的應用程式，請選取 [偵錯] 功能表中的 [開始偵錯] 或按 **F5**。IIS Express 會啟動，瀏覽器會開啟並啟動應用程式的首頁。您可以新增一項工作，該工作將會新增至在 Azure 中您的虛擬機器上執行的 MongoDB 資料庫。

![My Task List Application][TaskListAppBlank]

## 發佈至 Azure App Service Web Apps

在本節中，您會將您的變更發佈至 Azure App Service Web Apps。

1. 在 [方案總管] 中，再次以滑鼠右鍵按一下 **MyTaskListApp**，然後按一下 [發佈] 。
2. 按一下 [發行]。

	現在，您應該會看到您的 Web 應用程式在 Azure App Service 中執行，並且存取 Azure Virtual Machines 中的 MongoDB 資料庫。

## 摘要 ##

您現在已成功將 ASP.NET 應用程式部署至 Azure App Service Web Apps。若要檢視 Web 應用程式：

1. 登入 Azure 入口網站。
2. 按一下 [Web 應用程式]。 
3. 在 [Web Apps] 清單中選取您的 Web 應用程式。

如需針對 MongoDB 開發 C# 應用程式的詳細資訊，請參閱＜[CSharp Language Center][MongoC#LangCenter]＞(英文)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[建立虛擬機器和安裝 MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=62-->