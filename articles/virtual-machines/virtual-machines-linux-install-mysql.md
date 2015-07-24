<properties
	pageTitle="如何在 Azure 上安裝 MySQL"
	description="了解如何在 Azure 中的 Linux 虛擬機器 (VM) 上安裝 MySQL 堆疊。您可以在 Ubuntu 或 CentOS 上進行安裝。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#如何在 Azure 上安裝 MySQL


在這個主題中，假設讀者已有 Azure 帳戶。如果沒有，建議您造訪 [Azure](http://azure.microsoft.com) 並註冊。



##在 Microsoft Azure 中建立 VM 映像。
現在我們會從 Microsoft Azure 管理入口網站建立新的 VM。
###產生「SSH 驗證金鑰」
我們需要 SSH 金鑰，才能存取 Azure 入口網站。


- 從[這裡](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載以及安裝 puttygen： 
- 執行 puttygen.exe。
- 按一下 [產生] 來產生金鑰。


 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- 產生金鑰之後，就會看到以下的畫面。 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- 複製公開金鑰，然後儲存在名為 "publicKey.pem" 的檔案中。不要只是按一下 [儲存公開金鑰] 按鈕，因為儲存的公開金鑰檔案格式與我們想要的公開金鑰不同。
- 按一下 [儲存私密金鑰] 按鈕，然後儲存為 "privateKey.ppk"。 

###登入 Azure 入口網站

移至 https://portal.azure.com/，然後登入。

###建立 Linux VM

按一下左下方的 [新增]，依照下列操作方法建立映像，並根據實際需要選擇 Linux 映像。現在我選擇 Ubuntu 14.04 做為範例。

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###設定主機名稱

「主機名稱」：您就是利用這個 URL 來存取虛擬機器。您只需要指定 DNS 名稱，例如 "mysqlnode1"，Azure 就會產生像是 "mysqlnode1.cloudapp.net" 的 URL；「SSH 驗證金鑰」：就是指 puttygen 產生的公開金鑰，請您從 "publicKey.key" 檔案的內容複製這個金鑰。

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##開啟 MySQL 預設連接埠
Microsoft Azure 中的端點包含通訊協定以及公用連接埠和私用連接埠。私用連接埠是這個服務接聽本機電腦時會用到的連接埠。公用連接埠是服務接聽外部時會用到的連接埠。連接埠 3306 是 MySQL 會接聽的預設連接埠號碼。按一下 [瀏覽] ⇒ [虛擬機器]，然後按一下您建立的映像。
 
   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##連線到您所建立的映像
您可以選擇任何 SSH 工具來連線到虛擬機器。現在我們用 Putty 當做範例。
 

- 首先下載 Putty，這裡是 Putty 的下載 URL。
- 下載之後，按一下可執行檔 PUTTY.EXE。按照以下方式設定。


     當您建立映像時，[主機名稱 (或 IP 位址)] 是當成 [DNS 名稱] 的 URL。
     
     我們可以為 [連接埠] 選擇 22。這是 SSH 服務的預設連接埠。

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- 選取 [開啟] 之前，先依序按一下 [連線] > [SSH] > [驗證] 索引標籤，然後找出 Puttygen 產生的檔案，裡面有一個私密金鑰。請參閱以下螢幕擷取畫面，以了解要填入的欄位。

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- 然後按一下 [開啟]，您可能會看見警告訊息方塊，指出您連接的電腦可能不是真正想連接的電腦。如果您已正確設定 DNS 名稱和連接埠號碼，按一下 [是]。
  
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- 之後，您會看到類似以下的畫面。 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##在虛擬機器上安裝 MySQL
MySQL 支援 3 種安裝方法：二進位套件、rpm 套件和來源套件。考量到效率，在本文章的範例中，MySQL 5.6 會使用 rpm 套件。透過 MySQL5.5 可以大幅改進 MySQL5.6 的效率。如需詳細資訊，請參閱[這裡](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)。


###如何在 Ubuntu 或 Debian 上安裝 MySQL5.6
在本文章中，我們會使用 Ubuntu 14.04 LTS 做為範例。

- 步驟 1：安裝 MySQL Server 5.6

    利用 apt-get 命令安裝 mysql-server 5.6

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    在安裝期間，您會看到一個快顯對話方塊，要求您設定 MySQL 根密碼。您必須指定新的 MySQL 使用者根密碼。下面是螢幕擷取畫面。

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- 步驟 2：登入 MySQL Server

    MySQL Server 安裝完成之後，就會自動啟動 MySQL 服務。您可以使用根使用者來登入 MySQL Server。若要登入 MySQL Server，請使用下列命令。這個命令會要求您輸入當您安裝 MySQL Server 所設定的 MySQL Server 根密碼。

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- 步驟 3：檢查 VM 上的 MySQL 服務
    
    登入後，請確定 MySQL 服務正在執行中，您可以使用下列命令來啟動/重新啟動服務。

    (a) 取得 MySQL 服務的狀態

             #sudo service mysql status

    (b) 啟動 MySQL 服務

             #sudo service mysql start

    (c) 停止 MySQL 服務

             #sudo service mysql stop

    (d) 重新啟動 MySQL 服務

             #sudo service mysql restart


###如何在 Redhat OS 系列或 Oracle Linux 上安裝 MySQL
- 步驟 1：新增 MySQL Yum 儲存機制來取得根權限，請執行以下命令： 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- 步驟 2：選取版本數列
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    檔案一般會有發行數列的 subrepository 記錄：

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- 步驟 3：執行下列命令，利用 Yum 安裝 MySQL：

           #[root@azureuser ~]#yum install mysql-community-server 

    這樣會安裝 MySQL Server 的套件，以及其他必要的套件。

- 步驟 4：檢查 MySQL 執行狀態

    您可以使用下列命令，檢查 MySQL Server 的狀態：
   
           #[root@azureuser ~]#service mysqld status

    您可以檢查 MySQL Server 的預設連接埠是否正在執行中：

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- 步驟 5：啟動和停止 MySQL Server。

    使用下列命令來啟動 MySQL Server：

           #[root@azureuser ~]#service mysqld start

    使用下列命令來停止 MySQL Server：

           #[root@azureuser ~]#service mysqld stop

    若要將 MySQL 設為在系統開機時啟動，請執行下列命令：

           #[root@azureuser ~]#chkconfig mysqld on


###如何在 Suse Linux 上安裝 MySQL

- 步驟 1：安裝 MySQL Server

    若要提升權限，請執行以下命令：

           #sudo su -

    執行下列命令來安裝 MySQL：

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- 步驟 2：檢查 MySQL 執行狀態

    您可以使用下列命令，檢查 MySQL Server 的狀態：

           #mysql-test:~ # rcmysql status

    您可以檢查是否正在執行的 MySQL Server 的預設連接埠。

           #mysql-test:~ # netstat  –tunlp|grep 3306

- 步驟 3：啟動和停止 MySQL Server

    使用下列命令來啟動 MySQL Server：

           #mysql-test:~ # rcmysql start

    使用下列命令來停止 MySQL Server：

           #mysql-test:~ # rcmysql stop

    若要將 MySQL 設為在系統開機時啟動，請執行下列命令：

           #mysql-test:~ # insserv mysql
 

<!---HONumber=July15_HO2-->