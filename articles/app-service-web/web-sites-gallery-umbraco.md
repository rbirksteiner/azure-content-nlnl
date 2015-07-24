<properties 
	pageTitle="從 Microsoft Azure 中的 Marketplace 建立 Umbraco Web 應用程式" 
	description="建立 Umbraco 內容管理系統，然後部署到 Azure App Service Web Apps。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#從 Microsoft Azure 中的 Marketplace 建立 Umbraco Web 應用程式#

Umbraco CMS 是個功能齊全的開放原始碼內容管理系統，可用來建立從小型到複雜等各式各樣的應用程式。Azure Marketplace 提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。透過套用入門套件或整合自己的設計，映像庫可讓您在幾分鐘內建立 Umbraco CMS 應用程式。

本文展示了可大幅簡化資源管理的 Azure 預覽入口網站。Azure 預覽入口網站旨在加快您的軟體交付程序速度，方法是在單一工作區中放置 Microsoft 及其夥伴的跨平台工具、技術和服務。與其使用獨立資源 (如 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps、Visual Studio 專案或資料庫)，您可以單一資源群組的方式來建立、管理及分析整個應用程式。

在本教學課程中，您將了解：

- 如何使用 Azure 預覽入口網站，透過 Markeplace 來建立新的 Web 應用程式
- 如何使用 Umbraco CMS 來建置部落格網站 

##從 Azure 預覽入口網站中的 Markeplace 建立 Web 應用程式

1. 登入 [Azure 預覽入口網站](https://portal.azure.com/)。

2. 選擇 [Marketplace] 圖示。
	
	![Choose Web Gallery][01Startboard]
	
3. 在 [Marketplace] 中，選取 [Web Apps] 索引標籤，然後選取 [Umbraco CMS]。
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. 若要建立新的 Umbraco CMS Web 應用程式，請按一下 [建立]。
	
	![Click Create][03UmbracoCMS]
	
5. 下一個步驟是設定所有與 Umbraco CMS 相關的資源。在此案例中的資源是 Web 應用程式和 SQL Server 資料庫。首先，選取 [Web 應用程式] 以進行 Web 應用程式設定，例如 [URL]、[應用程式服務計劃]、[Web 應用程式設定] 和 [位置]。
	
	![Configure resources][04AppSettings]
	
6. 現在開始設定資料庫。選取 [資料庫]，然後選擇 [伺服器]。本範例將為 Azure 上的資料庫建立 SQL Server。
	
	![Create a SQL Server on Azure][05NewServer]
	
7. 現在已設定好 Web 應用程式和資料庫，您可以開始部署應用程式，方法是在第一個 [Umbraco CMS] 分頁 (上圖中所示) 的底部上，按一下 [建立]。
	
	![Click Create][06UmbracoCMSGroup]
	
完成部署之後，入口網站將會顯示您的 Umbraco CMS Web 應用程式的資源群組分頁。在 [摘要] 區段中，按一下 Web 應用程式名稱以檢視 Web 應用程式屬性。另外，在 [摘要] 區段中，您還可以選取資料庫資源，以查看相關資料庫的屬性。
	
![][07UmbracoCMSGroupBlade]

## 啟動和設定 Umbraco CMS Web 應用程式 ##

1. 在 Web 應用程式的詳細資料分頁中，按一下 [瀏覽] 以瀏覽您的 Web 應用程式。
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. 瀏覽 Web 應用程式時，Umbraco CMS 會啟動其安裝程式精靈。輸入要求的資訊，然後按一下 [自訂]。
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. 輸入 Umbraco 即將使用的資料庫連線和驗證詳細資料。在資料庫類型中選取 [Microsoft SQL Azure]。您可以從 Web 應用程式的 [網站設定] 區段中取得資料庫的連接字串。
	
	![Configure your database][10ConfigureYourDatabase]
	
4. 如果您不熟悉 Umbraco CMS，您可以選取網站入門套件。您也可以按一下 [No thanks, I do not want to install a starter website] 。
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Umbraco 安裝程式將會完成設定您的應用程式。在設定您的應用程式之後，您將會被重新導向至 Umbraco CMS 系統管理儀表板。
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. 您現在可以建立您將發行的範例文字頁面。依序選取 [連線]、[溢位] 及 [TextPage]。
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. 輸入文字頁面的標題和部分內容，如下所示。結束時，請按一下 [儲存並發行]。
	
	![Enter a title and some content][16EnterAName]
	
8. 等候發行您的頁面。發行完成時，您將在畫面的右下角看到一則小警示。您現在可以開始瀏覽 Web 應用程式上的新內容。
	
	![Published web site page][17MyPage]
	

這樣就大功告成了！ 只需要幾分鐘的時間，您已成功使用 Umbraco CMS 建立部落格 Web 應用程式。

##其他資源

[Umbraco 文件](http://our.umbraco.org/documentation)

[Umbraco 影片教學課程](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Azure 入口網站文件](../preview-portal.md)

[Azure 入口網站 (第 9 頻道)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Azure App Service Web Apps 文件](/documentation/services/websites/)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
 

<!---HONumber=62-->