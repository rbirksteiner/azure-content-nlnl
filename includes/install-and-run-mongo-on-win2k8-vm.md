遵循下列步驟，在執行 Windows Server 的虛擬機器上安裝並執行 MongoDB。

> [AZURE.IMPORTANT]MongoDB 安全性功能，例如驗證和 IP 位址繫結，均非預設為已啟用。安全性功能應該在將 MongoDB 部署到生產環境前加以啟用。請參閱[安全性與驗證](http://www.mongodb.org/display/DOCS/Security+and+Authentication)以取得詳細資訊。

1. 使用遠端桌面連線到虛擬機器時，請從 [開始] 功能表中開啟 Internet Explorer。
2. 選取右上方的 [工具] 按鈕。在 [網際網路選項] 中，選取 [安全性] 索引標籤，接著選取 [受信任的網站] 圖示，最後按一下 [網站] 按鈕。將 *http://*.mongodb.org* 新增至受信任的網站清單。3. 移至[下載 - MongoDB][MongoDownloads]。
4. 在**產品版本 (建議)** 一節中尋找最新的版本，並按一下 Windows 64 位元欄中的 [***2008+**] 連結。按一下 [另存新檔] 並將壓縮檔案儲存到桌面。
5. 在壓縮檔案上按一下滑鼠右鍵，並選取 [解壓縮全部...] 指定 "C:\" 並按一下 [**解壓縮**]。檔案解壓縮之後，您可能希望重新命名並簡化安裝資料夾的名稱。例如 "MongoDB"。
6. 在您先前建立的資料磁碟中建立 MongoDB 資料和記錄目錄 (例如磁碟機 **F:**)。在 [開始] 功能表中，選取 [命令提示字元] 以開啟命令提示字元視窗。輸入：

		C:> F:
		F:> mkdir \MongoData
		F:> mkdir \MongoLogs

7. 若要執行資料庫，執行：

		F:> C:
		C:> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	在 mongod.exe 伺服器啟動和預先配置日誌檔案時，所有記錄訊息都會傳送至 *F:\\MongoLogs\\mongolog.log* 檔案。MongoDB 可能需要花費數分鐘來預先配置日誌檔案，並開始接聽連線。

8. 若要啟動 MongoDB 管理殼層，請從 [開始] 功能表中開啟另一個命令視窗，並輸入下列內容：

		C:> cd \my_mongo_dir\bin  
		C:\my_mongo_dir\bin> mongo  
		>db  
		test  	  
		> db.foo.insert( { a : 1 } )  
		> db.foo.find()  
		{ _id : ..., a : 1 }  
		> show dbs  
		...  
		> show collections  
		...  
		> help  

	資料庫由插入項目建立。
9. (選擇性) mongod.exe 支援安裝並執行 Windows 服務。要安裝 mongod.exe 做為服務，請在命令提示字元視窗中執行：

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	這樣會建立一個名為 "Mongo DB" 的服務，其說明為 "Mongo DB"。**--logpath** 選項必須用來指定記錄檔，因為執行中的服務沒有命令視窗可以顯示輸出。**--logpath** 選項指出重新啟動服務會導致輸出附加在現有的記錄檔案中。**--dbpath** 選項指出資料目錄的位置。F如需更多服務相關的命令列選項，請參閱[服務相關的命令列選項][MongoWindowsSvcOptions]。
10. 現在，MongoDB 已安裝並正在執行，您必須在 Windows 防火牆中開啟一個連接埠，才能遠端連線至 MongoDB。在 [開始] 功能表中，選取 [管理員工具] 以及 [具備進階安全性的 Windows 防火牆]。 

11. 在左側窗格中，選取 [內送規則]。在右側的 [動作] 窗格中，選取 [新增規則...]。
	
	![Windows 防火牆][Image1]

	在 [新內送規則精靈] 中，選取 [連接埠] 並按一下 [下一步]。
	
	![Windows 防火牆][Image2]

	選取 [TCP] 以及 [指定本機連接埠]。指定連接埠為 "27017" (MongoDB 接聽的連接埠) 並按 [**下一步**]。

	![Windows 防火牆][Image3]

	選取 [允許連線] 並按一下 [下一步]。

	![Windows 防火牆][Image4]

	再按一下 [下一步]。
	
	![Windows 防火牆][Image5]

	指定規則名稱，例如 "MongoPort"，並按一下 [結束]。

	![Windows 防火牆][Image6]
	
12. 如果您在建立虛擬機器時沒有設定 MongoDB 的端點，您現在可以進行此動作。您需要防火牆規則和端點兩者，才能遠端連線至 MongoDB。在管理入口網站中，按一下 [虛擬機器]，再按一下新虛擬機器的名稱，然後按一下 [端點]。

	![Endpoints][Image7]
13. 按一下頁面底部的 [新增端點]。選取 [新增端點] 並按一下 [下一步]。
	
	![Endpoints][Image8]

14. 新增名為 "Mongo" 的端點，通訊協定為 **TCP**，且**公開**以及**私人**連接埠均設為 "27017"。這樣可允許 MongoDB 遠端存取。

	![Endpoints][Image9]

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

<!---HONumber=July15_HO2-->