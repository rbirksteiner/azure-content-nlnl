<properties 
	pageTitle="如何對於 Microsoft Azure 在 Linux 虛擬機器上設定 Tomcat7" 
	description="了解如何使用執行 Linux 的 Azure 虛擬機器 (VM) 對於 Microsoft Azure 設定 Tomcat7。" 
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
	ms.date="05/21/2015" 
	ms.author="ningk"/>

#如何對於 Microsoft Azure 在 Linux 虛擬機器上設定 Tomcat7 

Apache Tomcat (或直接稱為 Tomcat，以往也稱為 Jakarta Tomcat) 是 Apache Software Foundation (ASF) 開發的開放原始碼 Web 伺服器和 Servlet 容器。Tomcat 實作 Sun Microsystems 的 Java Servlet 和 JavaServer 頁面 (JSP) 規格，並提供執行 Java 程式碼的純 Java HTTP 網頁伺服器環境。在最簡單的組態中，Tomcat 會在單一作業系統處理序中執行。此程序會執行 Java 虛擬機器 (JVM)。從瀏覽器到 Tomcat 的每個 HTTP 要求都會以 Tomcat 程序中個別的執行緒形式予以處理。

在本指南中，您將在 Linux 映像上安裝 tomcat7，並將它部署於 Microsoft Azure。

您將了解：

-	如何在 Azure 中建立虛擬機器。
-	如何準備 tomcat7 的虛擬機器。
-	如何安裝 tomcat7。

