<properties
	pageTitle="開始使用 SQL Database"
	description="使用 Azure 入口網站和 AdventureWorks 範例資料庫，幾分鐘內即可以 Azure SQL Database (雲端中的 Microsoft 關聯式資料庫管理服務 (RDBMS))，建立您的第一個的雲端資料庫。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="genemi"/>


# 建立您的第一個 Azure SQL Database


本文將說明如何在 5 分鐘內建立一個範例 Azure SQL Database。您會學到如何：


- 使用 [Azure 入口網站](http://portal.azure.com/)佈建邏輯伺服器。
- 建立填入範例資料的資料庫。
- 設定資料庫的防火牆規則，以設定哪些 IP 位址可以存取您的資料庫。


本教學課程假設您已經有 Azure 訂用帳戶。如果您沒有訂用帳戶，可以註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/)。


## 步驟 1：登入


1. 登入 [Azure 入口網站](http://portal.azure.com/)。
2. 按一下 [新增] > [資料 + 儲存體] > [SQL Database]。


![New SQL Database][1]


## 步驟 2：建立邏輯伺服器



1. 在 [SQL Database] 刀鋒視窗中，選擇資料庫的**名稱**，在此範例中為 **AdventureWorks**。
2. 若要建立資料庫的邏輯伺服器，請依序按一下 [伺服器]、[建立新伺服器]。


## 步驟 3：設定伺服器


1. 在 [伺服器] 刀鋒視窗的 [伺服器名稱] 中輸入您可以輕鬆記住的唯一名稱。
2. 在 [伺服器系統管理員登入] 中輸入 **AdventureAdmin**。
3. 在 [密碼] 和 [確認密碼] 中輸入正確的值。
4. 選取慣用的地理 [位置]。通常此位置應該是接近您執行應用程式的地方。
5. 按一下 [確定]。


![建立伺服器][2]


## 步驟 4：建立資料庫


1. 在 [SQL Database] 刀鋒視窗中，按一下 [選取來源] 以指定資料庫的來源。
 - 如果您略過此步驟，則會建立空的資料庫。
2. 選取 [範例]。
 - 這會建立名為 **AdventureWorks** 的標準範例資料庫的複本資料庫 。
 - 在 Azure SQL Database 上使用的是「輕型結構描述」版的 AdventureWorks。
3. 按一下刀鋒視窗底部的 [建立]。


## 步驟 5：設定防火牆


下列步驟示範如何指定可存取您資料庫的 IP 位址範圍。


![瀏覽伺服器][3]


1. 在畫面左側功能區上，依序按一下 [瀏覽]、[SQL Server]。
2. 從可用的選項中，按一下您稍早建立的 SQL Server。
3. 依序按一下 [設定]、[防火牆]。
4. 按一下此連結以從 [Bing](http://www.bing.com/search?q=my%20ip%20address) 取得您目前的 IP 位址。
5. 在 [防火牆設定] 中，輸入 [規則名稱]，然後將上一步取得的公用 IP 位址，貼入 [起始 IP] 和 [結束 IP] 欄位。
6. 完成後，按一下頁面頂端的 [儲存]。


![防火牆][4]


## 後續步驟


您現在已準備好撰寫小型用戶端程式，以連接到您的資料庫。如需快速入門的程式碼範例，請按以下連結之一：


- [使用 C# 連接及查詢您的 SQL Database](sql-database-connect-query.md)
- 敬請期待：SQL Database 的用戶端開發和快速入門範例


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=62-->