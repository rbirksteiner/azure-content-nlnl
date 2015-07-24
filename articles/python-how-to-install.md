<properties
	pageTitle="安裝 Python 和 SDK - Azure"
	description="了解如何安裝 Python 和搭配 Azure 的 SDK。"
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/05/2015"
	ms.author="huvalo"/>




# 安裝 Python 和 SDK

您可以在 Windows 上輕易地設定 Python；此外，它也預先安裝於 Mac 和 Linux 中。本指南將逐步引導您完成安裝作業，並讓機器做好搭配 Azure 的準備。本指南將提供以下事項的協助：

* Python Azure SDK 含有哪些內容？
* 該使用哪個 Python 和哪個版本
* 在 Windows 上進行安裝
* 在 Mac 和 Linux 上進行安裝

## Python Azure SDK 含有哪些內容？

Azure SDK for Python 內含的元件可讓您開發、部署及管理適用於 Azure 的 Python 應用程式。尤其是 Azure SDK for Python 包含下列各項：

* **適用於 Azure 的 Python 用戶端程式庫**。這些類別庫提供一個用來存取 Azure 功能 (例如儲存體和服務匯流排)，以及管理 Azure 資源 (例如儲存體帳戶、虛擬機器等等) 的介面。
* **Azure 模擬器 (僅限 Windows)**。計算和儲存體模擬器都是雲端服務和資料管理服務的本機模擬器，可讓您在本機測試應用程式。Azure 模擬器只能在 Windows 上執行。

## 該使用哪個 Python 和哪個版本

可用的 Python 解譯器有數種，範例包括：

* CPython - 標準和最常見的 Python 解譯器
* IronPython - 可在 .Net/CLR 上運作的 Python 解譯器
* Jython - 可在 JVM 上運作的 Python 解譯器

只有 Python Azure SDK 和 Azure 服務 (例如網站和雲端服務) 只支援已經過測試的 **CPython**。我們建議的版本為 2.7 或 3.4。

## 可在哪裡取得 Python？

取得 CPython 的方法有數種：

* 直接從 [www.python.org][] 取得
* 從聲譽良好的散發網站取得，如 [www.continuum.io][]、[www.enthought.com][] 或 [www.activestate.com][]
* 從原始碼組建！

除非您有特定的需求，否則我們建議您採用前兩個選項，如下文所述。

## 在 Windows 上進行安裝

對於 Windows，您可以使用 Web Platform Installer 加速安裝。這些包括 [www.python.org][] 提供的 CPython。

* [Microsoft Azure SDK for Python 2.7][]
* [Microsoft Azure SDK for Python 3.4][]

**注意：**在 Windows Server 上，若要下載 WebPI 安裝程式，您可能需要設定 IE ESC 設定 ([開始]/[系統管理工具]/[伺服器管理員]/[本機伺服器]，然後按一下 [**IE 增強式安全性設定**]，將其設定為 [關閉])


### Python 2.7

WebPI 安裝程式提供開發 Python Azure 應用程式所需的任何項目。

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

完成後，您應該可以看見這個確認安裝選擇的畫面：

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

安裝完成後，請在提示字元中輸入 `python` 以確認安裝作業順利完成。由於安裝方法不盡相同，您可能需要設定 "path" 變數才能找到 (正確版本的) Python：

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

安裝完成後，您應該可以在預設位置取用 Python 和用戶端程式庫：

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI 安裝程式提供開發 Python Azure 應用程式所需的任何項目。

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

完成後，您應該可以看見這個確認安裝選擇的畫面：

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

安裝完成後，請在提示字元中輸入 python 以確認安裝作業順利完成。由於安裝方法不盡相同，您可能需要設定 "path" 變數才能找到 (正確版本的) Python：

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

安裝完成後，您應該可以在預設位置取用 Python 和用戶端程式庫：

		C:\Python34\Lib\site-packages\azure


### 取得更多封裝

[Python 套件索引][] (PyPI) 具有選擇性豐富的 Python 程式庫。如果您選擇安裝散發版本，便擁有從 Web 開發到工程運算等多樣化案例中令人感興趣的主要部分。


### Python Tools for Visual Studio

[Python Tools for Visual Studio][] (PTVS) 是 Microsoft 提供的免費/OSS 外掛程式，它能將 VS 轉變為成熟的 Python IDE：

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

您可以選擇是否要使用 PTVS，不過我們建議您使用，因為它能提供 Python 和 Web 專案/方案支援、偵錯、程式碼剖析、互動式視窗、範本編輯和 IntelliSense。

PTVS 也能讓您使用部署至[雲端服務][]和[網站][]的支援，輕鬆部署至 Microsoft Azure。

PTVS 可以和您現有的 Visual Studio 2010、2012 或 2013 安裝一同運作。如需文件、下載項目和相關討論，請參閱 [CodePlex 上的 Python Tools for Visual Studio][]。

## Windows 解除安裝

就傳統上來說，**Azure SDK for Python** WebPI 產品並不是應用程式，而是將多個不同產品 (如 32 位元的 Python 2.7/3.4、適用於 Python 的 Azure 用戶端程式庫等) 一起搭售的集合。這樣的做法導致 WebPI 本身沒有傳統的解除安裝程式，因此您需要從 Windows 控制台分別移除 WebPI 安裝的程式。

