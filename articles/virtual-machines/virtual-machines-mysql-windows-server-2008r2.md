<properties 
	pageTitle="在 Azure 中建立執行 MySQL 的虛擬機器" 
	description="建立執行 Windows Server 2012 R2 的 Azure 虛擬機器，然後在該虛擬機器上安裝及設定 MySQL 資料庫。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="kathydav"/>


# 在 Azure 中執行 Windows Server 2012 R2 的虛擬機器上安裝 MySQL


[MySQL](http://www.mysql.com) 是一種很受歡迎的開放原始碼 SQL 資料庫。在 [Azure 管理入口網站](http://manage.windowsazure.com)中，您可以從映像庫建立執行 Windows Server 2012 R2 的虛擬機器。您可以接著安裝並將它設定為 MySQL 伺服器。


本教學課程說明如何：

- 使用管理入口網站來建立執行 Windows Server 2012 R2 的虛擬機器。

- 在虛擬機器上安裝並執行 MySQL 5.6.23 社群版本作為 MySQL 伺服器。


## 建立執行 Windows Server 的虛擬機器

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## 連接資料磁碟

建立虛擬機器之後，您可以選擇連接其他資料磁碟。對於生產工作負載建議這個做，這可避免目前限制為 127 GB 且包括作業系統的作業系統磁碟機 (c:) 空間不足。

請參閱[如何將資料磁碟連接至 Windows 虛擬機器](storage-windows-attach-disk.md)，並遵循指示來連接空的磁碟。將主機快取設定設為 [無] 或 [唯讀]。

## 登入虛擬機器

接著，您將登入虛擬機器，以安裝 MySQL。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]
 
##在虛擬機器上安裝和執行 MySQL Community Server

請依照下列步驟安裝、設定和執行 MySQL Server 社群版本：

> [AZURE.NOTE]這些步驟適用於 MySQL 5.6.23.0 社群版本和 Windows Server 2012 R2。對於 MySQL 或 Windows Server 的不同版本，操作方式可能不同。

1.	使用遠端桌面連線到虛擬機器之後，按一下 [開始] 畫面中的 [Internet Explorer]。
2.	選取右上角的 [工具] 按鈕 (齒輪圖示)，然後按一下 [網際網路選項]。依序按一下 [安全性] 索引標籤和[信任的網站] 圖示，然後按一下 [網站] 按鈕。將 **http://*.mysql.com** 新增至受信任的網站清單。按一下 [關閉]，然後按一下 [確定]。3.	在 Internet Explorer 的網址列中，輸入 **http://dev.mysql.com/downloads/mysql/**。4.	使用 MySQL 網站尋找並下載 Windows 版 MySQL 最新版安裝程式。選擇 MySQL 安裝程式時，下載的版本都有完整的檔案集 (例如，mysql-installer-community-5.6.23.0.msi 的檔案大小為 282.4 MB)，並且將安裝程式檔案儲存到 Windows 桌面。
5.	從桌面上，按兩下安裝程式檔案即可開始安裝。
6.	在 [授權合約] 頁面上接受授權合約，然後按 [下一步]。
7.	在 [選擇安裝類型] 頁面上，按一下所需的安裝類型，然後按 [下一步]。下列步驟假設選取 [僅限伺服器] 安裝類型。
8.	在 [安裝] 頁面上，按一下 [執行]。安裝完成時，按 [下一步]。
9.	在 [產品組態] 頁面上，按 [下一步]。
10.	在 [類型和網路] 頁面上，視需要指定所需的組態類型和連線選項，包括 TCP 連接埠。按一下 [顯示進階選項]，然後按 [下一步]。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)
 
11.	在 [帳戶及角色] 頁面上，指定強式的 MySQL 根密碼。視需要新增其他 MySQL 使用者帳戶，然後按 [下一步]。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)
 
12.	在 [Windows 服務] 頁面上，視需要對於執行 MySQL 伺服器作為 Windows 服務的預設設定指定變更，然後按 [下一步]。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)
 
