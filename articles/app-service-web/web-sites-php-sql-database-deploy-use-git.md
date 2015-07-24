<properties 
	pageTitle="建立 PHP-SQL Web 應用程式並使用 Git 部署至 Azure App Service" 
	description="示範如何建立 PHP Web 應用程式將資料儲存於 Azure SQL Database 以及使用 Git 部署至 Azure App Service 的教學課程。" 
	services="app-service\web, sql-database" 
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

# 建立 PHP-SQL Web 應用程式並使用 Git 部署至 Azure App Service

本教學課程會示範如何在 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中建立連線到 Azure SQL Database 的 PHP Web 應用程式，以及如何使用 Git 來部署它。本教學課程假設您的電腦上已安裝 [PHP][install-php]、[SQL Server Express][install-SQLExpress]、[Microsoft Drivers for SQL Server for PHP](http://www.microsoft.com/download/en/details.aspx?id=20098)、Web 伺服器和 [Git][install-git]。完成本指南的步驟後，您將擁有在 Azure 上運作的 PHP-SQL Web 應用程式。

> [AZURE.NOTE]您可以使用 [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 來安裝和設定 PHP、SQL Server Express、適用於 SQL Server for PHP 的 Microsoft 驅動程式和 Internet Information Services (IIS)。

您將了解：

* 如何使用 [Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)建立 Azure Web 應用程式和 SQL Database。由於預設會在 App Service Web Apps 上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 Git 來發行與重新發行應用程式到 Azure。
 
依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊網頁應用程式。該應用程式將在 Azure 網站中託管。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP Web Site](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

##建立 Azure Web 應用程式並設定 Git 發行功能

請遵循以下步驟來建立 Azure Web 應用程式與 SQL Database：

1. 登入 [Azure 預覽入口網站](https://portal.azure.com/)。

2. 按一下 [Marketplace] 圖示，或按一下儀表板左下方的 [新增] 圖示，選取 [Web + 行動]，然後選取底部的 [Azure Marketplace] 以開啟 Azure Marketplace。
	
3. 在 Marketplace 中，選取 [Web Apps]。

4. 按一下 [Web 應用程式 + SQL] 圖示。

5. 讀取 Web 應用程式 + SQL 應用程式的描述之後，選取 [建立]。

6. 按一下每個部分 (**資源群組**、[Web 應用程式]、[資料庫] 以及 [訂閱])，然後輸入或選取必填欄位的值：
	
	- 輸入您選擇的 URL 名稱	
	- 設定資料庫伺服器認證
	- 選取最靠近您的區域

	![設定您的應用程式](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. 定義 Web 應用程式完成之後，按一下 [建立]。

	建立 Web 應用程式後，[通知] 按鈕便會閃爍綠色**成功**並開啟資源群組分頁，以顯示群組中的 Web 應用程式與 SQL 資料庫。

4. 按一下資源群組分頁中的 Web 應用程式圖示，開啟 Web 應用程式分頁。

	![Web 應用程式的資源群組](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. 按一下 [設定連續部署**]**** > [選擇來源]**。選取 [本機 Git 儲存機制]，按一下 [確定]。

	![where is your source code](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

	如果您從未設定 Git 儲存機制，則必須提供使用者名稱和密碼。若要這樣做，請按一下 Web 應用程式刀鋒視窗中的 [設定部署認證]。

	![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. [設定連續部署] 會變成 [找不到部署]。按一下以查看稍後部署 PHP 應用程式時需要使用的 Git 遠端 URL。

##取得 SQL Database 連線資訊

若要連線到連結至 Web 應用程式的 SQL Database 執行個體，您將會需要在建立資料庫時所指定的連線資訊。若要取得 SQL Database 連接資訊，請依照下列步驟進行：

1. 回到資源群組分頁，按一下 SQL 資料庫的圖示。

2. 在 SQL 資料庫的分頁中，按一下 [屬性]，然後按一下 [顯示資料庫連接字串]。

	![檢視資料庫屬性](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
	
3. 從所產生對話方塊的 [PHP] 區段中，請記下 `Server`、`SQL Database` 和 `User Name` 的值。稍後將 PHP Web 應用程式發行至 Azure App Service 時，您將使用這些值。

##在本機建置與測試您的應用程式

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。先前的註冊者相關資訊會顯示在資料表中。註冊資訊會儲存在 SQL Database 執行個體中。該應用程式包含兩個檔案 (複製/貼上以下提供的程式碼)：

* **index.php**：顯示註冊表單，以及內含註冊者資訊的資料表。
* **createtable.php**：為應用程式建立 SQL 資料表。只會使用一次此檔案。

若要在本機執行應用程式，請遵循下列步驟。請注意，這些步驟假設您的本機電腦上已設定 PHP、SQL Server Express 和 Web 伺服器，且您已啟用 [SQL Server 的 PDO 延伸模組][pdo-sqlsrv]。

1. 建立名為 `registration` 的 SQL Server 資料庫。您可以從 `sqlcmd` 命令提示字元中使用下列命令來建立此資料庫：

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. 在 Web 伺服器的根目錄中，建立名為 `registration` 的資料夾，並於其中建立兩個檔案，一個名為 `createtable.php`，另一個名為 `index.php`。

3. 在文字編輯器或 IDE 中開啟 `createtable.php` 檔案，加入下列程式碼。此程式碼將會在 `registration` 資料庫中用於建立 `registration_tbl` 資料表。

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	請注意，您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 SQL Server 使用者名稱和密碼。

4. 開啟網頁瀏覽器，並瀏覽到 **http://localhost/registration/createtable.php**。這會在資料庫中建立 `registration_tbl` 資料表。

5. 在文字編輯器或 IDE 中開啟 **index.php** 檔案，加入頁面的基本 HTML 和 CSS 程式碼 (稍後的步驟中將加入 PHP 程式碼)。

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. 在 PHP 標籤內，加入用來連線至資料庫的 PHP 程式碼。

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    同樣地，您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 MySQL 使用者名稱和密碼。

7. 在資料庫連接程式碼後面，加入可將登錄資訊插入至資料庫的程式碼。

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. 最後，在上述程式碼後面，加入可從資料庫擷取資料的程式碼。

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

您現在可以瀏覽至 **http://localhost/registration/index.php** 測試應用程式。

##發行您的應用程式

當您在本機完成應用程式測試之後，就可以使用 Git 將其發行至 App Service Web Apps。不過，您必須先更新應用程式中的資料庫連接資訊。使用您稍早取得的資料庫連線資訊 (在＜取得 SQL Database 連線資訊＞一節中)，將 `createdatabase.php` 和 `index.php`「兩者」檔案中的下列資訊都更新為適當的值：

	// DB connection info
	$host = "tcp:<value of Server>";
	$user = "<value of User Name>";
	$pwd = "<your password>";
	$db = "<value of SQL Database>";

> [AZURE.NOTE]在 <code>$host</code> 中，伺服器的值的前面必須加上 <code>tcp:</code>。


現在，您可以準備設定 Git 發行，並發行應用程式。

> [AZURE.NOTE]這些步驟與前述＜建立 Azure Web 應用程式並設定 Git 發行功能＞一節中最後面的步驟相同。


1. 開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄 (**registration** 目錄)，並執行下列命令：

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	系統會提示您輸入先前建立的密碼。

2. 瀏覽至 **http://[web 應用程式名稱].azurewebsites.net/createtable.php**，建立應用程式的 SQL Database 資料表。
3. 瀏覽至 **http://[web 應用程式名稱].azurewebsites.net/index.php**，開始使用應用程式。

發行應用程式之後，您可以開始對其進行變更，並使用 Git 來發行它們。

##將變更發行至您的應用程式

若要將變更發行至應用程式，請依照以下步驟進行：

1. 在本機對您的應用程式進行變更。
2. 開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

		git add .
		git commit -m "comment describing changes"
		git push azure master

	系統會提示您輸入先前建立的密碼。

3. 瀏覽至 **http://[web 應用程式名稱].azurewebsites.net/index.php** 以查看您的變更。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
 

<!---HONumber=62-->