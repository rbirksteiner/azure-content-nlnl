
#### 從 Windows PowerShell for StorSimple 安裝 Update 1

1. 執行下列步驟以下載軟體更新。

    1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)。
    2. 如果您是第一次使用者，系統會提示您安裝 Microsoft Update Catalog。按一下 [Install]。
    
        ![安裝目錄](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. 您會看到目錄搜尋畫面。在搜尋方塊中輸入 **3063418** ，然後按一下 [**搜尋**]。

        ![搜尋目錄](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. 您會看到 **StorSimple Update 1.0 Appliance Update** 套件組合。按一下 [新增]。更新將會加入到購物籃中。

        ![更新套件組合](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. 按一下 [**檢視購物籃**]。
 
        ![檢視購物籃](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. 按一下 [下載]。指定或瀏覽至您想要儲存下載項目的本機位置。更新 (all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) 將下載到所選位置的 StorSimple Update 1.0 Appliance Update 套件組合 (KB3063418) 資料夾中。資料夾也可以複製到裝置可連線的網路共用位置。
        
2. 若要安裝軟體更新，請存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。請依照[使用 PuTTy 連接到序列主控台](#use-putty-to-connect-to-the-serial-console)中的詳細指示執行作業。

3. 在命令提示字元中，按 Enter 鍵。

4. 選取 [**選項 1**] 以使用完整的存取權限登入裝置。

5. 若要安裝更新套件，請在命令提示字元中輸入：

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    只有當您要存取已驗證的共用位置時，才會用到認證參數。

    範例輸出如下所示。

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. 當系統提示您確認 Hotfix 安裝時，請輸入 **Y**。

7. 使用 Get-HcsUpdateStatus Cmdlet 監視更新。

    下列範例輸出顯示更新進行中。

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     下列範例輸出指出更新已完成。

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. 軟體更新完成後，請瀏覽至管理入口網站中的 [維護] 頁面。掃描可用的更新。您應該會看到更多可用的軟體更新。

9. 按一下 [**安裝更新**] 以從入口網站套用所有可用的更新。

10. 軟體更新完成之後，請確認系統軟體、驅動程式和韌體版本。輸入以下命令：

    `Get-HcsSystem`

    您應該會看見下列版本：

    - HcsSoftwareVersion：6.3.9600.17491
    - CisAgentVersion：1.0.9037.0
    - MdsAgentVersion：26.0.4696.1433 
 
11. 若要確認已正確更新韌體，請輸入：

    `Start-HcsFirmwareCheck`

    韌體狀態應該是 **UpToDate**。

<!---HONumber=July15_HO2-->