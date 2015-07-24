
接下來，您必須變更推播通知註冊的方法，以確認使用者在嘗試註冊前已經驗證。

1. 在 Visual Studio 的 [方案總管] 中開啟 app.xaml.cs 專案檔案，並在 **Application_Launching** 事件處理常式中註解化或刪除對 **AcquirePushChannel** 方法的呼叫。 
 
2. 將 **AcquirePushChannel** 方法的存取設定性由 `private` 變更為 `public`，然後新增 `static` 修飾詞。

3. 開啟 MainPage.xaml.cs 專案檔，並使用下列程式碼來取代 **OnNavigatedTo** 方法覆寫：

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }

<!---HONumber=July15_HO2-->