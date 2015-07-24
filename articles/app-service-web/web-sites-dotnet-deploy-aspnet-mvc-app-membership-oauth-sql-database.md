<properties 
	pageTitle="使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure 應用程式服務" 
	description="了解如何開發具有 SQL Database 後端的 ASP.NET MVC 5 應用程式、加入驗證和授權，並將它部署至 Azure。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="riande"/>



# 使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure 應用程式服務

本教學課程將示範如何建立可讓使用者以 Facebook 或 Google 認證登入的安全 ASP.NET MVC 5 Web 應用程式。您也會將應用程式部署至 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個安全的資料驅動 Web 應用程式已在雲端中啟動並執行、並已在使用雲端資料庫。

您將了解：

* 如何建立安全的 ASP.NET MVC 5 專案，並將它發行至 Azure App Service 中的 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。
* 如何使用 [OAuth](http://oauth.net/ "http://oauth.net/") 與 ASP.NET 成員資格資料庫來確保您的應用程式安全。
* 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 5 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成之應用程式的登入頁面：

![登入頁面][rxb]

>[AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以<a href="/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 MSDN 訂戶權益</a>或是<a href="/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>。

>如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

若要設定開發環境，您必須安裝 [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkId=390521) 或更高版本，以及最新版本的 [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)。本文專為 Visual Studio Update 4 和 SDK 2.5.1 所撰寫。

## 建立 ASP.NET MVC 5 應用程式

### 建立專案

1. 從 [檔案] 功能表，按一下 [新增專案]。

	![[檔案] 功能表中的 [新增專案]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. 在 [新增專案] 對話方塊中，展開 [C#] 並選取 [已安裝的範本] 下的 [Web]，再選取 [ASP.NET Web 應用程式]。

1. 將應用程式命名為 **ContactManager**，再按一下 [確定]。

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**注意：**請確定您輸入的是 ContactManager。您稍後將要複製的程式碼區塊會假設專案名稱為 ContactManager。

1. 在 [新增 ASP.NET 專案] 對話方塊中，選取 [MVC] 範本。確認已將 [**驗證**] 設為 [**個別使用者帳戶**]、已勾選 [**雲端主機**]，且已選取 **[Web 應用程式**]。

	![[New ASP.NET Project] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. [組態] 精靈會根據 *ContactManager* 建議唯一名稱。您必須決定是否建立新 App Service 方案和資源群組。 如需是否建立新方案或資源群組的相關指引，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。在本教學課程中，您可能會想要建立至少一個新的資源群組。選取您附近的區域。若要尋找最低延遲的資料中心，您可以使用 [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com")。接下來您將設定資料庫，所以請不要按一下 [**確定**]。

   ![新方案和資源群組](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. 如果您之前尚未建立資料庫伺服器，請選取 [**建立新伺服器**]，並輸入資料庫名稱、使用者名稱和密碼。

   ![使用新資料庫](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

3. 如果您有資料庫伺服器，請用它來建立新的資料庫。資料庫伺服器是非常寶貴的資源，通常您會想要在相同伺服器上建立多個資料庫進行測試和開發，而非在每個資料庫上建立資料庫伺服器。請確定您的 Web 應用程式和資料庫位於相同區域。

    ![使用現有資料庫](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/useexistingdb.png)

### 設定頁首及頁尾


1. 在 [方案總管]，開啟 *Views\Shared* 資料夾中的 *Layout.cshtml* 檔案。

	![方案總管中的 _Layout.cshtml][newapp004]

1. 使用下列程式碼來取代 *Layout.cshtml* 檔案中的標記。變更重點如下。

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。

	應用程式首頁隨即出現在預設瀏覽器中。

	![本機執行的 Web 應用程式](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。

## 對專案啟用 SSL ##

1. 啟用 SSL。在 [方案總管]，按一下 [ContactManager] 專案，再按一下 F4 帶出 [屬性] 對話方塊。將 [SSL 已啟用] 變更為 true。複製 [SSL URL]。除非您先前已建立 SSL Web 應用程式，否則 SSL URL 將是 https://localhost:44300/。

	![enable SSL][rxSSL]
 
1. 在 [方案總管]，於 [Contact Manager] 專案上按一下滑鼠右鍵，再按一下 [屬性]。
1. 在左側索引標籤中按一下 [Web]。
1. 將 [專案 URL] 變為使用 [SSL URL]，並且儲存頁面 (Control S)。

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. 驗證 Internet Explorer 是 Visual Studio 所啟動的瀏覽器，如下圖所示：

	![預設瀏覽器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	瀏覽器選取器可讓您指定 Visual Studio 啟動的瀏覽器。

 	![瀏覽器選取器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

	您可以選取多個瀏覽器，並讓 Visual Studio 在您進行變更時更新每個瀏覽器。如需詳細資訊，請參閱[在 Visual Studio 2013 中使用瀏覽器連結](http://www.asp.net/visual-studio/overview/2013/using-browser-link)。


1. 按 CTRL+F5 執行應用程式。遵循指示來信任 IIS Express 產生的自我簽署憑證。

	 ![信任 IIS Express 產生的自我簽署憑證的指示](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. 閱讀 [安全性警告] 對話方塊，如果您要安裝代表 **localhost** 的憑證，請按一下 [是]。

 	![localhost IIS Express 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE 顯示*首頁*，沒有出現 SSL 警告。

	 ![IE 出現 localhost SSL，沒有出現警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	Google Chrome 也會接受憑證，並且不出現警告便顯示 HTTPS 內容。Firefox 會使用自己的憑證存放區，因此會顯示警告。

	 ![FireFox 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## 將應用程式部署至 Azure

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

	![專案內容功能表中的 [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
   此時會開啟 [發行 Web] 精靈。

1. 在 [發行 Web] 對話方塊中，按一下 [發行]。

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。

	![在雲端中執行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## 新增資料庫至應用程式

接下來，您將更新應用程式，以新增顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式會使用 Entity Framework (EF) 來建立資料庫及讀取和更新資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1. 在 [方案總管]，於 Models 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [類別]。

	![在 Models 資料夾內容功能表中新增類別](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. 在 [加入新項目] 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 [新增]。

	![[加入新項目] 對話方塊][adddb002]

3. 以下列程式碼取代 Contacts.cs 檔案的內容。

        using System.ComponentModel.DataAnnotations;
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
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
**Contacts** 類別定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 *ContactID*。

### 建立可讓應用程式使用者使用連絡人的網頁

ASP.NET MVC 樣板功能可自動產生程式碼來執行建立、讀取、更新和刪除 (CRUD) 動作。

## 新增控制器和資料檢視

1. 建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案)。 
1. 在 [方案總管]，於 Controllers 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [控制器]。

	![在 Controllers 資料夾內容功能表中新增控制器][addcode001]

5. 在 [Add Scaffold] 對話方塊中，選取 [MVC 5 Controller with views, using EF]，再按一下 [新增]。
	
	![[Add Scaffold] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)


1. 在 [模型類別] 下拉式方塊中選取 [Contact (ContactManager.Models)]。(請參閱下圖)。
1. 在 [資料內容類別] 中選取 [ApplicationDbContext (ContactManager.Models)]。[ApplicationDbContext] 將用於成員資格 DB 和我們的連絡人資料。
1. 在 [控制器名稱] 文字輸入方塊中，輸入 "CmController" 作為控制器名稱。 

	![新增資料內容對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. 按一下 [新增]。

   Visual Studio 隨即針對 **Contact** 物件的 CRUD 資料庫操作，建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式 ##

下一個工作是啟用 [Code First 移轉](http://msdn.microsoft.com/library/hh770484.aspx)功能，以便根據建立的資料模型建立資料庫。

1. 在 [**工具**] 功能表中，依序選取 [**NuGet 封裝管理員**] 和 [**Package Manager Console**]。![[工具] 功能表中的 Package Manager Console](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		enable-migrations
	**enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定 [移轉]。

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		add-migration Initial


	**add-migration Initial** 命令會在建立資料庫的 *Migrations* 資料夾中產生名為 **&lt;date_stamp&gt;Initial** 的檔案。第一個參數 (**Initial**) 是任意的，用於建立檔案的名稱。您可以在 [方案總管] 中看到新的類別檔案。在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。
3. 開啟 *Migrations\Configuration.cs* 檔案。 
4. 新增下列命名空間。 

    	 using ContactManager.Models;



5. 以下列程式碼取代 *Seed* 方法：

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
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
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	此程式碼會以連絡人資訊初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。


6. 在 [Package Manager Console] 中輸入命令：

		update-database

	![Package Manager Console commands][addcode009]

	**update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。

7. 按 CTRL+F5 執行應用程式，再按一下 [**CM Demo**] 連結；或瀏覽 https://localhost:(port#)/Cm。

	應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。您可以建立、編輯、刪除及檢視資料。

	![資料的 MVC 檢視][rx2]



## 新增 OAuth2 提供者

[OAuth](http://oauth.net/ "http://oauth.net/") 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何其中一個提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。若要使用 Facebook 作為驗證提供者，請參閱[使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)教學課程。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 *canEdit* 角色的使用者才能變更資料 (也就是建立、編輯或刪除連絡人)。

依照 [**建立 Google app for OAuth 2 以設定 Google app for OAuth2**] 下 [[使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 App](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)] 的教學課程指示進行。執行與測試應用程式，以確認您可以使用 Google 驗證登入。

## 使用成員資格 API
在本節中，您會將本機使用者和 *canEdit* 角色新增至成員資格資料庫。只有 *canEdit* 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非新增較欠缺描述性的 *superAdmin*。

1. 開啟 *migrations\configuration.cs* 檔案並新增下列 `using` 陳述式：

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. 將下列 **AddUserAndRole** 方法新增至類別：

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. 從 **Seed** 方法呼叫新方法：
	<pre>
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
        context.Contacts.AddOrUpdate(p => p.Name,
            // 移除了程式碼以求簡單明瞭
    }
</pre>
<span></span>下圖顯示對 *Seed* 方法的變更：

	![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

   此程式碼會建立名為 *canEdit* 的新角色、建立新本機使用者 *user1@contoso.com*，並將 *user1@contoso.com* 新增至 *canEdit* 角色。如需詳細資訊，請參閱[我的 ASP.NET 識別教學課程](http://www.asp.net/identity/overview/features-api)。

## 使用暫時程式碼，將新的社交登入使用者新增至 canEdit 角色  ##
在本節中，您將在帳戶控制器中暫時修改 **ExternalLoginConfirmation** 方法，以將使用 OAuth 提供者註冊的新使用者新增至 *canEdit* 角色。我們會暫時修改 **ExternalLoginConfirmation** 方法，以將新使用者自動新增至管理角色。在我們提供工具來新增及管理角色前，我們將暫時使用以下的自動註冊程式碼。我們希望將來能提供類似 [WSAT](http://msdn.microsoft.com/zh-tw/library/ms228053.aspx) 的工具，讓您能建立及編輯使用者帳戶與角色。

1. 開啟 **Controllers\AccountController.cs** 檔案並瀏覽到 **ExternalLoginConfirmation** 方法。
1. 將下列呼叫新增至就在 **SignInAsync** 呼叫前面的 **AddToRoleAsync**。

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   上述程式碼將新註冊的使用者新增至「canEdit」角色，使用者因此能存取用來變更 (編輯) 資料的動作方法。 <pre> // POST: /Account/ExternalLoginConfirmation [HttpPost] [AllowAnonymous] [ValidateAntiForgeryToken] public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl) { if (User.Identity.IsAuthenticated) { return RedirectToAction("Index", "Manage"); } if (ModelState.IsValid) { // 從外部登入提供者取得使用者資訊 var info = await AuthenticationManager.GetExternalLoginInfoAsync(); if (info == null) { return View("ExternalLoginFailure"); } var user = new ApplicationUser { UserName = model.Email, Email = model.Email }; var result = await UserManager.CreateAsync(user); if (result.Succeeded) { result = await UserManager.AddLoginAsync(user.Id, info.Login); if (result.Succeeded) { <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark> await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false); return RedirectToLocal(returnUrl); } } AddErrors(result); } ViewBag.ReturnUrl = returnUrl; return View(model); } </pre>

稍後在本教學課程中，您會將應用程式部署至 Azure，並在後者使用 Google 或其他協力廠商驗證提供者來登入。這會將您新註冊的帳戶新增至 *canEdit* 角色。任何人只要有您 Web 應用程式的 URL 和 Google ID，就可以註冊及更新您的資料庫。若要防止其他人這麼做，您可以停止網站。您可以檢查資料庫來確認 *canEdit* 角色中有誰。

在 [Package Manager Console] 點擊向上鍵以帶出下列命令：

		Update-Database

執行 **Update-Database** 命令，以執行 **Seed** 方法並進而執行您剛剛新增的 **AddUserAndRole**。**AddUserAndRole** 將建立使用者，*user1@contoso.com*並將她新增至 *canEdit* 角色。

## 使用 SSL 和 Authorize 屬性保護應用程式 ##

在本節中，您將套用 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性來限制對動作方法的存取。匿名使用者只能檢視首頁控制器的 **Index** 動作方法。註冊的使用者能夠查看連絡人資料 (Cm 控制器的 [索引] 和 [詳細資料] 頁面)、[關於] 和 [連絡人] 頁面。只有 *canEdit* 角色中的使用者才能存取用來變更資料的動作方法。

1. 將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選器新增至應用程式。替代的方法是將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 屬性新增至每個控制器，但將這些屬性套用至整個應用程式是最安全的做法。藉由全面新增這些屬性，您所新增的每個新控制器和動作方法都會自動受到保護，而不需要您記得哪些已套用、哪些未套用。如需詳細資訊，請參閱[保護您的 ASP.NET MVC 應用程式和新 AllowAnonymous 屬性](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)。開啟 *App_Start\FilterConfig.cs* 檔案，並使用以下程式碼 (新增兩個篩選器) 取代 *RegisterGlobalFilters* 方法：<pre> public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute());</mark> } </pre>




	上述程式碼中套用的 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器將防止匿名使用者存取應用程式中的任何方法。您將使用 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性來選擇略過一些方法中的授權需求，讓匿名使用者可登入及檢視首頁。使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 時，所有對 Web 應用程式的存取都必須透過 HTTPS 進行。

1. 將 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性新增至首頁控制器的 **Index** 方法。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性能讓您將想要選擇略過授權的方法加到白名單。<pre> public class HomeController : Controller { <mark>[AllowAnonymous]</mark> public ActionResult Index() { return View(); } </pre>

2. 全面搜尋 *AllowAnonymous*，您會發現帳戶控制器的登入與註冊方法中都使用了它。
1. 在 *CmController.cs* 中，將 `[Authorize(Roles = "canEdit")]` 新增至 *Cm* 控制器中用來變更資料的 HttpGet 和 HttpPost 方法 (Create、Edit、Delete，也就是 Index 和 Details 外的每個動作方法)。以下顯示完整程式碼的部分內容：<pre> // GET: Cm/Create <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create() { return View(new Contact { Address = "123 N 456 W", City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT", Zip = "59405"}); } // POST: Cm/Create // 若要防範 overposting 攻擊，請啟用您想繫結的特定屬性，如需 // 更多詳細資料，請參閱 http://go.microsoft.com/fwlink/?LinkId=317598。 [HttpPost] [ValidateAntiForgeryToken] <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact) { if (ModelState.IsValid) { db.Contacts.Add(contact); db.SaveChanges(); return RedirectToAction("Index"); } return View(contact); } // GET: Cm/Edit/5 <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Edit(int? id) { if (id == null) { return new HttpStatusCodeResult(HttpStatusCode.BadRequest); } Contact contact = db.Contacts.Find(id); if (contact == null) { return HttpNotFound(); } return View(contact); } </pre>

1. 如果您在前個工作階段仍保持登入狀態，請點擊 [登出] 連結。
1. 按一下 [關於] 或 [連絡人] 連結。因為匿名使用者無法檢視那些頁面，所以系統會將您重新導向至登入頁面。 
1. 按一下 [**註冊為新使用者**] 連結並使用電子郵件 *joe@contoso.com* 新增本機使用者。確認 *Joe* 可檢視 [首頁]、[關於] 和 [連絡人] 頁面。
	![登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. 按一下 [CM Demo] 連結並確認看到資料。
1. 按一下頁面上的編輯連結，系統會將您重新導向至登入頁面 (因為未將新的本機使用者新增至 *canEdit* 角色)。
1. 使用 *user1@contoso.com* 的身分和密碼 "P_assw0rd1" (word 中的「0」是數字零) 登入。系統隨即將您重新導向到先前選取的編輯頁面。<br/> 如果無法以該帳戶和密碼登入，請嘗試複製並貼上原始程式碼中的密碼。如果仍然無法登入，請檢查 **AspNetUsers** 資料表的 **UserName** 欄以確認已新增 *user1@contoso.com*。
1. 確認您可進行資料變更。

## 將應用程式部署至 Azure

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

	![專案內容功能表中的 [發行]][firsdeploy003]

	此時會開啟 [發行 Web] 精靈。

1. 按一下 [發行 Web] 對話方塊左側的 [設定] 索引標籤。按一下 [**v**] 圖示以選取 [**ApplicationDbContext**] 的 [**遠端連接字串**]，並選取 [**ContactManagerNN_db**]。

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. 在 [ContactManagerContext] 下選取 [Execute Code First Migrations]。

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. 按一下 [發行]。
1. 以 *user1@contoso.com* (使用密碼 "P_assw0rd1") 身分登入，並確認您可以編輯資料。1. 登出。
1. 請移至 [[Google Developers Console](https://console.developers.google.com/)]，並在 [**認證**] 索引標籤上更新 [重新導向 URI] 和 [JavaScript Orgins] 以使用 Azure URL。
1. 使用 Google 或 Facebook 登入。如此將使 Google 或 Facebook 帳戶新增至 **canEdit** 角色。如果您收到 HTTP 400 錯誤訊息：*要求中的重新導向 URI：https://contactmanager{my https://contactmanager{my version}.azurewebsites.net/signin-google 與已註冊的重新導向 URI 不符。*，您必須等到您所做的變更都已傳播為止。如果您是在超過數分鐘之後收到此錯誤，請確認 URI 是正確的。

### 停止 Web 應用程式以防止其他人註冊  

1. 在 [**伺服器總管**] 中，導覽至 [**Web Apps**]。
4. 在 Web 應用程式上按一下滑鼠右鍵，然後選取 [**停止**]。 

	![停止 Web 應用程式](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s1.png)

	或者，您也可以從 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)選取 Web 應用程式，然後按一下頁面底部的 [**停止**] 圖示。

	![停止 Web 應用程式入口網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### 移除 AddToRoleAsync、發行和測試

1. 在帳戶控制器中，從 **ExternalLoginConfirmation** 方法註解化下列程式碼或將其移除：`await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. 建置專案 (如此會儲存檔案變更並確認沒有任何編譯錯誤)。
5. 在 [方案總管] 中以滑鼠右鍵按一下專案，再選取 [發行]。

	   ![專案內容功能表中的 [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. 按一下 [開始預覽] 按鈕。只會部署需要更新的檔案。
5. 從 Visual Studio 或透過入口網站啟動 Web 應用程式。**您無法在 Web 應用程式停止時進行發行**。

	![啟動 Web 應用程式](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. 返回 Visual Studio 並按一下 [發行]。
3. 您的 Azure 應用程式隨即在預設瀏覽器中開啟。如果您已登入，請登出，以便您可以使用匿名使用者的身分檢視首頁。  
4. 按一下 [關於] 連結。系統會將您重新導向至 [登入] 頁面。
5. 按一下 [登入] 頁面上的 [註冊] 連結並建立本機帳戶。我們將使用此本機帳戶，確認您可以存取唯讀頁面但無法存取用來變更資料的頁面 (這些頁面受 *canEdit* 角色保護)。本教學課程稍後將移除本機帳戶存取權。 

	![註冊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. 確認您可瀏覽到 [關於] 和 [連絡人] 頁面。

	![登出](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. 按一下 [CM Demo] 連結並瀏覽到 **Cm** 控制器。或者，您也可以在 URL 後加上 *Cm*。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. 按一下 [編輯] 連結。系統會將您重新導向至登入頁面。在 [Use another service to log in] 下按一下 [Google] 或 [Facebook]，並以您先前註冊的帳戶登入。(如果您的動作夠快，同時工作階段 Cookie 未逾時，將使用您先前所使用的 Google 或 Facebook 帳戶為您自動登入。)
2. 確認您在登入該帳戶後可編輯資料。**注意：**您無法在從此應用程式登出 Google 後，又使用相同瀏覽器登入不同的 Google 帳戶。若是使用一個瀏覽器，則必須瀏覽到 Google 再登出。您可以使用不同瀏覽器，以自己在相同協力廠商驗證者 (例如 Google) 那邊的其他帳戶登入。

如果您並未填寫 Google 帳戶資訊中的姓名，則會發生 NullReferenceException。


## 檢查 SQL Azure DB ##

1. 在 [伺服器總管] 中，瀏覽到 [ContactDB]
2. 在 [ContactDB] 上按一下滑鼠右鍵，再選取 [Open in SQL Server Object Explorer]。
 
	![在 SSOX 中開啟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. 若您先前未曾連線至此資料庫，系統將提示您新增防火牆規則，以允許您從目前 IP 位址存取資料庫。IP 位址將預先填入。只需按一下 [**新增防火牆規則**] 即可存取。

  ![新增防火牆規則](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
        
  接下來，使用您建立資料庫時指定的使用者名稱和密碼來登入資料庫。
 
1. 在 [AspNetUsers] 資料表上按一下滑鼠右鍵並選取 [檢視資料]。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. 記下您以 Google 帳戶註冊且要加到 **canEdit** 角色中的識別碼，以及 *user1@contoso.com* 的識別碼。這些應為 **canEdit** 角色中唯一的使用者(您將會在下一個步驟進行確認)。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. 在 [SQL Server 物件總管]，於 [AspNetUserRoles] 上按一下滑鼠右鍵，再選取 [檢視資料]。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
確認所列的 **UserId** 屬於 *user1@contoso.com* 和您註冊的 Google 帳戶。


## 後續步驟

請遵循以此範例為基礎的教學課程：

1.	[建立安全的 ASP.NET MVC 5 Web 應用程式，並重設登入、電子郵件確認與密碼](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[使用 SMS 和電子郵件雙因素驗證的 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[將密碼和其他機密資料部署至 ASP.NET 和 Azure 的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[使用 Facebook 和 Google OAuth2 建立 ASP.NET MVC 5 App](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) 這包括如何將設定檔資料新增至使用者註冊 DB 的指示，並取得使用 Facebook 作為驗證提供者的詳細指示。
5.	[開始使用 ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

若要啟用此教學課程頂端顯示的社交登入按鈕，請參閱 [ASP.NET MVC 5 的美觀社交登入按鈕](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/) (英文)。

Tom Dykstra 見解精闢的[開始使用 EF 和 MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (英文) 將示範更進階的 MVC 和 EF 程式設計。

本教學課程和範例應用程式是由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) 在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 的協助下所撰寫。

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，***歡迎留下意見反應***。您的意見反應將協助我們訂出優先改善要務。您也可以在[告訴我如何處理程式碼](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (英文) 提出並對新主題進行投票。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=62-->