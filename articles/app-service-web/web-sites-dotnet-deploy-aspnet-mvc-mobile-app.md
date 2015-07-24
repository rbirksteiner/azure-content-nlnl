<properties 
	pageTitle="在 Azure App Service 中部署 ASP.NET MVC 5 行動 Web 應用程式" 
	description="指導您如何使用 ASP.NET MVC 5 Web 應用程式的行動功能，將 Web 應用程式部署到 Azure App Service 的教學課程。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="cephalin;riande"/>


# 在 Azure App Service 中部署 ASP.NET MVC 5 行動 Web 應用程式

本教學課程指導您如何建置行動便利的 ASP.NET MVC 5 Web 應用程式，並將其部署至 Azure App Service 的基本做法。在本教學課程中，您需要 [Visual Studio Express 2013 for Web][Visual Studio Express 2013] 或 Professional Edition 的 Visual Studio (如果您已具備此版本)。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 您要建置的內容

在本教學課程中，您將把行動功能新增至[入門專案][StarterProject]裡提供的簡單會議清單應用程式。如同在 Internet Explorer 11 F12 開發人員工具的瀏覽器模擬器中所見，以下螢幕擷取畫面示範完成的應用程式中的 ASP.NET 工作階段。

![][FixedSessionsByTag]

您可以利用 Internet Explorer 11 F12 開發人員工具和 [Fiddler 工具][Fiddler]來偵錯應用程式。

## 您要學習的技術

以下是您要學習的內容：

-	如何使用 Visual Studio 2013，將 Web 應用程式直接發行到 Azure App Service 中的 Web 應用程式。
-   ASP.NET MVC 5 範本如何使用 CSS Bootstrap 架構，改善行動裝置的顯示畫面。
-   如何以特定的行動瀏覽器做為目標，建立行動專用的檢視，例如 iPhone 和 Android。
-   如何建立回應靈敏的檢視 (可回應各種裝置中不同瀏覽器的檢視)。

## 設定開發環境

安裝 Azure SDK for .NET 2.5.1 或更新版本，以設定您的開發環境。

1. 若要安裝 Azure SDK for .NET，請按一下底下連結：如果您尚未安裝 Visual Studio 2013，按下該連結會進行安裝。本教學課程需要 Visual Studio 2013。[Azure SDK for Visual Studio 2013][AzureSDKVs2013]
1. 在 [Web Platform Installer] 視窗中，按一下 [安裝] 並繼續進行安裝。

您還需要一個行動瀏覽器模擬器。下列任一項目都可使用：

-   [Internet Explorer 11 F12 開發人員工具][EmulatorIE11]中的瀏覽器模擬器 (使用於所有行動瀏覽器螢幕擷取畫面中)。它具有 Windows Phone 8、Windows Phone 7 和 Apple iPad 的使用者代理程式字串預設項目。
-	[Google Chrome DevTools][EmulatorChrome] (英文) 中的瀏覽器模擬器。它包含許多 Android 裝置，以及 Apple iPhone、Apple iPad 和 Amazon Kindle Fire 的預設項目。它也會模擬觸控事件。
-   [Opera Mobile 模擬器][EmulatorOpera]

此處提供具有 C# 原始程式碼的 Visual Studio 專案來幫助您完成本主題：

