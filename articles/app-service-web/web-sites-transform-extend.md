<properties
	pageTitle="Azure App Service Web 應用程式進階設定和擴充功能"
	description="使用 XML 文件轉換 (XDT) 宣告來轉換 Azure App Service Web 應用程式中的 ApplicationHost.config 檔案，以及新增私用擴充功能來啟用自訂系統管理動作。"
	authors="cephalin"
	writer="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

# Azure App Service Web 應用程式進階設定和擴充功能

使用 [XML 文件轉換](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) 宣告，即可在 Azure App Service 的 Web 應用程式中轉換 [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) 檔案。您也可以使用 XDT 宣告來新增私用擴充功能，以啟用自訂的 Web 應用程式系統管理動作。本文包含一個 PHP Manager Web 應用程式擴充功能範例，該範例會透過 Web 介面來啟用 PHP 設定的管理功能。

##<a id="transform"></a>透過 ApplicationHost.config 的進階設定
App Service 平台提供適用於 Web 應用程式設定的彈性和控制。雖然無法在 App Service 中直接編輯標準的 IIS ApplicationHost.config 設定檔，但此平台支援以 XML 文件轉換 (XDT) 為基礎的宣告式 ApplicationHost.config 轉換模型。

若要利用此轉換功能，您可以建立含有 XDT 內容的 ApplicationHost.xdt 檔案並置於 Web 應用程式根目錄之下。您可能需要重新啟動 Web 應用程式，變更才會生效。

下列 applicationHost.xdt 範例示範如何將新的自訂環境變數新增至採用 PHP 5.4 的 Web 應用程式。

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


從 FTP 根目錄的 LogFiles\Transform 之下可取得含有轉換狀態和詳細資料的記錄檔案。

如需其他範例，請參閱＜[https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)＞。

**注意**<br /> 無法移除或重新排序 `system.webServer` 之下模組清單中的元素，但可以在清單中新增元素。


##<a id="extend"></a> 擴充 Web 應用程式

###<a id="overview"></a> 私人 Web 應用程式擴充功能的概觀

App Service 支援使用 Web 應用程式擴充功能做為系統管理動作的擴充點。事實上，有些 App Service 平台功能已當作預先安裝的擴充功能來實作。雖然您無法修改預先安裝的平台擴充功能，但可建立和設定自己 Web 應用程式的私人擴充功能。這項功能也會依賴 XDT 宣告。建立私人 Web 應用程式擴充功能的主要步驟如下：

1. Web 應用程式擴充功能的「內容」：建立任何 App Service 所支援的 Web 應用程式
2. Web 應用程式擴充功能的「宣告」：建立 ApplicationHost.xdt 檔案
3. Web 應用程式擴充功能的「部署」：將內容放置於 `root` 下方的 SiteExtensions 資料夾中

Web 應用程式的內部連結應指向在 ApplicationHost.xdt 檔案中指定之應用程式路徑的相對路徑。對 ApplicationHost.xdt 檔案所做的任何變更都需要回收 Web 應用程式。

**注意**：在 [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) 可取得這些主要元素的其他資訊。

其中包含一個詳細的範例，可說明建立和啟用私人 Web 應用程式擴充功能的步驟。您可以從 [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager) 下載以下 PHP Manager 範例的原始程式碼。

###<a id="SiteSample"></a> Web 應用程式擴充功能範例：PHP Manager

PHP Manager 是一個 Web 應用程式擴充功能，讓 Web 應用程式系統管理員可以使用 Web 介面輕鬆檢視及設定其 PHP 設定，而不需直接修改 PHP .ini 檔案。PHP 的常見設定檔包括位於 Program Files 之下的 php.ini 檔案，以及位於 Web 應用程式根資料夾的 .user.ini 檔案。因為無法在 App Service 平台上直接編輯 php.ini 檔案，所以 PHP Manager 擴充功能會使用 .user.ini 檔案來套用設定變更。

####<a id="PHPwebapp"></a> PHP Manager Web 應用程式

以下是 PHP Manager 部署的首頁：

![TransformSitePHPUI][TransformSitePHPUI]

如您所見，Web 應用程式擴充功能就像是一般的 Web 應用程式，但該 Web 應用程式的根資料夾中有額外的 ApplicationHost.xdt 檔案 (本文的下一節會提供更多有關 ApplicationHost.xdt 檔案的詳細資料)。

PHP Manager 擴充功能是使用 Visual Studio ASP.NET MVC 4 Web 應用程式範本建立的。下列的 [方案總管] 檢視顯示 PHP Manager 擴充功能的結構。

![TransformSiteSolEx][TransformSiteSolEx]

檔案 I/O 所需的唯一特殊邏輯就是指出 Web 應用程式的 wwwroot 目錄位於何處。如下列程式碼範例所示，環境變數 "HOME" 表示 Web 應用程式的根路徑，而附加 "site\wwwroot" 即可建構 wwwroot 路徑：

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot.user.ini");
    		return userSettingsFile;
	}


擁有目錄路徑之後，您即可使用一般檔案 I/O 作業來讀寫檔案。

Web 應用程式擴充功能中值得注意的一點與內部連結的處理有關。如果 HTML 檔案中有任何連結可提供 Web 應用程式上內部連結的絕對路徑，您就必須確定那些連結的前面都已加上您的擴充功能名稱作為根目錄。必須這麼做的原因是您擴充功能的根目錄現在是 "/`[your-extension-name]`/" 而不只是 "/"，所以所有內部連結都必須隨之更新。例如，假設程式碼包含下列項目的連結：

`"<a href="/Home/Settings">PHP Settings</a>"`

當連結是 Web 應用程式擴充功能的一部分時，連結的格式必須如下：

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

您可以在 Web 應用程式中只使用相對路徑，或是在 ASP.NET 應用程式案例中，使用 `@Html.ActionLink` 方法來建立適當的連結，藉以達到此需求。

####<a id="XDT"></a> applicationHost.xdt 檔案

Web 應用程式擴充功能的程式碼位於 %HOME%\SiteExtensions[your-extension-name] 之下。我們將此稱為擴充功能根目錄。

若要使用 applicationHost.config 檔案登錄您的 Web 應用程式擴充功能，您必須將名為 ApplicationHost.xdt 的檔案放在擴充功能根目錄中。ApplicationHost.xdt 檔案的內容應如下所示：

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

您選取作為擴充功能名稱的名稱應與擴充功能根資料夾同名。

此舉的作用就是將新的應用程式路徑新增至列在 SCM 網站之下的 `system.applicationHost` 網站。SCM 網站是具有特定存取認證的網站管理端點。其具有 URL `https://[your-site-name].scm.azurewebsites.net`。

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Web 應用程式擴充功能部署

若要安裝 Web 應用程式擴充功能，您可以使用 FTP，將 Web 應用程式的所有檔案複製到要安裝擴充功能之 Web 應用程式的 `\SiteExtensions[your-extension-name]` 資料夾。請務必同時將 ApplicationHost.xdt 檔案複製到此位置。重新啟動 Web 應用程式以啟用擴充功能。

您應該能在下列位置看見 Web 應用程式擴充功能：

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

請注意，除了使用 HTTPS 並包含 ".scm" 之外，此 URL 看起來就像 Web 應用程式的 URL。

在開發和調查期間，您可以藉由新增索引鍵為 `WEBSITE_PRIVATE_EXTENSIONS` 且值為 `0` 的應用程式，來停用 Web 應用程式的所有私人 (非預先安裝) 擴充功能。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=62-->