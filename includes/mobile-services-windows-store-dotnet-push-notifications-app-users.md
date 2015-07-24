
接下來，您需要變更註冊推播通知的方式，以便在嘗試註冊之前，能夠確定使用者已通過驗證。用戶端應用程式更新會根據您實作推播通知的方法而定。

###在 Visual Studio 2013 Update 2 或更新版本中使用加入推播通知精靈

在這個方法中，精靈會在專案中產生新的 push.register.cs 檔案。

1. 在 Visual Studio 的 [方案總管] 中，開啟 app.xaml.cs 專案檔，然後在 **OnLaunched** 事件處理常式中，將 **UploadChannel** 方法的呼叫標記為註解或加以刪除。 

2. 開啟 push.register.cs 專案檔，並使用下列程式碼來取代 **UploadChannel** 方法：

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	這樣就能確定已使用具有已通過驗證之使用者認證的相同用戶端執行個體完成註冊。否則，註冊將會失敗，並產生「未經授權」(401) 的錯誤。

3. 開啟共用的 MainPage.cs 專案檔案，並將 **ButtonLogin_Click** 處理常式取代為：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			todolistPush.UploadChannel();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }

	這樣可確保在嘗試推送註冊之前先進行驗證。

4. 	在上一段程式碼中，使用精靈產生的類別名稱取代所產生的推送類別名稱 (`todolistPush`)，格式通常是 <code><em>mobile_service</em>Push</code>。

###手動啟用推播通知		

在這個方法中，您已將從教學課程取得的註冊程式碼直接新增到 app.xaml.cs 專案檔。

1. 在 Visual Studio 的 [方案總管] 中，開啟 app.xaml.cs 專案檔，然後在 **OnLaunched** 事件處理常式中，將 **InitNotificationsAsync** 的呼叫標記為註解或加以刪除。 
 
2. 將 **InitNotificationsAsync** 方法的存取設定從 `private` 變更為 `public`，然後新增 `static` 修飾詞。

3. 開啟共用的 MainPage.cs 專案檔案，並將 **ButtonLogin_Click** 處理常式取代為：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			App.InitNotificationsAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
	
	這樣可確保在嘗試推送註冊之前先進行驗證。

<!---HONumber=July15_HO2-->