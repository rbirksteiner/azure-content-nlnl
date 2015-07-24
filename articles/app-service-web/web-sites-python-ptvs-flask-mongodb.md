<properties 
	pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 MongoDB" 
	description="了解如何使用 Python Tools for Visual Studio 建立 Flask Web 應用程式，藉此將資料儲存在 MongoDB 資料庫執行個體中，並部署到 Azure App Service Web Apps。" 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="huguesv"/>



# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 MongoDB

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

在此教學課程中，我們將使用 [Python Tools for Visual Studio]，並使用其中一個 PTVS 範例範本來建立簡單的民調 Web 應用程式。本教學課程也提供[教學影片](https://www.youtube.com/watch?v=eql-crFgrAE)。

民調 Web 應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何在 Azure 上使用其中一項裝載的 MongoDB 服務、如何設定 Web 應用程式來使用 MongoDB，以及如何將 Web 應用程式發佈至 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure App Service Web Apps 開發)，請參閱 [Python 開發人員中心]。雖然本文著重於 App Service，但其開發步驟類似於開發 [Azure 雲端服務]。

## 必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX]
 - [Azure SDK Tools for VS 2013] 或 [Azure SDK Tools for VS 2012]
 - [Python 2.7 32 位元]或 [Python 3.4 32 位元]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡，無需承諾。

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。然後將使用預設記憶體內部存放庫，在本機執行應用程式。

1.  在 Visual Studio 中，選取 [檔案]、[新增專案]。 

