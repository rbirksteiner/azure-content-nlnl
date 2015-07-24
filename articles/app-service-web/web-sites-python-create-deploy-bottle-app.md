<properties 
	pageTitle="在 Azure 中使用 Bottle 建立 Python Web 應用程式" 
	description="介紹在 Azure App Service Web Apps 中執行 Python Web 應用程式的教學課程。" 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# 在 Azure 中使用 Bottle 建立 Web 應用程式

本教學課程說明如何在 Azure App Service Web Apps 上開始執行 Python。Web Apps 提供有限的免費裝載和快速部署，而您可以使用 Python！ 隨著應用程式規模增加，您可以切換為付費主控，也可以與其他所有 Azure 服務整合。

您將使用 Bottle Web 架構建立 Web 應用程式 (請參閱本教學課程的其他版本中有關 [Django](web-sites-python-create-deploy-django-app.md) 和 [Flask](web-sites-python-create-deploy-flask-app.md) 的說明)。您會從 Azure Marketplace 建立 Web 應用程式、設定 Git 部署，並於本機複製儲存機制。接著您將在本機執行 Web 應用程式、進行變更、認可及推送至 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。本教學課程示範如何從 Windows 或 Mac/Linux 執行這項操作。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 必要條件

- Windows、Mac 或 Linux
- Python 2.7 或 3.4
- setuptools、pip、virtualenv (僅 Python 2.7)
- Git
- [Python Tools 2.1 for Visual Studio](optional)

**注意**：Python 專案目前不支援 TFS 發佈。

### Windows

如果您還沒有安裝 Python 2.7 或 3.4 (32 位元)，建議您使用 Web Platform Installer 安裝 [Azure SDK for Python 2.7] 或 [Azure SDK for Python 3.4]。這會安裝 32 位元版本的 Python、setuptools、pip、virtualenv 等 (32 位元 Python 安裝於 Azure 主機電腦上)。或者，您可以從 [python.org] 取得 Python。

針對 Git，建議您安裝 [Git for Windows] 或 [GitHub for Windows]。如果您使用 Visual Studio，您可以使用整合式的 Git 支援。

我們也建議您安裝 [Python Tools 2.1 for Visual Studio]。這是選擇性的，但如果您有 [Visual Studio] (包含免費的 Visual Studio Community 2013 或 Visual Studio Express 2013 for Web)，它會提供您絕佳的 Python IDE。

### Mac/Linux

您應該已經安裝 Python 與 Git，但請確定您擁有的是 Python 2.7 或 3.4。


## 在 Azure Preview 入口網站上建立 Web 應用程式

