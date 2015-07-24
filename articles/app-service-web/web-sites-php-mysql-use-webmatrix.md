<properties 
	pageTitle="使用 WebMatrix 在 Azure App Service 建立及部署 PHP-MySQL Web 應用程式" 
	description="示範如何使用免費的 WebMatrix IDE 在 Azure App Service 中建立和部署 PHP Web 應用程式將資料儲存在 MySQL 中的教學課程。"
	tags="azure-portal" 
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
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# 使用 WebMatrix 在 Azure App Service 建立及部署 PHP-MySQL Web 應用程式

本教學課程說明如何使用 WebMatrix 來開發 PHP-MySQL 應用程式並部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps。WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含網站開發所需的一切。WebMatrix 支援 PHP 且包含 PHP 開發的 intellisense。

本教學課程假設您已在電腦上安裝 [MySQL][install-mysql]，以便在本機測試應用程式。不過，您也可以在沒有安裝 MySQL 的情況下完成本教學課程。取而代之，您可以將應用程式直接部署至 Azure App Service Web Apps。

完成本指南的步驟後，您將擁有在 Web Apps 上運作的 PHP-MySQL 網站。
 
您將了解：

* 如何使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)在 App Service Web Apps 中建立網站和 MySQL 資料庫。由於預設會在 Web Apps 上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 WebMatrix 開發 PHP 應用程式。
* 如何使用 WebMatrix 將應用程式發佈與重新發佈到 Web Apps。
 
依照本教學課程進行，您將使用 PHP 建立一個簡易的「工作清單」Web 應用程式。此應用程式將裝載於 App Service Web Apps 中。以下螢幕擷取畫面顯示執行中的應用程式：

