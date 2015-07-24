
先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。

>[AZURE.NOTE]無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。本教學課程使用服務管理驗證。

1. 在 MainPage.xaml.cs 專案檔中，新增下列 **using** 陳述式：

		using System.IO.IsolatedStorage;
		using System.Security.Cryptography;		

2. 使用下列程式碼來取代 **AuthenticateAsync** 方法：

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

	在這個版本的 **AuthenticateAsync** 中，應用程式會嘗試使用已在本機儲存體中加密儲存的認證來存取行動服務。系統會傳送簡單的查詢，以確認儲存的權杖尚未到期。傳回 401 時，即會嘗試以提供者為主的一般登入。如果沒有儲存任何認證，也會執行一般登入。

	>[AZURE.NOTE]這個應用程式會在登入期間測試到期的權杖，但是權杖可能會在應用程式使用期間經驗證之後到期。如需處理與權杖到期相關之授權錯誤的方案，請參閱下列文章：[在 Azure 行動服務管理的 SDK 中快取和處理到期的權杖](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx) (英文)。
	
3. 重新啟動應用程式兩次。

	請注意，第一次啟動時，需要再次使用該提供者登入。不過，在第二次重新啟動時，可以使用快取的認證，並略過登入。

<!---HONumber=July15_HO2-->