-   [入門專案下載][StarterProject]
-   [完成專案下載][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>將入門專案部署至 Azure Web 應用程式

1.	下載會議清單應用程式[入門專案][StarterProject] (英文)。

2. 	接著在 Windows 檔案總管中，以滑鼠右鍵按一下 Mvc5Mobile.zip 檔案並選擇 [內容]。

3. 	在 [**Mvc5Mobile.zip 屬性**] 對話方塊中，選擇 [**解除封鎖**] 按鈕。(取消封鎖後，當您嘗試使用從網路下載的 *.zip* 檔案時，就不會出現安全性警告。)

4.	以滑鼠右鍵按一下 *Mvc5Mobile.zip* 檔案，然後選取 [**解壓縮全部**]，以解壓縮檔案。

5. 	在 Visual Studio 中，開啟 *Mvc5Mobile.sln* 檔案。

6.  在 [方案總管] 中，於專案上按一下滑鼠右鍵，再按一下 [發行]。

	![][DeployClickPublish]

7.	在 [發行 Web] 中，按一下 [**Microsoft Azure Web Apps**]。

	![][DeployClickWebSites]

8.	按一下 [登入]。

	![][DeploySignIn]

9.	依照提示來登入您的 Azure 帳戶。

11. [選取現有的 Web 應用程式] 對話方塊此時應顯示為已登入。按一下 [新增]。

	![][DeployNewWebsite]

12. 在 [**Web App 名稱**] 欄位中，指定唯一的 App 名稱前置詞。您的完整 Web 應用程式名稱將是 *&lt;prefix>*.azurewebsites.net。同時設定 Web 應用程式的 **App Service 方案**、**資源群組**，以及 [**區域**] 欄位。然後按一下 [建立]。

	![][DeploySiteSettings]

13.	隨即會將新 Web 應用程式的設定填入 [發行 Web] 對話方塊中。按一下 [發行]。

	![][DeployPublishSite]

	當 Visual Studio 完成將入門專案發行至 Azure Web 應用程式之後，隨即會開啟桌面瀏覽器以顯示作用中的 Web 應用程式。

14.	啟動行動瀏覽器模擬器，將會議應用程式的 URL (*<prefix>*.azurewebsites.net) 複製到模擬器中，然後按一下右上角的按鈕，並選取 [**依標籤瀏覽**]。若您使用 Internet Explorer 11 做為預設瀏覽器，只要輸入 `F12`，再按鍵 `Ctrl+8`，然後將瀏覽器設定檔變更為 **Windows Phone** 即可。下圖顯示直向模式的 *AllTags* 檢視 (透過選擇 [**依標籤瀏覽**])。

	![][AllTags]

>[AZURE.NOTE]當您在 Visual Studio 中偵錯 MVC 5 應用程式時，可以再次將 Web 應用程式發行至 Azure，直接從行動瀏覽器或瀏覽器模擬器確認作用中的 Web 應用程式。

該顯示內容在行動裝置上非常清楚易讀。您也可以看到一些 Bootstrap CSS 架構所套用的視覺效果。按一下 [**ASP.NET**] 連結。

![][SessionsByTagASP.NET]

ASP.NET 標籤檢視會縮放至適合螢幕的大小，而這是 Bootstrap 自動為您執行的效果。但您可以改善此檢視，使其更適合行動瀏覽器。例如，[**日期**] 欄非常難以閱讀。您將在稍後的教學課程中變更 *AllTags* 檢視，使其更適合行動用途。

##<a name="bkmk_bootstrap"></a> Bootstrap CSS 架構

MVC 5 範本中的新功能是內建的 Bootstrap 支援。您已經看到它是如何即時改善應用程式中的不同檢視。例如，頂端的導覽列會在瀏覽器寬度較小時自動摺疊。嘗試在桌面瀏覽器上重新調整瀏覽器視窗的大小，並觀察導覽列如何改變它的外觀與風格。這就是內建於 Bootstrap 中回應靈敏的 Web 設計。

若要查看 Web 應用程式沒有 Bootstrap 時的外觀，請開啟 *App_Start\BundleConfig.cs*，並註解化包含 *bootstrap.js* 和 *bootstrap.css* 的行。以下程式碼顯示在執行這些變更之後，`RegisterBundles` 方法的最後兩個陳述式：

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

按下 `Ctrl+F5` 執行應用程式。

您會發現可摺疊的導覽列現在只是一般的未排序清單。再按一下 [**依標籤瀏覽**]，然後按一下 [**ASP.NET**]。您現在可以在行動模擬器檢視中看到檢視已不再縮放至適合螢幕的大小，且您必須橫向捲動才能看到右邊的表格。

![][SessionsByTagASP.NETNoBootstrap]

復原這些變更，並重新整理行動瀏覽器，以確認適合行動的顯示畫面已還原。

Bootstrap 並非專屬於 ASP.NET MVC 5，您也可以在所有 Web 應用程式中利用這些功能。但它目前內建於 ASP.NET MVC 5 專案範本中，所以 MVC 5 Web 應用程式可根據預設利用 Bootstrap。

如需 Bootstrap 的詳細資訊，請移至 [Bootstrap][BootstrapSite] 網站。

您將在下一節看到如何提供行動瀏覽器專用的檢視。

##<a name="bkmk_overrideviews"></a> 覆寫檢視、配置與部分檢視

您可以覆寫大多數的行動瀏覽器、個別行動瀏覽器或任何特定瀏覽器的所有檢視，包含配置及部分檢視。若要提供行動裝置專屬的檢視，您可以複製檢視檔案並將 *.Mobile* 新增至檔案名稱。例如，若要建立行動 [*索引*] 檢視，您可以將 *Views\Home\Index.cshtml* 複製到 *Views\Home\Index.Mobile.cshtml*。

本節將建立行動裝置專屬的配置檔案。

一開始，請將 *Views\Shared_Layout.cshtml* 複製到 *Views\Shared_Layout.Mobile.cshtml*。開啟 *_Layout.Mobile.cshtml*，並將標題從 **MVC5 Application** 變更為 **MVC5 Application (Mobile)**。

在每個導覽列的 `Html.ActionLink` 呼叫當中，移除每個 *ActionLink* 連結中的「Browse by」。以下程式碼顯示行動配置檔案的已完成之 `<ul class="nav navbar-nav">` 標籤。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

將 *Views\Home\AllTags.cshtml* 檔案複製到 *Views\Home\AllTags.Mobile.cshtml*。開啟新檔案並將 `<h2>` 元素從 "Tags" 變更為 "Tags (M)"：

    <h2>Tags (M)</h2>

使用桌面瀏覽器及行動瀏覽器模擬器，瀏覽至標籤頁面。行動瀏覽器模擬器會顯示您執行的兩項變更 (*_Layout.Mobile.cshtml* 的標題及 *AllTags.Mobile.cshtml* 的標題)。

![][AllTagsMobile_LayoutMobile]

相較之下，桌面顯示則沒有變更 (*_Layout.cshtml* 和 *AllTags.cshtml* 的標題)。

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a> 建立瀏覽器專用的檢視

除了行動與桌面專用的檢視之外，您還可以為個別瀏覽器建立檢視。例如，您可以為 iPhone 或 Android 瀏覽器建立專用的檢視。在本節中，您要建立 iPhone 瀏覽器的配置，以及 iPhone 版的 *AllTags* 檢視。

開啟 *Global.asax* 檔案，並將以下程式碼加入 `Application_Start` 方法的底部。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

此程式碼會定義要比對每個連入要求且名為 "iPhone" 的新顯示模式。若連入的要求符合您定義的條件 (亦即使用者代理程式包含 "iPhone" 字串)，則 ASP.NET MVC 會尋找名稱包含 "iPhone" 字尾的檢視。

>[AZURE.NOTE] 新增行動瀏覽器專用的顯示模式時，例如 iPhone 和 Android，請務必將第一個引數設為 `0` (插入於清單的頂端)，才能確保瀏覽器的專用模式會優先於行動範本 (*.Mobile.cshtml)。若位於清單頂端的是行動範本，則會優先選取該行動範本，而不是您想要的顯示模式 (第一個相符的會成功，而行動範本符合所有行動瀏覽器)。

以滑鼠右鍵按一下程式碼的 `DefaultDisplayMode`，選擇 [**解析**]，然後選擇 `using System.Web.WebPages;`。這麼做會將參考加入定義 `DisplayModeProvider` 和 `DefaultDisplayMode` 類型的 `System.Web.WebPages` 命名空間。

![][ResolveDefaultDisplayMode]

或者，您可以手動將以下的行加入檔案的 `using` 區段即可。

    using System.Web.WebPages;

儲存變更。將 *Views\Shared_Layout.Mobile.cshtml* 檔案複製到 *Views\Shared_Layout.iPhone.cshtml*。開啟新檔案，然後將標題從 `MVC5 Application (Mobile)` 變更為 `MVC5 Application (iPhone)`。

將 *Views\Home\AllTags.Mobile.cshtml* 檔案複製到 *Views\Home\AllTags.iPhone.cshtml*。在新檔案中，將 `<h2>` 元素從「Tags (M)」變更為「Tags (iPhone)」。

執行應用程式。執行行動瀏覽器模擬器，請確認其使用者代理程式設為「iPhone」，然後瀏覽至 *AllTags* 檢視。若您使用 Internet Explorer 11 F12 開發人員工具中的模擬器，請將模擬設為以下內容：

-   瀏覽器設定檔 = **Windows Phone**
-   使用者代理程式字串 = **Custom**
-   自訂字串 = **Apple-iPhone5C1/1001.525**

以下螢幕擷取畫面顯示在 Internet Explorer 11 F12 開發人員工具的模擬器中，使用自訂使用者代理程式字串 (此為 iPhone 5C 使用者代理程式字串) 呈現的 *AllTags* 檢視。

![][AllTagsIPhone_LayoutIPhone]

在行動瀏覽器中，選取 [演講者] 連結。由於沒有行動檢視 (*AllSpeakers.Mobile.cshtml*)，預設的演講者檢視 (*AllSpeakers.cshtml*) 會透過行動配置檢視 (*_Layout.Mobile.cshtml*) 來呈現。如下所示，標題 **MVC5 Application(Mobile)** 定義於 *_Layout.Mobile.cshtml* 中。

![][AllSpeakers_LayoutMobile]

您可以在 *Views_ViewStart.cshtml* 檔案中，將 `RequireConsistentDisplayMode` 設為 `true` ，即可全域停用預設 (非行動) 檢視，使其無法在行動配置內呈現，如下所示：

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

當 `RequireConsistentDisplayMode` 設為 `true` 時，行動配置 (*_Layout.Mobile.cshtml*) 僅能用於行動檢視 (例如，檢視檔案的格式為 ***ViewName**.Mobile.cshtml*)。若行動配置不適用於您的非行動檢視，您可以將 `RequireConsistentDisplayMode` 設為 `true`。以下螢幕擷取畫面顯示當 `RequireConsistentDisplayMode` 設為 `true` 時，[*演講者*] 頁面的呈現方式 (頂端導覽列中沒有「(Mobile)」字串)。

![][AllSpeakers_LayoutMobileOverridden]

您可以在檢視檔案中將 `RequireConsistentDisplayMode` 設為 `false`，以停用特定檢視中的一致顯示模式。*Views\Home\AllSpeakers.cshtml* 檔案中的以下標記會將 `RequireConsistentDisplayMode` 設為 `false`：

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

在本節中，我們已了解如何建立行動配置和檢視，以及如何為特定裝置 (例如 iPhone) 建立配置和檢視。不過，Bootstrap CSS 架構的主要優點是回應靈敏的配置，這表示單一樣式表可以套用到桌面、電話和平板電腦瀏覽器中，並建立一致的外觀及操作。您將在下一節看到如何利用 Bootstrap 建立適合行動裝置的檢視。

##<a name="bkmk_Improvespeakerslist"></a> 改善演講者清單

如您適才所見，行動裝置上的 [*演講者*] 檢視已可讀取，但是連結卻非常微小而不容易點選。在本節中，您要使 *AllSpeakers* 檢視適合行動用途，以顯示大尺寸又容易點選的連結，並包含可快速找到演講者的搜尋方塊。

您可以使用 Bootstrap [連結清單群組][]樣式改善 [*演講者*] 檢視。在 *Views\Home\AllSpeakers.cshtml* 中，使用以下程式碼取代 Razor 檔案的內容。

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

`<div>` 標籤中的 `class="list-group"` 屬性會套用 Bootstrap 清單樣式，且 `class="input-group-item"` 屬性會將 Bootstrap 清單項目樣式套用至每個連結。

重新整理行動瀏覽器。更新的檢視如下所示：

![][AllSpeakersFixed]

Bootstrap [連結清單群組][] (英文) 樣式讓每個連結的整個方塊都可以點選，以提供更好的使用者體驗。切換成桌面檢視，會發現此檢視也有一致的外觀與風格。

![][AllSpeakersFixedDesktop]

雖然已經改善行動瀏覽器檢視，但要瀏覽冗長的演講者清單還是很不方便。Bootstrap 並沒有現成的搜尋篩選功能，但您可以用數行程式碼來新增此功能。首先要先將搜尋方塊新增至檢視，然後與 JavaScript 程式碼連結，以取得篩選功能。在 *Views\Home\AllSpeakers.cshtml* 中，在 \<h2\> 標籤後面加入 \<form\> 標籤，如下所示：

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

請注意，`<form>` 和 `<input>` 標籤都已套用 Bootstrap 樣式。`<span>` 元素會將 Bootstrap [[glyphicon][]] 新增至搜尋方塊。

在 *Scripts* 資料夾中，加入名為 *filter.js* 的 JavaScript 檔案。開啟該檔案，並將以下程式碼貼入其中：

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

您也需要在註冊的套件中包含 filter.js。開啟 *App_Start\BundleConfig.cs*，並變更第一組套件。變更第一個 `bundles.Add` 陳述式 (針對 **jquery** 套件)，使其包含 *Scripts\filter.js*，如下所示：

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**jquery** 套件已經由預設的 *_Layout* 檢視呈現。您可以在稍後利用相同的 JavaScript 程式碼，將篩選功能套用至其他清單檢視。

請重新整理行動瀏覽器，並移至 *AllSpeakers* 檢視。在搜尋方塊中，輸入 "sc"。此時應該會根據您的搜尋字串，篩選出演講者清單。

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a> 改善標籤清單

就像 [*演講者*] 檢視，您可以在行動裝置上閱讀 [*標籤*] 檢視，但連結卻非常微小而不容易點選。若您使用先前描述的程式碼變更 (但在 *Views\Home\AllTags.cshtml* 中要包含以下 `Html.ActionLink` 方法語法)，就可以像修正 [*演講者*] 檢視一樣修正[ *標籤*] 檢視：

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

重新整理後的桌面瀏覽器外觀如下：

![][AllTagsFixedDesktop]

而重新整理後的行動瀏覽器外觀如下：

![][AllTagsFixed]

>[AZURE.NOTE]若您發現行動瀏覽器中仍顯示原始的清單格式，想知道設好的 Bootstrap 樣式有什麼問題，這其實是您先前建立行動專用檢視的動作所產生的結果。然而，既然您使用 Bootstrap CSS 架構建立回應靈敏的 Web 設計，請移除這些行動專用的檢視和行動專用的配置檢視。完成移除後，再重新整理行動瀏覽器，就會顯示 Bootstrap 樣式。

##<a name="bkmk_improvedates"></a> 改善日期清單

若您使用先前描述的程式碼變更 (但在 *Views\Home\AllDates.cshtml* 中要包含以下 `Html.ActionLink` 方法語法)，就可以像改善 [*演講者*] 和 [*標籤*] 檢視一樣改善 [*日期*] 檢視：

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

重新整理後，您會獲得如下的行動瀏覽器檢視：

![][AllDatesFixed]

您可以依照日期，組織時間日期值，以進一步改善 [*日期*] 檢視。這可以透過 Bootstrap [面板][] (英文) 樣式來完成。以下列程式碼取代 *Views\Home\AllDates.cshtml* 檔案的內容：

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

此程式碼會為清單中每個不同的日期建立個別 `<div class="panel panel-primary">` 標籤，並分別為連結使用如上所述的[連結清單群組][] (英文)。以下是此程式碼執行時行動瀏覽器的樣貌：

![][AllDatesFixed2]

切換成桌面瀏覽器。然後會再次發現有一致的外觀。

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a> 改善 SessionsTable 檢視

您要在本節中使 *SessionsTable* 檢視更適合行動用途。這項變更比先前的變更更加廣泛。

在行動瀏覽器中，點選 [**標籤**] 按鈕，然後在搜尋方塊中輸入 `asp`。

![][AllTagsFixedSearchByASP]

點選 **ASP.NET** 連結。

![][SessionsTableTagASP.NET]

如您所見，顯示畫面會格式化為表格，目前這表格設計為可在桌面瀏覽器中檢視。但在行動瀏覽器上則有點難以閱讀。若要修正此問題，請開啟 *Views\Home\SessionsTable.cshtml*，然後使用下列程式碼取代檔案內容：

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

此程式碼會執行 3 個動作：

-   使用 Bootstrap [自訂連結清單群組][] (英文)，以垂直方式格式化工作階段資訊，使您可以在行動瀏覽器上閱讀所有資訊 (使用 list-group-item-text 之類的類別)
-   將[方格系統][] (英文) 套用至配置，讓工作階段項目能在桌面瀏覽器中水平流動，並在行動瀏覽器中垂直流動 (使用 col-md-4 類別)。
-   使用[回應靈敏的公用程式][]，於行動瀏覽器中檢視時，隱藏工作階段標籤 (使用 hidden-xs 類別)

您也可以點選標題連結，以進入個別工作階段。下圖反映了程式碼變更。

![][FixedSessionsByTag]

您自動套用的 Bootstrap 方格系統會在行動瀏覽器中自動以垂直方式排列工作階段。此外，請注意標籤並未顯示。切換成桌面瀏覽器。

![][SessionsTableFixedTagASP.NETDesktop]

在桌面瀏覽器，您會發現有顯示標籤。而且您還會看到套用的 Bootstrap 方格系統以兩欄方式排列工作階段項目。若您放大瀏覽器，會發現排列變更為三欄式。

##<a name="bkmk_improvesessionbycode"></a> 改善 SessionByCode 檢視

最後，您要修正 *SessionByCode* 檢視，使其適合行動用途。

在行動瀏覽器中，點選 [**標籤**] 按鈕，然後在搜尋方塊中輸入 `asp`。

![][AllTagsFixedSearchByASP]

點選 **ASP.NET** 連結。隨即會顯示 ASP.NET 標籤的工作階段。

![][FixedSessionsByTag]

選擇**使用 ASP.NET 和 AngularJS 建置單一頁面應用程式**連結。

![][SessionByCode3-644]

預設的桌面檢視雖然不錯，但您可以使用一些 Bootstrap GUI 元件輕鬆地改善它的外觀。

開啟 *Views\Home\SessionByCode.cshtml*，並以下列標記取代內容：

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

新的標記會使用 Bootstrap 的面板樣式改善行動檢視。

重新整理行動瀏覽器。下圖反映您剛做的程式碼變更：

![][SessionByCodeFixed3-644]

## 總結與複習

本教學課程已示範如何使用 ASP.NET MVC 5 開發適合行動的 Web 應用程式。其中包含：

-	將 ASP.NET MVC 5 應用程式部署至 App Service Web 應用程式
-   使用 Bootstrap 建立 MVC 5 應用程式中回應靈敏的 Web 配置
-   以全域方式覆寫個別檢視的配置、檢視和部分檢視
-   使用 `RequireConsistentDisplayMode` 屬性，控制配置和部分覆寫的強制執行
-   建立以特定瀏覽器做為目標的檢視，例如 iPhone 瀏覽器
-   在 Razor 程式碼中套用 Boostrap 樣式

## 另請參閱

-   [回應性 Web 設計的 9 個基本原則](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Bootstrap][BootstrapSite]
-   [官方 Bootstrap 部落格][]
-   [Tutorial Republic 的 Twitter Bootstrap 教學課程][]
-   [Bootstrap 練習場][]
-   [W3C 推薦的行動 Web 應用程式最佳做法][]
-   [W3C 針對媒體查詢的候選推薦做法][]

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]: #bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[連結清單群組]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[面板]: http://getbootstrap.com/components/#panels
[自訂連結清單群組]: http://getbootstrap.com/components/#list-group-custom-content
[方格系統]: http://getbootstrap.com/css/#grid
[回應靈敏的公用程式]: http://getbootstrap.com/css/#responsive-utilities
[官方 Bootstrap 部落格]: http://blog.getbootstrap.com/
[Tutorial Republic 的 Twitter Bootstrap 教學課程]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Bootstrap 練習場]: http://www.bootply.com/
[W3C 推薦的行動 Web 應用程式最佳做法]: http://www.w3.org/TR/mwabp/
[W3C 針對媒體查詢的候選推薦做法]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 

<!---HONumber=62-->