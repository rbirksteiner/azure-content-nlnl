<properties 
	pageTitle="Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體" 
	description="了解如何使用 Python Tools for Visual Studio 建立 Flask Web 應用程式，藉此將資料儲存在 Azure 資料表儲存體中，並部署到 Azure App Service Web Apps。" 
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




# Azure 上採用 Python Tools 2.1 for Visual Studio 的 Flask 和 Azure 資料表儲存體 

在此教學課程中，我們將使用 [Python Tools for Visual Studio]，並使用其中一個 PTVS 範例範本來建立簡單的民調 Web 應用程式。本教學課程也提供[教學影片](https://www.youtube.com/watch?v=qUtZWtPwbTk)。

民調 Web 應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何建立 Azure 儲存體帳戶、如何設定 Web 應用程式以使用 Azure 資料表儲存體，以及如何將 Web 應用程式發佈至 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。

如需更多相關文章 (說明透過使用 Bottle、Flask 和 Django 架構的 PTVS、透過 MongoDB、Azure 資料表儲存體、MySQL 和 SQL Database 服務進行 Azure App Service Web Apps 開發)，請參閱 [Python 開發人員中心]。雖然本文著重於 App Service，但其開發步驟類似於開發 [Azure 雲端服務]。

## 必要條件

 - Visual Studio 2012 或 2013
 - [Python Tools 2.1 for Visual Studio]
 - [Python Tools 2.1 for Visual Studio 範例 VSIX]
 - [Azure SDK Tools for VS 2013] 或 [Azure SDK Tools for VS 2012]
 - [Python 2.7 32 位元]或 [Python 3.4 32 位元]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡，無需承諾。

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。我們將建立虛擬環境並安裝必要的套件。然後將使用預設記憶體內部存放庫，在本機執行應用程式。

1.  在 Visual Studio 中，選取 [檔案]、[新增專案]。

1.  在 [Python]、[範例] 之下可取得 PTVS 範例 VSIX 中的專案範本。選取 [Polls Flask Web Project]，然後按一下 [確定] 以建立專案。

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  系統會提示您安裝外部套件。選取 [安裝到虛擬環境]。

  	![外部套件對話方塊](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  選取 [Python 2.7] 或 [Python 3.4] 作為基礎解譯器。

  	![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  按 `F5` 確認應用程式可運作。根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。當 Web 伺服器停止時，所有資料都會遺失。

1.  按一下 [Create Sample Polls]，然後按一下某項民調並投票。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## 建立 Azure 儲存體帳戶

若要使用儲存體作業，您需要 Azure 儲存體帳戶。您可以依照下列步驟來建立儲存體帳戶。

1.  登入 [Azure 入口網站]。

2. 按一下入口網站左下方的 [新增] 圖示，然後按一下 [資料 + 儲存體] >[ 儲存體]。為儲存體帳戶指定唯一名稱，並為它建立新的[資源群組](../resource-group-overview.md)。

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png) -->建立儲存體帳戶後，[通知] 按鈕便會閃爍綠色 [成功]，儲存體帳戶的刀鋒視窗會開啟，顯示它屬於您所建立的新資源群組。

5. 按一下儲存體帳戶刀鋒視窗中的 [設定] 部分。記下帳戶名稱和金鑰。

	我們在下一節中將需要此資訊來設定您的專案。

## 設定專案

在這一節中，我們會將應用程式設定為使用我們剛才建立的儲存體帳戶。我們將了解如何從 Azure 入口網站取得連線設定。然後會在本機執行此應用程式。

1.  在 Visual Studio 的 [方案總管] 中，在您的專案節點上按一下滑鼠右鍵，然後選取 [屬性]。按一下 [偵錯] 索引標籤。

  	![專案偵錯設定](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  在 [偵錯伺服器命令]、[環境] 中設定應用程式所需的環境變數值。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    此舉會在您 [開始偵錯] 時設定環境變數。如果您想要在 [啟動但不偵錯] 時設定變數，請在 [執行伺服器命令] 下設定相同的值。

    此外，您也可以使用 Windows 控制台定義環境變數。如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 Azure 資料表儲存體儲存機制的程式碼位於 **models/azuretablestorage.py**。如需有關如何從 Python 使用表格服務的詳細資訊，請參閱[說明文件]。

1.  使用 `F5` 執行應用程式。使用 [Create Sample Polls] 建立的民調以及投票所提交的資料將會在 Azure 資料表儲存體中序列化。

1.  瀏覽至 [關於] 頁面，確認應用程式是使用「Azure 資料表儲存體」儲存機制。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## 探索 Azure 資料表儲存體

使用 Visual Studio 中的 [伺服器總管] 即可輕易檢視和編輯儲存體資料表。在這一節中，我們將使用 [伺服器總管] 來檢視民調應用程式資料表的內容。

> [AZURE.NOTE]這需要安裝 Microsoft Azure Tools (可在 [Azure SDK for .NET] 中取得)。

1.  開啟 [伺服器總管]。依序展開 [Azure]、[儲存體]、您的儲存體帳戶和 [資料表]。

  	<!-- ![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png) -->

1.  按兩下 [民調] 或 [選擇] 資料表可在文件視窗中檢視目錄，以及新增/移除/編輯實體。

  	<!-- ![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png) -->

## 將 Web 應用程式發佈至 Azure App Service

Azure .NET SDK 提供簡單的方法將 Web 應用程式部署至 Azure App Service。

1.  按一下 [Microsoft Azure Web Apps]。

1.  按一下 [新增] 以建立新的 Web 應用程式。

1.  填寫下列欄位，然後按一下 [建立]。
	-	Web 應用程式名稱
	-	App Service 計劃
	-	資源群組
	-	區域
	-	讓「資料庫伺服器」維持設定為「沒有資料庫」

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png) -->

1.  接受所有其他預設值並按一下 [發佈]。

1.  您的 Web 瀏覽器將會自動開啟到已發佈的 Web 應用程式。如果您瀏覽至 [關於] 頁面，您會看到它使用 [記憶體內部] 儲存機制，而非 [Azure 資料表儲存體] 儲存機制。

    這是因為 Azure App Service 中未設定 Web Apps 執行個體的環境變數，所以使用在 **settings.py** 中指定的預設值。

## 設定 Web Apps 執行個體

在本節中，我們將設定 Web Apps 執行個體的環境變數。

1.  在 [Azure 入口網站] 中，依序按一下 [瀏覽] > [Web Apps] > Web 應用程式名稱，開啟 Web 應用程式的刀鋒視窗。

1.  在您的 Web 應用程式刀鋒視窗中，按一下 [所有設定] > [應用程式設定]。

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  向下捲動到 [應用程式設定] 區段，並如上一節所述設定 **REPOSITORY_NAME**、**STORAGE_NAME** 和 **STORAGE_KEY** 的值。

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. 依序按一下 [儲存]、[重新啟動] 及 [瀏覽]。

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  透過使用「Azure 資料表儲存體」儲存機制，應該會看到 Web 應用程式如預期般運作。

    恭喜！

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## 後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Flask 和 Azure 資料表儲存體。

- [Python Tools for Visual Studio 說明文件]
  - [Web 專案]
  - [雲端服務專案]
  - [在 Microsoft Azure 上進行遠端偵錯]
- [Flask 說明文件] (英文)
- [Azure 儲存體]
- [Azure SDK for Python]
- [如何從 Python 使用資料表儲存體服務]

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[瀏覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Python 開發人員中心]: /develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs.md
[說明文件]: ../storage-python-how-to-use-table-storage.md
[如何從 Python 使用資料表儲存體服務]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Management Portal]: https://portal.azure.com
[Azure SDK for .NET]: http://azure.microsoft.com/downloads/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 範例 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[Flask 說明文件]: http://flask.pocoo.org/
[在 Microsoft Azure 上進行遠端偵錯]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web 專案]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[雲端服務專案]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure 儲存體]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python
 

<!---HONumber=62-->