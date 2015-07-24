

下面的指示適用於測試 Windows 市集用戶端應用程式，但您可以在 Azure 行動服務所支援的任何平台上進行測試。


1. 在 Visual Studio 中，開啟 MainPage.xaml.cs 並在檔案頂端新增下列 `using` 陳述式。
 
        using System.Net.Http;

2. 在 MainPage.xaml.cs 中，在命名空間中新增下列類別定義，以協助序列化使用者資訊。

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. 在 MainPage.xaml.cs 中，更新 `AuthenticateAsync` 方法以呼叫自訂 API 從 AAD 中傳回使用者的其他資訊。

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
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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


4. 儲存變更，並建置服務以驗證沒有語法錯誤。

<!---HONumber=July15_HO2-->