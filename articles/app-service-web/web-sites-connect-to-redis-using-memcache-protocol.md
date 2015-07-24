<properties
   pageTitle="透過 Memcache 通訊協定，將 Azure App Service 中的 Web 應用程式連線到 Redis Cache"
   description="使用 Memcache 通訊協定，將 Azure App Service 中的 Web 應用程式連線到 Redis Cache"
   services="app-service\web"
   documentationCenter="php"
   authors="SyntaxC4"
   manager="wpickett"
   editor="riande"/>

<tags
   ms.service="app-service-web"
   ms.devlang="php"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="windows"
   ms.workload="web"
   ms.date="03/31/2015"
   ms.author="cfowler"/>

# 透過 Memcache 通訊協定，將 Azure App Service 中的 Web 應用程式連線到 Redis Cache

本文說明如何使用 [Memcache][13] 通訊協定，將 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中的 WordPress Web 應用程式連接到 [Azure Redis 快取][12]。如果現有的 Web 應用程式在記憶體內部快取中使用 Memcached 伺服器，則可將它移轉至 Azure App Service，並使用 Microsoft Azure 本身的快取解決方案，而不需要變更您的應用程式程式碼，或只要些許變更。此外，您可以利用現有的 Memcache 專長，在 Azure App Service 中建立高擴充性、分散式應用程式，並於記憶體內部快取中使用 Azure Redis Cache，同時使用常用應用程式架構，例如 .NET、PHP、Node.js、Java 和 Python。

App Service Web 應用程式可讓此應用程式案例使用 Web Apps Memcache 填充碼，它是本機 Memcached 伺服器，可做為 Memcache proxy，快取對於 Azure Redis Cache 的呼叫。這可讓任何使用 Memcache 通訊協定進行通訊的應用程式，以 Redis Cache 快取資料。此 Memcache 填充碼作用於通訊協定層級，因此可供任何應用程式或應用程式架構使用，只要它們使用 Memcache 通訊協定進行通訊。

## 必要條件

Web Apps Memcache 填充碼可以搭配任何應用程式，只要應用程式使用 Memcache 通訊協定進行通訊。在此範例中，參考應用程式是 Scalable WordPress 網站，可以從 Azure Marketplace 佈建。

請遵循這些文章中所述的步驟：

* [佈建 Azure Redis 快取服務的執行個體][1]
* [在 Azure 中部署可調整的 WordPress 網站][0]

部署 Scalable WordPress 網站與佈建 Redis Cache 執行個體之後，就可以準備在 Azure App Service Web 應用程式中啟用 Memcache 填充碼。

## 啟用 Web Apps Memcache 填充碼

若要設定 Memcache 填充碼，您必須建立三個應用程式設定。這可以使用各種不同的方法來達成，包括 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)、[舊的入口網站][3]、[Azure PowerShell Cmdlet][5]，或是 [Azure 命令列介面][5]。基於這篇文章的目的，我將使用 [Azure 入口網站][4]來設定應用程式設定。下列值可擷取自 Redis 快取執行個體的 [設定] 刀鋒視窗。

