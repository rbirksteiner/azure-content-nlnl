<properties 
	pageTitle="存取 Azure Active Directory Graph 資訊 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows 市集應用程式中使用 Graph API 存取 Azure Active Directory 資訊。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="wesmc"/>

# 存取 Azure Active Directory 圖形資訊



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##概觀

如同其他隨行動服務提供的身分識別提供者，Azure Active Directory (AAD) 提供者也支援豐富的圖形 API，可用於對目錄的程式設計存取。在本教學課程中，您會使用[Graph REST API] 從目錄中擷取的其他使用者資訊來更新 ToDoList 應用程式，藉此個人化已驗證使用者的應用程式體驗。

如需 Azure AD Graph API 的詳細資訊，請參閱 [Azure Active Directory 圖形團隊部落格]。


>[AZURE.NOTE]本教學課程的目的是要擴充您使用 Azure Active Directory 進行驗證的知識。您應先使用 Azure Active Directory 驗證提供者，完成[將驗證新增至您的應用程式]教學課程。本教學課程接著會更新[將驗證新增至您的應用程式]教學課程中使用的 TodoItem 應用程式。




##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [將驗證新增至您的應用程式]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [自訂 API 教學課程]<br/>說明如何呼叫自訂 API。



## <a name="generate-key"></a>在 AAD 中產生應用程式註冊的存取金鑰


在進行[將驗證新增至您的應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>建立 GetUserInfo 自訂 API

在本節中，您將建立 GetUserInfo 自訂 API，以使用 Azure AD Graph API 從 AAD 擷取使用者的其他相關資訊。

如果您未曾搭配使用自訂 API 與行動服務，請先參閱[自訂 API 教學課程]，再開始執行本節步驟。

1. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務.NET 後端專案，然後按一下 [管理 NuGet 封裝]。
2. 在 [NuGet Package Manager] 對話方塊中，在搜尋條件中輸入 **ADAL**，以尋找並安裝您的行動服務的 [Active Directory Authentication Library]。本教學課程近期多搭配 3.3.205061641-alpha (搶鮮版) 版本的 ADAL 封裝進行測試。


3. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務專案的 [控制器] 資料夾，然後按一下 [新增]，以新增名為 `GetUserInfoController` 的 [Microsoft Azure 行動服務自訂控制器]。用戶端會呼叫此 API，從 Active Directory 中取得使用者資訊。

4. 在新的 GetUserInfoController.cs 檔案中，新增下列 `using` 陳述式。

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. 在新的 GetUserInfoController.cs 檔案中，新增下列 `UserInfo` 類別，以存放我們想從 AAD 蒐集的資訊。

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
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. 在 GetUserInfoController.cs 中，將下列成員變數新增至 `GetUserInfoController` 類別。

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. 在 GetUserInfoController.cs 中，將下列 `GetAADToken` 方法新增至類別。

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    此方法會使用您在 [Azure 管理入口網站]中為行動服務設定的應用程式設定，取得用來存取 Active Directory 的權杖。

7. 在 GetUserInfoController.cs 中，將下列 `GetAADUser` 方法新增至類別。

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    此方法會取得已獲授權之使用者的 Active Directory 物件識別碼，然後使用 Graph REST API 從 Active Diretory 取得使用者的資訊。


8. 在 GetUserInfoController.cs 中，以下列方法取代 `Get` 方法。此方法會使用 Graph REST API 從 Azure Active Directory 傳回使用者實體，並要求已獲授權的使用者呼叫 API。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. 儲存變更，並建置服務以驗證沒有語法錯誤。
10. 將行動服務專案發行至您的 Azure 帳戶。 


## <a name="update-app"></a>更新應用程式以使用 GetUserInfo

在本節中，您會更新在[將驗證新增至您的應用程式]教學課程中實作的 `AuthenticateAsync` 方法，以呼叫自訂 API 並從 AAD 傳回使用者的其他相關資訊。

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>接續步驟

在下一個教學課程[行動服務中使用 AAD 的角色型存取控制]中，您將會搭配使用角色型存取控制與 Azure Active Directory (AAD) 來檢查群組成員資格，然後允許存取。



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[將驗證新增至您的應用程式]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[自訂 API 教學課程]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[註冊使用 Azure Active Directory 登入]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Active Directory 圖形團隊部落格]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[行動服務中使用 AAD 的角色型存取控制]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md
 

<!---HONumber=July15_HO2-->