<properties 
	pageTitle="在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 FTP 部署" 
	description="示範如何建立 PHP Web 應用程式，將資料儲存於 MySQL 中並對 Azure 使用 FTP 部署的教學課程。" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="tomfitz"/>


#在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 FTP 部署

本教學課程說明如何建立 PHP-MySQL Web 應用程式，以及如何使用 FTP 部署該 Web 應用程式。本教學課程假定您的電腦已經安裝 [PHP][install-php]、[MySQL][install-mysql]、一台 Web 伺服器，以及一個 FTP 用戶端。本教學課程裡的說明可運用在包括 Windows、Mac 與 Linux 的任何作業系統上。看完本指南後，您將擁有可在 Azure 上執行的 PHP/MySQL Web 應用程式。
 
您將了解：

* 如何使用 Azure 入口網站建立 Web 應用程式和 MySQL 資料庫。由於預設會在 Web Apps 上啟用 PHP，因此您不需要執行任何特殊步驟就能執行 PHP 程式碼。
* 如何使用 FTP 將應用程式發行到 Azure。
 
依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊 Web 應用程式。此應用程式將裝載於 Web 應用程式中。完成之應用程式的螢幕擷取畫面如下：

![Azure PHP Web Site][running-app]

>[AZURE.NOTE]如果您想要在註冊 Azure 帳戶之前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您能夠立即在應用程式服務中建立短期的入門 Web 應用程式。不需要信用卡，無需承諾。


##建立 Web 應用程式並設定 FTP 發行

請遵循以下步驟來建立 Web 應用程式與 MySQL 資料庫：

1. 登入 [Azure 入口網站][management-portal]。
2. 按一下入口網站左下方的 [+ 新增] 圖示。

	![Create New Azure Web Site][new-website]

3. 依序按一下 [Web + 行動] 和 [Web 應用程式 + MySQL]。

	![Custom Create a new Web Site][custom-create]

4. 輸入資源群組的有效名稱。

    ![設定資源群組名稱][resource-group]

5. 輸入新 Web 應用程式的值。

     ![建立 Web 應用程式][new-web-app]

6. 輸入新資料庫的值，包括同意法律條款。

	![建立新的 MySQL 資料庫][new-mysql-db]
	
7. 建立 Web 應用程式之後，您將會看到新的資源群組。按一下 Web 應用程式名稱以進行設定。

	![開啟 Web 應用程式][go-to-webapp]

6. 向下捲動，直到您找到 [設定部署認證] 為止。

	![設定部署認證][set-deployment-credentials]

7. 若要啟用 FTP 發行，您必須提供使用者名稱與密碼。儲存認證，並記下您建立的使用者名稱和密碼。

	![Create publishing credentials][portal-ftp-username-password]

##在本機建置及測試您的應用程式

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。先前的註冊者相關資訊會顯示在資料表中。註冊資訊會存放在 MySQL 資料庫。該應用程式包含兩個檔案：

* **index.php**：顯示註冊表單，以及內含註冊者資訊的資料表。
* **createtable.php**：為應用程式建立 MySQL 資料表。只會使用一次此檔案。

若要在本機建置與執行應用程式，請遵循下列步驟。請注意，這些步驟假定您已在本機電腦上安裝 PHP、MySQL 以及 Web 伺服器，而且您已經啟用了[適用 MySQL 的 PDO 延伸功能][pdo-mysql]。

1. 建立名為 `registration` 的 MySQL 資料庫。您可以使用以下命令，從 MySQL 命令提示字元中完成此工作：

		mysql> create database registration;

2. 在 Web 伺服器的根目錄中，建立名為 `registration` 的資料夾，並於其中建立兩個檔案，一個名為 `createtable.php`，另一個名為 `index.php`。

3. 在文字編輯器或 IDE 中開啟 `createtable.php` 檔案，加入下列程式碼。此程式碼將會在 `registration` 資料庫中用於建立 `registration_tbl` 資料表。

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [AZURE.NOTE]您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 MySQL 使用者名稱和密碼。

4. 開啟網頁瀏覽器並瀏覽至 [http://localhost/registration/createtable.php][localhost-createtable]。這會在資料庫中建立 `registration_tbl` 資料表。

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
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [AZURE.NOTE]同樣地，您需要將 <code>$user</code> 和 <code>$pwd</code> 的值，更新為您的本機 MySQL 使用者名稱和密碼。

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

您現在可以瀏覽至 [http://localhost/registration/index.php][localhost-index] 並測試應用程式。

##取得 MySQL 與 FTP 連線資訊

若要連接至在 Web Apps 中執行的 MySQL 資料庫，您需要連線資訊。若要取得 MySQL 連線資訊，請依照以下步驟執行：

1. 從資源群組中，按一下資料庫：

	![選取資料庫][select-database]

2. 從資料庫摘要中，選取 [屬性]。

    ![選取屬性][select-properties]
	
2. 記下 `Database`、`Host`、`User Id` 及 `Password` 的值。

    ![注意屬性][note-properties]

3. 從 Web 應用程式中，按一下頁面右下角的 [下載發行設定檔] 連結：

	![Download publish profile][download-publish-profile]

4. 在 XML 編輯器中開啟 `.publishsettings` 檔案。

3. 尋找含 `<publishProfile >` 的 `publishMethod="FTP"` 元素，其看起來如下：

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
記下 `publishUrl`、`userName` 及 `userPWD` 屬性。

##發佈您的應用程式

當您在本機完成應用程式測試之後，可以使用 FTP 將其發行至您的 Web 應用程式。不過，您必須先更新應用程式中的資料庫連接資訊。使用您稍早取得的資料庫連接資訊 (在＜取得 MySQL 和 FTP 連線資訊＞一節中），將 `createdatabase.php` 和 `index.php` **兩者**檔案中的下列資訊都更新為適當的值：

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

現在您可以使用 FTP 來發佈應用程式。

1. 開啟您選擇的 FTP 用戶端。

2. 將您在以上步驟中從 `publishUrl` 屬性記下的*主機名稱部分*輸入到您的 FTP 用戶端。

3. 將您在以上步驟中記下的 `userName` 與 `userPWD` 屬性，原封不動地輸入到您的 FTP 用戶端。

4. 建立連線。

連線建立之後，您就能視需要上傳與下載檔案。務必將檔案上傳至根目錄，亦即 `/site/wwwroot`。

同時將 `index.php` 與 `createtable.php` 上傳完畢後，瀏覽至 **http://[site 名稱].azurewebsites.net/createtable.php** 為應用程式建立 MySQL 資料表，然後瀏覽至 **http://[site 名稱].azurewebsites.net/index.php** 開始使用該應用程式。
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 

<!---HONumber=62-->