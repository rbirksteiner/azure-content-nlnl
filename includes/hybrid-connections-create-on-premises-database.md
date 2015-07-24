
本節說明如何安裝 SQL Server Express、啟用 TCP/IP、設定靜態連接埠，以及建立可以與「混合式連線」搭配使用的資料庫。

###安裝 SQL Server Express

若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。因此，我們將會安裝預設執行個體。如果您已經安裝 SQL Server Express 的預設執行個體，您可以略過本節。

1. 若要安裝 SQL Server Express，請執行您已下載的 **SQLEXPRWT_x64_ENU.exe** 或 **SQLEXPR_x86_ENU.exe** 檔案。[SQL Server 安裝中心] 精靈會隨即出現。
	
2. 選擇 [**新的 SQL Server 獨立安裝或將功能加入到現有安裝**]、遵循指示、接受預設選項和設定，直到您進入 [**執行個體組態**] 頁面。
	
3. 在 [**執行個體組態**] 頁面上，選擇 [**預設執行個體**]，然後接受 [**伺服器組態**] 頁面上的預設設定。

	>[AZURE.NOTE]如果您已經安裝 SQL Server 的預設執行個體，您可以跳到下一節，搭配「混合式連線」使用此執行個體。
	
5. 在 [**資料庫引擎組態**] 頁面上的 [**驗證模式**] 下，選擇 [**混合模式 (SQL Server 驗證和 Windows 驗證)**]，並提供內建 **sa** 系統管理員帳戶的安全密碼。
	
	在本教學課程中，您將使用 SQL Server 驗證。請務必記住您提供的密碼，因為後續將會用到。
	
6. 完成精靈，以完成安裝。

###啟用 TCP/IP 和設定靜態連接埠

本節使用您在安裝 SQL Server Express 時所安裝的 SQL Server 組態管理員，以啟用 TCP/IP 和設定靜態 IP 位址。

1. 遵循[為 SQL Server 啟用 TCP/IP 網路通訊協定](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)中的步驟，以啟用執行個體的 TCP/IP 存取。

2. (選擇性) 如果您不能使用預設執行個體，您必須遵循[設定伺服器接聽特定 TCP 連接埠](https://msdn.microsoft.com/library/ms177440.aspx)中的步驟，以設定執行個體的靜態連接埠。如果您完成此步驟，您將會使用您定義的新連接埠來連接，而不是使用連接埠 1433。

3. (選擇性) 如有需要，新增防火牆例外狀況以允許遠端存取 SQL Server 處理序 (sqlservr.exe)。

###在內部部署 SQL Server 執行個體中建立新的資料庫

1. 在 SQL Server Management Studio 中，連接到您剛剛安裝的 SQL Server。(如果 [**連線到伺服器**] 對話方塊未自動出現，請導覽至左窗格中的 [**物件總管**]，依序按一下 [**連接**] 和 [**資料庫引擎**]。) 	

	![連接到伺服器](./media/hybrid-connections-create-on-premises-database/A04SSMSConnectToServer.png)
	
	針對 [**伺服器類型**]，選擇 [**資料庫引擎**]。對於 [**伺服器名稱**]，您可以使用 **localhost** 或您要安裝 SQL Server 之電腦的名稱。選擇 [**SQL Server 驗證**]，並且提供您先前建立的 sa 登入密碼。
	
2. 若要使用 SQL Server Management Studio 建立新資料庫，請在 [物件總管] 中以滑鼠右鍵按一下 [資料庫]，然後按一下 [**New Database**]。
	
3. 在 [**新的資料庫**] 對話方塊中，輸入 `OnPremisesDB`，然後按一下 [**確定**]。
	
4. 在 [物件總管] 中，如果您展開 [**資料庫**]，您會發現新資料庫已建立。

###建立新的 SQL Server 登入並且設定權限

最後，您將會建立新的 SQL Server 登入，具有限制的權限。您的 Azure 服務會使用此登入而不是內建 sa 登入 (具有伺服器的完整權限)，連接到在內部部署 SQL Server。

1. 在 SQL Server Management Studio 物件總管中，以滑鼠右鍵按一下 [**OnPremisesDB**] 資料庫，然後按一下 [**新增查詢**]。

2.  將下列 TSQL 查詢貼上至查詢視窗。

		USE [master]
		GO
		
		/* Replace the PASSWORD in the following statement with a secure password. 
		   If you save this script, make sure that you secure the file to 
		   securely maintain the password. */ 
		CREATE LOGIN [HybridConnectionLogin] WITH PASSWORD=N'<**secure_password**>', 
			DEFAULT_DATABASE=[OnPremisesDB], DEFAULT_LANGUAGE=[us_english], 
			CHECK_EXPIRATION=OFF, CHECK_POLICY=ON
		GO
	
		USE [OnPremisesDB]
		GO
	
		CREATE USER [HybridConnectionLogin] FOR LOGIN [HybridConnectionLogin] 
		WITH DEFAULT_SCHEMA=[dbo]
		GO

		GRANT CONNECT TO [HybridConnectionLogin]
		GRANT CREATE TABLE TO [HybridConnectionLogin]
		GRANT CREATE SCHEMA TO [HybridConnectionLogin]
		GO  
   
3. 在上述指令碼 中，將字串 `<**secure_password**>` 取代為新的 *HybridConnectionsLogin* 的安全密碼。

4. **執行**查詢以在內部部署資料庫中建立新的登入，並且授與必要的權限。

<!---HONumber=July15_HO2-->