1.  在 [Python]、[範例] 之下可取得 PTVS 範例 VSIX 中的專案範本。選取 [Polls Flask Web Project]，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  系統會提示您安裝外部套件。選取 [安裝到虛擬環境]。

  	![外部套件對話方塊](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  選取 [Python 2.7] 或 [Python 3.4] 作為基礎解譯器。

  	![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  按 `F5` 確認應用程式可運作。根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。當 Web 伺服器停止時，所有資料都會遺失。

1.  按一下 [Create Sample Polls]，然後按一下某項民調並投票。

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

## 建立 SQL 資料庫

對於資料庫，我們將在 Azure 上建立 MongoLab 主控的資料庫。

或者，您可以為自己建立在 Azure 上執行的虛擬機器，然後自行安裝和管理 MongoDB。

您可以依照下列步驟，透過 MongoLab 建立免費試用版。

1.  登入 [Azure 管理入口網站]。

1.  在瀏覽窗格的底部，按一下 [新增]。

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png) -->

1.  依序按一下 [MARKETPLACE]、[MongoLab]，然後按一下 [下一步]。

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  在 [名稱] 中輸入要用於資料庫服務的名稱。

1.  選擇一個要放置資料庫服務的 [區域]。如果您會從 Azure 應用程式使用資料庫，請選取您會在其中部署應用程式的相同區域。

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  按一下 [下一步]，然後按一下 [購買]。

## 設定專案

在這一節中，我們會將應用程式設定為使用我們剛才建立的 MongoDB 資料庫。我們將了解如何從 Azure 入口網站取得連線設定。然後會在本機執行此應用程式。

1.  在 [Azure 管理入口網站]中，按一下 [MARKETPLACE]，然後按一下您先前建立的 MongoLab 服務。

1.  按一下 [連線資訊]。您可以使用複製按鈕，將 **MONGOLAB_URI** 的值放在剪貼簿上。

  	![連接資訊對話方塊](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  在 Visual Studio 的 [方案總管] 中，在您的專案節點上按一下滑鼠右鍵，然後選取 [屬性]。按一下 [偵錯] 索引標籤。

  	![專案偵錯設定](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  在 [偵錯伺服器命令]、[環境] 中設定應用程式所需的環境變數值。

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    此舉會在您 [開始偵錯] 時設定環境變數。如果您想要在 [啟動但不偵錯] 時設定變數，請在 [執行伺服器命令] 下設定相同的值。

    此外，您也可以使用 Windows 控制台定義環境變數。如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 MongoDB 存放庫的程式碼位於 **models/mongodb.py** 中。

1.  使用 `F5` 執行應用程式。使用 [Create Sample Polls] 建立的民調以及投票所提交的資料將會在 MongoDB 中序列化。

1.  瀏覽至 [關於] 頁面，確認應用程式目前使用 **MongoDB** 存放庫。

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

## 瀏覽 MongoDB 資料庫

您可以使用 [RoboMongo] 之類的應用程式來查詢及編輯 MongoDB 資料庫。在這一節中，我們將使用 RoboMongo 來檢視民調應用程式資料庫的內容。

1.  建立新連線。您會需要我們在上一節中擷取的 **MONGOLAB_URI**。

    注意 URI 的格式：`mongodb://<name>:<password>@<address>:<port>/<name>`

    此名稱符合您使用 Azure 建立服務時輸入的名稱。該名稱同時用於資料庫名稱和使用者名稱。

1.  在連線頁面中，將 [名稱] 設定為任何您要用於連線的名稱。此外，將 [位址] 和 [連接埠] 欄位設定為 **MONGOLAB_URI** 中的 *address* 和 *port*。

  	![連線設定對話方塊](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  在驗證頁面中，將 [資料庫] 和 [使用者名稱] 設定為 **MONGOLAB_URI** 中的 *name*。此外，將 [密碼] 設定為 **MONGOLAB_URI** 中的 *password*。

  	![連線設定對話方塊](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  儲存並連線至資料庫。您現在可以查詢民調集合。

  	![RoboMongo 查詢結果](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

## 將 Web 應用程式發佈至 Azure App Service

PTVS 提供簡單的方法將 Web 應用程式部署至 Azure App Service。

1.  在 [方案總管] 中，以滑鼠右鍵按一下專案節點並選取 [發佈]。

1.  按一下 [Microsoft Azure Web Apps]。

3. 如果您還沒有登入 Azure，請按一下 [登入] 並使用您 Azure 訂用帳戶的 Microsoft 帳戶登入。

2.  按一下 [新增] 以建立新的 Web 應用程式。

1.  填寫下列欄位，然後按一下 [建立]。
	-	Web 應用程式名稱
	-	App Service 計劃
	-	資源群組
	-	區域
	-	讓「資料庫伺服器」維持設定為「沒有資料庫」

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png) -->

1.  接受所有其他預設值並按一下 [發佈]。

1.  您的 Web 瀏覽器將會自動開啟到已發佈的 Web 應用程式。如果您瀏覽至 [關於] 頁面，您會看見網站使用「記憶體內部」存放庫，而非「MongoDB」存放庫。

    這是因為 Azure App Service 中未設定 Web Apps 執行個體的環境變數，所以使用在 **settings.py** 中指定的預設值。

## 設定 Web Apps 執行個體

在本節中，我們將設定 Web Apps 執行個體的環境變數。

1.  在 [Azure 管理入口網站]中，按一下在上一節中建立的 Web 應用程式。

1.  在頂端功能表中，按一下 [設定]。

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  向下捲動到 [應用程式設定] 區段，並如上一節所述設定 **REPOSITORY_NAME**、**MONGODB_HOST** 和 **MONGODB_DATABASE** 的值。

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  在底部的功能表中，依序按一下 [儲存]、[重新啟動] 及 [瀏覽]。

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  您應該會看到應用程式如預期般運作 (使用 **MongoDB** 儲存機制)。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

## 後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Flask 和 MongoDB。

- [Python Tools for Visual Studio 說明文件]
  - [Web 專案]
  - [雲端服務專案]
  - [在 Microsoft Azure 上進行遠端偵錯]
- [Flask 說明文件] (英文)
- [MongoDB]
- [PyMongo 說明文件]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 


<!--Link references-->
[Python 開發人員中心]: /develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure 管理入口網站]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[Flask 說明文件]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[PyMongo 說明文件]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
 

<!---HONumber=62-->