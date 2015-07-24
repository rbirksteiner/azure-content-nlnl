
1. 開啟共用的專案檔案 MainPage.cs 並新增下列 using 陳述式：

        using Windows.UI.Popups;

2. 將下列程式碼片段新增至 MainPage 類別：
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
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

    此使用者即可透過 Facebook 登入來驗證。如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 **MobileServiceAuthenticationProvider** 值變更成您提供者。

3. 將現有 **OnNavigatedTo** 方法覆寫中的 **RefreshTodoItems** 方法呼叫註解化或刪除。

	如此能避免在使用者通過驗證之前載入資料。

	>[AZURE.NOTE]若要成功地從 Windows Phone 市集 8.1 應用程式驗證，您必須在系統呼叫 **OnNavigated** 方法後，以及在引發頁面的 **Loaded** 事件後，呼叫 LoginAsync。在本教學課程中，您可以藉由將 **Sign in** 按鈕新增至應用程式中來達成目的。

4. 將下列程式碼片段新增至 MainPage 類別：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile app.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. 在 Windows 市集應用程式專案中，開啟 MainPage.xaml 專案檔案，並將以下 **Button** 元素新增至定義 **Save** 按鈕的元素前方：

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. 針對 Windows Phone 市集應用程式專案重複上一個步驟，不過這次請將 **Button** 新增至 **TitlePanel** 中，使其位於 **TextBlock** 元素後方。

7. 開啟共用的 App.xaml.cs 專案檔案並新增下列 using 陳述式 (如果該陳述式不存在的話)：

        using Microsoft.WindowsAzure.MobileServices;  
 
8. 在 App.xaml.cs 專案檔案中，新增下列程式碼：

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	如果 **OnActivated** 方法已存在，只要新增 `#if...#endif` 程式碼區塊即可。

9. 按 F5 鍵以執行 Windows 市集應用程式、按一下 **Sign in** 按鈕，然後以您選擇的身分識別提供者登入應用程式。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動應用程式並更新資料。

10. 以滑鼠右鍵按一下 Windows Phone 市集應用程式專案、按一下 [設定為啟始專案]，然後重複上一個步驟以驗證 Windows Phone 市集應用程式是否也能正常運作。

<!---HONumber=62-->