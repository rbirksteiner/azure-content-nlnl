遵循下列步驟，在執行 CentOS Linux 的虛擬機器上安裝並執行 MongoDB。

> [AZURE.WARNING]MongoDB 安全性功能，例如驗證和 IP 位址繫結，均非預設為已啟用。安全性功能應該在將 MongoDB 部署到生產環境前加以啟用。請參閱[安全性與驗證](http://www.mongodb.org/display/DOCS/Security+and+Authentication)以取得詳細資訊。

1. 設定套件管理系統 (YUM)，以使安裝 MongoDB。建立一個 */etc/yum.repos.d/10gen.repo* 檔案，保留您的儲存機制並新增以下項目：

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. 儲存儲存機制檔案，並執行以下命令以更新本機套件資料庫：

		$ sudo yum update

3. 要安裝套件，請執行下列命令以安裝最新版本的 MongoDB 以及相關工具：

		$ sudo yum install mongo-10gen mongo-10gen-server

	等待 MongoDB 下載及安裝完成。

4. 建立資料目錄。依預設，MongoDB 會在 */data/db* 目錄中儲存資料，但您必須先建立該目錄。若要建立目錄，請執行：

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	如需在 Linux 上安裝 MongoDB 的詳細資訊，請參閱[快速入門 Unix][QuickstartUnix]。

5. 若要啟動資料庫，請執行：

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	在 MongoDB 伺服器啟動和預先配置日誌檔案時，所有記錄訊息都會傳送至 */srv/datadrive/data/mongod.log* 檔案。MongoDB 可能需要花費數分鐘來預先配置日誌檔案，並開始接聽連線。

6. 若要啟動 MongoDB 管理殼層，請先個別開啟 SSH 或 PuTTY 視窗並執行：

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	資料庫由插入項目建立。

7. 一旦 MongoDB 安裝完成，您必須設定端點，讓 MongoDB 可以遠端存取。在 [管理入口網站] 中，按一下 [虛擬機器]，接著按一下新虛擬機器的名稱，再按一下 [端點]。
	
	![Endpoints][Image7]

8. 按一下頁面底部的 [新增端點]。
	
	![Endpoints][Image8]

9. 新增具有下列設定的端點：

 - **名稱**：Mongo
 - **通訊協定**：TCP
 - **公用連接埠**：27017
 - **私人連接埠**：27017
 
 這樣可允許 MongoDB 遠端存取。



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!---HONumber=July15_HO2-->