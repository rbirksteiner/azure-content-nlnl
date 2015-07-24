

1. 從 Eclipse 的頂端工具列按一下 [視窗] 來開啟 Android SDK Manager。尋找專案屬性中指定之 Android SDK 的目標版本，開啟該版本並選擇 [Google API]。

2. 向下捲動至 [Extras] 並展開，然後選擇 [Google Play 服務]，如下所示。按一下 [安裝套件]。記錄以下步驟使用的 SDK 路徑。重新啟動 Eclipse。

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 在您的專案中安裝 Google Play 服務 SDK。在 Eclipse 中，按一下 [檔案]，然後按 [匯入]。依序選取 [Android] 及 [匯入現有的 Android 程式碼至工作區]，然後按 [下一步]。按一下 [瀏覽]，導覽至 Android SDK 路徑 (通常位於包含 Eclipse 的資料夾內，且名為 `adt-bundle-windows-x86_64` 的資料夾)，然後移至 `\extras\google\google_play_services\libproject` 子資料夾，在該處選取 google-play-services-lib 資料夾，再按一下 [確定]。核取 [將專案複製到工作區] 核取方塊，再按一下 [完成]。

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. 您接著必須從專案參照剛才匯入的 Google Play 服務 SDK 程式庫。

5. 在 [封裝總管] 中以滑鼠右鍵按一下專案，再選擇 [屬性]。
 
6. 在 [屬性] 視窗中，選擇左側的 Android。

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. 在 [專案建置目標] 下，確定已針對適當的 SDK 層級選取 `Google APIs x86` (或 `Google APIs`，依您的開發平台而定)。

 
8. 在 [程式庫] 區段中，選擇 [新增]，然後選取 Google Play 服務專案 (*google-play-services-lib*)，再按一下 [確定]。

9. 依序按一下 [套用] 及 [確定]。

<!---HONumber=July15_HO2-->