
本教學課程以 [GetStartedWithMobileServices 應用程式](http://go.microsoft.com/fwlink/p/?LinkID=510826)　(這是 Visual Studio 2013 中的通用 Windows 應用程式專案) 做為建置基礎。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1. 從 [開發人員程式碼範例網站] 中下載 JavaScript 版本的 GetStartedWithMobileServices 範例應用程式。 

3. 在 Visual Studio 2013 中開啟下載的專案、在 [方案總管] 中展開 js 資料夾，然後檢查 default.js 檔案。

   	請注意，新增的 **TodoItem** 物件儲存在記憶體內部 `WinJS.Binding.List` 中。

4. 按 **F5** 鍵，以重建專案並啟動應用程式。

5. 在應用程式的 [Insert a TodoItem] 中輸入一些文字，然後按一下 [儲存]。

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png)

   	請注意，儲存的文字隨即會顯示。

6. 以滑鼠右鍵按一下 Windows Phone 8.1 專案、按一下 [設定為啟始專案]，然後按 **F5** 以啟動 Windows Phone 市集應用程式。

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. 重複步驟 3 和 4 以確認範例的行為相同。

<!---HONumber=July15_HO2-->