![Azure Redis 快取設定刀鋒視窗](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### 新增 REDIS_HOST 應用程式設定

您必須建立的第一個應用程式設定是 **REDIS_HOST** 應用程式設定。此設定會設定目的地，供填充碼轉送快取資訊至此目的地。REDIS_HOST 應用程式設定所需要的值，可擷取自 Redis 快取執行個體的 [屬性] 刀鋒視窗。

![Azure Redis 快取主機名稱](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

將應用程式設定的索引鍵設定為 **REDIS_HOST**，應用程式設定的值設定為 Redis 快取執行個體的 [主機名稱]。

![Web 應用程式 AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### 新增 REDIS_KEY 應用程式設定

您必須建立的第二個應用程式設定是 **REDIS_KEY** 應用程式設定。此設定提供安全存取 Redis Cache 執行個體所需的驗證權杖。REDIS_KEY 應用程式設定所需要的值，可擷取自 Redis 快取執行個體的 [存取索引鍵] 刀鋒視窗。

![Azure Redis 快取主要索引鍵](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

將應用程式設定的索引鍵設定為 **REDIS_KEY**，應用程式設定的值設定為 Redis 快取執行個體的 [主要索引鍵]。

![Azure 網站 AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### 新增 MEMCACHESHIM_REDIS_ENABLE 應用程式設定

最後的應用程式設定可用來在 Web 應用程式中啟用 Memcache 填充碼，它將使用 REDIS_HOST 和 REDIS_KEY 來連線到 Azure Redis Cache 並轉送快取呼叫。將應用程式設定的索引鍵設定為 **MEMCACHESHIM_REDIS_ENABLE**，值設定為 **true**。

![Web 應用程式 AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

完成新增三 (3) 項應用程式設定後，按一下 [儲存]。

## 啟用適用於 PHP 的 Memcache 延伸模組

為了讓應用程式得以使用 Memcache 通訊協定，必須安裝適用於 PHP (WordPress 網站語言架構) 的 Memcache 延伸模組。

### 下載 php_memcache 延伸模組

瀏覽至 [PECL][6]，在快取類別底下按一下 [memcache][7]。在下載欄中，按一下 DLL 連結。

![PHP PECL 網站](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

下載具有 Web 應用程式功能 PHP 版本的非執行緒安全 (NTS) x86 連結。(預設為 PHP 5.4)

![PHP PECL 網站 Memcache 封裝](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### 啟用 php_memcache 延伸模組

下載檔案之後，解壓縮並上傳 **php_memcache.dll** 至 **d:\home\site\wwwroot\bin\ext** 目錄。php_memcache.dll 上傳至 Web 應用程式之後，延伸模組必須啟用至 PHP 執行階段。若要在 Azure 入口網站中啟用 Memcache 延伸模組，請開啟 Web 應用程式的 [應用程式設定] 刀鋒視窗，然後新增索引鍵為 **PHP_EXTENSIONS** 且值為 **bin\ext\php_memcache.dll** 的應用程式設定。


> 如果 Web 應用程式需要載入多個 PHP 延伸模組，PHP_EXTENSIONS 的值應該是 DLL 檔案相對路徑以逗號分隔的清單。

![Web 應用程式 AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

完成後，按一下 [儲存]。

## 安裝 Memcache WordPress 外掛程式

在 WordPress 外掛程式頁面上，按一下 [新增] 按鈕。

![WordPress 外掛程式頁面](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

在搜尋方塊中，輸入「memcached」並按 **Enter** 鍵。

![WordPress 新增新的外掛程式](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

在清單中尋找 **Memcached Object Cache**，然後按一下 [立即安裝] 按鈕。

![WordPress 安裝 Memcache 外掛程式](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### 啟用 Memcache WordPress 外掛程式

>[AZURE.NOTE]遵循此部落格中有關[如何啟用 Web Apps 中的網站延伸模組][8] (英文) 的指示，以安裝 Visual Studio Online。

在 `wp-config.php` 檔案中，在檔案結尾附近停止編輯註解的上方，新增下列程式碼片段。

```php
$memcached_servers = array(
	'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

貼上此程式碼片段之後，monaco 將會自動儲存文件。

下一個步驟是啟用 object-cache 外掛程式。將 **object-cache.php** 從 **wp-content/memcached** 資料夾拖放到 **wp-content** 資料夾即可啟用 Memcache Object Cache 功能。

![尋找 memcache object-cache.php 外掛程式](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

現在 **object-cache.php** 檔案已在 **wp-content** 資料夾中，代表現在已啟用 Memcached 物件快取。

![啟用 memcache object-cache.php 外掛程式](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## 確認 Memcache 物件快取外掛程式正常運作

啟用 Web 應用程式 Memcache 填充碼的所有步驟現在皆已完成。剩下的唯一一件事，就是要確認資料會填入 Redis Cache 執行個體。

### 啟用 Azure Redis Cache 中的非 SSL 連接埠支援

>[AZURE.NOTE]撰寫本文時，Redis CLI 不支援 SSL 連線，因此需要下列步驟。

在 Azure 入口網站中，瀏覽至您為此 Web 應用程式建立的 Redis Cache 執行個體。開啟快取的刀鋒視窗後，按一下 [設定] 圖示。

![Azure Redis 快取設定按鈕](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

從清單中選取 [存取連接埠]。

![Azure Redis 快取存取連接埠](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

針對 [只允許透過 SSL 存取]，按一下 [否]。

![Azure Redis 快取存取連接埠僅限 SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

您將會看到現在已設定非 SSL 連接埠。按一下 [儲存]。

![Azure Redis 快取 Redis 存取入口網站非 SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### 從 redis cli 連線至 Azure Redis Cache

>[AZURE.NOTE]這個步驟假設 redis 安裝在開發電腦的本機上。[使用這些指示，在本機上安裝 Redis][9]。

開啟選擇的命令列主控台，並輸入下列命令：

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

以實際的 xxxxx.redis.cache.windows.net 主機名稱取代 **<hostname-for-redis-cache>**，並以快取的存取索引鍵取代 **<primary-key-for-redis-cache>**，然後按 **Enter**。CLI 連線至 Redis Cache 執行個體後，發出任何 redis 命令。在下列螢幕擷取畫面中，我選擇列示索引鍵。

![從終端機中的 Redis CLI 連接至 Azure Redis 快取](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

列出索引鍵呼叫應會傳回一個值。如果沒有，請試著移至 Web 應用程式並再試一次。

## 結論

恭喜！ WordPress 應用程式現在已經有一個集中式記憶體內部快取，可協助不斷增加的流量。請記住，Web 應用程式 Memcache 填充碼可以搭配任何 Memcache 用戶端，而不論使用何種程式設計語言或應用程式架構。若要提供意見反應或詢問有關 Web Apps Memcache 填充碼的問題，請張貼至 [MSDN 論壇][10]或 [Stackoverflow][11]。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


[0]: http://bit.ly/1F0m3tw
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org
 

<!---HONumber=62-->