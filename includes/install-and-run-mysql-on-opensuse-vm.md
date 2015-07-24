
1. 若要提升權限，請執行：

		sudo -s
	
	輸入您的密碼。

2. 執行下列命令以安裝 MySQL Community Server 版本：

		# zypper install mysql-community-server

	等待 MySQL 進行下載和安裝。
3. 若要將 MySQL 設為在系統開機時啟動，請執行下列命令：

		# insserv mysql
4. 您現在可以利用下列命令手動啟動 MySQL 精靈 (mysqld)：

		# rcmysql start

	若要檢查 MySQL 精靈的狀態，請執行：

		# rcmysql status

	如果您要停止 MySQL 精靈，請執行：

		# rcmysql stop

5. 警告！ 安裝之後，MySQL 根密碼預設為空白。建議您執行 **mysql_secure_installation**，該指令碼有助於保護 MySQL 的安全。執行 **mysql_secure_installation** 時，系統會提示您變更 MySQL 根密碼、移除匿名使用者帳戶、停用遠端根登入、移除測試資料庫，以及重新載入權限資料表。建議您對所有的選項回答 [是] 並且變更根密碼。執行下列命令以執行指令碼：

		$ mysql_secure_installation

6. 執行之後，即可登入 MySQL：

		$ mysql -u root -p

	輸入 MySQL 根密碼 (您在前一個步驟中變更的密碼)，畫面上將會出現提示，以便您發出 SQL 陳述式來與資料庫互動。

7. 若要建立新的 MySQL 使用者，請在 **mysql>** 提示字元中執行下列命令：

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	請注意，行尾的分號 (;) 對結束命令而言十分重要。

8. 若要建立資料庫並授與 `mysqluser` 使用者權限，請發出下列命令：

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	請注意，只有連線到資料庫的指令碼會使用資料庫使用者名稱和密碼。資料庫使用者帳戶名稱不一定代表系統上的實際使用者帳戶。

9. 若要從其他電腦登入，請執行下列命令：

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	`ip-address` 是您從中連線到 MySQL 的電腦 IP 位址。
	
10. 若要結束 MySQL 資料庫管理公用程式，請發出下列命令：

		quit

11. 安裝 MySQL 後，您必須設定端點，以便遠端存取 MySQL。登入 [Azure 管理入口網站][AzurePreviewPortal]。在 Azure 入口網站中，依序按一下 [**虛擬機器**]、新 VM 的名稱和 [**端點**]。

	![Endpoints][Image7]

12. 按一下頁面底部的 [新增]。![Endpoints][Image8]

13. 新增名為 "MySQL" 的端點，通訊協定為 **TCP**，且 [**公開**] 及 [**私人**] 連接埠均設為 "3306"。這樣可允許 MySQL 遠端存取。![Endpoints][Image9]

14. 若要從遠端連線到在 Azure 中的 OpenSUSE 虛擬機器上執行的 MySQL，請在本機電腦上執行下列命令：

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	例如，使用我們在本教學課程中建立的虛擬機器，命令如下：

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. 您已成功設定 MySQL，並建立了資料庫和新使用者。如需 MySQL 的詳細資訊，請參閱 [MySQL 文件][MySQLDocs]。

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=July15_HO2-->