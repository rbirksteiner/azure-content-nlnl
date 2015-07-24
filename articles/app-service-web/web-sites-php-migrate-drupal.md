<properties 
	pageTitle="將 Drupal 移轉至 Azure App Service" 
	description="將 Drupal PHP 網站移轉到 Azure App Service。" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>


# 將 Drupal 移轉至 Azure App Service

因為 Azure App Service 同時支援 PHP 與 MySQL，所以將 Drupal 網站移轉至 Azure App Service Web Apps 相當直接。而且，因為 Drupal 與 PHP 可在任何平台上執行，所以不論您目前的平台為何，此處理程序應可適用於將 Drupal 移至 Azure App Service Web Apps。如此一來，Drupal 安裝可能大不相同，可能有一些未涵蓋於下列資料中的獨特移轉步驟。請注意，因為 Azure App Service 不支援 Drush 工具，所以並未採用。

如果您正在移動大型而複雜的 Drupal 應用程式，另一個選項就是考慮使用 Azure 雲端服務。如需 App Service 與雲端服務之間差異的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure App Service、雲端服務與 VM 使用時機</a>。如需將 Drupal 移至雲端服務的說明，請參閱<a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">將 Drupal 網站從 LAMP 移轉至 Azure</a> (英文)。
 
## 建立 Web 應用程式和 MySQL 資料庫

首先，先完成逐步解說教學課程，瞭解如何使用 MySQL 建立新的 Web 應用程式：[在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 Git 部署][]。如果您打算使用 Git 來發行 Drupal 網站，請遵循教學課程中說明如何設定 Git 儲存機制的步驟。請務必遵循＜**取得遠端 MySQL 連線資訊**＞一節中的指示，因為您稍後會需要該資訊。您可以為了部署 Drupal 網站而忽略其餘的教學課程，但如果這是您第一次使用 Azure App Service (和 Git)，您可能會發現發現其他有用文章。

設定採用 MySQL 資料庫的新 Web 應用程式之後，您現在即可取得 MySQL 資料庫連線資訊和 (選用) Git 儲存機制。下一步是將資料庫複製到 Web 應用程式的 MySQL 資料庫。

## 將資料庫複製到 Web 應用程式的 MySQL 資料庫

將資料庫移轉到 Azure 中的方法有很多種。其中一種適用於 MySQL 資料庫的方法是使用 [MySqlDump][] 工具。下列命令提供如何從本機電腦複製到 Azure 的範例：

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

當然，您必須提供現有 Drupal 資料庫的使用者名稱和密碼。您還必須為您在第一個步驟中建立的 MySQL 資料庫提供主機名稱、使用者名稱、密碼和資料庫名稱。此資訊可在您先前收集的連接字串資訊中取得。連接字串的格式應類似以下字串：

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

視資料庫的大小而定，複製程序可能需要幾分鐘的時間。

您的 Drupal 資料庫現在已存留在 Azure 中。在部署 Drupal 程式碼之前，您必須加以修改，才能連接到新資料庫。

## 修改 settings.php 中的資料庫連線資訊

此時，您再度需要新的資料庫連線資訊。以文字編輯器開啟 **/drupal/sites/default/setting.php** 檔案，接著將 **$databases** 陣列中的 'database'、'username'、'password' 及 'host 的值取代為新資料庫的正確值。完成後，您會取得類似下列的程式碼：

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

儲存 **settings.php** 檔案。您現在即可準備進行部署。

## 使用 Git 或 FTP 部署 Drupal 程式碼

最後一個步驟是使用 Git 或 FTP 將程式碼部署至 Web 應用程式。

如果您使用 FTP，請從 [Azure Preview 入口網站](https://portal.azure.com)的 Web 應用程式刀鋒視窗中取得 FTP 主機名稱和使用者名稱。然後，使用任何 FTP 用戶端將 Drupal 檔案上傳至遠端站台的 **/site/wwwroot** 資料夾。

如果您是使用 Git，您應該已在上述步驟中設定 Git 儲存機制。您必須在本機電腦上安裝 Git。然後，遵循在建立儲存機制之後提供的指示。

> [AZURE.NOTE]視您的 Git 設定而定，您可能必須編輯 .gitignore 檔案 (一個隱藏檔，而且與您執行 Git 認可後在本機根目錄中建立的 .git 資料夾同層級)。此檔案可指定 Drupal 應用程式中可忽略的檔案。如果其中包含應該部署的檔案，請移除這些項目，那麼就不會忽略這些檔案。

將 Drupal 部署至 Web 應用程式之後，您可以透過 Git 或 FTP 繼續部署更新。

## 相關資訊

如需詳細資訊，請參閱下列文章和主題：

- [Azure App Service Web Apps - PHP 觀點][] (英文)
- [Azure App Service、雲端服務與 VM 使用時機][]
- [使用 .user.ini 檔案在 Azure App Service Web Apps 中設定 PHP][] (英文)
- [Azure 整合模組](https://drupal.org/project/azure_auth) (英文)
- [Azure Blob 儲存體模組](https://drupal.org/project/azure_blob) (英文)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

  [在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 Git 部署]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure App Service Web Apps - PHP 觀點]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure App Service、雲端服務與 VM 使用時機]: http://go.microsoft.com/fwlink/?LinkId=310123
  [使用 .user.ini 檔案在 Azure App Service Web Apps 中設定 PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module]: http://drupal.org/project/azure
 

<!---HONumber=62-->