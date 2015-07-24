<properties
	pageTitle="Azure App Service 上的企業級 WordPress"
	description="了解如何在 Azure App Service 上裝載企業級 WordPress 網站"
	services="app-service\web"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.devlang="php"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="web"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

#Azure App Service 上的企業級 WordPress

Azure App Service 針對關鍵的大規模 [WordPress][wordpress] 網站，提供可擴充、安全又容易使用的環境。Microsoft 本身執行企業級網站，例如 [Office][officeblog] 和 [Bing][bingblog] 部落格。本文件說明如何使用 Azure App Service Web Apps，來建立與維護可應付大量訪客的企業級雲端架構 WordPress 網站。

## 架構與規劃

基本 WordPress 安裝僅有兩項需求。

* **MySQL 資料庫** - 可透過 [Azure Marketplace 中的 ClearDB][cdbnstore] 取得，或您可在 Azure 虛擬機器上，使用 [Windows][mysqlwindows] 或 [Linux][mysqllinux] 來管理自己的 MySQL 安裝。

    > [AZURE.NOTE]ClearDB 提供數個 MySQL 組態，每個組態具有不同的效能特性。請參閱 [Azure 市集][cdbnstore]，以取得透過 Azure 市集所提供的產品相關資訊，請參閱 [ClearDB 定價](http://www.cleardb.com/pricing.view)，以取得 ClearDB 直接提供的產品。

* **PHP 5.2.4 或更高版本** - Azure App Service 目前提供 [PHP 5.3、5.4 和 5.5 版][phpwebsite]。

	> [AZURE.NOTE]建議您務必在最新版的 PHP 上執行，以確保您擁有最新的安全性問題修正。

###基本部署

只需使用基本需求，您便可在 Azure 區域內建立基本的解決方案。

![Azure Web 應用程式與 MySQL 資料庫裝載於單個 Azure 區域中][basic-diagram]

這可讓您透過建立網站的多個 Web 應用程式執行個體來相應放大應用程式，並同時在特定地理區域的資料中心內託管所有項目。此區域以外的訪客可能會在使用此網站時察覺到緩慢的回應時間，而且如果此區域內的資料中心發生故障，您的應用程式也就無法運作。


###多重區域部署

使用 Azure [流量管理員][trafficmanager]，您可以在多個地理區域中調整 WordPress 網站，但只提供一個 URL 給訪客。所有透過流量管理員進入的訪客，會根據負載平衡組態接著被路由傳送至某個區域。

![Azure Web 應用程式，裝載於多個區域中，使用 CDBR 高可用性路由器，跨區域路由傳送至 MySQL][multi-region-diagram]

在每個區域內，您仍然可以在多個 Web 應用程式執行個體中調整 WordPress 網站，但此調整會是區域特定；高流量區域和低流量區域的調整可能會有所不同。

您可以使用 ClearDB 的 [CDBR 高可用性路由器][cleardbscale] (如左側所示) 或 [MySQL 叢集 CGE][cge]，進行複寫和路由傳送至多個 MySQL 資料庫。

###包含媒體儲存體和快取的多重區域部署

如果網站接受上傳或主機媒體檔案，請使用 Azure Blob 儲存體。如果您需要快取，請考慮 [Redis cache][rediscache]、[Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) 或 [Azure 市集](http://azure.microsoft.com/gallery/store/)中的其中一個快取產品。

![Azure Web 應用程式，裝載於多個區域中，為 MySQLapp 使用 CDBR 高可用性路由器，搭配受管理的快取、Blob 儲存體和 CDN][performance-diagram]

依預設，Blob 儲存體會在不同區域中異地發佈，因此您無需擔心在所有網站中複寫檔案。您也可以為 Blob 儲存體啟用 Azure [內容發佈網路 (Content Distribution Network，CDN)][cdn]，這會將檔案發佈至靠近訪客的端點。

###規劃

####其他需求

作法... | 目的...
------------------------|-----------
**上傳或儲存大型檔案** | [使用 Blob 儲存體的 WordPress 外掛程式][storageplugin]
**傳送電子郵件** | [SendGrid][storesendgrid] 和[使用 SendGrid 的 WordPress 外掛程式][sendgridplugin]
**自訂網域名稱** | [在 Azure App Service 中設定自訂網域名稱][customdomain]
**HTTPS** | [針對 Azure App Service 中的 Web 應用程式啟用 HTTPS][httpscustomdomain]
**生產前驗證** | [針對 Azure App Service 中的 Web 應用程式設定預備環境][staging] <p>請注意，從預備 Web 應用程式切換成生產 Web 應用程式時，也會移動 WordPress 組態。您應確定所有設定會更新到生產應用程式的需求，才能將預備應用程式切換成生產應用程式。</p>
**監視與疑難排解** | [針對 Azure App Service 中的 Web 應用程式啟用診斷記錄功能][log]及[監視 Azure App Service 中的 Web 應用程式][monitor]
**部署您的網站** | [在 Azure App Service 中部署 Web 應用程式][deploy]

####可用性和災難復原

作法... | 目的...
------------------------|-----------
**負載平衡網站**或**異地發佈網站** | [使用 Azure 流量管理員路由傳送流量][trafficmanager]
**備份與還原** | [備份 Azure App Service 中的 Web 應用程式][backup]及[還原 Azure App Service 中的 Web 應用程式][restore]

####效能

雲端中的效能主要會透過快取和相應放大實現；然而也應將記憶體、頻寬和 Web 應用程式主控的其他屬性納入考量。

作法... | 目的...
------------------------|-----------
**了解 App Service 執行個體功能** | [定價詳細資料，包括 App Service 層的功能][websitepricing]
**快取資源** | [Redis cache][rediscache]、[Memcache Cloud](/gallery/store/garantiadata/memcached/)、[MemCachier](/gallery/store/memcachier/memcachier/) 或 [Azure 市集](/gallery/store/)中的其中一個快取產品。
**調整您的應用程式** | [調整 Azure App Service 中的 Web 應用程式][websitescale]及 [ClearDB 高可用性路由][cleardbscale]。如果您選擇主控與管理自己的 MySQL 安裝，您應考量可相應放大的 [MySQL 叢集 CGE][cge] (英文)。

####移轉

將現有的 WordPress 網站移轉成 Azure App Service 的方法有兩種。

* **[WordPress 匯出][export]** - 這將匯出您的部落格內容，並接著使用 [WordPress 匯入工具外掛程式][import]將其匯入到 Azure App Service 上的新 WordPress 網站。

	> [AZURE.NOTE]此程序可讓您移轉內容，但它無法移轉任何外掛程式、主題或其他自訂。您必須手動重新安裝這些項目。

* **手動移轉** - [備份您的網站][wordpressbackup]和[資料庫][wordpressdbbackup] (英文)，然後手動將它還原成 Azure App Service 中的 Web 應用程式和相關聯 MySQL 資料庫，以移轉高度自訂的網站，並避免冗長的手動外掛程式、佈景主題或其他自訂。

## 逐步指示

### 建立新的 WordPress 網站

1. 使用 [Azure Marketplace][cdbnstore]，在您將主控網站的區域中建立 MySQL 資料庫，其大小為您在[架構與規劃](#planning)一節中所識別的大小。

2. 請依照[在 Azure App Service 中建立 WordPress Web 應用程式][createwordpress]中的步驟，建立新的 WordPress Web 應用程式。建立 Web 應用程式時，請選取 [使用現有的 MySQL 資料庫]，然後選取在步驟 1 中所建立的資料庫。

如果您打算移轉現有的 WordPress 網站，在建立新的 Web 應用程式後，請參閱[將現有的 WordPress 網站移轉至 Azure](#Migrate-an-existing-WordPress-site-to-Azure)。

### 將現有的 WordPress 網站移轉至 Azure

如[架構與規劃](#planning)一節所提到的，移轉 WordPress 網站的方法有兩種。

* **匯出與匯入** - 適用於沒有許多自訂的網站，或只想要移動內容的網站。

* **備份與還原** - 適用於具有許多自訂，並且要移動所有項目的網站。

使用下列其中一個區段來移轉網站。

####匯出與匯入方法

1. 使用 [WordPress 匯出][export]來匯出您的現有網站。

2. 使用[建立新的 WordPress 網站](#Create-a-new-WordPress-site)一節中的步驟建立新 Web 應用程式。

3. 在 Web Apps 上登入 WordPress 網站，然後依序按一下 [外掛程式] -> [新增]。搜尋與安裝 [WordPress Importer] 外掛程式。

4. 在安裝匯入工具外掛程式後，依序按一下 [工具] -> [匯入]，然後選取 [WordPress] 以使用 WordPress 匯入工具外掛程式。

5. 在 [Import WordPress] 頁面上，按一下 [選擇檔案]。瀏覽至從現有 WordPress 網站匯出的 WXR 檔案，然後選擇 [Upload file and import]。

6. 按一下 [提交]。系統將提示您已成功匯入。

8. 完成所有這些步驟之後，在 [Azure Preview 入口網站][mgmtportal]中，從 Web 應用程式刀鋒視窗重新啟動您的網站。

在匯入網站後，您可能需要執行下列步驟才能啟用不包含在匯入檔案中的設定。

如果使用... | 執行此動作...
------------------ | ----------
**固定連結** | 從新網站的 WordPress 儀表板中，依序按一下 [設定] -> [固定連結]，然後更新固定連結結構
**影像/媒體連結** | 若要將連結更新到新位置，請使用 [Velvet Blues Update URLs 外掛程式][velvet] (搜尋和取代工具)，或手動在資料庫中更新
**佈景主題** | 移至 [外觀] -> [佈景主題]，然後視需要更新網站佈景主題
**功能表** | 如果您的主題支援功能表，前往首頁的連結可能仍然內嵌舊的子目錄。移至 [外觀] -> [功能表]，然後進行更新

####備份與還原方法

1. 使用 [WordPress 備份][wordpressbackup]上的資訊，來備份現有的 WordPress 網站。

2. 使用[備份資料庫][wordpressdbbackup]上的資訊，來備份現有的資料庫。

3. 建立新的資料庫並還原備份。

	1. 從 [Azure Marketplace][cdbnstore] 中購買新的資料庫，或在 [Windows][mysqlwindows] 或 [Linux][mysqllinux] VM 上設定 MySQL 資料庫。

	2. 使用 MySQL 用戶端 (如 [MySQL Workbench][workbench]) 連線到新的資料庫，並匯入您的 WordPress 資料庫。

	3. 更新資料庫，將網域項目變更成新的 Azure App Service 網域。例如，mywordpress.azurewebsites.net。使用[搜尋與取代 WordPress 資料庫指令碼][searchandreplace] (英文) 以安全的方式變更所有執行個體。

4. 在 Azure 入口網站中建立新的 Web 應用程式，並發佈 WordPress 備份。

	1. 在 [Azure Preview 入口網站][mgmtportal]中建立包含資料庫的新 Web 應用程式，方法是使用 [新增] -> [Web + Mobile] -> [Azure Marketplace] -> [Web 應用程式] -> [Web 應用程式 + SQL] (或 [Web 應用程式 + MySQL]) -> [建立]。設定所有必要的設定，來建立空的 Web 應用程式。

	2. 在您的 WordPress 備份中，尋找 **wp-config.php** 檔案，並在編輯器中開啟該檔案。使用新 MySQL 資料庫的資訊來取代下列項目。

		* **DB_NAME** - 資料庫的使用者名稱

		* **DB_USER** - 用來存取資料庫的使用者名稱

		* **DB_PASSWORD** - 使用者密碼

		變更這些項目後，請儲存與關閉 **wp-config.php** 檔案。

	3. 使用[在 Azure App Service 中部署 Web 應用程式][deploy]中的資訊，啟用您想要使用的部署方法，然後將 WordPress 備份部署到 Azure App Service 中您的 Web 應用程式。

5. 在部署 WordPress 網站後，您應能夠使用網站的 *.azurewebsite.net URL 來存取新網站 (作為 App Service 的 Web 應用程式)。

###設定網站

在建立或移轉 WordPress 網站後，請使用下列資訊以提升效能或啟用其他功能。

作法... | 目的...
------------- | -----------
**設定 App Service 計劃模式、大小，以及啟用調整規模** | [在 Azure App Service 中調整 Web 應用程式規模][websitescale]
**啟用持續資料庫連線** <p>WordPress 預設為不會使用持續資料庫連線，因為在多個連線後，此選項會造成資料庫連線進入流速控制狀態。</p> | <ol><li><p>編輯 <strong>wp-includes/wp-db.php</strong> 檔案。</p></li><li><p>尋找下列程式碼行。</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>使用下列程式碼來取代先前的程式碼行。</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>尋找下行。</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>使用下列程式碼來取代先前的程式碼行。</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client_flags ); </code></li><li><p>儲存 <strong>wp-includes/wp-db.php</strong> 檔案，然後重新部署網站。</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>WordPress 更新時可能會覆寫這些變更。</p><p>WordPress 預設為自動更新，若要停用，請編輯 <strong>wp-config.php</strong> 檔案，並新增 <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>另一個處理更新的方法是使用 WebJob 來監視 <strong>wp-db.php</strong> 檔案，然後在每次更新檔案時執行上述修改。如需詳細資訊，請參閱 <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">WebJobs 簡介</a>。</p></div>
**提升效能** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">停用 ARR Cookie</a> (英文) - 在多個 Web Apps 執行個體上執行 WordPress 時可提升效能</p></li><li><p>啟用快取。<a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (預覽) 可以搭配 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 物件快取 WordPress 外掛程式</a>，或使用 <a href="/gallery/store/">Azure 市集</a>中的其中一個快取產品。</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">如何透過 Wincache 使 WordPress 變快</a> (英文) - 預設啟用 Web Apps 的 Wincache </p></li><li><p><a href="../web-sites-scale/">調整 Azure App Service 中的 Web 應用程式</a>，並使用 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 高可用性路由</a>或 <a href="http://www.mysql.com/products/cluster/">MySQL 叢集 CGE</a></p></li></ul> (英文)
**使用 Blob 進行儲存** | <ol><li><p><a href="../storage-create-storage-account/">建立 Azure 儲存體帳戶</a></p></li><li><p>深入了解如何<a href="../cdn-how-to-use/">使用內容發佈網路 (CDN)</a> 來異地發佈儲存在 Blob 中的資料。</p></li><li><p>安裝及設定 <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress 外掛程式</a>。</p><p>如需外掛程式的詳細安裝和設定資訊，請參閱<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">使用者指南</a> (英文)。</p> </li></ol>
**啟用電子郵件** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">使用 Azure 市集啟用 SendGrid </a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">安裝適用於 WordPress 的 SendGrid 外掛程式</a></p></li></ol>
**設定自訂網域名稱** | [在 Azure App Service 中設定自訂網域名稱][customdomain]
**啟用自訂網域名稱的 HTTPS** | [針對 Azure App Service 中的 Web 應用程式啟用 HTTPS][httpscustomdomain]
**負載平衡或異地發佈您的網站** | [使用 Azure 流量管理員路由傳送流量][trafficmanager] (英文)。如果您打算使用自訂網域，請參閱[在 Azure App Service 中設定自訂網域名稱][customdomain]，以取得搭配自訂網域名稱使用流量管理員的相關資訊
**啟用自動備份** | [在 Azure App Service 中備份 Web 應用程式][backup]
**啟用診斷記錄** | [在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能][log]

## 後續步驟

* [WordPress 最佳化](http://codex.wordpress.org/WordPress_Optimization) (英文)

* [在 Azure App Service 中將 WordPress 轉換成多站台](web-sites-php-convert-wordpress-multisite.md)

* [適用於 Azure 的 ClearDB 升級精靈](http://www.cleardb.com/store/azure/upgrade) (英文)

* [將 WordPress 裝載到 Azure App Service 中 Web 應用程式的子資料夾中](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx) (英文)

* [逐步解說：使用 Azure 建立 WordPress 網站](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx) (英文)

* [在 Azure 上裝載現有的 WordPress 部落格](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx) (英文)

* [在 WordPress 中啟用美化的固定連結](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress) (英文)

* [如何在 Azure App Service 上移轉與執行 WordPress 部落格](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/) (英文)

* [如何在 Azure App Service 上免費執行 WordPress](http://architects.dzone.com/articles/how-run-wordpress-azure) (英文)

* [在 2 分鐘內完成在 Azure 上安裝與執行 WordPress](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/) (英文)

* [ WordPress 部落格移至 Azure - 第 1 部：在 Azure 上建立 WordPress 部落格](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1) (英文)

* [ WordPress 部落格移至 Azure - 第 2 部：傳輸內容](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content) (英文)

* [ WordPress 部落格移至 Azure - 第 3 部：設定自訂網域](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain) (英文)

* [ WordPress 部落格移至 Azure - 第 4 部：美化永固定結與 URL 重新寫入規則](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules) (英文)

* [將 WordPress 部落格移至 Azure - 第 5 部分：從子資料夾移至根目錄](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root) (英文)

* [如何在 Azure 帳戶中設定 WordPress Web 應用程式](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/) (英文)

* [在 Azure 上維持 WordPress](http://www.johnpapa.net/wordpress-on-azure/) (英文)

* [在 Azure 上的 WordPress 秘訣](http://www.johnpapa.net/azurecleardbmysql/) (英文)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: ../virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../install-configure-powershell.md
[Azure CLI]: ../xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: ../cdn-how-to-use.md
 

<!---HONumber=62-->