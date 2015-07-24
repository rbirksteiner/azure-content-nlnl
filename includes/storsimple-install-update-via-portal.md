
#### 從管理入口網站安裝 Update 1

1. 在 [StorSimple 服務] 頁面上，選取您的裝置。瀏覽至 [**裝置**] > [**維護**]。

2. 按一下頁面底部的 [**掃描更新**]。將建立掃描可用更新的工作。工作成功完成時，系統將會通知您。

3. 在相同頁面的 [**軟體更新**] 區段中，您會看到新的軟體更新可供使用。我們建議您先檢閱版本資訊，然後再於裝置上套用 Update 1.0。

    ![安裝軟體更新](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. 按一下頁面底部的 [**安裝更新**]。

5. 系統將提示您進行確認。按一下 [確定]。

6. 將顯示 [**安裝更新**] 對話方塊。請確定您的裝置符合此對話方塊中所列出的檢查。選取 [**我了解上述需求而且已準備好更新我的裝置**]。按一下核取圖示。

    ![確認訊息](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. 將會通知您更新前檢查正在進行中。
  
    ![前置檢查通知](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    下列是升級前檢查失敗的範例。您必須確認這兩個裝置控制器狀況良好且在線上。您也必須檢查硬體元件的健全狀態。在此範例中，需要注意控制器 0 及控制器 1 元件。如果您不能自行解決這些問題，您可能需要連絡 Microsoft 支援服務。

    ![前置檢查失敗](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. 升級前檢查都成功完成後，將會建立更新工作。成功建立更新工作時，系統將會通知您。
 
    ![建立更新工作](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    接著，更新將套用到您的裝置上。
 
9. 若要監視更新工作的進度，請按一下 [**檢視工作**]。在 [工作] 頁面中，您可以看到更新進度。

    ![更新工作進度](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. 更新將需要幾個小時來完成。您可以隨時檢視工作的詳細資料。

    ![更新工作詳細資料](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. 工作完成後，瀏覽至 [**維護**] 頁面，並向下捲動至 [**軟體更新**]。

12. 確認您的裝置是否正在執行 **StorSimple 8000 Series Update 1.0 (6.3.9600.17491)**。[**上次更新日期**] 應該也已修改。

    ![維護頁面](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=July15_HO2-->