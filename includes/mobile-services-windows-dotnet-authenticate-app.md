
1. 開啟專案檔案 MainPage.xaml.cs，並新增下列 using 陳述式：

        using Windows.UI.Popups;

2. 將下列程式碼片段新增至 MainPage 類別：
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    如此會建立一個成員變數來存放目前使用者，並建立一個方法來處理驗證程序。使用者會透過 Facebook 登入受到驗證。如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 **MobileServiceAuthenticationProvider** 值變更成您提供者。

3. 將現有 **OnNavigatedTo** 方法覆寫，取代為以下這個會呼叫新 **Authenticate** 方法的方法：

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
4. 按 F5 鍵執行應用程式，並以您選擇的身分識別提供者登入應用程式。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!---HONumber=July15_HO2-->