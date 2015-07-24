<properties 
	pageTitle="使用成員資格、OAuth 和 SQL Database，建立安全的 ASP.NET Web Forms 應用程式並部署至 Azure App Service。 " 
	description="本教學課程說明如何建置包含 SQL Database 的安全 ASP.NET 4.5 Web Forms Web 應用程式，並將應用程式部署至 Azure。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="erikre"/>


# 使用成員資格、OAuth 和 SQL Database，建立安全的 ASP.NET Web Forms 應用程式並部署至 Azure App Service。

##概觀
本教學課程說明如何建置包含 SQL Database 的安全 ASP.NET 4.5 Web Forms Web 應用程式，並將應用程式部署至 Azure。

>[AZURE.NOTE]如需本教學課程 MVC 版本，請參閱[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Microsoft Azure 的經驗。完成此教學課程後，您將會有一個已在雲端中啟動並執行、並使用雲端資料庫的 Web 應用程式。

您將了解：

- 如何建立 ASP.NET 4.5 Web Forms 專案，並將該專案發行至 Azure App Service。
- 如何使用 OAuth 與 ASP.NET 成員資格來確保您的應用程式安全。
- 如何將單一資料庫同時用於成員資格和應用程式資料。
- 如何使用 Web Forms Scaffolding 來建立可讓您修改資料的網頁。
- 如何使用新的成員資格 API 來新增使用者和角色。
- 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個採用 ASP.NET 4.5 Web Forms，並使用 Entity Framework 存取資料庫的簡單連絡人清單 Web 應用程式。下圖顯示允許讀取與寫入資料庫的 Web Forms 頁面：

![Contacts - Edit Page](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms00.png)

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有這類帳戶，可以<a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 MSDN 訂戶權益</a>或是<a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>。如果您要在註冊帳戶前開始使用 Azure，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 中立即建立短期的免費 ASP.NET 簡易版網站。不需要信用卡，沒有承諾。

##設定開發環境 
首先，安裝 Visual Studio 2013 和 Azure SDK for .NET 以設定您的開發環境。

1. 如果還沒有安裝 [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566)，請先安裝此軟體。  
2. 安裝 [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)。本教學課程需要 Visual Studio 2013，才能安裝 Azure SDK for Visual Studio 2013。  

	>[AZURE.NOTE]視您的電腦上有多少 SDK 相依性而定，安裝 SDK 可能需要很長的時間 (從數分鐘到半小時以上不等)。

3. 如果系統提示您執行或儲存安裝可執行檔時，請按一下 [執行]。
4. 在 [**Web Platform Installer**] 視窗中，按一下 [**安裝**] 並繼續進行安裝。![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-01.png)  

	>[AZURE.NOTE]如果您已安裝 SDK，則待安裝的項目會為 0。[Web Platform Installer] 視窗的左下角會記錄待安裝的項目數。

5. 如果您還沒有 **Visual Studio Update 2**，請下載並安裝 **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** 或更高版本。

	>[AZURE.NOTE]您必須安裝 Visual Studio 2013 Update 2 或更高版本，才能在不會出現警告的情況下，在本機使用 Goggle OAuth 2.0 和 SSL。此外，您需要 Update 2 才能使用 Web Forms Scaffolding。

安裝完成時，您即可開始進行開發。

##設定 Azure 環境
在這一節中，您將利用在 Azure 中建立 Azure 和 SQL 資料庫，來設定 Azure 環境。

###在 Azure 中建立 Web 應用程式和 SQL Database 
在本教學課程中，您的 Web 應用程式會在共用主控環境中執行，意即其會與其他 Azure App Service 中的 Web 應用程式共用的虛擬機器 (VM) 上執行。共用主控環境是一種在雲端中開始營運的低成本方法。因為應用程式是在專用 VM 上執行，所以如果日後您的 Web 流量增加，可依需求對應用程式進行延展。如果您需要更複雜的架構，可以移轉至 Azure 雲端服務。雲端服務是執行於您可視本身需求來設定的專用 VM 上。

Azure SQL Database 是以 SQL Server 技術為基礎來建置的雲端型關聯式資料庫服務。工具和應用程式如果使用 SQL Server，同樣也可以使用 SQL Database。

1. 在 [Azure 管理入口網站中](https://manage.windowsazure.com/)，按一下左側索引標籤的 [**Web Apps**]，再按一下 [**新增**]。![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-02.png)
2. 按一下 [**Web 應用程式**]，再按一下 [**自訂建立**]。![自訂建立](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-03.png) [**新 Web 應用程式 - 自訂建立**] 精靈隨即開啟。  

3. 在精靈的 [**建立 Web 應用程式**] 步驟中，於 [**URL**] 方塊中輸入字串做為應用程式的唯一 URL。完整的 URL 將包含您在此處輸入的字串，加上您在文字方塊旁看到的尾碼。下圖顯示該 URL 可能已被佔用，因此**您必須選擇不同的 URL**。![連絡人 - 建立新網站](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-04.png)
4. 在 [Web 主控方案] 下拉式清單中，選擇最接近您位置的區域。此設定可指定 VM 將在哪個資料中心執行。
5. 在 [資料庫] 下拉式清單中，選擇 [Create a free 20 MB SQL database]。
6. 在 [DB Connection String Name] 方塊中，保留預設值 *DefaultConnection*。
7. 按一下方塊底部的箭頭。精靈隨即前進至 [指定資料庫設定] 步驟。
8. 在 [**名稱**] 方塊中，輸入 *`ContactDB`*。![資料庫設定](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-05.png)  
9. 在 [**伺服器**]方塊中，選取 [**New SQL Database**] 伺服器。或者，如果您已建立 SQL Server 資料庫，可從下拉式清單控制項選取該 SQL Server。
10. 將 [**區域**] 設為當初建立 Web 應用程式時的相同區域。
11. 輸入系統管理員的 [登入名稱] 和 [密碼]。如果選取 [New SQL Database server]，則不要在此處輸入現有的名稱和密碼，而是輸入新的名稱和密碼；您現在定義的名稱和密碼將供未來存取資料庫時使用。如果選取先前建立的 SQL Server，系統會提示您提供先前建立之 SQL Server 帳戶名稱的密碼。在本教學課程中，請不要勾選 [進階] 方塊。
12. 按一下方塊右下角的打勾記號來表示完成。

[**Azure 管理入口網站**] 會隨即返回 [**Web Apps**] 頁面，且 [**狀態**] 欄顯示正在建立網站。不久之後 (通常不到一分鐘)，[狀態] 欄就會顯示建立網站成功。在左側的導覽列中，[**Web 應用程式**] 圖示旁會出現您帳戶中已有的網站數；[**SQL Database**] 圖示旁則出現資料庫數目。
##建立 ASP.NET Web Forms 應用程式 
您已建立 Web 應用程式，但其中還沒有內容。接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。
###建立專案 
1. 在 Visual Studio 的 [**檔案**] 功能表中，選取 [**新增專案**]。![[檔案] 功能表 - 新增專案](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms01.png)  
2. 依序選取 [範本] -> [Visual C#] -> 左側的 [Web] 範本群組。 
3. 選擇中間欄的 [ASP.NET Web 應用程式] 範本。
4. 將您的專案命名為 *ContactManager*，並按一下 [**確定**]。![New Project Dialog](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms02.png)  

	>[AZURE.NOTE]此教學課程系列中的專案名稱為 **ContactManager**。建議您使用完全相同的專案名稱，以便整個教學課程系列中所提供的程式碼可如預期般運作。

5. 在 [New ASP.NET Project] 對話方塊中，選取 [Web Forms] 範本。取消核取 [**雲端中的主機**] 核取方塊 (若已選取)，然後按一下 [**確定**]。![[New ASP.NET Project] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms03.png) Web Forms 應用程式將隨即建立。
###更新主要頁面
在 ASP.NET Web Forms 中，主要頁面可用來建立應用程式中頁面的一致性版面配置。單一主要頁面可為應用程式中的所有頁面 (或頁面群組) 定義您想要的外觀與風格及標準行為。接著，您可以建立包含所需顯示內容的個別內容頁面。當使用者要求內容頁面時，ASP.NET 會將這些頁面與主要頁面合併，以產生結合主要頁面之配置與內容頁面之內容的輸出。新的網站需要更新的應用程式名稱與連結。此連結將指向顯示連絡人詳細資料的頁面。若要進行這些變更，您需修改主要頁面上的 HTML。

1. 在 [方案總管] 中，尋找並開啟 *Site.Master* 頁面。
2. 如果頁面處於 [設計] 檢視模式，請切換至 [來源] 檢視。
3. 您可透過修改或新增標記 (以黃色強調顯示) 來更新主要頁面：

<pre class="prettyprint">
&lt;%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

&lt;!DOCTYPE html>

&lt;html lang="en">
&lt;head runat="server">
    &lt;meta charset="utf-8" />
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0" />
    &lt;title>&lt;%: Page.Title %> - <mark>Contact Manager</mark>&lt;/title>

    &lt;asp:PlaceHolder runat="server">
        &lt;%: Scripts.Render("~/bundles/modernizr") %>
    &lt;/asp:PlaceHolder>
    &lt;webopt:bundlereference runat="server" path="~/Content/css" />
    &lt;link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

&lt;/head>
&lt;body>
    &lt;form runat="server">
        &lt;asp:ScriptManager runat="server">
            &lt;Scripts>
                &lt;%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                &lt;%--Framework Scripts--%>
                &lt;asp:ScriptReference Name="MsAjaxBundle" />
                &lt;asp:ScriptReference Name="jquery" />
                &lt;asp:ScriptReference Name="bootstrap" />
                &lt;asp:ScriptReference Name="respond" />
                &lt;asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                &lt;asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                &lt;asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                &lt;asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                &lt;asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                &lt;asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                &lt;asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                &lt;asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                &lt;asp:ScriptReference Name="WebFormsBundle" />
                &lt;%--Site Scripts--%>
            &lt;/Scripts>
        &lt;/asp:ScriptManager>

        &lt;div class="navbar navbar-inverse navbar-fixed-top">
            &lt;div class="container">
                &lt;div class="navbar-header">
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                    &lt;/button>
                    &lt;a class="navbar-brand" runat="server" <mark>id="ContactDemoLink"</mark> href="~/<mark>Contacts/Default.aspx</mark>"><mark>Contact Demo</mark>&lt;/a>
                &lt;/div>
                &lt;div class="navbar-collapse collapse">
                    &lt;ul class="nav navbar-nav">
                        &lt;li>&lt;a runat="server" href="~/">Home&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/About">About&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/Contact">Contact&lt;/a>&lt;/li>
                    &lt;/ul>
                    &lt;asp:LoginView runat="server" ViewStateMode="Disabled">
                        &lt;AnonymousTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Register">Register&lt;/a>&lt;/li>
                                &lt;li>&lt;a runat="server" href="~/Account/Login">Log in&lt;/a>&lt;/li>
                            &lt;/ul>
                        &lt;/AnonymousTemplate>
                        &lt;LoggedInTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Manage" title="Manage your account">Hello, &lt;%: Context.User.Identity.GetUserName()  %> !&lt;/a>&lt;/li>
                                &lt;li>
                                    &lt;asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                &lt;/li>
                            &lt;/ul>
                        &lt;/LoggedInTemplate>
                    &lt;/asp:LoginView>
                &lt;/div>
            &lt;/div>
        &lt;/div>
        &lt;div class="container body-content">
            &lt;asp:ContentPlaceHolder ID="MainContent" runat="server">
            &lt;/asp:ContentPlaceHolder>
            &lt;hr />
            &lt;footer>
                &lt;p>&amp;copy; &lt;%: DateTime.Now.Year %> - <mark>Contact Manager</mark>&lt;/p>
            &lt;/footer>
        &lt;/div>
    &lt;/form>
&lt;/body>
&lt;/html>
</pre>

後續在本教學課程中，您將新增 Web Forms 樣板。Scaffolding 將建立上述 “Contact Demo” 連結的參考頁面。
###在本機執行應用程式 
1. 在 [方案總管] 中，以滑鼠右鍵按一下 *Default.aspx* 頁面並選取 [設定為起始頁]。 
2. 接著，按下 **CTRL+F5** 執行應用程式。預設的瀏覽器視窗中會出現應用程式預設頁面。![連絡人 - 建立新網站](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms04.png)  

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。稍後，您將新增資料庫功能，以及可顯示與編輯連絡人資料的必要頁面。
###將應用程式部署至 Azure
現在您已在本機建立與執行您的應用程式，是將應用程式部署至 Azure 的時候了。

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。![選取 [發佈]](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms05.png) [**發佈 Web**] 對話方塊隨即出現。  

2. 在 [**發佈 Web**] 對話方塊的 [**設定檔**] 索引標籤上，按一下 [**Azure Web 應用程式**]。![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms06.png)
3. 如果您尚未登入，請按一下 [**選取現有 Web 應用程式**] 對話方塊中的 [**登入**] 按鈕。完成登入後，請選取您在本教學課程第一個部分中所建立的 Web 應用程式。按一下 [確定] 以繼續。![選取 [現有網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms07.png) Visual Studio 將會下載您的發佈設定。
4. 在 [發行 Web] 對話方塊中，按一下 [發行]。![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms08.png) 您會在 Visual Studio 的 [**Web 發佈活動**] 視窗中，看到整體的發佈狀態：![Web 發佈活動](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms09.png)  

您建立的應用程式現在正在雲端中執行。下次當您從 Visual Studio 部署應用程式時，只會部署變更的 (或新的) 檔案。![App in Browser](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms10.png)

>[AZURE.NOTE]如果您在發佈到已建立的 Web 應用程式時遇到錯誤，則您可以在加入新檔案之前先清除位置。重新發佈您的應用程式，但在 [**發佈 Web**] 對話方塊中，請選取 [**設定**] 索引標籤。接著，設定 [偵錯] 的組態，並選取 [Remove additional files at destination] 選項。選取 [**發佈**] 以部署您的應用程式。![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms11.png)

##新增資料庫至應用程式 
接下來，您將更新 Web Forms 應用程式，在預設資料庫中新增顯示與更新連絡人，以及儲存資料的功能。建立 Web Forms 專案時，預設也會建立資料庫。應用程式將使用 Entity Framework，以存取資料庫以及讀取和更新資料庫中的資料。
###新增資料模型類別 
首先，使用程式碼建立簡單的資料模型。此資料模型會包含在名為 `Contacts` 的類別內。`Contacts` 類別名稱，是為了避免與根據 Web Forms 範本建立的 Contact.aspx.cs 檔案中所包含的 `Contact` 類別衝突。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 *Models* 資料夾，然後依序選取 [**新增**] -> [**類別**]。![選取類別](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms12.png) [**加入新項目**] 對話方塊隨即出現。  

2. 將這個新類別命名為 *Contacts.cs*。![[加入新項目] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13.png)
3. 使用下列程式碼來取代預設程式碼：  
	<pre class="prettyprint">
using System.ComponentModel.DataAnnotations;
using System.Globalization;

namespace ContactManager.Models
{
    public class Contacts
    {
        [ScaffoldColumn(false)]
        [Key]
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
</pre>

**Contacts** 類別定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 (`ContactID`) 。[連絡人] 類別代表將會顯示的連絡人資料。每個連絡人物件的執行個體會對應到關聯式資料庫資料表中的資料列，而每個連絡人類別的屬性會對應到關聯式資料庫資料表中的資料欄。後續在本教學課程中，您將檢閱資料庫中所包含的連絡人資料。

###Web Forms Scaffolding 
您已建立上述的 [連絡人] 模型類別。現在，您可以使用 Web Forms Scaffolder 來產生 *List*、*Insert*、*Edit* 和 *Delete* 頁面，以供使用此資料時使用。Web Forms Scaffolder 使用 Entity Framework、Bootstrap 和動態資料。依預設，使用 Visual Studio 2013 時，Web Forms Scaffolder 會以擴充功能的形式安裝在您的專案上，以作為專案範本的一部分。

您可透過下列步驟來使用 Web Forms Scaffolder。

1. 在 Visual Studio 中，從功能表列依序選取 [**工具**] -> [**擴充功能和更新**]。[**擴充功能和更新**] 對話方塊隨即出現。
2. 在此對話方塊的左窗格中，依序選取 [線上] -> [Visual Studio 組件庫] -> [工具] -> [Scaffolding]。
3. 如果您在清單中看不到 'Web Forms Scaffolding'，請在對話方塊右側的搜尋方塊中輸入 'Web Forms Scaffolding'。  
4. 如果未安裝 Web Forms Scaffolder，請選取 [下載] 以下載並安裝 Web Forms Scaffolding。如有需要，請重新啟動 Visual Studio。系統提出要求時，請務必將變更儲存至專案。![[擴充功能和更新] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/ExtensionsAndUpdatesDB.png)  
5. 建置專案 (**Ctrl+Shift+B**)。使用樣板機制前必須先建置專案。  
6. 在 [**方案總管**] 中，以滑鼠右鍵按一下 [*專案*]，然後依序選取 **[新增**] -> [**新增建構項目**]。[**新增 Scaffold**] 對話方塊隨即出現。
7. 從左窗格中選取 [Web Forms]，並從中央窗格中選取 [Web Forms Pages using Entity Framework]。然後按一下 [新增]。![[新增 Scaffold] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13a.png) [**新增 Web Forms 頁面**] 對話方塊隨即出現。  

8. 在 [**新增 Web Forms 頁面**] 對話方塊中，將 [**模型類別**] 設為 `Contact (ContactManager.Models)`。將 [**資料內容類別**] 設為 `ApplicationDbContext (ContactManager.Models)`。然後按一下 [**新增**]。![[新增 Web Forms 頁面] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13b.png)

Web Forms Scaffolder 便會新增一個包含 *Default.aspx*、*Delete.aspx*、*Edit.aspx* 和 *Insert.aspx* 頁面的資料夾。Web Forms Scaffolder 還會建立一個包含 *EntityTemplates* 資料夾和 *FieldTemplates* 資料夾的 *DynamicData* 資料夾。`ApplicationDbContext` 同時用於成員資格資料庫和連絡人資料。

###設定應用程式以使用資料模型 
下一個工作是啟用 Code First 移轉功能，以便根據建立的資料模型建立資料庫。另外，您將新增範例資料和資料初始設定式。

1. 在 [**工具**] 功能表中，依序選取 [**NuGet 封裝管理員**] 和 [**Package Manager Console**]。![[新增 Web Forms 頁面] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13c.png)  
2. 在 [Package Manager Console] 視窗中，輸入下列命令：  
	<pre class="prettyprint">
enable-migrations
</pre>此 enable-migrations 命令會建立 *Migrations* 資料夾，並在該資料夾中放入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定資料移轉。  
3. 在 [Package Manager Console] 視窗中，輸入下列命令：  
	<pre class="prettyprint">
add-migration Initial
</pre>`add-migration Initial` 命令會在建立資料庫的 *Migrations* 資料夾中產生一個名為 <date_stamp>Initial 的檔案。第一個參數 ( Initial ) 是任意的，可用來建立檔案的名稱。您可以在 [方案總管] 中看到新的類別檔案。在 `Initial` 類別中，`Up` 方法會建立 `Contact` 資料表，`Down` 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。  
4. 開啟 *Migrations\Configuration.cs* 檔案。 
5. 新增下列命名空間：  
	<pre class="prettyprint">
using ContactManager.Models;
</pre>
6. 以下列程式碼取代 `Seed` 方法：  
	<pre class="prettyprint">
protected override void Seed(ContactManager.Models.ApplicationDbContext context)
{
    context.Contacts.AddOrUpdate(p => p.Name,
       new Contacts
       {
           ContactId = 1,
           Name = "Ivan Irons",
           Address = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           Zip = "10999",
           Email = "ivani@wideworldimporters.com",
       },
       new Contacts
        {
            ContactId = 2,
            Name = "Brent Scholl",
            Address = "5678 1st Ave W",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "brents@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 3,
            Name = "Terrell Bettis",
            Address = "9012 State St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "terrellb@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 4,
            Name = "Jo Cooper",
            Address = "3456 Maple St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "joc@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 5,
            Name = "Ines Burnett",
            Address = "7890 2nd Ave E",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "inesb@wideworldimporters.com",
        }
        );
}
</pre>
此程式碼會以連絡人資訊初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。  
7. 在 [Package Manager Console] 中輸入命令：  
	<pre class="prettyprint">
update-database
</pre>
`update-database` 會執行第一次移轉，並建立資料庫。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。![封裝管理員主控台](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13d.png)

###在本機執行應用程式並顯示資料 
現在就執行應用程式，了解如何檢視連絡人。

1. 首先，建置專案 (**Ctrl+Shift+B**)。  
2. 按 **CTRL+F5** 執行應用程式。瀏覽器便會開啟並顯示 *Default.aspx* 頁面。
3. 選取頁面頂端的 [**Contact Demo**] 連結，以顯示 [*連絡人清單*] 頁面。![[連絡人清單] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms17.png)  

##對專案啟用 SSL 
安全通訊端層 (SSL) 是一種定義的通訊協定，允許 Web 伺服器和 Web 用戶端透過加密，以更安全的方式進行通訊。未使用 SSL 時，在用戶端和伺服器之間傳送的資料會開放給任何可實體存取網路的人員進行封包探查。此外，數種常見驗證結構描述在一般的 HTTP 上並不是很安全。尤其是，基本驗證和表單驗證會傳送未加密的認證。為了安全的理由，這些驗證結構描述必須使用 SSL。

1. 在 [方案總管] 中，按一下 [ContactManager] 專案，再按 **F4** 鍵以顯示 [屬性] 視窗。 
2. 將 [**SSL 已啟用**] 變更為 `true`。 
3. 複製 **SSL URL**，以便稍後使用。除非您先前已建立 SSL Web 應用程式，否則 SSL URL 將是 `https://localhost:44300/` (如下所示)。![專案屬性](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms18.png)  
4. 在 [方案總管] 中，以滑鼠右鍵按一下 [ContactManager] 專案，再按一下 [屬性]。
5. 在左側索引標籤中按一下 [Web]。
6. 將 [**專案 URL**] 變更為使用您先前儲存的 **SSL URL**。![專案 Web 屬性](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms19.png)  
7. 按 **CTRL+S** 儲存頁面。
8. 按 **CTRL+F5** 執行應用程式。Visual Studio 將會顯示可避開 SSL 警告的選項。  
9. 按一下 [**是**] 以信任 IIS Express SSL 憑證並繼續。![IIS Express SSL 憑證資訊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms20.png) 隨即顯示一則安全性警告。  

10. 按一下 [**是**] 將憑證安裝到您的 localhost。![[安全性警告] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21.png) 瀏覽器視窗隨即出現。

您可以輕鬆地使用 SSL 在本機測試 Web 應用程式。



##新增 OAuth 2.0 提供者 
ASP.NET Web Forms 提供成員資格和驗證的增強功能選項。這些增強功能包括 OAuth。OAuth 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 `canEdit` 角色的使用者才能建立、編輯或刪除連絡人。

下列步驟可新增 Google 驗證提供者。

1. 開啟 *App_Start\Startup.Auth.cs* 檔案。 
2. 移除 `app.UseGoogleAuthentication()` 方法中的註解字元，然後此方法會顯示如下：  
	<pre class="prettyprint">
            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "",
                ClientSecret = ""
            });
</pre>
3. 瀏覽至 [Google Developers Console](https://console.developers.google.com/)。您還需要使用您的 Google 開發人員電子郵件帳戶 (gmail.com) 登入。如果您沒有 Google 帳戶，請選取 [**建立帳戶**] 連結。接下來，您會看到 [**Google 開發人員主控台**]。![Google 開發人員主控台](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21a.png)  

4. 按一下 [建立專案] 按鈕，並輸入專案名稱和識別碼 (您可以使用預設值)。然後依序按一下 [**協議核取方塊**] 和 [**建立**] 按鈕。![Google - 新增專案](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21b.png) 幾秒鐘內即可建立新的專案，您的瀏覽器便會顯示新的專案頁面。
5. 在左側索引標籤中，按一下 [APIs & auth]，然後按一下 [認證]。
6. 按一下 **OAuth** 下的 [**建立新的用戶端識別碼**]。[**建立用戶端識別碼**] 對話方塊隨即出現。![Google - 建立用戶端識別碼](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21c.png)  
7. 在 [**建立新的用戶端識別碼**] 對話方塊中，保留應用程式類型中預設的 [**Web 應用程式**]。  
8. 將 [**授權 JavaScript 來源**] 設定為您稍早在本教學課程中使用的 SSL URL (**除非您已建立其他 SSL 專案，否則會是 https://localhost:44300/** )。此 URL 會是應用程式的原始來源。在此範例中，您將僅輸入 localhost 測試 URL。不過，您可以在帳戶中輸入多個 URL 以供 localhost 和生產使用。  

9. 將 [Authorized Redirect URI] 設定如下：
	<pre class="prettyprint">  
https://localhost:44300/signin-google  
</pre>此值是 ASP.NET OAuth 使用者與 Google OAuth 伺服器進行通訊的 URI。請記住您在上面使用的 SSL URL (**https://localhost:44300/**，除非您已建立其他 SSL 專案)。  
10. 按一下 [Create Client ID] 按鈕。
11. 在 Visual Studio 中，將 [AppId] 和 [應用程式機密] 複製並貼入 `UseGoogleAuthentication` 方法，以便更新位於 *Startup.Auth.cs* 頁面的此方法。以下所示的 [AppId] 和 [應用程式機密] 值僅作為範例，所以不會有作用。  
	<pre class="prettyprint">  
using System;
using Microsoft.AspNet.Identity;
using Microsoft.AspNet.Identity.EntityFramework;
using Microsoft.AspNet.Identity.Owin;
using Microsoft.Owin;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.DataProtection;
using Microsoft.Owin.Security.Google;
using Owin;
using ContactManager.Models;

namespace ContactManager
{
    public partial class Startup {

        // 如需設定驗證的詳細資訊，請瀏覽 http://go.microsoft.com/fwlink/?LinkId=301883
        public void ConfigureAuth(IAppBuilder app)
        {
            // 設定資料庫內容和使用者管理員，以針對各要求使用單一執行個體
            app.CreatePerOwinContext(ApplicationDbContext.Create);
            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

            // 讓應用程式使用 Cookie 儲存已登入使用者的資訊
            // 並使用 Cookie 暫時儲存以協力廠商登入提供者登入之使用者的相關資訊
            // 設定登入 Cookie
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                LoginPath = new PathString("/Account/Login"),
                Provider = new CookieAuthenticationProvider
                {
                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity&lt;ApplicationUserManager, ApplicationUser>(
                        validateInterval: TimeSpan.FromMinutes(20),
                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                }
            });
            // 使用 Cookie 暫時儲存以協力廠商登入提供者登入之使用者的相關資訊
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            // 取消下列幾行註解，以啟用使用協力廠商登入提供者登入
            //app.UseMicrosoftAccountAuthentication(
            //    clientId: "",
            //    clientSecret: "");

            //app.UseTwitterAuthentication(
            //   consumerKey: "",
            //   consumerSecret: "");

            //app.UseFacebookAuthentication(
            //   appId: "",
            //   appSecret: "");

            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
                ClientSecret = "<mark>00000000000</mark>"
            });
        }
    }
}
</pre>
12. 按 **CTRL+F5** 以建置並執行應用程式。按一下 [登入] 連結。
13. 在 [**使用其他伺服器登入**] 下，按一下 [**Google**]。![登入](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21d.png)  
14. 如果您需要輸入認證，您會被重新導向至 Google 網站，您可以在此輸入認證。![Google - 登入](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21e.png)  
15. 輸入認證後，系統便會提示您提供權限給剛建立的 Web 應用程式：![專案預設服務帳戶](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21f.png)  
16. 按一下 [接受]。系統會將您重新導向回到 **ContactManager** 應用程式的 [**註冊**] 頁面，您可以在此註冊 Google 帳戶。![以您的 Google 帳戶註冊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21g.png)  
17. 您可以選擇變更用於 Gmail 帳戶的本機電子郵件註冊名稱，但您通常會想保留預設電子郵件別名 (也就是，您用來驗證的名稱)。按一下 [登入]。

##使用成員資格 API 來限制存取 
ASP.NET 身分識別是成員資格系統，可用於建置 ASP.NET Web 應用程式時的驗證。它會讓整合使用者特定設定檔資料與應用程式資料變得再容易不過了。另外，ASP.NET 身分識別可讓您選擇應用程式中使用者設定檔的持續性模型。您可以將資料儲存在 SQL Server 資料庫或其他資料存放區中，包括 *NoSQL* 資料存放區 (例如 Azure 儲存體資料表)。

使用預設的 ASP.NET Web Forms 範本，您便擁有內建的成員資格功能，可在應用程式執行時立即使用。您將使用 ASP.NET 身分識別新增系統管理員角色，並對該角色指派使用者。接著，您將了解如何限制存取管理資料夾，以及該資料夾中用來修改連絡人資料的頁面。

###新增系統管理員 
使用 ASP.NET 身分識別，您可以新增系統管理員角色，並使用程式碼對該角色指派使用者。

1. 在 [方案總管] 中，開啟 *Migrations* 資料夾中的 *Configuration.cs* 檔案。
2. 在 `ContactManger.Migrations` 命名空間中新增以下 `using` 陳述式：  
	<pre class="prettyprint">
using Microsoft.AspNet.Identity;
using Microsoft.AspNet.Identity.EntityFramework;
</pre>
3. 將下列 `AddUserAndRole` 方法新增至 `Seed` 方法之後的 `Configuration` 類別：  
	<pre class="prettyprint">
    public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
    {
        IdentityResult IdRoleResult;
        IdentityResult IdUserResult;

        var roleStore = new RoleStore&lt;IdentityRole>(context);
        var roleMgr = new RoleManager&lt;IdentityRole>(roleStore);

        if (!roleMgr.RoleExists("canEdit"))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = "canEdit" });
        }

        //var userStore = new UserStore&lt;ApplicationUser>(context);
        //var userMgr = new UserManager&lt;ApplicationUser>(userStore);
        var userMgr = new UserManager&lt;ApplicationUser>(new UserStore&lt;ApplicationUser>(context));

        var appUser = new ApplicationUser
        {
            UserName = "canEditUser@wideworldimporters.com",
            Email = "canEditUser@wideworldimporters.com"
        };
        IdUserResult = userMgr.Create(appUser, "Pa$$word1");

        if (!userMgr.IsInRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit"))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, "canEdit");
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit");
        }
    }
</pre>
4. 從 `Seed` 方法開頭新增呼叫至 `AddUserAndRole` 方法。請注意，這裡只顯示 `Seed` 的開頭。  
	<pre class="prettyprint">
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
</pre>
5. 儲存所有變更後，請在 [Package Manager Console] 中執行下列命令：  
	<pre class="prettyprint">
Update-Database
</pre>此程式碼會建立名為 `canEdit` 的新角色，並建立電子郵件為 canEditUser@wideworldimporters.com 的新本機使用者。然後，此程式碼則會將 canEditUser@wideworldimporters.com 新增至 `canEdit` 角色。如需詳細資訊，請參閱 [ASP.NET 身分識別](http://www.asp.net/identity)資源頁面 (英文)。

###限制對管理資料夾的存取 
[ContactManager] 範例應用程式允許匿名使用者和登入使用者檢視連絡人。不過，完成此區段之後，指派給 "canEdit" 角色的已登入使用者將會是唯一能夠修改連絡人的使用者。

您將建立名為 *Admin* 的資料夾，只有指派給 "canEdit" 角色的使用者才能存取該資料夾。

1. 在 [方案總管] 中，在 *Contacts* 資料夾中新增子資料夾，並將此新子資料夾命名為 *Admin*。
2. 將下列檔案從 *Contacts* 資料夾移至 *Contacts/Admin* 資料夾：  
	- *Delete.aspx *和* Delete.aspx.cs*
	- *Edit.aspx *和* Edit.aspx.cs*
	- *Insert.aspx *和* Insert.aspx.cs*
3. 更新 *Contacts/Default.aspx* 中的連結參考，方法是在連結 *Insert.aspx*、*Edit.aspx* 和 *Delete.aspx* 的頁面參考前面加上 "Admin/"：  
	<pre class="prettyprint">
&lt;%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %>
&lt;%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>

&lt;asp:Content runat="server" ContentPlaceHolderID="MainContent">
    &lt;h2>Contacts List&lt;/h2>
    &lt;p>
        &lt;asp:HyperLink runat="server" NavigateUrl="<mark>Admin/</mark>Insert.aspx" Text="Create new" />
    &lt;/p>
    &lt;div>
        &lt;asp:ListView runat="server"
            DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts"
            AutoGenerateColumns="false"
            AllowPaging="true" AllowSorting="true"
            SelectMethod="GetData">
            &lt;EmptyDataTemplate>
                There are no entries found for Contacts
            &lt;/EmptyDataTemplate>
            &lt;LayoutTemplate>
                &lt;table class="table">
                    &lt;thead>
                        &lt;tr>
                            &lt;th>Name&lt;/th>
                            &lt;th>Address&lt;/th>
                            &lt;th>City&lt;/th>
                            &lt;th>State&lt;/th>
                            &lt;th>Zip&lt;/th>
                            &lt;th>Email&lt;/th>
                            &lt;th>&amp;nbsp;&lt;/th>
                        &lt;/tr>
                    &lt;/thead>
                    &lt;tbody>
                        &lt;tr runat="server" id="itemPlaceholder" />
                    &lt;/tbody>
                &lt;/table>
            &lt;/LayoutTemplate>
            &lt;ItemTemplate>
                &lt;tr>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;a href="<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%>">Edit&lt;/a> | 
                        &lt;a href="<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%>">Delete&lt;/a>
                    &lt;/td>
                &lt;/tr>
            &lt;/ItemTemplate>
        &lt;/asp:ListView>
    &lt;/div>
&lt;/asp:Content>
</pre>
4. 在下列三個檔案中，將 `Response.Redirect("Default.aspx")` 程式碼的六個參考更新到 `Response.Redirect("~/Contacts/Default.aspx")`：  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	現在，當您顯示與更新連絡人資料時，這些連結皆可正常運作。
5. 若要限制對 *Admin* 資料夾的存取，請在 [方案總管] 中以滑鼠右鍵按一下 *Admin* 資料夾，並選取 [新增項目]。
6. 從 Visual C# Web 範本清單中，選取中間清單中的 [Web 組態檔]，接受預設名稱為 *Web.config*，然後選取 [新增]。
7. 使用下列內容來取代 *Web.config* 檔案中的現有 XML 內容：
	<pre class="prettyprint">
&lt;?xml version="1.0"?>
&lt;configuration>
  &lt;system.web>
    &lt;authorization>
      &lt;allow roles="canEdit"/>
      &lt;deny users="*"/>
    &lt;/authorization>
  &lt;/system.web>
&lt;/configuration>
</pre>
8. 儲存 *Web.config* 檔案。*Web.config* 檔案指定只有指派給「canEdit」角色的使用者可以存取 *Admin* 資料夾中所包含的頁面。 

當不屬於 "canEdit" 角色的使用者嘗試修改資料時，他們會被重新導向至 *Log in* 頁面。

##將使用資料庫的應用程式部署至 Azure 
現在，Web 應用程式已完成，您可以將它發行至 Azure。

###發行應用程式 
1. 在 Visual Studio 中，建置專案 (**Ctrl+Shift+B**)。
2. 在 [方案總管] 中以滑鼠右鍵按一下專案，再選取 [發行]。![發佈功能表選項](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22.png) [**發佈 Web**] 對話方塊隨即出現。![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22a.png)  
3. 在 [**設定檔**] 索引標籤中，選取 [**Azure Web應用程式**] 做為發佈目標 (如果尚未選取)。![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms23.png)  
4. 如果您尚未登入，請按一下 [登入]。
5. 從 [**現有 Web Apps**] 下拉式方塊中，選取您稍早在本教學課程中建立的現有 Web 應用程式，然後按一下 [**確定**] 按鈕。![選取 [現有網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms25.png) 如果系統詢問您是否要在設定檔中儲存所做的變更，請選取 [**是**]。
6. 按一下 [**設定**] 索引標籤。![選取 [現有網站] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26.png)  
7. 將 [組態] 下拉式方塊設為 [偵錯]。
8. 按一下 [ApplicationDbContext] 旁邊的 [向下箭頭] 圖示，並將它設為 [ContactDB]。
9. 勾選 [Execute Code First Migrations] 核取方塊。  

	>[AZURE.NOTE]在本範例中，您應只在首次發佈應用程式時選取此核取方塊。如此一來，將只呼叫一次 *Configuration.cs* 檔案中的 *Seed* 方法。

10. 然後按一下 [**發佈**]。您的應用程式便會發佈到 Azure。

>[AZURE.NOTE]若在建立發佈設定檔後關閉再重新開啟 Visual Studio，可能無法在下拉式清單中看到連接字串。在此情況下，請不要編輯稍早建立的發行設定檔，而是依照稍早的方式建立一個新設定檔，然後在 [設定] 索引標籤上執行這些步驟)。

###在 Azure 中檢閱應用程式 
1. 在瀏覽器中，按一下 [**Contact Demo**] 連結。[連絡人清單] 便會隨即顯示。![瀏覽器中列出的連絡人](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms27.png)  

2. 選取 [**連絡人清單**] 頁面上的 [**建立新的**]。![瀏覽器中列出的連絡人](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms29.png) 您會被重新導向至 [**登入**] 頁面，這是因為您尚未使用可修改連絡人的帳戶登入。
3. 在輸入以下的電子郵件和密碼之後，請按一下 [**登入**] 按鈕。**電子郵件**：`canEditUser@wideworldimporters.com` **密碼**：`Pa$$word1` ![Log in Page](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms28.png)  

4. 為每個欄位輸入新的資料，然後按下 [**插入**] 按鈕。![[新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms30.png) *EditContactList.aspx* 頁面會顯示新的記錄。![[新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms31.png)

5. 選取 [登出] 連結。

###停止應用程式 
為了防止其他人註冊並使用您的範例應用程式，您將停止 Web 應用程式。

1. 在 Visual Studio 的 [檢視功能表] 中，選取 [伺服器總管]。 
2. 在 [**伺服器總管**] 中，導覽到 [**Web 應用程式**]。
3. 在每個 Web 應用程式執行個體上按一下滑鼠右鍵，再選取 [**停止 Web 應用程式**]。![停止網站功能表項目](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26a.png)  

	或者，您也可以從 Microsoft Azure 管理入口網站選取 Web 應用程式，然後按一下頁面底部的 [ **停止**] 圖示。![[新增新連絡人] 頁面](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26b.png)

##檢閱資料庫 
了解如何直接檢視與修改資料庫十分重要。了解如何直接與資料庫合作，可讓您確認資料庫中的資料，並且了解如何在每個資料表中儲存資料。

###檢查 SQL Azure DB 
1. 在 Visual Studio 中，開啟 [伺服器總管] 並瀏覽至 [ContactDB]。
2. 在 [**ContactDB**] 上按一下滑鼠右鍵，再選取 [**在 SQL Server 物件總管中開啟**]。![在 [SQL Server 物件總管] 功能表項目中開啟](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms32.png)  
3. 如果 [Add Firewall Rule] 對話方塊隨即顯示，請選取 [Add Firewall Rule]。  

	>[AZURE.NOTE]若您無法展開 [**SQL Database**]，並且在 Visual Studio 中看不到 [**ContactDB**]，您可以依照指示開放一個防火牆連接埠或某個範圍的連接埠。若要這樣做，請在 **MVC 教學課程**快結束時，依照 [Set up Azure firewall rules][](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) 下的指示執行。做為替代的方法，您也可以透過將資料建置、執行及新增至本機的應用程式，以檢閱本機資料庫的資料 (在 Visual Studio 中按 **CTRL+F5**)。

4. 如果 [連線到伺服器] 對話方塊隨即顯示，請輸入您在本教學課程一開始時建立的 [密碼]，然後按 [連線] 按鈕。

	>[AZURE.NOTE]**方案總管***屬性**PublishProfiles**contactmanager.pubxml*
5. 展開 [contactDB] 資料庫，然後展開 [資料表]。
6. 以滑鼠右鍵按一下 **dbo.AspNetUsers** 資料表，然後選取 [**檢視資料**]。![檢視 [資料] 功能表項目](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms34.png) 您可以檢視與 canEditUser@contoso.com 使用者相關的資料。![[ContactManager] 視窗](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms35.png)  

###透過編輯資料庫將使用者新增至管理角色 
在本教學課程稍早的章節中，您使用程式碼將使用者新增至 canEdit 角色。替代的方法是直接在成員資格資料表中操作資料。下列步驟顯示如何使用此替代方法，將使用者新增至角色。

1. 在 [**SQL Server 物件總管**] 中，以滑鼠右鍵按一下 **dbo.AspNetUserRoles** 並選取 [**檢視資料**]。![[AspNetUserRoles] 資料](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms36.png)  
2. 複製 *RoleId* 並將其貼至空白 (新) 資料列。![[AspNetUserRoles] 資料](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms37.png)  
3. 在 [dbo.AspNetUsers] 資料表中，尋找您要放到角色中的使用者，並複製使用者的 *Id*。
4. 將複製的 *Id* 貼入 [AspNetUserRoles] 資料表中新資料列的 [UserId] 欄位。  

>[AZURE.NOTE]我們正在開發一項工具，讓使用者和角色管理起來簡單得多。

##後續步驟
如需 ASP.NET Web Forms 的詳細資訊，請參閱 ASP.NET Web 應用程式上的[了解 ASP.NET Web Forms](http://www.asp.net/web-forms) 和 [Microsoft Azure 教學課程和指南](http://azure.microsoft.com/documentation/services/web-sites/#net)。

本教學課程會以此 MVC 教學課程為基礎：Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) 主筆，Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 協助創作的[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。

如果您發現喜歡的地方和希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，歡迎留下意見反應。您的意見反應將協助我們訂出優先改善要務。您也可以在[告訴我如何處理程式碼](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (英文) 提出並對新主題進行投票。

 

<!---HONumber=62-->