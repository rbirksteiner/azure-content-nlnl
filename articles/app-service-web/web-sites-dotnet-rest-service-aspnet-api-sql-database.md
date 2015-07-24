<properties 
	pageTitle="在 Azure App Service 中使用 ASP.NET Web API 和 SQL Database 建立 REST 服務" 
	description="指導如何使用 Visual Studio，將使用 ASP.NET Web API 的應用程式部署至 Azure Web 應用程式的教學課程。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>

# 在 Azure App Service 中使用 ASP.NET Web API 和 SQL Database 建立 REST 服務

本教學課程示範如何使用 Visual Studio 2013 或 Visual Studio 2013 for Web Express 中的 [發行 Web] 精靈，將 ASP.NET Web 應用程式部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。如此您就能開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成本教學課程後，您將有個簡單的 Web 應用程式已在雲端中啟動並執行。
 
您將了解：

* 如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
* 如何建立 Visual Studio ASP.NET MVC 5 專案，並將它發行至 Azure 應用程式。
* 如何使用 ASP.NET Web API 來啟用符合 REST 限制的 API 呼叫。
* 如何使用 SQL 資料庫在 Azure 中儲存資料。
* 如何將應用程式更新發行至 Azure。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 5 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成的應用程式：

![網站的螢幕擷取畫面][intro001]

<a name="bkmk_setupdevenv"></a> <!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### 建立專案

