1. 開啟專案檔案 mainpage.xaml.cs，然後將下列程式碼片段新增至 MainPage 類別：
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
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

                MessageBox.Show(message);
            }
        }

    如此會建立一個成員變數來存放目前使用者，並建立一個方法來處理驗證程序。使用者透過 Facebook 登入來驗證。

    >[AZURE.NOTE]如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 <strong>MobileServiceAuthenticationProvider</strong> 值變更成您的提供者。</p> </div>

2. 刪除或註解化現有的 **OnNavigatedTo** 方法，這將會以下列處理頁面上 **Loaded** 事件的方法覆寫和取代該現有方法。

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	此方法會呼叫新的 **Authenticate** 方法。

3. 以下列程式碼取代 MainPage 建構函式：

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	This constructor also registers the handler for the Loaded event.
		
4. 按 F5 鍵執行應用程式，並以您選擇的身分識別提供者登入應用程式。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!---HONumber=July15_HO2-->