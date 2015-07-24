<properties 
	pageTitle="使用 WebMatrix 在 Azure App Service 建立及部署 PHP-SQL Web 應用程式" 
	description="示範如何使用免費的 WebMatrix IDE 在 Azure App Service 中建立和部署 PHP Web 應用程式將資料儲存在 SQL 中的教學課程。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
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





# 使用 WebMatrix 在 Azure App Service 建立及部署 PHP-SQL Web 應用程式

本教學課程說明如何使用 WebMatrix 來開發一個使用 Azure SQL Database 的 PHP 應用程式，並將它部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps。WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含 Web 應用程式開發所需的一切。WebMatrix 支援 PHP 且包含 PHP 開發的 intellisense。

本教學課程假設您已在電腦上安裝 [SQL Server Express][install-SQLExpress]，以便在本機測試應用程式。不過，即使沒有安裝 SQL Server Express，您也可以完成本教學課程。取而代之，您可以將應用程式直接部署至 Azure App Service Web Apps。

完成本指南後，您將會有一個在 Azure 中執行的 PHP-SQL Database Web 應用程式。
 
您將了解：

* 如何使用 [Azure 預覽入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)在 App Service Web Apps 中建立 Web 應用程式和 SQL 資料庫。由於預設會在 Web Apps 上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 WebMatrix 開發 PHP 應用程式。
* 如何使用 WebMatrix 將應用程式發行與重新發行到 Azure。
 
依照本教學課程進行，您將使用 PHP 建立一個簡易的「工作清單」Web 應用程式。此應用程式將裝載於 App Service Web Apps 中。以下螢幕擷取畫面顯示執行中的應用程式：

![Azure PHP Web Site][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

##必要條件

1. [下載][tasklist-sqlazure-download]「工作清單」應用程式檔案。工作清單應用程式是一個簡單的 PHP 應用程式，可讓您在工作清單中加入、標示完成和刪除項目。工作清單項目儲存在 SQL Database 中 (本機測試使用 SQL Server Express)。此應用程式包含下列檔案：

	* **index.php**：顯示工作並提供表單將項目加入至清單。
	* **additem.php**：將項目新增至清單。
	* **getitems.php**：取得資料庫中的所有項目。
	* **markitemcomplete.php**：將項目的狀態變更為完成。
	* **deleteitem.php**：刪除一個項目。
	* **taskmodel.php**：包含函數讓您在資料庫中加入、取得、更新和刪除項目。
	* **createtable.php**：為應用程式建立 SQL 資料表。只會呼叫一次此檔案。

2. 建立名為 `tasklist` 的 SQL Server 資料庫。您可以從 `sqlcmd` 命令提示字元中使用下列命令來建立此資料庫：

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	只有當您想要在本機測試應用程式時，才需要執行此步驟。

## 建立 Web 應用程式和 SQL 資料庫

請遵循以下步驟來建立 Azure Web 應用程式與 SQL Database：

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)。

2. 按一下 [Marketplace] 圖示，或按一下儀表板左下方的 [新增] 圖示，選取 [Web + 行動]，然後選取底部的 [Azure Marketplace] 以開啟 Azure Marketplace。
	
3. 在 Marketplace 中，選取 [Web Apps]。

4. 按一下 [Web 應用程式 + SQL] 圖示。

5. 讀取 Web 應用程式 + SQL 應用程式的描述之後，選取 [建立]。