1. 啟動 Visual Studio 2013。
1. 從 [檔案] 功能表，按一下 [新增專案]。
3. 在 [新增專案] 對話方塊中，展開 [Visual C#] 並選取 [Web]，再選取 [ASP.NET MVC 5 Web 應用程式]。將應用程式命名為 **ContactManager**，再按一下 [確定]。

	![New Project dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]

1. 在 [New ASP.NET Project] 對話方塊中，選取 [MVC] 範本，勾選 [Web API]，再按一下 [變更驗證]。

	![[New ASP.NET Project] 對話方塊](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. 在 [變更驗證] 對話方塊中，按一下 [不需要驗證]，然後按一下 [確定]。

	![不需要驗證](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)

	您要建立的範例應用程式將不會有需要使用者登入的功能。如需關於如何實作驗證與授權功能的詳細資訊，請參閱本教學課程最後的[後續步驟](#nextsteps)小節。

1. 在 [New ASP.NET Project] 對話方塊中，按一下 [確定]。

	![[New ASP.NET Project] 對話方塊](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

如果您先前未登入 Azure，系統將提示您登入。

1. [組態] 精靈會根據 *ContactManager* 建議唯一名稱 (請參閱下圖)。選取您附近的區域。若要尋找最低延遲的資料中心，您可以使用 [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com")。 
2. 如果您之前尚未建立資料庫伺服器，請選取 [建立新的伺服器]，並輸入資料庫使用者名稱和密碼。

	![設定 Azure 網站](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

如果您有資料庫伺服器，請用它來建立新的資料庫。資料庫伺服器是非常寶貴的資源，通常您會想要在相同伺服器上建立多個資料庫進行測試和開發，而非在每個資料庫上建立資料庫伺服器。請確定您的網站和資料庫位於相同的區域。

![設定 Azure 網站](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### 設定頁首及頁尾


1. 在 [方案總管]中，展開 *Views\Shared* 資料夾並開啟 *_Layout.cshtml* 檔案。
	![[方案總管] 中的 _Layout.cshtml][newapp004]

1. 以下列程式碼取代 *Views\Shared_Layout.cshtml* 檔案中的內容：


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
以上的標記會將應用程式名稱從 "My ASP.NET App" 變更為 "Contact Manager"，同時也移除 **Home**、**About** 及 **Contact** 的連結。

### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。應用程式首頁隨即出現在預設瀏覽器中。![待辦事項清單首頁](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。稍後您將新增資料庫功能。

<h2><a name="bkmk_deploytowindowsazure1"></a>將應用程式部署至 Azure</h2>

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

	![專案內容功能表中的 [發行]][PublishVSSolution]

	此時會開啟 [發行 Web] 精靈。

12. 按一下 [發行]。

![[設定] 索引標籤](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

Visual Studio 隨即開始進行將檔案複製至 Azure 伺服器的程序。[輸出] 視窗會顯示已採取的部署動作，並報告部署作業已順利完成。

14. 預設瀏覽器會自動開啟已部署之網站的 URL。

	您建立的應用程式現在正在雲端中執行。
	
	![在 Azure 中執行的待辦事項清單首頁][rxz2]

<h2><a name="bkmk_addadatabase"></a>新增資料庫至應用程式</h2>

接下來，您將更新 MVC 應用程式，以加上顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式將使用 Entity Framework，以建立資料庫以及讀取和更新資料庫中的資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1. 在 [方案總管]，於 Models 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [類別]。

	![在 Models 資料夾內容功能表中新增類別][adddb001]

2. 在 [加入新項目] 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 [新增]。

	![[加入新項目] 對話方塊][adddb002]

3. 以下列程式碼取代 Contacts.cs 檔案的內容。

		using System.Globalization;
		namespace ContactManager.Models
		{
    		public class Contact
   			{
        		public int ContactId { get; set; }
				public string Name { get; set; }
				public string Address { get; set; }
	        	public string City { get; set; }
				public string State { get; set; }
				public string Zip { get; set; }
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

**Contacts** 類別定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 ContactID。您可以在本教學課程結尾處的[後續步驟](#nextsteps)一節取得資料模型的詳細資訊。

### 建立可讓應用程式使用者使用連絡人的網頁

ASP.NET MVC 樣板功能可自動產生程式碼來執行建立、讀取、更新和刪除 (CRUD) 動作。

<h2><a name="bkmk_addcontroller"></a>新增控制器和資料檢視</h2>

1. 在 [方案總管] 中展開 Controllers 資料夾。

3. 建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案。)

4. 在 Controllers 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [控制器]。

	![在 Controllers 資料夾內容功能表中新增控制器][addcode001]

1. 在 [Add Scaffold] 對話方塊中，選取 [MVC Controller with views, using Entity Framework]，再按一下 [新增]。

 ![新增控制器](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. 將控制器名稱設定為 **HomeController**。選取 [Contact] 模型類別。按一下 [新資料內容] 按鈕，並接受 [新資料內容類型] 的預設值 "ContactManager.Models.ContactManagerContext"。按一下 [新增]。

	![[新增控制器] 對話方塊](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	對話方塊會提示您：「名為 HomeController 的檔案已存在。您要取代該檔案嗎？」按一下 [是]。我們會覆寫隨著新專案一同建立的首頁控制器。我們會將新的首頁控制器用於連絡人清單。

	Visual Studio 隨即針對 **Contact** 物件的 CRUD 資料庫操作，建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式 ##

下一個工作是啟用 [Code First 移轉](http://curah.microsoft.com/55220)功能，以便根據建立的資料模型建立資料庫。

1. 在 [工具] 功能表中，依序選取 [Library Package Manager] 及 [Package Manager Console]。

	![[工具] 功能表中的 Package Manager Console][addcode008]

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		enable-migrations 
  
	**enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來設定 [移轉]。

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		add-migration Initial

	**add-migration Initial** 命令會產生可建立資料庫、名為 **&lt;date_stamp&gt;Initial** 的類別。第一個參數 (*Initial*) 是任意的，用於建立檔案的名稱。您可以在 [方案總管] 中看到新的類別檔案。

	在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。

3. 開啟 *Migrations\Configuration.cs* 檔案。

4. 新增下列命名空間。

    	 using ContactManager.Models;

5. 以下列程式碼取代 *Seed* 方法：
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	以上的這個程式碼會以連絡人資訊初始化資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) (英文)。


1. 在 [Package Manager Console] 中輸入命令：

		update-database

	![Package Manager Console commands][addcode009]

	**update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。

1. 按 CTRL+F5 執行應用程式。

應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。

![資料的 MVC 檢視][rxz3]

<h2><a name="bkmk_addview"></a>編輯檢視</h2>

1. 開啟 *Views\Home\Index.cshtml* 檔案。在下一個步驟中，我們會將產生的標記取代為使用 [jQuery](http://jquery.com/) 和 [Knockout.js](http://knockoutjs.com/) 的程式碼。這個新的程式碼會使用 Web API 和 JSON 來擷取連絡人清單，然後再使用 knockout.js 使連絡人資料與 UI 繫結。如需詳細資訊，請參閱本教學課程結尾處的[後續步驟](#nextsteps)一節。 


2. 以下列程式碼取代檔案的內容。

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. 在 Content 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [新增項目...]。

	![Content 資料夾內容功能表中的 [加入樣式表]][addcode005]

4. 在 [加入新項目] 對話方塊中，於右上角的搜尋方塊中輸入 **Style**，然後選取 [樣式表]。![[加入新項目] 對話方塊][rxStyle]

5. 將檔案命名為 *Contacts.css*，然後按一下 [新增]。以下列程式碼取代檔案的內容。
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	我們會將此樣式表用於 Contact Manager 應用程式所用的版面配置、色彩及樣式。

6. 開啟 *App_Start\BundleConfig.cs* 檔案。


7. 新增以下程式碼以註冊 [Knockout](http://knockoutjs.com/index.html "KO") 外掛程式。

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	此範例使用 knockout 來簡化處理螢幕範本的動態 JavaScript 程式碼。

8. 修改 contents/css 項目以註冊 *contacts.css* 樣式表。變更以下文字行：

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
變更為：

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. 在 Package Manager Console 中，執行下列命令以安裝 Knockout。

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>為符合 Web API REST 限制的介面新增控制器</h2>

1. 在 [方案總管]，於 Controllers 上按一下滑鼠右鍵，按一下 [新增]，再按一下 [控制器...] 

1. 在 [Add Scaffold] 對話方塊中，選取 [Web API 2 Controller with actions, using Entity Framework]，再按一下 [新增]。

	![新增 API 控制器](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. 在 [加入控制器] 對話方塊中，輸入 "ContactsController" 作為控制器名稱。選取 "Contact (ContactManager.Models)" [模型類別]。保留 [資料內容類別] 的預設值。

6. 按一下 [新增]。

### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。

	![索引頁面][intro001]

2. 輸入連絡人，然後按一下 [新增]。應用程式會返回首頁並顯示您輸入的連絡人。

	![含有待辦事項清單的索引頁面][addwebapi004]

3. 在瀏覽器中，於 URL 後方加上 **/api/contacts**。

	所產生的 URL 將類似 http://localhost:1234/api/contacts。您新增之符合 REST 限制的 Web API 會傳回儲存的連絡人。Firefox 和 Chrome 會顯示 XML 格式的資料。

	![含有待辦事項清單的索引頁面][rxFFchrome]
	

	IE 會提示您開啟或儲存連絡人。

	![Web API 儲存對話方塊][addwebapi006]
	
	
	您可以利用記事本或瀏覽器開啟傳回的連絡人。
	
	行動網頁或應用程式之類的其他應用程式亦可取用此輸出。

	![Web API 儲存對話方塊][addwebapi007]

	**安全性警告**：此時您的應用程式並未受到保護，且容易遭受 CSRF 攻擊。在本教學課程稍後的內容中，我們將移除這項弱點。如需詳細資訊，請參閱[避免跨網站偽造要求 (CSRF) 攻擊][prevent-csrf-attacks] (英文)。

<h2><a name="xsrf"></a>新增 XSRF 保護</h2>

跨網站偽造要求 (亦稱為 XSRF 或 CSRF) 為以 Web 主控之應用程式為目標的攻擊，惡意網站能藉此影響用戶端瀏覽器和該瀏覽器信任之網站間的互動。這些攻擊之所以能得逞，是因為網頁瀏覽器會隨著對網站的每個要求自動傳送驗證權杖。ASP.NET 的 Forms Authentication 票證即是驗證 Cookie 的標準範例。然而，使用任何持續驗證機制 (如 Windows 驗證、基本驗證等等) 的網站都可能成為這些攻擊的目標。

XSRF 攻擊與網路釣魚攻擊不同。網路釣魚攻擊需要與受害者互動。對於網路釣魚攻擊，惡意網站會偽裝成目標網站，致使受害者因受騙而將機密資訊提供給攻擊者。XSRF 攻擊則通常不需要與受害者互動。反之，攻擊者需仰賴瀏覽器將所有相關的 Cookie 自動傳送給目的地網站。

如需詳細資訊，請參閱 [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)))。

1. 在 [方案總管] 中，於 **ContactManager** 專案上按一下滑鼠右鍵，按一下 [新增]，再按一下 [類別]。

2. 將檔案命名為 *ValidateHttpAntiForgeryTokenAttribute.cs*，然後新增以下程式碼：

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. 將以下 *using* 陳述式新增至連絡人控制器，使您得以存取 **[ValidateHttpAntiForgeryToken]** 屬性。

	using ContactManager.Filters;

1. 將 **[ValidateHttpAntiForgeryToken]** 屬性新增至 **ContactsController** 的 Post 方法，使其免於遭受 XSRF 威脅的攻擊。您需要將其新增至 PutContact"、"PostContact" 及 **DeleteContact** 動作方法。

	[ValidateHttpAntiForgeryToken] public IHttpActionResult PutContact(int id, Contact contact) {

1. 更新 *Views\Home\Index.cshtml* 檔案的 *Scripts* 區段，使其包含取得 XSRF 權杖的程式碼。

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


<h2><a name="bkmk_deploydatabaseupdate"></a>將應用程式更新發行至 Azure 和 SQL Database</h2>

若要發行應用程式，您需要重複先前遵循過的程序。

1. 在 [方案總管] 中以滑鼠右鍵按一下專案，再選取 [發行]。

	![Publish][rxP]

5. 按一下 [設定] 索引標籤。
	

1. 在 **ContactsManagerContext(ContactsManagerContext)** 下方按一下 **v** 圖示，將 *Remote connection string* 變更為連絡人資料庫的連接字串。按一下 [ContactDB]。

	![設定](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. 勾選 [Execute Code First Migrations (runs on application start)] 的方塊。

1. 依序按 [下一步] 和 [預覽]。Visual Studio 會顯示即將新增或更新的檔案清單。

8. 按一下 [發行]。部署完成後，瀏覽器會開啟應用程式的首頁。

	![Index page with no contacts][intro001]

	Visual Studio 發行程序會自動設定已部署之 *Web.config* 檔案中的連接字串，使其指向 SQL 資料庫。它也設定了 Code First 移轉，使其在應用程式於部署完成後首次存取資料庫時，將資料庫自動升級為最新版本。

	由於這項組態的關係，Code First 會執行您稍早於 **Initial** 類別中建立的程式碼，進而建立資料庫。它會在應用程式於部署完成後首次嘗試存取資料庫時執行這項作業。

9. 與在本機執行應用程式時一樣的方式輸入連絡人，以驗證資料庫部署是否成功。

當您發現輸入的項目已儲存且出現在 Contact Manager 頁面時，表示該項目已儲存在資料庫中。

![含有連絡人的索引頁面][addwebapi004]

應用程式現已在雲端運作，並使用 SQL Database 來儲存資料。在 Azure 中完成應用程式測試後，請將應用程式刪除。應用程式已處於公開狀態且不具有限制存取權限的機制。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

<h2><a name="nextsteps"></a>後續步驟</h2>

真實的應用程式會要求驗證和授權，而您需要使用成員資格資料庫來達成目的。[部署含有 OAuth、成員資格及 SQL Database 的安全 ASP.NET MVC 應用程式](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)教學課程是以本教學課程為基礎，其展示如何部署含有成員資格資料庫的 Web 應用程式。

另一個儲存 Azure 應用程式資料的方法是使用 Azure 儲存體，它能以 Blob 和資料表的形式提供非關聯式的資料儲存。以下連結提供 Web API、ASP.NET MVC 及 Window Azure 的詳細資訊。
 

* [使用 MVC 的 Entity Framework 入門][EFCodeFirstMVCTutorial]
* [ASP.NET MVC 5 入門](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [您的第一個 ASP.NET Web API](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [偵錯 WAWS](web-sites-dotnet-troubleshoot-visual-studio.md)

本教學課程和範例應用程式是由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) 在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 的協助下所撰寫。

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，歡迎留下意見反應。您的意見反應將協助我們訂出優先改善要務。我們非常希望能了解您對於將設定和部署成員資格資料庫之程序更進一步自動化的期待為何。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
 

<!---HONumber=62-->