假設讀者已具備 Azure 訂用帳戶。若還不是，您可以在 [http://azure.microsoft.com](http://azure.microsoft.com) 註冊免費試用。如果您擁有 MSDN 訂用帳戶，請參閱 [Microsoft Azure 特價：MSDN、MPN 及 Bizspark 優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)。若要深入了解 Azure，請參閱[什麼是 Azure？](http://azure.microsoft.com/overview/what-is-azure/)。

本主題假設您有 tomcat 和 Linux 的基本操作知識。

##第 1 階段：建立映像。
在這個階段，您將在 Azure 中使用 Linux 映像建立虛擬機器。

###步驟 1：產生 SSH 驗證金鑰
SSH 對系統管理員而言是很重要的工具。不過，依據人為決定的密碼設定存取安全性不是最佳作法。惡意使用者可以依據使用者名稱和弱式密碼侵入系統。

好消息是，有辦法保持遠端存取開啟，而且不需要擔心密碼。方法是由使用非對稱密碼編譯的驗證所組成。使用者的私密金鑰就是一種授與驗證的金鑰。您甚至可以鎖定使用者帳戶完全不允許密碼驗證。

這個方法的另一個優點是，您不需要不同的密碼來登入到不同的伺服器。您可以使用所有伺服器上的個人私密金鑰進行驗證，因此不需要記住多組密碼。

此外，也可以使用此方法要求輸入密碼以登入。

依照下列步驟來產生 SSH 驗證金鑰。

1.	從下列位置下載並安裝 puttygen： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 
2.	執行 PUTTYGEN.EXE。
3.	按一下 [產生] 來產生金鑰。在這個程序中，您可以在視窗中的空白區域移動滑鼠來提高隨機性。![][1]
4.	在產生程序之後，Puttygen.exe 會顯示產生的金鑰。例如：![][2]
5.	選取並複製 [金鑰] 中的公開金鑰，並將它儲存在名為 publicKey.pem 的檔案中。不要按 [儲存公開金鑰]，因為儲存的公開金鑰檔案格式與我們想要的公開金鑰不同。
6.	按一下 [儲存私密金鑰]，並將它儲存在名為 privateKey.ppk 的檔案中。 

###步驟 2：在 Azure Preview 入口網站中建立映像。
在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下工作列中的 [新增] 來建立映像，並根據您的需求選擇 Linux 映像。下列範例使用 Ubuntu 14.04 映像。![][3]
 
對於 [主機名稱]，指定您和網際網路用戶端將用來存取此虛擬機器的 URL 名稱。定義 DNS 名稱的最後一個部分，例如 tomcatdemo，然後 Azure 會產生如 tomcatdemo.cloudapp.net 的 URL。

對於 [SSH 驗證金鑰]，從 **publicKey.pem** 檔案複製金鑰值，其中包含 puttygen 所產生的公開金鑰。![][4]
  
視需要設定其他設定，然後按一下 [建立]。

##第 2 階段：準備用於 Tomcat7 的虛擬機器
在這個階段，您將設定 tomcat 流量的端點，然後連線到新的虛擬機器。
###步驟 1：開啟允許 Web 存取的 HTTP 連接埠
Azure 中的端點包含通訊協定 (TCP 或 UDP) 以及公用和私人的連接埠。私人連接埠是服務接聽虛擬機器的連接埠。公用連接埠是 Azure 雲端服務接聽外部內送、網際網路流量的連接埠。

TCP 連接埠 8080 是 tomcat 接聽的預設連接埠號碼。在 Azure 端點開啟這個連接埠，可讓您和其他網際網路用戶端存取 tomcat 頁面。

1.	在 Azure Preview 入口網站中，按一下 [瀏覽] -> [虛擬機器]，然後按一下您建立的虛擬機器。![][5]
2.	若要將端點新增至虛擬機器，請按一下 [端點] 方塊。![][6] 
3.	按一下 [新增]。  
	1.	對於**端點**，在 [端點] 中輸入端點的名稱，然後在 [公用連接埠] 中輸入 80。  
	  
		如果設定為 80，不需要在 URL 中包含連接埠號碼就可讓您存取 tomcat。例如，http://tomcatdemo.cloudapp.net。

		如果您將它設定為另一個值 (例如 81)，您就必須將此連接埠號碼加入 URL 才能存取 tomcat。例如，http://tomcatdemo.cloudapp.net:81/。
	2.	在 [私人連接埠] 中輸入 8080。tomcat 預設接聽 TCP 連接埠 8080。如果您變更 tomcat 的預設接聽連接埠，則必須更新私人連接埠，使其與 tomcat 接聽連接埠相同。![][7]
 
4.	按一下 [確定]，將端點加入您的虛擬機器。



###步驟 2：連線到您建立的映像。
您可以選擇任何 SSH 工具來連線到虛擬機器。在此範例中，我們使用 Putty。

首先，從 Azure Preview 入口網站取得您虛擬機器的 DNS 名稱。按一下 [瀏覽] -> [虛擬機器] -> 您的虛擬機器名稱 -> [內容]，然後查看 [內容] 磚的 [網域名稱] 欄位。

從 [SSH] 欄位取得 SSH 連線的連接埠號碼。範例如下。![][8]
 
從[這裡](http://www.putty.org/)下載 Putty。

下載之後，按一下可執行檔 PUTTY.EXE。使用來自虛擬機器內容的主機名稱和連接埠號碼設定基本選項。範例如下：![][9]
 
在左窗格中，按一下 [連線] -> [SSH] -> [驗證]，然後按一下 [瀏覽] 來指定 **privateKey.ppk** 檔案的位置，其中包含 puttygen 在＜第 1 階段：建立映像＞中產生的私密金鑰。範例如下：![][10]
 
按一下 [開啟]。您可能會收到警告訊息方塊。如果您已正確設定 DNS 名稱和連接埠號碼，按一下 [是]。![][11]


您應該會看見下列內容：![][12]

輸入您在＜第 1 階段：建立映像＞中建立虛擬機器時指定的使用者名稱。您會看到類似下列的畫面：![][13]





##第 3 階段：安裝軟體
在這個階段，您可以安裝 Java 執行階段環境、tomcat 和其他 tomcat 元件。

###Java 執行階段環境
Tomcat 是以 Java 撰寫的。有兩種類型的 Java 開發套件 (JDK) (OpenJDK 和 Oracle JDK)，您可以選擇所需的其中一個。

>AZURE.NOTE︰這兩個 JDK 對於 Java API 中的類別擁有幾乎相同的程式碼，但是對於虛擬機器的程式碼則截然不同。對於程式庫，OpenJDK 傾向使用開放程式庫，而 Oracle 傾向於使用非開放程式庫。但是 Oracle JDK 有較多的類別和一些已修復的錯誤，而 Oracle JDK 則比 OpenJDK 穩定。

下列命令會下載不同的 JDK。

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	若要從 Oracle 網站下載 JDK：  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	若要建立包含 JDK 檔案的目錄：

		sudo mkdir /usr/lib/jvm  

-	若要將 JDK 檔案擷取到 /usr/lib/jvm/ 目錄：

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	若要設定 Oracle JDK 成為預設 JVM：

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####測試：
您可以使用如下所示的命令，測試是否已正確安裝 Java 執行階段環境：

	java -version  

如果您已安裝 open-jdk，應該會看見如下的訊息：![][14]

如果您已安裝 oracle-jdk，應該會看見如下的訊息：![][15]

###Tomcat7
使用下列命令安裝 tomcat7：

	sudo apt-get install tomcat7  

如果您不使用 tomcat7，請適度修改此命令。

####測試：

若要檢查是否已成功安裝 tomcat7，請瀏覽至 tomcat 伺服器的 DNS 名稱 (http://tomcatexample.cloudapp.net/ 是本文中的範例 URL)。如果您看到類似下列的頁面，表示 tomcat7 安裝正確。![][16]


###安裝其他 Tomcat 元件
您也可以安裝其他選用 tomcat 元件。

使用 **sudo apt-cache search tomcat7** 命令來查看所有可用的元件。下列命令是安裝一些實用元件的一部分命令。

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##第 4 階段：設定 Tomcat
在這個階段，您可以管理 tomcat。
###啟動和停止 tomcat7
tomcat7 伺服器將在您安裝時自動啟動。您也可以自行使用下列命令啟動它：

	sudo /etc/init.d/tomcat7 start

若要停止 tomcat7：

	sudo /etc/init.d/tomcat7 stop 

若要檢視 tomcat7 的狀態：

	sudo /etc/init.d/tomcat7 status

若要重新啟動 tomcat 服務：

	sudo /etc/init.d/tomcat7 restart

###Tomcat 管理
您可以使用下列命令，編輯 Tomcat 使用者組態檔案來設定您的系統管理員認證：

	sudo vi  /etc/tomcat7/tomcat-users.xml   

範例如下：![][17]

>AZURE.NOTE：建立系統管理員使用者名稱的強式密碼。

編輯此檔案之後, 您應該使用下列命令重新啟動 tomcat7 服務，以確保變更生效：

	sudo /etc/init.d/tomcat7 restart  

開啟瀏覽器，並輸入 URL **http://<your tomcat server DNS name>/manager/html**。本文中的範例 URL 是 http://tomcatexample.cloudapp.net/manager/html。

連接之後，您應該會看到類似下面的內容：![][18]
 
##常見問題

###無法從網際網路存取使用 Tomcat 和 Moodle 的虛擬機器

-	**徵兆** Tomcat 正在執行中，但無法使用瀏覽器來查看 Tomcat 預設頁面。
-	**可能的根本原因**   
	1.	Tomcat 接聽連接埠與您虛擬機器針對 Tomcat 流量的端點私人連接埠不同。  
	
		檢查您的公用連接埠和私人連接埠端點設定，確定私人連接埠與 Tomcat 接聽連接埠相同。請參閱＜第 1 階段：建立映像＞，以取得為虛擬機器設定端點的指示。

		若要決定 tomcat 接聽連接埠，請開啟 /etc/httpd/conf/httpd.conf (Red Hat 版本) 或 /etc/tomcat7/server.xml (Debian 版本)。tomcat 接聽連接埠預設為 8080。下列是一個範例：

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		如果您使用 Debian 或 Ubuntu 之類的虛擬機器，而且您要變更 Tomcat 接聽的預設連接埠 (例如 8081)，您也應該對於作業系統開啟該連接埠。首先，開啟設定檔：

			sudo vi /etc/default/tomcat7  

		然後移除最後一行的註解，並將「否」變更為「是」。

			AUTHBIND=yes

	2.	防火牆已停用 tomcat 的接聽連接埠。

		如果您只能夠從本機主機看見 Tomcat 預設頁面，則問題很可能是 Tomcat 接聽的連接埠被防火牆封鎖。您可以使用 w3m 工具瀏覽網頁。下列命令會安裝 w3m，並瀏覽至 Tomcat 預設頁面：

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**解決方法**
	1. 如果 Tomcat 接聽連接埠與虛擬機器上針對流量的端點私人連接埠不同，您需要將私人連接埠變更為與 tomcat 接聽連接埠相同。   
	
	2.	如果問題是防火牆/iptables 造成，請在 /etc/sysconfig/iptables 中加入下列幾行：
	
			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		請注意，如為 https 流量才需要第二行。

		請確定這幾行位於會全域限制存取的任何程式行上方，例如下列這一行：

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		若要重新載入 iptables，請執行下列命令：

			service iptables restart  

		這在 CentOS 6.3 上已經過測試。

###上傳您的專案檔案到 /var/lib/tomcat7/webapps/ 時權限遭拒  

-	**徵兆**當您使用任何 SFTP 用戶端 (例如 FileZilla) 連線到虛擬機器，並瀏覽到 /var/lib/tomcat7/webapps/ 以發佈網站時，您收到類似下列的錯誤訊息：  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**可能的根本原因**您沒有存取 /var/lib/tomcat7/webapps 資料夾的權限。
-	**解決方案**您需要從 root 帳戶取得權限。您可以從 root 將該資料夾的擁有權變更為佈建機器時所使用的使用者名稱。下列是一個 azureuser 帳戶名稱的範例：  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	使用-R 選項對目錄內的所有檔案也一併套用權限。

	請注意，此命令也適用於目錄。-R 選項會變更目錄內所有檔案和目錄的權限。下列是一個範例：

		sudo chown -R username:group directory  

	此命令會變更目錄內所有檔案和目錄以及目錄本身的擁有權 (使用者和群組兩者)。

	下列命令只會變更資料夾目錄的權限，卻不會變更目錄內檔案和資料夾的權限。

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png
 

<!---HONumber=July15_HO1-->