如果您想要重新安裝 **Azure SDK for Python**，只要以系統管理員身分開啟 PowerShell 命令提示字元，然後再執行以下命令即可：

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

接著再重新執行 WebPI。

## 在 Linux 和 MacOS 上進行安裝

您的開發電腦很可能已安裝 Python。您可以輸入以下內容來加以查看：

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

我們現在看到這個在 Azure 上執行的 Ubuntu Server 14.04 LTS VM 已安裝 CPython 2.7.6。如果您想要升級，請遵循 OS 的建議套件升級指示。

若要安裝 Python Azure 用戶端程式庫，請使用 **pip** 從 **PyPI** 取得。

如果您沒有安裝 **pip**，請使用此命令來進行安裝：

	curl https://bootstrap.pypa.io/get-pip.py | sudo python

上述命令會以無訊息的方式提示您輸入根密碼。請輸入密碼並按 Enter。

接下來，使用 `pip` 命令來安裝 Python Azure 用戶端程式庫：

	sudo pip install azure

您現在應該能看見用戶端程式庫已安裝在 **site-packages** 下。在 MacOS 上：

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## 適用於 Linux 和 MacOS 的 Python Azure 案例

對於 Linux 或 MacOS，這些是支援的 Azure 主要案例：

1. 使用 Python 的用戶端程式庫取用 Azure 服務

2. 在 Linux VM 上執行應用程式

3. 使用 Git 開發和發佈至 Azure 網站

第一個案例可讓您撰寫透過 Azure REST API 的 Pythonic 包裝函式利用 Azure PaaS 功能 (例如 [Blob 儲存體][]、[佇列儲存體][]、[資料表儲存體][]等) 豐富的 Web 應用程式。這些 Web 應用程式在 Windows、Mac 和 Linux 上的運作方式相同。您也可以從您的本機開發電腦或 Azure 上執行的 Linux VM，使用這些用戶端程式庫。

對於 VM 案例，您只需要啟動選擇的 Linux VM (Ubuntu、CentOS、Suse)，便能執行/管理所需的項目。例如，您可以在 Windows/Mac/Linux 機器上執行 [IPython][] REPL/notebook，然後將瀏覽器指向在 Azure 上執行 IPython Engine 的 Linux 或 Windows 多重處理器 VM。如需詳細資訊，請參閱 [Azure 上的 IPython Notebook][] 教學課程。

如需如何設定 Linux VM 的資訊，請參閱[建立執行 Linux 的虛擬機器][]教學課程。

您可以使用 Git 部署開發 Python Ｗeb 應用程式，並從任何作業系統將其發佈至 Azure 網站中。當您將您的儲存機制推送至 Azure 時，就會自動建立虛擬環境和 pip 安裝所需的封裝。

如需有關開發和發佈 Azure 網站的詳細資訊，請參閱[使用 Django 建立網站][]、[使用 Bottle 建立網站][]和[使用 Flask 建立網站][]教學課程。如需使用任何 WSGI 相容架構的詳細資訊，請參閱[在 Azure 網站上設定 Python][]。


## 其他軟體和資源：

* [Continuum Analytics Python 發佈][]
* [Enthought Python 發佈][]
* [ActiveState Python 發佈][]
* [SciPy - Scientific Python 程式庫套件][]
* [NumPy - Python 的數值程式庫][]
* [Django 專案 - 成熟的 Web 架構/CMS][]
* [IPython - 先進的 Python REPL/Notebook][]
* [Azure 上的 IPython Notebook][]
* [CodePlex 上的 Python Tools for Visual Studio][]


[Continuum Analytics Python 發佈]: http://continuum.io
[Enthought Python 發佈]: http://www.enthought.com
[ActiveState Python 發佈]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - Scientific Python 程式庫套件]: http://www.scipy.org
[NumPy - Python 的數值程式庫]: http://www.numpy.org
[Django 專案 - 成熟的 Web 架構/CMS]: http://www.djangoproject.com
[IPython - 先進的 Python REPL/Notebook]: http://ipython.org
[IPython]: http://ipython.org
[Azure 上的 IPython Notebook]: virtual-machines-python-ipython-notebook.md
[雲端服務]: cloud-services-python-ptvs.md
[網站]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[CodePlex 上的 Python Tools for Visual Studio]: http://pytools.codeplex.com
[Python 套件索引]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[建立執行 Linux 的虛擬機器]: virtual-machines-linux-tutorial.md
[使用 Django 建立網站]: web-sites-python-create-deploy-django-app.md
[使用 Bottle 建立網站]: web-sites-python-create-deploy-bottle-app.md
[使用 Flask 建立網站]: web-sites-python-create-deploy-flask-app.md
[在 Azure 網站上設定 Python]: web-sites-python-configure.md
[資料表儲存體]: storage-python-how-to-use-table-storage.md
[佇列儲存體]: storage-python-how-to-use-queue-storage.md
[Blob 儲存體]: storage-python-how-to-use-blob-storage.md

<!---HONumber=July15_HO1-->