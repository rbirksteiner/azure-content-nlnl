<properties 
	pageTitle="整合 Azure Web 應用程式與 Azure CDN" 
	description="教學課程，指導如何部署可提供整合式 Azure CDN 端點內容的 Azure Web 應用程式" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="cephalin"/>

<a name="intro"></a>
# 整合 Azure Web 應用程式與 Azure CDN #

Azure App Service 可以與 [Azure CDN](http://azure.microsoft.com/services/cdn/) 整合，並透過從靠近您客戶的伺服器節點全域地提供 Web 應用程式內容，來加入 App Service Web 應用程式中原有的全域調整功能 (在[這裡](http://msdn.microsoft.com/library/azure/gg680302.aspx)可以找到更新的所有目前節點位置清單)。這項整合大幅增加您 Azure 應用程式服務 Web 應用程式的效能，同時大幅提升全球 Web 應用程式的使用者體驗。

整合 Azure Web 應用程式與 Azure CDN 提供下列優點：

- 將內容部署 (影像、指令碼和樣式表) 整合到 Azure Web 應用程式的[連續部署](web-sites-publish-source-control.md)程序中
- 輕鬆地升級 Azure 應用程式服務的 Web 應用程式中的 NuGet 封裝 (例如 jQuery 或 Bootstrap 版本) 
- 從相同的 Visual Studio 介面來管理 Web 應用程式和 CDN 提供的內容
- 整合 ASP.NET 統合和縮製與 Azure CDN

## 將建置的項目 ##

您將在 Visual Studio 中使用預設 ASP.NET MVC 範本將 Web 應用程式部署至 Azure App Service、加入程式碼從整合式 Azure CDN 提供內容 (例如影像、控制器動作結果，以及預設 JavaScript 和 CSS 檔案)，也會撰寫程式碼來為 CDN 離線時提供的套件組合設定後援機制。

## 必要元件 ##

本教學課程有下列先決條件：

-	使用中的 [Microsoft Azure 帳戶](http://azure.microsoft.com/account/)
-	Visual Studio 2013 (含 [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409))

> [AZURE.NOTE]您需要 Azure 帳戶才能完成本教學課程：+ 您可以[免費申請 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如 Web 應用程式。+ 您可以[啟用 MSDN 訂用帳戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

<a name="deploy"></a>
## 部署具有整合式 CDN 端點的 Azure Web 應用程式 ##

在本節中，您將在 Visual Studio 2013 中將預設 ASP.NET MVC 應用程式範本部署至 Azure Web 應用程式，然後將它與新的 CDN 端點整合。請遵循下列指示：

1. 在 Visual Studio 2013 中，從功能表列中移至 **[檔案] > [新增] > [專案] > [Web] > [ASP.NET Web 應用程式]**，以建立新的 ASP.NET Web 應用程式。命名並按一下 [確定]。

	![](media/app-service-with-cdn/1-new-project.png)

3. 選取 [**MVC**]，然後按一下 [**管理訂閱**]。

	![](media/app-service-with-cdn/2-webapp-template.png)

4. 按一下 [登入]。

	![](media/app-service-with-cdn/3-manage-subscription.png)

6. 在登入頁面中，使用您用來啟用 Azure 帳戶的 Microsoft 帳戶登入。
7. 登入後，按一下 [**關閉**]。然後，按一下 [**確定**] 繼續。

	![](media/app-service-with-cdn/4-signed-in.png)

8. 假設您尚未建立 Azure Web 應用程式，Visual Studio 可協助您建立它。在 [**設定 Microsoft Azure 網站**] 對話方塊中，請確定您的網站名稱是唯一的。然後按一下 **[確定]**。

	![](media/app-service-with-cdn/5-create-website.png)

9. 建立 ASP.NET 應用程式之後，在 [Web 發佈活動] 窗格中按一下 [**將 `<app name>` 立即發佈至此網站**]，將它發佈至 Azure。按一下 [**發佈**] 完成程序。

	![](media/app-service-with-cdn/6-publish-website.png)

	當發佈完成時，您會在瀏覽器中看到您已發佈的 Web 應用程式。

1. 若要建立 CDN 端點，請登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
2. 按一下 [新增] > [應用程式服務] > [CDN] > [快速建立]。選取 **http://*&lt;sitename>*.azurewebsites.net/**，然後按一下 [**建立**]。

	![](media/app-service-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE]建立 CDN 端點之後，Azure 入口網站會顯示其 URL 及與它整合的原始網域。不過，需要花費一些時間，新的 CDN 端點的設定才能完全傳播至所有 CDN 節點位置。

3. 回到 Azure 入口網站，在 [CDN] 索引標籤中，按一下您剛建立的 CDN 端點的名稱。

	![](media/app-service-with-cdn/8-select-cdn.png)

3. 按一下 [啟用查詢字串]，在 CDN 快取中啟用查詢字串。啟用此選項後，將以個別項目來快取以不同查詢字串存取的相同連結。

	![](media/app-service-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE]雖然本節教學課程並不需要啟用查詢字串，但為了方便起見，請儘早這樣做，因為此處的任何變更需要花費很長時間才能傳播至所有 CDN 節點，您不希望任何未啟用查詢字串的內容塞滿 CDN 快取 (稍後會討論更新 CDN 內容)。

2. 現在，按一下 CDN 端點位址。如果端點已準備就緒，您應該會看到顯示的 Web 應用程式。如果您收到 **HTTP 404** 錯誤，表示 CDN 端點還未準備就緒。您可能需要等候一小時，CDN 組態才能傳播到所有邊緣節點。

	![](media/app-service-with-cdn/11-access-success.png)

1. 接下來，試著存取 ASP.NET 專案中的 **~/Content/bootstrap.css** 檔案。在瀏覽器視窗中，瀏覽至 **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**。在我的設定中，此 URL 為：

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	對應至 CDN 端點上的下列原始 URL：

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	瀏覽至 **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** 時，將提示您下載來自 Azure 中 Web 應用程式的 bootstrap.css。

	![](media/app-service-with-cdn/12-file-access.png)

同樣地，您可以直接從 CDN 端點，以 **http://*&lt;serviceName>*.cloudapp.net/** 存取任何可公開存取的 URL。例如：

-	/Script 路徑中的 .js 檔案
-	/Content 路徑中的任何內容檔案
-	任何控制器/動作 
-	任何含有查詢字串的 URL (若 CDN 端點已啟用查詢字串的話)
-	整個 Azure Web 應用程式 (若所有內容都是公用)

請注意，透過 Azure CDN 提供整個 Azure Web 應用程式不見得是個好主意 (或通常是個好主意)。有幾點需要注意：

-	此作法需要公開整個網站，因為 Azure CDN 無法提供任何私人內容。
-	如果 CDN 端點因故離線 (不論是排定的維護或使用者錯誤)，則除非能夠將客戶重新導向至原始 URL **http://*&lt;sitename>*.azurewebsites.net/**，否則整個 Web 應用程式都會離線。 
-	即使使用自訂 Cache-Control 設定 (請參閱[在 Azure Web 應用程式中設定靜態檔案的快取選項](#caching))，CDN 端點也無法改善高度動態內容的效能。如果您嘗試從 CDN 端點載入首頁，如上所示，請注意，第一次載入預設首頁 (非常簡單的頁面) 至少需要 5 秒。設想，如果此頁面包含必須每分鐘更新的動態內容，客戶體驗有何影響。從 CDN 端點提供動態內容需要有較短的快取到期時間，這也說明 CDN 端點經常會發生快取遺漏。這會降低 Azure Web 應用程式的效能，也會折損 CDN 的效用。

替代方法是在 Azure Web 應用程式中依個別情況決定從 Azure CDN 提供什麼內容。總之，您已了解如何從 CDN 端點存取個別的內容檔案。我將在[透過 Azure CDN 從控制器動作提供內容](#controller)中說明如何透過 CDN 端點提供特定的控制器動作。

<a name="caching"></a>
## 在 Azure Web 應用程式中設定靜態檔案的快取設定 ##

利用 Azure Web 應用程式中的 Azure CDN 整合，您可以指定如何在 CDN 端點中快取靜態內容。若要這樣做，請從 ASP.NET 專案 (例如 **cdnwebapp**) 開啟 *Web.config*，將 `<staticContent>` 元素加入至 `<system.webServer>`。以下的 XML 將快取設為 3 天過期。<pre class="prettyprint"> &lt;system.webServer&gt; <mark>&lt;staticContent&gt; &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt; &lt;/staticContent&gt;</mark> ... &lt;/system.webServer&gt; </pre>

這樣做時，Azure Web 應用程式中的所有靜態檔案會在您的 CDN 快取中遵守相同規則。若要更精確控制快取設定，請將 *Web.config* 檔案加入至資料夾，並在檔案中新增您的設定。例如，將 *Web.config* 檔案加入至 *\Content* 資料夾，並將內容改成下列 XML：

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

此設定會將 *\Content* 資料夾中的所有靜態檔案快取 15 天。

如需有關如何設定 `<clientCache>` 元素的詳細資訊，請參閱[用戶端快取 &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)。

在[透過 Azure CDN 從控制器動作提供內容](#controller)中，我也會說明如何在 CDN 快取中設定控制器動作結果的快取設定。

<a name="controller"></a>
## 透過 Azure CDN 從控制器動作提供內容 ##

整合 Web 應用程式與 Azure CDN 時，透過 Azure CDN 從控制器動作提供內容就非常簡單。同樣地，如果您決定透過 CDN 來提供整個 Azure Web 應用程式，則您不需要這麼做，因為已可透過 CDN 來呼叫所有控制器動作。但是，就像我在[部署具有整合式 CDN 端點的 Azure Web 應用程式](#deploy)中所提出的理由，您可能決定不這樣做，而是選擇選取您想要從 Azure CDN 提供的控制器動作。[Maarten Balliauw](https://twitter.com/maartenballiauw) 在[使用 Azure CDN 縮短網路延遲時間](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)中以一個有趣的 MemeGenerator 控制器來說明作法。我在這裡簡單地重述一次。

假設您想在 Web 應用程式中利用查克羅禮士年輕時的一張相片 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) 拍攝) 來引起網路瘋傳：

![](media/app-service-with-cdn/cdn-5-memegenerator.PNG)

您有一個簡單的 `Index` 動作可讓客戶在照片上指定笑梗，再傳給此動作來引起網路瘋傳。因為是查克羅禮士，您預期此頁面一定會在全球造成一股旋風。這就是利用 Azure CDN 來提供半動態內容的一個最佳例子。

請依照上述步驟來設定此控制器動作：

1. 在 *\Controllers* 資料夾中，建立一個新的 .cs 檔案稱為 *MemeGeneratorController.cs*，並將內容改成下列程式碼。請記得將反白部分取代為您的檔案路徑和 CDN 名稱。
	<pre class="prettyprint">
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.IO;
using System.Net;
using System.Web.Hosting;
using System.Web.Mvc;
using System.Web.UI;

namespace cdnwebapp.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

        public ActionResult Index()
        {
            return View();
        }

        [HttpPost, ActionName("Index")]
    	public ActionResult Index_Post(string top, string bottom)
        {
            var identifier = Guid.NewGuid().ToString();
            if (!Memes.ContainsKey(identifier))
            {
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            }

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        }

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            }

            if (Debugger.IsAttached) // Preserve the debug experience
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
            else // Get content from Azure CDN
            {
                return Redirect(string.Format("http://<mark>&lt;yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
        }

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

            using (Graphics graphics = Graphics.FromImage(bitmap))
            {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                }
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                }
            }

            MemoryStream ms = new MemoryStream();
            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
            return File(ms.ToArray(), "image/png");
        }

        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
        {
            // Compute actual size, shrink if needed
            while (true)
            {
                size = g.MeasureString(text, font);

                // It fits, back out
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // Try a smaller font (90% of old size)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            }
        }
    }
}
</pre>

2. 以滑鼠右鍵按一下預設的 `Index()` 動作，選取 [**加入檢視**]。

	![](media/app-service-with-cdn/cdn-6-addview.PNG)

3.  接受下列設定，按一下 [加入]。

	![](media/app-service-with-cdn/cdn-7-configureview.PNG)

4. 開啟新的 *Views\MemeGenerator\Index.cshtml*，將內容改成下列簡單的 HTML 來提交笑梗：

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. 重新發佈至 Azure Web 應用程式，然後在瀏覽器中瀏覽至 **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**。

當您將表單值提交至 `/MemeGenerator/Index` 時，`Index_Post` 動作方法會傳回 `Show` 動作方法的連結及個別的輸入識別碼。當您按一下連結時，您會連到下列程式碼：<pre class="prettyprint"> [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)] public ActionResult Show(string id) { Tuple&lt;string, string&gt; data = null; if (!Memes.TryGetValue(id, out data)) { return new HttpStatusCodeResult(HttpStatusCode.NotFound); }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
} </pre>

如果已附加本機偵錯程式，您將經由本機重新導向而享有一般的偵錯體驗。如果是在 Azure Web 應用程式中執行，則會重新導向至：

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

對應至 CDN 端點上的下列原始 URL：

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

先前套用 URL 重寫規則之後，實際快取至 CDN 端點的檔案為：

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

然後，您可以在 `Generate` 方法上使用 `OutputCacheAttribute` 屬性，以指定如何快取 Azure CDN 將接受的動作結果。以下程式碼指定快取到期時間 1 小時 (3,600 秒)。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同樣地，您可以在 Azure Web 應用程式中透過 Azure CDN，並指定想要的快取選項，從任何控制器動作提供內容。

在下一節，我將說明如何透過 Azure CDN 提供統合和縮製的指令碼與 CSS。

<a name="bundling"></a>
## 整合 ASP.NET 統合和縮製與 Azure CDN ##

指令碼和 CSS 樣式表不常變更，是 Azure CDN 快取的最佳候選項。透過 Azure CDN 提供整個 Web 應用程式是整合統合和縮製與 Azure CDN 最輕鬆的方法。不過，由於您可能因為[整合 Azure CDN 端點與 Azure Web 應用程式，並從 Azure CDN 提供網頁的靜態內容](#deploy)中所述的理由而不選擇這種作法，我將說明如何這樣做，但同時保留開發人員希望的 ASP.NET 統合和縮製體驗，例如：

-	絕佳的偵錯模式體驗
-	流暢的部署
-	隨著指令碼/CSS 版本升級立即更新用戶端
-	CDN 端點失敗時的後援機制
-	儘可能不修改程式碼

在您於[整合 Azure CDN 端點與 Azure 網站，並從 Azure CDN 提供網頁的靜態內容](#deploy)中所建立的 ASP.NET 專案中，開啟 *App_Start\BundleConfig.cs*，然後查看 `bundles.Add()` 方法呼叫。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

第一個 `bundles.Add()` 陳述式將指令碼套件組合加入至虛擬目錄 `~/bundles/jquery`。然後，開啟 *Views\Shared_Layout.cshtml*，查看指令碼套件組合標籤如何轉譯。您應該可以找到下列這一行 Razor 程式碼：

    @Scripts.Render("~/bundles/jquery")

當此 Razor 程式碼在 Azure Web 應用程式中執行時，它會類似如下轉譯指令碼套件組合的 `<script>` 標籤：

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

不過，當在 Visual Studio 中按 `F5` 執行時，則會個別轉譯套件組合中的每一個指令碼檔案 (在上述案例中，套件組合中只有一個指令碼檔案)：

    <script src="/Scripts/jquery-1.10.2.js"></script>

這樣可讓您在開發環境中進行 JavaScript 程式碼偵錯，同時減少並行的用戶端連線 (統合)，在實際執行環境中提升檔案下載效能 (縮製)。這是 Azure CDN 整合中保留的絕佳功能。此外，由於轉譯的套件組合已包含自動產生的版本字串，您可以仿照此功能，每當您透過 NuGet 更新 jQuery 版本時，就會以最快速度更新用戶端。

請遵循下列步驟來整合 ASP.NET 統合和縮製與 CDN 端點。

1. 回到 *App_Start\BundleConfig.cs*，修改 `bundles.Add()` 方法來使用不同的 [Bundle 建構函數](http://msdn.microsoft.com/library/jj646464.aspx) (此函數會指定 CDN 位址)。若要這樣做，請將 `RegisterBundles` 方法定義改成下列程式碼：  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from.Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}
</pre>請記得將 `<yourCDNName>` 取代為您的 Azure CDN 名稱。

	簡單地說，您正在設定 `bundles.UseCdn = true`，且已在每一個套件組合中加入謹慎建構的 CDN URL。例如，程式碼的第一個建構函式：

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	就如同：

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	此建構函式告知 ASP.NET 統合和縮製在本機偵錯時轉譯個別指令碼檔案，但使用指定的 CDN 位址來存取所提及的指令碼。不過，對於此謹慎建構的 CDN URL，請注意兩項重要特性：
	
	-	此 CDN URL 的來源是 `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`，實際上就是 Web 應用程式中指令碼套件組合的虛擬目錄。
	-	由於是使用 CDN 建構函式，套件組合的 CDN 指令碼標籤在轉譯的 URL 中已不再包含自動產生的版本字串。指令碼套件組合每次修改時，您都必須手動產生唯一的版本字串，以強制在 Azure CDN 上發生快取遺漏。同時，在部署套件組合之後，此唯一的版本字串在部署的整個存在期間內必須保持不變，讓 Azure CDN 的快取命中率達到最高。
	-	查詢字串 v=<W.X.Y.Z> 會從 ASP.NET 專案的 *Properties\AssemblyInfo.cs* 中提取。您的部署工作流程中可以包含每次發佈至 Azure 時就遞增組件版本。或者，您可以直接修改專案中的 *Properties\AssemblyInfo.cs*，使用萬用字元 '*' 表示每次建置時就自動遞增版本字串。例如：
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		在此可採取其他任何策略在部署的存在期間內產生唯一字串。

3. 重新發行 ASP.NET 應用程式和存取首頁。
 
4. 檢視頁面的 HTML 程式碼。您應該會看到轉譯的 CDN URL，以及每次將變更重新發佈至 Azure Web 應用程式時的唯一版本字串。例如：
	<pre class="prettyprint">
...

&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. 在 Visual Studio 中，按 `F5`，在 Visual Studio 中進行 ASP.NET 應用程式偵錯。

6. 檢視頁面的 HTML 程式碼。您仍然會看到每一個指令檔以個別方式轉譯，讓您在 Visual Studio 中享有一致的偵錯體驗。
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...   
</pre>

<a name="fallback"></a>
## CDN URL 的後援機制 ##

當 Azure CDN 端點因故失敗時，您一定希望網頁能夠聰明地存取原始 Web 伺服器，當作後援選項來載入 JavaScript 或 Bootstrap。因 CDN 無法使用而遺失 Web 應用程式的影像就已經夠嚴重，而遺失由指令碼和樣式表所提供的重要頁面功能又更加嚴重。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) 類別包含一個稱為 [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) 的屬性，可讓您設定 CDN 失敗時的後援機制。若要使用此屬性，請遵循下列步驟：

1. 在 ASP.NET 專案中，開啟 *App_Start\BundleConfig.cs* (其中，您已在每一個 [Bundle 建構函式](http://msdn.microsoft.com/library/jj646464.aspx)中加入 CDN URL)，進行下列醒目提示的變更將後援機制加入至預設套件組合：  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from.Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}</pre>當 `CdnFallbackExpression` 不是 null 時，指令碼會插入 HTML 中來測試是否已成功載入套件組合，如果不是，則直接從原始 Web 伺服器存取套件組合。此屬性必須設為 JavaScript 運算式來測試個別的 CDN 套件組合是否正確載入。測試每一個套件組合所需的運算式根據內容而不同。在以上的預設套件組合中：
	
	-	`window.jquery` 定義於 jquery-{version}.js 中
	-	`$.validator` 定義於 jquery.validate.js 中
	-	`window.Modernizr` 定義於 modernizer-{version}.js 中
	-	`$.fn.modal` 定義於 bootstrap.js 中
	
	您可能發現到我沒有為 `~/Cointent/css` 套件組合設定 CdnFallbackExpression。這是因為目前 [System.Web.Optimization 中有錯誤](https://aspnetoptimization.codeplex.com/workitem/104)，導致插入後援 CSS 的 `<script>` 標籤，而非預期的 `<link>` 標籤。
	
	不過，[Ember 顧問團](https://github.com/EmberConsultingGroup/StyleBundleFallback) (英文) 提供一套良好的[樣式套件組合後援](https://github.com/EmberConsultingGroup) (英文)。

2. 若要使用 CSS 的解決方案，請在 ASP.NET 專案的 *App_Start* 資料夾中，建立新的 .cs 檔案 (稱為 *StyleBundleExtensions.cs*)，並將內容改成 [GitHub 提供的程式碼](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)。

4. 在 *App_Start\StyleFundleExtensions.cs* 中，將命名空間重新命名為您的 ASP.NET 應用程式的命名空間 (例如 **cdnwebapp**)。

3. 回到 `App_Start\BundleConfig.cs`，將最後一個 `bundles.Add` 陳述式修改為下列醒目提示的程式碼：
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
</pre>這個新的延伸方法採用相同的概念，將指令碼插入 HTML 中來檢查 DOM，尋找 CSS 套件組合中定義的相符類別名稱、規則名稱和規則值，而如果找不到相符項，則退一步存取原始 Web 伺服器。

4. 重新發佈至 Azure Web 應用程式並存取首頁。
5. 檢視頁面的 HTML 程式碼。您應該會發現類似下方的插入指令碼：    
	<pre class="prettyprint">...

	&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
</pre>請注意，針對 CSS 套件組合插入的指令碼，仍在這一行中包含 `CdnFallbackExpression` 屬性殘留的遊蕩部分：

        }())||document.write('<script src="/Content/css"></script>');</script>

	但因為 || 運算式的開頭部分一定會傳回 true (緊鄰的上一行)，所以 document.write() 函數永遠不會執行。

6. 若要測試後援指令碼是否可運作，請回到 CDN 端點的儀表板，按一下 [**停用端點**]。

	![](media/app-service-with-cdn/13-test-fallback.png)

7. 重新整理瀏覽器視窗來顯示 Azure Web 應用程式。您現在應該會看到所有的指令碼和樣式表都已正確載入。

# 相關資訊 #
- [Azure 內容傳遞網路 (CDN) 概觀](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [在 Web 應用程式中從 Azure CDN 提供內容](../cdn-serve-content-from-cdn-in-your-web-application.md)
- [整合雲端服務與 Azure CDN](../cdn-cloud-service-with-cdn.md)
- [ASP.NET 統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [使用 Azure 的 CDN](../cdn-how-to-use.md)
 

<!---HONumber=62-->