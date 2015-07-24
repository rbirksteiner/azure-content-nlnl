由於持續進行開發，因此 Eclipse 中安裝的 Android SDK 版本可能不符合程式碼中的版本。此教學課程參照的 Android SDK 為 21 版，是撰寫本文時的最新版本。隨著新修訂版 SDK 發行，版本號碼可能會隨之增加，我們建議您使用最新的可用版本。

版本不符合的兩個徵兆為：

1. 查看底部窗格的 Eclipse Console。您可能會看到格式為「**無法解析目標 'android-n'**」的錯誤訊息。

2. 錯誤訊息可能是程式碼中必須根據 `import` 陳述式解析的標準 Android 物件所產生。

若出現其中任一項，則 Eclipse 中安裝的 Android SDK 版本可能不符合下載專案的 SDK 目標。若要確認版本，請進行下列變更：


1. 在 Eclipse 中，按一下 [視窗]，然後按 [Android SDK Manager]。若尚未安裝最新版的 SDK 平台，則按一下以安裝它。記下版本號碼。

2. 開啟專案檔案 **AndroidManifest.xml**。確定在 **uses-sdk** 元素中，將 **targetSdkVersion** 設定為安裝的最新版本。**uses-sdk** 標籤外觀如下：
 
	 	    <uses-sdk
	 	        android:minSdkVersion="8"
	 	        android:targetSdkVersion="21" />
	
3. 在 Eclipse [封裝總管] 中，以滑鼠右鍵按一下專案節點，選擇 [屬性]，然後在左欄中選擇 [Android]。確定將 [專案建置目標] 設為 **targetSdkVersion** 所具有的相同 SDK 版本。

<!---HONumber=July15_HO2-->