![Azure PHP Web Site][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

##必要條件

1. [下載][tasklist-mysql-download]「工作清單」應用程式檔案。工作清單應用程式是一個簡單的 PHP 應用程式，可讓您在工作清單中加入、標示完成和刪除項目。工作清單項目會儲存在 MySQL 資料庫中。此應用程式包含下列檔案：

	* **additem.php**：將項目新增至清單。
	* **createtable.php**：為應用程式建立 MySQL 資料表。只會呼叫一次此檔案。
	* **deleteitem.php**：刪除一個項目。
	* **getitems.php**取得資料庫中的所有項目。
	* **index.php**：顯示工作並提供表單將項目加入至清單。
	* **markitemcomplete.php**：將項目的狀態變更為完成。
	* **taskmodel.php**：包含函數讓您在資料庫中加入、取得、更新和刪除項目。

1. 建立名為 `tasklist` 的本機 MySQL 資料庫。您可以在 WebMatrix 中的資料庫工作區 (於下方教學課程中安裝後) 執行此步驟，或使用以下命令從 MySQL 命令提示字元中執行：

		mysql> create database tasklist;

	只有當您想要在本機測試應用程式時，才需要執行此步驟。

## 建立 Web 應用程式和 MySQL 資料庫

請遵循以下步驟來建立 Web 應用程式與 MySQL 資料庫：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下入口網站左下方的 [新增] 圖示。

	![Create New Azure web site](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. 依序按一下 [Web + 行動] 和 [Web 應用程式 + MySQL]。

	![Custom Create a new web site](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. 輸入資源群組的有效名稱。

    ![設定資源群組名稱](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. 輸入新 Web 應用程式的值。

    ![建立 Web 應用程式](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. 輸入新資料庫的值，包括同意法律條款。

	![建立新的 MySQL 資料庫](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	建立 Web 應用程式之後，您將會看到新的資源群組。

## 取得遠端 MySQL 連線資訊

若要連接至在 Web Apps 中執行的 MySQL 資料庫，您需要連線資訊。若要取得 MySQL 連線資訊，請依照以下步驟執行：

1. 從資源群組中，按一下資料庫：

	![選取資料庫](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. 從資料庫摘要中，選取 [屬性]。

    ![選取屬性](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. 記下 `Database`、`Host`、`User Id` 及 `Password` 的值。

    ![注意屬性](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## 在 WebMatrix 中建立您的應用程式

接下來幾個步驟，您將會加入稍早下載的檔案並做一些修改，以開發「工作清單」應用程式。不過，可以加入您自己現有的檔案或建立新的檔案。

1. 啟動 [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/)。如果尚未安裝，請立即安裝。
2. 如果這是您第一次使用 WebMatrix 3，則會提示您登入 Azure。否則，您可以按一下 [登入] 按鈕，然後選擇 [新增帳戶]。選擇使用您的 Microsoft 帳戶來 [登入]。

	![Add Account](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

	![Sign into Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. 在開始畫面上，按一下 [新增] 按鈕，然後選擇 [範本庫]，從範本庫建立新的網站：

	![New site from Template Gallery](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. 從可用的範本中，選擇 [PHP]。

	![Site from template][site-from-template]

5. 選取 [空白網站] 範本。提供網站的名稱，然後按 [下一個]。

	![Provide name for site][site-from-template-2]

	您的網站將在 WebMatrix 中開啟，且備妥一些預設檔案。

	接下來幾個步驟，您將會加入稍早下載的檔案並做一些修改，以開發「工作清單」應用程式。不過，可以加入您自己現有的檔案或建立新的檔案。

6. 按一下 **[新增現有的]** 來新增應用程式檔案：

	![WebMatrix - Add existing files][edit_addexisting]

	在結果對話方塊中，瀏覽至您稍早下載的檔案，全部選取，然後按一下 [開啟舊檔]。出現提示時，選擇取代 `index.php` 檔案。

7. 接下來，您需要將本機 MySQL 資料庫連接資訊加入至 `taskmodel.php` 檔案。按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。(**注意**：如果不要在本機測試應用程式，而想要直接發行至 Azure App Service Web Apps，請跳至 [發行您的應用程式][](#Publish)。)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	儲存 `taskmodel.php` 檔案。

8. 對於要執行的應用程式，需要建立 `items` 資料表。以滑鼠右鍵按一下 `createtable.php` 檔案，然後選取 [在瀏覽器中啟動]。這樣會在瀏覽器中啟動 `createtable.php`，並執行在 `tasklist` 資料庫中建立 `items` 資料表的程式碼。

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. 現在，您可以在本機測試應用程式。以滑鼠右鍵按一下 `index.php` 檔案，然後選取 [在瀏覽器中啟動]。請開始測試應用程式，包括加入項目、將項目標示為完成，然後刪除項目。


## 發行您的應用程式

將應用程式發行至 App Service Web Apps 之前，`taskmodel.php` 中的資料庫連接資訊需要更新為您稍早取得的連接資訊 (在[建立 Web 應用程式和 MySQL 資料庫](#CreateWebsite)這一節中）。

1. 按兩下以開啟 `taskmodel.php` 檔案，然後在 `connect` 函數中更新資料庫連接資訊。

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	儲存 `taskmodel.php` 檔案。

2. 按一下 WebMatrix 中的 [發行]。

	![WebMatrix - Publish][edit_publish]

3. 按一下[從 Windows Azure 選擇現有網站]。

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site.png)

3. 選取您先前建立的 App Service Web 應用程式。

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. 持續按 [繼續] 直到 WebMatrix 將網站發行至 Azure App Service Web Apps 為止。

3. 瀏覽至 http://[your 網站名稱].azurewebsites.net/createtable.php 以建立 `items` 資料表。

4. 最後，瀏覽至 http://[your 網站名稱].azurewebsites.net/index.php 以使用應用程式。
	
##修改和重新發行應用程式

您可以編輯稍早下載的網站本機複本，輕鬆地修改應用程式，然後重新發行，或者，您也可以在「遠端」模式下直接編輯。在此，您將在 `index.php` 檔案中簡單地變更標題，並直接儲存至即時網站。

1. 在 WebMatrix 中按一下 [遠端] 索引標籤，然後選取 [開啟遠端檢視]。這樣會開啟遠端網站來直接編輯。![WebMatrix - Open Remote View][OpenRemoteView]
 
2. 按兩下以開啟 `index.php` 檔案。![WebMatrix - Open index file][Remote_editIndex]

3. 在 **title** 和 **h1** 標籤中，將 **My ToDo List** 變更為 **My Task List**，然後儲存檔案。


4. 儲存完成時，按一下 [執行] 按鈕，在即時網站上查看變更結果。![WebMatrix - Launch site in Remote][Remote_run]


## 後續步驟

* [WebMatrix 網站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com













 

<!---HONumber=62-->