建立應用程式的第一步是透過 [Azure Preview 入口網站](https://portal.azure.com)建立 Web 應用程式。

1. 登入 Azure Preview 入口網站，並按一下左下角的 [新增] 按鈕。 
2. 按一下 [Web + 行動] > [Azure Marketplace] > [Web Apps]。
3. 在搜尋方塊中，輸入 "python"。
4. 在搜尋結果中，選取 [Bottle]，然後按一下 [建立]。
5. 設定新的 Bottle 應用程式，例如為其建立新的 App Service 方案和新的資源群組。然後按一下 [建立]。
6. 遵循[在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)的以下指示，為您新建立的 Web 應用程式設定 Git 發行功能。
 
## 應用程式概觀

### Git 儲存機制內容

以下是您會在初始的 Git 儲存機制中找到的檔案概觀，我們將在下一節中複製。

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

應用程式的主要來源。包含 3 個主要的版面配置頁面 (index、about、contact)。靜態內容和指令碼，包含啟動程序、jquery、modernizr 和回應。

    \app.py

本機開發伺服器支援。使用此項在本機執行應用程式。

    \BottleWebProject.pyproj
    \BottleWebProject.sln

搭配 [Python Tools for Visual Studio] 使用的專案檔。

    \ptvs_virtualenv_proxy.py

虛擬環境和 PTVS 遠端偵錯支援的 IIS Proxy。

    \requirements.txt

此應用程式所需的外部封裝。部署指令碼將 pip 安裝在這個檔案中所列的封裝。
 
    \web.2.7.config
    \web.3.4.config

IIS 組態檔。部署指令碼會使用適當的 web.x.y.config，並將它複製為 web.config。

### 選用的檔案 - 自訂部署

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### 選用的檔案 - Python 執行階段

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### 在伺服器上的其他檔案

某些檔案存在於伺服器上，但未加入至 Git 儲存機制。這些檔案由部署指令碼建立。

    \web.config

IIS 組態檔。從 web.x.y.config 建立於每個部署上。

    \env\

Python 虛擬環境。如果 Web 應用程式上不存在相容的虛擬環境，會於部署期間建立。requirements.txt 中所列封裝為 pip 安裝，但是如果封裝已安裝，pip 會跳過安裝。

接下來的 3 小節會說明如何在 3 個不同環境中繼續進行 Web 應用程式開發：

- Windows，使用 Python Tools for Visual Studio
- Windows，使用命令列
- Mac/Linux，使用命令列


## Web 應用程式開發 - Windows - 適用於 Visual Studio 的 Python 工具

### 複製儲存機制

首先，使用 Azure Preview 入口網站上提供的 URL 複製儲存機制。如需詳細資訊，請參閱[在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)。

開啟包含在儲存機制根目錄中的方案檔 (.sln)。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### 建立虛擬環境

現在我們要建立本機開發的虛擬環境。以滑鼠右鍵按一下 [Python 環境]，選取 [新增虛擬環境...]。

- 請確定環境的名稱是 `env`。

- 選取基礎解譯器。確認使用針對您 Web 應用程式選取的 Python 版本 (在 runtime.txt 中，或在 Azure Preview 入口網站中您 Web 應用程式的 [應用程式設定] 刀鋒視窗中) 相同的版本。

- 確定已勾選下載並安裝封裝的選項。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

按一下 [建立]。這會建立虛擬環境，並安裝 requirements.txt 中列出的相依性。

### 使用開發伺服器來執行

按 F5 開始偵錯，並且網頁瀏覽器會自動開啟在本機執行的頁面。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

您可以在來源中設定中斷點、使用監看式視窗等等。如需各種功能的詳細資訊，請參閱 [PTVS 文件][]。

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### 安裝更多封裝

您的應用程式可能會擁有 Python 和 Bottle 之外的相依性。

您可以使用 pip 安裝其他封裝。若要安裝封裝，以滑鼠右鍵按一下虛擬環境，然後選取 [安裝 Python 封裝]。

例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入 `azure`：

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

以滑鼠右鍵按一下虛擬環境，然後選取 [產生 requirements.txt] 更新 requirements.txt。

然後，將變更認可到 Git 儲存機制的 requirements.txt。

### 部署至 Azure

若要觸發部署，按一下 [同步] 或 [推送]。同步處理會推送和提取。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

第一次部署將需要一些時間，因為它會建立虛擬環境、安裝封裝等。

Visual Studio 不會顯示部署進度。如果您想要檢閱輸出，請參閱[疑難排解 - 部署](#troubleshooting-deployment)一節。

瀏覽至 Azure URL，以檢視您的變更。


## Web 應用程式開發 - Windows - 命令列

### 複製儲存機制

首先，使用 Azure Preview 入口網站上提供的 URL 複製儲存機制，並將 Azure 儲存機制加入為遠端。如需詳細資訊，請參閱[在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 建立虛擬環境

我們要建立開發用途的新虛擬環境 (不加入至儲存機制)。Python 虛擬環境不可重置，因此每位使用該應用程式的開發人員都會在本機建立。

確認使用針對您 Web 應用程式選取的 Python 版本 (在 runtime.txt 中，或在 Azure Preview 入口網站中您 Web 應用程式的 [應用程式設定] 刀鋒視窗中) 相同的版本。

針對 Python 2.7：

    c:\python27\python.exe -m virtualenv env

針對 Python 3.4：

    c:\python34\python.exe -m venv env

安裝應用程式所需的任何外部封裝。您可以在儲存機制的根目錄使用 requirements.txt 檔案，在虛擬環境中安裝封裝：

    env\scripts\pip install -r requirements.txt

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env\scripts\python app.py

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝更多封裝

您的應用程式可能會擁有 Python 和 Bottle 之外的相依性。

您可以使用 pip 安裝其他封裝。例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入：

    env\scripts\pip install azure

請務必更新 requirements.txt：

    env\scripts\pip freeze > requirements.txt

認可變更：

    git add requirements.txt
    git commit -m "Added azure package"

### 部署至 Azure

若要觸發部署，將變更推送至 Azure：

    git push azure master

您會看到部署指令碼的輸出，包括虛擬環境建立、安裝封裝、建立 web.config。

瀏覽至 Azure URL，以檢視您的變更。


## Web 應用程式開發 - Mac/Linux - 命令列

### 複製儲存機制

首先，使用 Azure Preview 入口網站上提供的 URL 複製儲存機制，並將 Azure 儲存機制加入為遠端。如需詳細資訊，請參閱[在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 建立虛擬環境

我們要建立開發用途的新虛擬環境 (不加入至儲存機制)。Python 虛擬環境不可重置，因此每位使用該應用程式的開發人員都會在本機建立。

確認使用針對您 Web 應用程式選取的 Python 版本 (在 runtime.txt 中，或在 Azure Preview 入口網站中您 Web 應用程式的 [應用程式設定] 刀鋒視窗內) 相同的版本。

針對 Python 2.7：

    python -m virtualenv env

針對 Python 3.4：

    python -m venv env

安裝應用程式所需的任何外部封裝。您可以在儲存機制的根目錄使用 requirements.txt 檔案，在虛擬環境中安裝封裝：

    env/bin/pip install -r requirements.txt

### 使用開發伺服器來執行

您可以使用下列命令，在開發伺服器下啟動應用程式：

    env/bin/python app.py

主控台會顯示 URL，以及伺服器接聽的通訊埠：

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

然後，開啟網頁瀏覽器指向該 URL。

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### 進行變更

現在您可以嘗試對應用程式來源和/或範本進行變更。

測試過您的變更之後，請將它們認可至 Git 儲存機制：

    git add <modified-file>
    git commit -m "<commit-comment>"

### 安裝更多封裝

您的應用程式可能會擁有 Python 和 Bottle 之外的相依性。

您可以使用 pip 安裝其他封裝。例如，若要安裝 Azure SDK for Python，讓您可存取 Azure 儲存體、服務匯流排和其他 Azure 服務，請輸入：

    env/bin/pip install azure

請務必更新 requirements.txt：

    env/bin/pip freeze > requirements.txt

認可變更：

    git add requirements.txt
    git commit -m "Added azure package"

### 部署至 Azure

若要觸發部署，將變更推送至 Azure：

    git push azure master

您會看到部署指令碼的輸出，包括虛擬環境建立、安裝封裝、建立 web.config。

瀏覽至 Azure URL，以檢視您的變更。


## 疑難排解 - 封裝安裝

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## 疑難排解 - 虛擬環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## 後續步驟

請遵循下列連結以深入了解 Bottle 和 Python Tools for Visual Studio：
 
- [Bottle 說明文件]
- [Python Tools for Visual Studio 說明文件]

如需有關使用 Azure 資料表儲存體和 MongoDB 的資訊：

- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 MongoDB]
- [Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 Azure 資料表儲存體]

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 MongoDB]: web-sites-python-ptvs-bottle-table-storage.md
[Azure 上採用 Python Tools 2.1 for Visual Studio 的 Bottle 和 Azure 資料表儲存體]: web-sites-python-ptvs-bottle-mongodb.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[PTVS 文件]: http://pytools.codeplex.com/documentation
[Python Tools for Visual Studio 說明文件]: http://pytools.codeplex.com/documentation
[Bottle 說明文件]: http://bottlepy.org/docs/dev/index.html
 

<!---HONumber=62-->