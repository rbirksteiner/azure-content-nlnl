<properties
	pageTitle="如何使用 Microsoft Azure 建立 LAMP 堆疊"
	description="了解如何透過 Microsoft Azure 使用執行 Linux 的 Azure 虛擬機器 (VM) 建立 LAMP 堆疊。"
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2015"
	ms.author="ningk"/>

#如何使用 Microsoft Azure 建立 LAMP 堆疊

"LAMP" 堆疊是一組開放原始碼軟體，通常會安裝在一起讓伺服器裝載動態網站與 Web 應用程式。這個詞彙其實是一個縮寫，指的是使用 Apache Web 伺服器的 Linux 作業系統。網站資料儲存在 MySQL 資料庫中，動態內容則由 PHP 處理。

在本指南中，我們會將 LAMP 堆疊安裝在 Linux 映像上，並將它部署在 Microsoft Azure。

您將了解：

-	如何建立 Azure 虛擬機器。
-	如何準備用於 LAMP 堆疊的虛擬機器。
-	如何在虛擬機器上安裝 LAMP 伺服器所需的軟體。

假設讀者已具備 Azure 訂用帳戶。若還不是，您可以在 [http://azure.microsoft.com](http://azure.microsoft.com) 註冊免費試用。如果您擁有 MSDN 訂用帳戶，請參閱 [Microsoft Azure 特價：MSDN、MPN 及 Bizspark 優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)。若要深入了解 Azure，請參閱[什麼是 Azure？](http://azure.microsoft.com/overview/what-is-azure/)

除了本主題以外，如果您已經有虛擬機器，而只是想了解在不同 Linux 散發套件上安裝 LAMP 堆疊的基本概念，請參閱[在 Azure 中的 Linux 虛擬機器上安裝 LAMP 堆疊](virtual-machines-linux-install-lamp-stack.md)。

您也可以部署 Azure Marketplace 預先設定的 LAMP 映像。下列 10 分鐘的影片將介紹如何部署 Azure Marketplace 預先建立的 LAMP 映像：

> [AZURE.VIDEO lamp-stack-on-azure-vms-with-guy-bowerman]

##第 1 階段：建立映像。
在這個階段，您將在 Azure 中使用 Linux 映像建立虛擬機器。

###步驟 1：產生 SSH 驗證金鑰
SSH 對系統管理員而言是很重要的工具。因為無論如何，依賴人們決定密碼不一定是明智的安全性做法。強式的 SSH 金鑰可讓您持續開啟遠端存取而不必擔心密碼的問題。方法是由使用非對稱密碼編譯的驗證所組成。使用者的私密金鑰就是一種授與驗證的金鑰。您甚至可以鎖定使用者帳戶完全不允許密碼驗證。

依照下列步驟來產生 SSH 驗證金鑰。

-	從下列位置下載並安裝 puttygen： [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html
-	執行 puttygen.exe。
-	按一下 [產生] 來產生金鑰。在這個程序中，您可以在視窗中的空白區域移動滑鼠來提高隨機性。![][1]
-	在產生程序之後，Puttygen.exe 會顯示產生的金鑰。例如：![][2]
-	選取並複製 [金鑰] 中的公開金鑰，並將它儲存在名為 **publicKey.pem** 的檔案中。不要按 [儲存公開金鑰]，因為儲存的公開金鑰檔案格式與我們想要的公開金鑰不同。
-	按一下 [儲存私密金鑰]，並將它儲存在名為 **privateKey.ppk** 的檔案中。

###步驟 2：在 Azure Preview 入口網站中建立映像。
在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下工作列中的 [新增]，然後遵循下列指示，根據您的需求選擇 Linux 映像來建立映像。此範例使用 Ubuntu 14.04 映像。

![][3]

對於 [主機名稱]，指定您和網際網路用戶端將用來存取此虛擬機器的 URL 名稱。定義 DNS 名稱的最後一個部分，例如 LAMPDemo，然後 Azure 會產生如 Lampdemo.cloudapp.net 的 URL。

對於 [使用者名稱]，挑選您稍後將用來登入虛擬機器的名稱。

對於 [SSH 驗證金鑰]，從 **publicKey.pem** 檔案複製金鑰值，其中包含 puttygen 所產生的公開金鑰。

![][4]

視需要設定其他設定，然後按一下 [建立]。

##第 2 階段：準備用於 LAMP 堆疊的虛擬機器
在這個階段，您將設定 Web 流量的端點，然後連線到新的虛擬機器。

###步驟 1：開啟允許 Web 存取的 HTTP 連接埠
Azure 中的端點是由一種通訊協定 (TCP 或 UDP) 以及公用和私用連接埠所組成。私人連接埠是服務接聽虛擬機器的連接埠。公用連接埠是 Azure 雲端服務接聽外部網際網路流量的連接埠。在某些情況下，這是相同的連接埠號碼。

TCP 連接埠 80 是 Apache 接聽的預設連接埠號碼。在 Azure 端點開啟這個連接埠，可讓您和其他網際網路用戶端存取 Apache Web 伺服器。

在 Azure Preview 入口網站中，按一下 [瀏覽] -> [虛擬機器]，然後按一下您建立的虛擬機器。

![][5]

若要將端點新增至虛擬機器，請按一下 [端點] 方塊。

![][6]

按一下 [新增]。佈建新的虛擬機器時，您可以視需要啟用或停用端點。

設定端點：

1.	在 [端點] 中輸入端點的名稱。
2.	在 [公用連接埠] 中輸入 80。如果您變更了 Apache 的預設接聽連接埠，則必須更新私用連接埠與 Apache 接聽連接埠相同。
3.	在 [公用連接埠] 中輸入 80。根據預設，HTTP 流量使用連接埠 80。如果設定為 80，不需要在 URL 中包含連接埠號碼就可讓您存取 Apache Web 服務。例如，http://lampdemo.cloudapp.net。如果您將 Apache 接聽連接埠設定為另一個值 (例如 81)，您就必須將此連接埠號碼加入 URL 才能存取 Apache Web 服務。例如，http://lampdemo.cloudapp.net:81/。

![][7]

按一下 [確定]，將端點加入您的虛擬機器。




###步驟 2：連線到您建立的映像
您可以選擇任何 SSH 工具來連線到新的虛擬機器。在此範例中，我們使用 Putty。

首先，從 Azure Preview 入口網站取得您虛擬機器的 DNS 名稱。按一下 [瀏覽] -> [虛擬機器] -> [您的虛擬機器名稱] -> [內容]，然後查看 [內容] 磚的 [網域名稱] 欄位。

從 [SSH] 欄位取得 SSH 連線的連接埠號碼。範例如下。

![][8]

從[這裡](http://www.putty.org/)下載 Putty。

下載之後，按一下可執行檔 PUTTY.EXE。使用來自虛擬機器內容的主機名稱和連接埠號碼設定基本選項。下列是一個範例：

![][9]

在左窗格中，按一下 [連線] -> [SSH] -> [驗證]，然後按一下 [瀏覽] 來指定 **privateKey.ppk** 檔案的位置，其中包含 puttygen 在＜第 1 階段：建立映像＞中產生的私密金鑰。下列是一個範例：

![][10]

按一下 [開啟]。您可能會收到警告訊息方塊。如果您已正確設定 DNS 名稱和連接埠號碼，按一下 [是]。

![][11]


您應該會看見下列畫面。

![][12]

輸入您在＜第 1 階段：建立映像＞中建立虛擬機器時指定的使用者名稱。您會看到類似下列畫面：

![][13]

##第 3 階段：安裝 LAMP 堆疊

視您用來建立虛擬機器的 Linux 散發套件而定，安裝 LAMP 堆疊有不同的方式。以下各節包含一些常見 Linux 作業系統上的一般步驟。

###Red Hat、CentOS 基底

####安裝 Apache
若要安裝 Apache，請開啟終端機，然後執行此命令：

	sudo yum install httpd

安裝之後，使用此命令啟動 Apache：

	sudo service httpd start
####測試 Apache
若要檢查是否已成功安裝 Apache，請瀏覽至 Apache 伺服器的 DNS 名稱 (本文中的範例 URL http://lampdemo.cloudapp.net/))。頁面應該會顯示 "It works!" 的字樣![][14]

####疑難排解
如果 Apache 正在執行，但是您無法看到上面的 Apache 預設頁面上方，您需要檢查下列項目：

-	Apache Web 服務接聽位址/連接埠
	-	檢查您 Azure 虛擬機器的端點設定。確定已正確設定端點。請參閱本文中＜第 1 階段：建立映像＞的指示。
	-	開啟 /etc/httpd/conf/httpd.conf，然後搜尋 "Listen" 字串。確定 Apache 接聽連接埠與您為端點設定的私用通訊埠相同。Apache 的預設連接埠為 80。範例如下。  

			……
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			……
			……  

-	防火牆、iptables 設定 如果您可以從本機主機看到 Apache 預設頁面，則問題可能是 Apache 接聽的連接埠被防火牆封鎖。您可以使用 w3m 工具瀏覽 Apache 網頁。下列命令會安裝 w3m，並瀏覽至 Apache 預設頁面：

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	如果問題是防火牆或 iptables 造成，請在 /etc/sysconfig/iptables 中加入下列幾行：

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	請注意，如為 https 流量才需要第二行。

	請確定這幾行位於會全域限制存取的任何程式行上方，例如下列這一行：

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	若要讓新設定生效，請使用下列命令：

		service iptables restart

####安裝 MySQL
若要安裝 MySQL，請開啟終端機，然後執行這些命令：

	sudo yum install mysql-server
	sudo service mysqld start

在安裝期間，MySQL 會詢問您的許可兩次。對兩個詢問都正面回應之後，將會安裝 MySQL。

####Configure MySQL
完成安裝之後，您可以使用下列命令設定 root 的 MySQL 密碼：

	sudo /usr/bin/mysql_secure_installation  

提示會詢問您目前的 root 密碼。

由於您剛安裝 MySQL，所以您不會有密碼，請按 ENTER 鍵將它留空。

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

系統會提示您設定 root 密碼。請繼續並選擇 Y，依照指示進行。

CentOS 會自動進行設定 MySQL 的程序，詢問您一系列是非題。這些問題如下所示。針對您的設定選擇 Y 或 N。最後，MySQL 會重新載入並實作新的變更。

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####安裝 PHP
PHP 是一種開放原始碼 Web 指令碼語言，廣泛用來建立動態網頁。

若要在虛擬機器上安裝 PHP，請開啟終端機並執行此命令：

	sudo yum install php php-mysql  

回答 "y" 下載軟體套件。然後回答 "y" 匯入 GPG 金鑰 0xE8562897 "CentOS 5 金鑰 (CentOS 5 官方簽署金鑰)。此時將會安裝 PHP。

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian、Ubuntu 基底
這在 Ubuntu 14.04 上已經過測試。

Ubuntu 是根據 Debian。您可以採用與 Red Hat 系列相同的方式安裝 LAMP 堆疊。若要簡化步驟，請使用 Tasksel 工具。

Tasksel 是一個 Debian/Ubuntu 工具，以協調工作的方式安裝多個相關套件到您的系統上。如需詳細資訊，請參閱 [Tasksel - 社群協助 Wiki](https://help.ubuntu.com/community/Tasksel) (英文)。

使用 tasksel 安裝 LAMP 堆疊所需的軟體。

- 若要從存放庫下載套件清單，並且更新清單以取得最新版本套件及其相依性的相關資訊：  

		sudo apt-get update
-	若要使用 Tasksel 安裝 Ubuntu LAMP 堆疊：  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

接著依照精靈指示，選擇您的 **MySQL root 密碼**。

![][15]


##在伺服器上測試 LAMP
您可以藉由建立快速的 php 資訊頁面測試 LAMP 系統。

首先，建立一個新檔案：

	sudo nano /var/www/html/info.php  

加入下列幾行：

	<?php
	phpinfo();
	?>  

然後，儲存並結束。

重新啟動 Apache，讓所有變更在您的電腦上生效。如果虛擬機器的作業系統是 CentOS，請使用下列命令重新啟動 Apache：

	sudo service httpd restart

如果虛擬機器的作業系統是 Ubuntu，請使用下列命令重新啟動 Apache：

	sudo service apache2 restart  

最後瀏覽至您的 php 資訊頁面 (如為本主題中的範例 Web 伺服器，該 URL 會是 http://lampdemo.cloudapp.net/info.php)。

您的瀏覽器看起來應該類似這樣：

![][16]

##額外的步驟

一般的做法是，您會變更某些預設設定以便準備 Web 應用程式部署。

###允許遠端存取 MySQL
如果您有多個 VM 安裝 MySQL，而且它們需要交換資料，您就應該啟用 MySQL 遠端存取，並授與適當的權限。

**命令參考格式：**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**範例：**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

您也應該變更 /etc/mysql/my.cnf 設定檔。如果有類似這幾行：

	skip-networking
	bind-address = 127.0.0.1  

您應該將它們變成註解 (在每行開頭加入 #)，然後重新啟動 MySQL。

若要新增端點以允許遠端存取，請參閱＜第 1 階段：建立映像＞的指示建立新的端點。MySQL 的預設遠端存取 TCP 連接埠號碼為 3306。下列是一個範例：

![][17]

###部署 Web 應用程式到 apache 伺服器
一旦您成功設定 LAMP 堆疊，就可以部署現有的 Web 應用程式到 Apache Web 伺服器 (虛擬機器)。步驟與在實體 Web 伺服器上部署現有 Web 應用程式相同。

-	Web 伺服器的根目錄位於 **/var/www/html**。您應該授與權限給需要將檔案上傳到此資料夾的使用者。下列範例顯示如何新增寫入權限到名為 lampappgroup 的群組，並且在此群組中加入 azureuser 使用者名稱：  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE]
-	使用任何 SFTP 用戶端 (例如 FileZilla) 連線到您虛擬機器的 DNS 名稱 (例如 lampdemo.cloudapp.net)，並瀏覽至 /**var/www/html** 發佈您的網站。![][18]



##常見問題和疑難排解

###無法從網際網路存取使用 Apache 和 Moodle 的虛擬機器

-	**徵兆** Apache 正在執行，但是使用瀏覽器無法看到 Apache 預設頁面。
-	**可能的根本原因**
	1.	Apache 接聽連接埠與您虛擬機器針對 Web 流量的端點私用連接埠不同。</br> 檢查您的公用連接埠和私用連接埠端點設定，確定私用連接埠與 Apache 接聽連接埠相同。請參閱＜第 1 階段：建立映像＞，以取得為虛擬機器設定端點的指示。</br> 若要判斷 Apache 的接聽連接埠，請開啟 /etc/httpd/conf/httpd.conf (Red Hat 版本) 或 /etc/apache2/ports.conf (Debian 版本)，搜尋字串 "Listen"。預設連接埠為 80。

	2.	防火牆已停用 Apache 的接聽連接埠。</br> 如果您可以從本機主機看到 Apache 預設頁面，則問題很可能是 Apache 接聽的連接埠被防火牆封鎖。您可以使用 w3m 工具瀏覽網頁。下列命令會安裝 w3m，並瀏覽至 Apache 預設頁面：

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**解決方案**

	1.	如果 Apache 接聽連接埠與虛擬機器上針對 Web 流量的端點私用連接埠不同，您需要將端點的私用連接埠變更為與 Apache 接聽連接埠相同。
	2.	如果問題是防火牆/iptables 造成，請在 /etc/sysconfig/iptables 中加入下列幾行：  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		請注意，如為 https 流量才需要第二行。

		請確定這幾行位於會全域限制存取的任何程式行上方，例如下列這一行：

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		若要重新載入 iptables，請執行下列命令：

			service iptables restart  

		這在 CentOS 6.3 上已經過測試。

###上傳您的專案檔案到 /var/www/html/ 時權限遭拒  

-	**徵兆** 當您使用任何 SFTP 用戶端 (例如 FileZilla) 連線到虛擬機器，並瀏覽到 /var/www/html 發佈網站時，您收到類似下列錯誤訊息：  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**可能的根本原因** 您沒有存取 /var/www/html 資料夾的權限。
-	**解決方案**您需要從 root 帳戶取得權限。您可以從 root 將該資料夾的擁有權變更為佈建機器時所使用的使用者名稱。下列是一個 azureuser 帳戶名稱的範例：  

		sudo chown azureuser -R /var/www/html  

	使用-R 選項對目錄內的所有檔案也一併套用權限。

	請注意，此命令也適用於目錄。-R 選項會變更目錄內所有檔案和目錄的權限。下列是一個範例：

		sudo chown -R username:group directory  

	此命令會變更目錄內所有檔案和目錄以及目錄本身的擁有權 (使用者和群組兩者)。

	下列命令只會變更資料夾目錄的權限，卻不會變更目錄內檔案和資料夾的權限。

		sudo chown username:group directory  

###無法可靠地判斷伺服器的完整網域名稱

-	**徵兆** 當您使用下列其中一個命令重新啟動 Apache 伺服器：  

		sudo /etc/init.d/apache2 restart  # Debian release  

	或

		sudo /etc/init.d/httpd restart   # Red Hat release  

	您收到下列錯誤：

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**可能的根本原因** 您尚未設定 Apache 的伺服器名稱。

-	**解決方案** 在 httpd.conf (Red Hat 版本) 或在 /etc/apache2 中的 apache2.conf (Debian 版本) 中插入 "ServerName localhost" 一行，然後重新啟動 Apache。警告將會消失。



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg
 

<!---HONumber=July15_HO2-->