13.	在 [進階選項] 頁面上，視需要對於記錄選項指定變更，然後按 [下一步]。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)
 
14.	在 [套用伺服器組態] 頁面上，按一下 [執行]。完成組態步驟時，按一下 [完成]。
15.	在 [產品組態] 頁面上，按 [下一步]。
16.	如果您想要以後再檢查，請在 [安裝完成] 頁面上，按一下 [將記錄複製到剪貼簿]，然後按一下 [完成]。
17.	從 [開始] 畫面，輸入 **mysql**，然後按一下 [MySQL 5.6 命令列用戶端]。
18.	輸入您在步驟 11 中指定的根密碼，隨即提示您發出命令來設定 MySQL。如需命令和語法的詳細資訊，請參閱 [MySQL 參考手冊](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)
 
19.	您也可以使用 **C:\\Program Files (x86)\\MySQL\\MySQL Server 5.6\\my-default.ini** 檔案中的項目來設定伺服器組態的預設設定，例如，基底與資料目錄和磁碟機。如需詳細資訊，請參閱 [5.1.2 伺服器組態預設值](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。


如果您要 MySQL 伺服器服務可供網際網路上的 MySQL 用戶端電腦使用，則必須設定 MySQL 伺服器服務接聽的 TCP 連接埠端點，並建立額外的 Windows 防火牆規則。除非您先前在 [類型和網路] 頁面 (先前程序的步驟 10) 指定不同的連接埠，否則這是 TCP 連接埠 3306。


> [AZURE.NOTE]您應該仔細考慮這麼做對安全性的影響，因為這會將 MySQL 伺服器服務提供給網際網路上的所有電腦。您可以定義來源 IP 位址的集合，以便使用具備存取控制清單 (ACL) 的端點。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。


若要設定 MySQL Server 服務的端點：

1.	在 Azure 管理入口網站中，依序按一下 [虛擬機器] 和您的 MySQL 虛擬機器名稱，然後按一下 [端點]。
2.	在命令列中，按一下 [新增]。
3.	在 [將端點加入至虛擬機器] 頁面上，按一下向右箭頭。
4.	如果您使用預設 MySQL TCP 連接埠 3306，請按一下 [名稱] 中的 [MySQL]，然後按一下勾號。
5.	如果您使用不同的 TCP 連接埠，請在 [名稱] 中輸入唯一名稱。選取通訊協定中的 [TCP]，並在 [公用連接埠] 和 [私人連接埠] 中輸入連接埠號碼，然後按一下勾號。

若要新增 Windows 防火牆規則以允許來自網際網路的 MySQL 流量，請在 MySQL 伺服器電腦上的系統管理員層級 Windows PowerShell 命令提示字元中執行這個命令。

	New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

若要測試 Azure 虛擬機器上執行的 MySQL Server 服務遠端連線，您必須先決定與執行 MySQL 伺服器的虛擬機器包含在內的雲端服務相對應的 DNS 名稱。

1.	在 Azure 管理入口網站中，依序按一下 [虛擬機器] 和您的 MySQL 伺服器虛擬機器名稱，然後按一下 [儀表板]。
2.	在虛擬機器儀表板中，記下 [快速概覽] 區段下方的 [DNS 名稱] 值。下列是一個範例： 

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)
 
3.	從執行 MySQL 或 MySQL 用戶端的本機電腦中執行此命令，以 MySQL 使用者身分登入：

		mysql -u <yourMysqlUsername> -p -h <yourDNSname>
	
	例如，對於 MySQL 使用者名稱 **dbadmin3** 和虛擬機器的 **testmysql.cloudapp.net** DNS 名稱，命令如下：

		mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## 資源

如需 MySQL 的相關資訊，請參閱 [MySQL 文件](http://dev.mysql.com/doc/)。


 

<!---HONumber=July15_HO2-->