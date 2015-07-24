<properties 
	pageTitle="在 Mac 上搭配 Django 的 Python Web 應用程式 - Azure 教學課程" 
	description="本教學課程說明如何使用 Linux 虛擬機器，在 Azure 上裝載 Django 型網站。" 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>





# Django Hello World Web 應用程式 (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

本教學課程說明如何使用 Linux 虛擬機器在 Microsoft Azure 上裝載 Django 型網站。本教學課程假設您先前沒有使用 Azure 的經驗。完成本指南之後，您將在雲端啟動並執行 Django 型應用程式。

您將了解如何：

* 設定 Azure 虛擬機器以裝載 Django。雖然本教學課程說明如何在 **Linux** 下完成這項程序，不過使用 Azure 中裝載的 Windows Server VM 也可以完成相同的程序。 
* 從 Linux 建立新的 Django 應用程式。

依照本教學課程的步驟，您將建置一個簡單的 Hello World Web 應用程式。該應用程式將裝載於 Azure 虛擬機器中。

完成之應用程式的螢幕擷取畫面如下：

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 建立及設定 Azure 虛擬機器以裝載 Django

1. 按照[此處][portal-vm] (英文) 所提供的指示，建立 *Ubuntu Server 14.04 LTS* 散發套件的 Azure 虛擬機器。

  **注意：**您*只*需要建立虛擬機器。請停在標題為＜如何在建立虛擬機器之後登入＞的小節。

1. 指示 Azure 將連接埠 **80** 的流量從 Web 導向虛擬機器上的連接埠 **80**：
	* 在 Azure 入口網站中瀏覽至新建立的虛擬機器，並按一下 [端點] 索引標籤。
	* 按一下畫面底部的 [新增] 按鈕。![新增端點](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* 開啟 [TCP] 通訊協定的 [公用連接埠 80]，比照 [私人連接埠 80]。![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>設定開發環境

**注意：**如果您需要安裝 Python 或想要使用用戶端程式庫，請參閱 [Python 安裝指南](../python-how-to-install.md)。

Ubuntu Linux VM 已經預先安裝 Python 2.7，但是並未安裝 Apache 或 Django。請按照下列步驟連線至您的 VM 以安裝 Apache 和 Django。

1.  啟動一個新的 [終端機] 視窗。
    
1.  輸入下列命令以連線至 Azure VM。

		$ ssh yourusername@yourVmUrl

1.  輸入下列命令以安裝 Django：

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  輸入下列命令以安裝含 mod-wsgi 的 Apache：

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## 建立新的 Django 應用程式

1.  開啟您在上一節中使用的 [終端機] 視窗，透過 ssh 登入您的 VM。
    
1.  輸入下列命令以建立新的 Django 專案：

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    **django-admin.py** 指令碼會適用於 Django 型網站的基本結構：- **helloworld/manage.py** 可協助您開始裝載及停止裝載 Django 型網站 - **helloworld/helloworld/settings.py** 包含適用於應用程式的 Django 設定。- **helloworld/helloworld/urls.py** 包含每個 URL 與其檢視之間的對應碼。

1.  在 **/var/www/helloworld/helloworld** 目錄中，建立名為 **views.py** 的新檔案。這將包含轉譯 "hello world" 頁面的檢視。啟動您的編輯器並輸入下列程式碼：
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  現在，以下列程式碼取代 **urls.py** 檔案的內容：

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## 設定 Apache

1.  建立 Apache 虛擬主機組態檔 **/etc/apache2/sites-available/helloworld.conf**。設定為以下內容，並務必以您使用之機器的實際 URL (例如 *pyubuntu.cloudapp.net*) 取代 *yourVmUrl*。

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  以下列命令啟用網站：

        $ sudo a2ensite helloworld

1.  以下列命令重新啟動 Apache：

        $ sudo service apache2 reload

1.  最後，在您的瀏覽器中載入網頁：

	![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## 關閉 Azure 虛擬機器

完成本教學課程時，請關閉並/或移除新建立的 Azure 虛擬機器釋出資源供其他教學課程使用，並避免產生 Azure 使用的費用。


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/
 

<!---HONumber=July15_HO1-->