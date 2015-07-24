1. 按一下 Android Studio 工具列上的圖示以開啟 Android SDK Manager，或按一下工能表上的 [工具] -> [Android] -> [SDK Manager]。尋找專案中使用之 Android SDK 的目標版本，開啟該版本，然後選擇 [Google API] (如果尚未安裝的話)。

2. 向下捲動至 [Extras] 並展開，然後選擇 [Google Play 服務]，如下所示。按一下 [安裝套件]。記錄以下步驟使用的 SDK 路徑。

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 開啟應用程式目錄中的 **build.gradle** 檔案。

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. 新增此行至 *相依項目*下方：

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. 在 *defaultConfig* 下方，將 *minSdkVersion* 值變更為 9。
 
6. 按一下工具列中的 [**同步處理專案與 Gradle 檔案**] 圖示。

7. 開啟 **AndroidManifest.xml** 並將此標記加入至應用程式標記。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=July15_HO2-->