6. 按一下每個部分 (**資源群組**、[Web 應用程式]、[資料庫] 以及 [訂閱])，然後輸入或選取必填欄位的值：
	
	- 輸入您選擇的 URL 名稱	
	- 設定資料庫伺服器認證
	- 選取最靠近您的區域

	![設定您的應用程式](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. 定義 Web 應用程式完成之後，按一下 [建立]。

	建立 Web 應用程式後，[通知] 按鈕便會閃爍綠色**成功**並開啟資源群組分頁，以顯示群組中的 Web 應用程式與 SQL 資料庫。

4. 按一下資源群組分頁中的 Web 應用程式圖示，開啟 Web 應用程式分頁。

	![Web 應用程式的資源群組](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##取得 SQL Database 連線資訊

若要連線到連結至 Web 應用程式的 SQL Database 執行個體，您將會需要在建立資料庫時所指定的連線資訊。若要取得 SQL Database 連接資訊，請依照下列步驟進行：

1. 回到資源群組分頁，按一下 SQL 資料庫的圖示。

2. 在 SQL 資料庫的分頁中，按一下 [屬性]，然後按一下 [顯示資料庫連接字串]。

	![檢視資料庫屬性](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. 從所產生對話方塊的 [PHP] 區段中，請記下 `Server`、`SQL Database` 和 `User Name` 的值。稍後將 PHP Web 應用程式發行至 Azure 應用程式服務 時，您將使用這些值。

## 在 WebMatrix 中建立您的應用程式

接下來幾個步驟，您將會加入稍早下載的檔案並做一些修改，以開發「工作清單」應用程式。不過，可以加入您自己現有的檔案或建立新的檔案。

1. 啟動 [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/)。如果尚未安裝，請立即安裝。
2. 如果這是您第一次使用 WebMatrix 3，則會提示您登入 Azure。否則，您可以按一下 [登入] 按鈕，然後選擇 [新增帳戶]。選擇使用您的 Microsoft 帳戶來 [登入]。

	![Add Account](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

	![Sign into Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. 在開始畫面上，按一下 [新增] 按鈕，然後選擇 [範本庫]，從範本庫建立新的網站：

	![New site from Template Gallery](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. 從可用的範本中，選擇 [PHP]。

	![Site from template][site-from-template]

5. 選取 [空白網站] 範本。提供網站的名稱，然後按 [下一個]。

	![Provide name for site][site-from-template-2]

3. 如果您已登入 Azure，現在可選擇為您的本機網站建立 Azure App Service Web Apps 執行個體。暫時選擇 [跳過]。

	![Create site on Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. 當 WebMatrix 完成建立本機網站之後，就會顯示 WebMatrix IDE。按一下 **[新增現有的]** 來新增應用程式檔案：

	![WebMatrix - Add existing files][edit_addexisting]

	在結果對話方塊中，瀏覽至您稍早下載的檔案，全部選取，然後按一下 [開啟舊檔]。出現提示時，選擇取代 `index.php` 檔案。

2. 接下來，您需要將本機 SQL Server 資料庫連接資訊加入至 `taskmodel.php` 檔案。按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。(**注意**：如果不要在本機測試應用程式，而想要直接發行至 Azure App Service Web Apps，請跳至 [發行您的應用程式][](#Publish)。)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	儲存 `taskmodel.php` 檔案。

3. 對於要執行的應用程式，需要建立 `items` 資料表。以滑鼠右鍵按一下 `createtable.php` 檔案，然後選取 [在瀏覽器中啟動]。這樣會在瀏覽器中啟動 `createtable.php`，並執行在 `tasklist` 資料庫中建立 `items` 資料表的程式碼。

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. 現在，您可以在本機測試應用程式。以滑鼠右鍵按一下 `index.php` 檔案，然後選取 [在瀏覽器中啟動]。請開始測試應用程式，包括加入項目、將項目標示為完成，然後刪除項目。

<a id="Publish"></a>
## 發行您的應用程式

將應用程式發行至 App Service Web Apps 之前，`taskmodel.php` 中的資料庫連接資訊需要更新為您稍早取得的連接資訊 (在[建立 Web 應用程式和 SQL 資料庫](#CreateWebsite)這一節中）。

1. 按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	儲存 `taskmodel.php` 檔案。

2. 按一下 WebMatrix 中的 [發行]。

	![WebMatrix - Publish][edit_publish]

3. 按一下 [從 Microsoft Azure 選擇現有網站]。

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. 選取您先前建立的 App Service Web 應用程式。

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. 持續按 [繼續] 直到 WebMatrix 將網站發行至 Azure App Service Web Apps 為止。

3. 瀏覽至 http://[your 網站名稱].azurewebsites.net/createtable.php 以建立 `items` 資料表。

4. 最後，瀏覽至 http://[your 網站名稱].azurewebsites.net/index.php 以啟動應用程式。
	
##修改和重新發行應用程式

您可以編輯網站本機複本，輕鬆地修改應用程式，然後重新發行，或者，您也可以在 [遠端] 模式下直接編輯。在此，您將在 `index.php` 檔案中簡單地變更標題，並直接儲存至即時的 App Service Web 應用程式。

1. 在 WebMatrix 中按一下 [遠端] 索引標籤，然後選取 [開啟遠端檢視]。這將開啟您的遠端檔案 (裝載於 Web Apps 上) 以便直接編輯。![WebMatrix - Open Remote View][OpenRemoteView]
 
2. 按兩下以開啟 `index.php` 檔案。![WebMatrix - Open index file][Remote_editIndex]

3. 在 **title** 和 **h1** 標籤中，將 **My ToDo List** 變更為 **My Task List**，然後儲存檔案。


4. 儲存完成時，按一下 [執行] 按鈕，在即時的 App Service Web 應用程式上查看變更結果。![WebMatrix - Launch site in Remote][Remote_run]



## 後續步驟

您已了解如何從 WebMatrix 建立 Web 應用程式並部署至 Azure App Service Web Apps。若要深入了解 WebMatrix，請參閱 [WebMatrix 網站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png

[preview-portal]: https://manage.windowsazure.com








 

<!---HONumber=62-->