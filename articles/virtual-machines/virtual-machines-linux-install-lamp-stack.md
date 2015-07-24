<properties
	pageTitle="在 Linux 虛擬機器上安裝 LAMP 堆疊"
	description="了解如何在 Azure 中的 Linux 虛擬機器 (VM) 上安裝 LAMP 堆疊。您可以在 Ubuntu 或 CentOS 上進行安裝。"
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="szark"/>



#在 Azure 中的 Linux 虛擬機器上安裝 LAMP 堆疊

LAMP 堆疊由下列不同元素組成：

- **L**inux - 作業系統
- **A**pache - Web 伺服器
- **M**ySQL - 資料庫伺服器
- **P**HP - 程式設計語言


##安裝在 Ubuntu 上

您需要安裝下列封裝：

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

在執行 `apt-get update` 來更新本機封裝清單之後，您接著可以使用單一 `apt-get install` 命令安裝這些封裝：

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

執行上述命令之後，隨即會提示您安裝這些封裝和一些其他相依性。按 'y' 然後按 'Enter' 鍵以繼續進行，並遵循所有其他提示，即可設定 MySQL 的管理密碼。

這會安裝使用 PHP 搭配 MySQL 時所需之最基本的 PHP 擴充功能。請執行下列命令，以查看可以封裝形式提供的其他 PHP 擴充功能：

	# apt-cache search php5


##安裝在 CentOS 和 Oracle Linux 上

您需要安裝下列封裝：

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

您可以使用單一 `yum install` 命令來安裝這些封裝：

	# sudo yum install httpd mysql mysql-server php php-mysql

執行上述命令之後，隨即會提示您安裝這些封裝和一些其他相依性。按 'y' 然後按 'Enter' 鍵以繼續進行。

這會安裝使用 PHP 搭配 MySQL 時所需之最基本的 PHP 擴充功能。請執行下列命令，以查看可以封裝形式提供的其他 PHP 擴充功能：

	# yum search php


## 在 SUSE Linux Enterprise Server 上安裝

您需要安裝下列封裝：

- apache2
- mysql
- apache2-mod_php53
- php53-mysql

您可以使用單一 `zypper install` 命令來安裝這些封裝：

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

執行上述命令之後，隨即會提示您安裝這些封裝和一些其他相依性。按 'y' 然後按 'Enter' 鍵以繼續進行。

這會安裝使用 PHP 搭配 MySQL 時所需之最基本的 PHP 擴充功能。請執行下列命令，以查看可以封裝形式提供的其他 PHP 擴充功能：

	# zypper search php


設定
----------

1. 設定 **Apache**

	- 執行下列命令，以確認 Apache Web 伺服器已開啟：

		- Ubuntu 和 SLES：`sudo service apache2 restart`

		- CentOS 和 Oracle：`sudo service httpd restart`

	- 根據預設，Apache 會接聽連接埠 80。您可能需要開啟端點，才能從遠端存取您的 Apache 伺服器。如需詳細指示，請參閱[設定端點](virtual-machines-set-up-endpoints.md)的文件。

	- 您現在可以檢查看看 Apache 是否正在執行並提供內容。使瀏覽器指向 `http://[MYSERVICE].cloudapp.net`，其中 **[MYSERVICE]** 是您虛擬機器所在的雲端服務名稱。您可能會在一些散發中遇到預設的歡迎網頁，這只代表「網頁是有效的！」。在其他散發中，您可能會看到更完整的網頁，其中具有其他文件的連結，以及設定 Apache 伺服器的內容。

2. 設定 **MySQL**

	- 請注意，這不是 Ubuntu 的必要步驟，已安裝 mysql-server 封裝時，系統會提示您提供 MySQL `root` 密碼。

	- 在其他散發中，執行下列命令以設定 MySQL 的根密碼：

			# mysqladmin -u root -p password yourpassword

	- 然後您就可以使用 `mysql` 或 `mysqladmin` 公用程式管理 MySQL。


##進階閱讀

假設您想要將這些步驟自動化，將應用程式部署到遠端 Linux 虛擬機器？ 您可以使用 Linux CustomScript 延伸模組來執行這個動作。請參閱[使用適用於 Linux 的 Azure CustomScript 延伸模組部署 LAMP 應用程式](virtual-machines-linux-script-lamp.md)。

有許多關於在 Ubuntu 上設定 LAMP 堆疊的其他資源可供參考。

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
 

<!---HONumber=July15_HO1-->