
1. 在 [混合式連線] 分頁中，按一下您剛剛建立的混合式連線，然後按一下 [接聽程式設定]。
	
	![Click Listener Setup](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
	
4. [混合式連線屬性] 分頁隨即開啟。在 [**內部部署 Hybrid Connection Manager**] 下，選擇 [**手動下載和設定**]，並儲存下載的 HybridConnectionManager.msi 封裝，然後複製閘道器連接字串。
	
	![Click here to install](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
	
5. 從系統管理員命令提示字元中，輸入下列命令以啟動安裝程式：

		start HybridConnectionManager.msi
 
7. 執行安裝程式之後，請按一下 [**不是現在**]，然後瀏覽至 %ProgramFiles%\Microsoft\HybridConnectionManager 資料夾，並執行 HCMConfigWizard.exe，然後按一下 [**使用者帳戶控制**] 對話方塊中的 [**是**]。
		
7. 貼上您之前複製的混合式連接字串，然後按一下 [**確定**]。
	
	![安裝](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
	
8. 安裝完成後，請按一下 [關閉]。
	
	![Click Close](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
	
	在 [混合式連線] 分頁上，[狀態] 欄此時會顯示為 [未連線]。
	
	![Connected Status](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

<!---HONumber=62-->