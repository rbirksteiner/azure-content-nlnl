
1. 在 Visual Studio 的 [方案總管] 中，展開 App_Start 資料夾，然後開啟 WebApiConfig.cs 專案檔。

2. 將下一行程式碼新增至 **ConfigOptions** 定義之後的 Register 方法中：

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	這樣會在註冊推播通知之前，強制執行使用者驗證。

2. 以滑鼠右鍵按一下專案、按一下 [新增]，然後按一下 [類別...]。

3. 將新的空類別命名為 `PushRegistrationHandler`，然後按一下 [新增]。

4. 在程式碼頁面頂端，新增下列 **using** 陳述式：

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. 使用下列程式碼來取代現有的 **PushRegistrationHandler** 類別：
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	**Register** 方法會在註冊期間呼叫。這樣讓您能夠將標籤新增到已登入使用者識別碼的註冊中。已驗證所提供的標記，防止使用者註冊另一位使用者的識別碼。當傳送通知給這位使用者時，此裝置和該使用者已註冊的任何其他裝置都會收到該通知。

6. 展開 Controllers 資料夾、開啟 TodoItemController.cs 專案檔、找到 **PostTodoItem** 方法，然後使用下列程式碼來取代呼叫 **SendAsync** 的程式碼行：

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. 重新發佈行動服務專案。

現在，服務會使用使用者識別碼標籤，將推播通知 (以及插入項目的文字) 傳送到該位已登入使用者建立的所有註冊中。
 

<!---HONumber=July15_HO2-->