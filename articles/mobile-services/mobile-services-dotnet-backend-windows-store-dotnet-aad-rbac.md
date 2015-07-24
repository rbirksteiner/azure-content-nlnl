<properties 
	pageTitle="行動服務和 Azure Active Directory 中的角色型存取控制 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows Store 應用程式中控制以 Azure Active Directory 角色為基礎的存取權。" 
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

# 行動服務和 Azure Active Directory 中的角色型存取控制

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##概觀

角色型存取控制 (RBAC) 是針對使用者可擔任的角色指派權限的作法。它可妥善定義特定類別的使用者能夠與無法執行哪些動作的界線。本教學課程將逐步解說如何將基本 RBAC 新增至 Azure 行動服務。

本教學課程將說明角色型存取控制，檢查每個使用者在 Azure Active Directory (AAD) 中定義之「銷售」群組的成員資格。存取檢查將在 .NET 行動服務後端中，使用 Azure Active Directory 的[Graph REST API] 來執行。只有屬於「銷售」群組的使用者能夠查詢資料。


>[AZURE.NOTE]本教學課程的目的是要擴充驗證知識以加入授權實務作法。您應先使用 Azure Active Directory 驗證提供者完成[將驗證新增至您的應用程式]教學課程。本教學課程接著將更新[將驗證新增至您的應用程式]教學課程中使用的 TodoItem 應用程式。

##必要條件

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 使用 Azure Active Directory 驗證提供者完成[將驗證新增至您的應用程式]教學課程。

 


##為整合的應用程式產生金鑰


在進行[將驗證新增至您的應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。

如果您已瀏覽[存取 Azure Active Directory Graph 資訊]教學課程，代表您已經完成此步驟，並可略過本節。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##建立具有成員資格的銷售群組

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##在行動服務上建立自訂授權屬性 

在本節中，您將新建可用來對行動服務作業執行存取檢查的自訂授權屬性。屬性將會根據傳至 Active Directory 群組的角色名稱查閱該群組。接著，它會根據該群組的成員資格執行存取檢查。

1. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務.NET 後端專案，然後按一下 [管理 NuGet 封裝]。

2. 在 [NuGet Package Manager] 對話方塊中，在搜尋條件中輸入 **ADAL**，以尋找並安裝您的行動服務的 [Active Directory Authentication Library]。本教學課程近期多搭配 3.3.205061641-alpha (搶鮮版) 版本的 ADAL 封裝進行測試。

3. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務專案，然後依序按一下 [新增] 和 [新資料夾]。將新資料夾命名為 **Utilities**。

4. 在 Visual Studio 中，以滑鼠右鍵按一下新的 **Utilities** 資料夾，然後新增名為 **AuthorizeAadRole.cs** 的類別檔案。

    ![][0]

5. 在 AuthorizeAadRole.cs 檔案中，在檔案最上方新增下列 `using` 陳述式。

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. 在 AuthorizeAadRole.cs 中，將下列列舉類型新增至「公用程式」命名空間。在此範例中，我們只會處理 [銷售] 角色。其他角色只是您可能使用之群組的範例。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. 在 AuthorizeAadRole.cs 中，將下列 `AuthorizeAadRole` 類別定義新增至「公用程式」命名空間。

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;
	
	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";
	
	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();
	
	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as 
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to 
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. 在 AuthorizeAadRole.cs 中，更新 `AuthorizeAadRole` 類別中的 `InitGroupIds` 方法，如下所示。此方法會建立一個字典，將群組識別碼對應至每個角色。

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. 在 AuthorizeAadRole.cs 中，更新 `AuthorizeAadRole` 類別中的 `GetAADToken` 方法。此方法會使用儲存在行動服務中的應用程式設定，以取得從 ADAL 存取 AAD 的權杖。

    >[AZURE.NOTE]根據預設，適用於 .NET 的 ADAL 包含記憶體內部權杖快取，有助於減輕對您 Active Directory 的額外網路流量。但是，您可以寫入自己的快取實作或完全停用快取。如需詳細資訊，請參閱[適用於 .NET 的 ADAL]。

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. 在 AuthorizeAadRole.cs 中，更新 `AuthorizeAadRole` 類別中的 `CheckMembership` 方法。此方法會接收使用者的物件識別碼。接著，它使用 AAD Graph Rest API 來檢查物件識別碼，以查看群組的成員識別碼是否與 `AuthorizeAadRole` 類別中選取的角色相關聯。

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. 在 AuthorizeAadRole.cs 中，使用下列程式碼更新 `AuthorizeAadRole` 類別中的 `OnAuthorization` 方法。此程式碼預期呼叫行動服務的使用者已利用 AAD 完成驗證。接著，它會取得使用者的 AAD 物件識別碼，檢查與該角色對應之 Active Directory 群組的成員資格。

    >[AZURE.NOTE]您可以依名稱查詢 Active Directory 群組。但是，在許多情況下，將群組識別碼儲存為行動服務應用程式設定，會是較理想的作法。這是因為群組名稱比較有可能會變更，但識別碼會保持相同。

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. 將您的變更儲存至 AuthorizeAadRole.cs。

##將角色型存取檢查新增至資料庫作業

1. 在 Visual Studio 中，展開行動服務專案下的 **Controllers** 資料夾。開啟 TodoItemController.cs。

2. 在 TodoItemController.cs 中，為您包含自訂授權屬性的公用程式命名空間新增 `using` 陳述式。

        using todolistService.Utilities;

3. 在 TodoItemController.cs 中，您可以根據檢查存取的方式，將屬性新增至您的控制器類別或個別方法。如果您要讓所有控制器作業根據相同的角色來檢查存取，您只需將屬性新增至類別即可。請依照下列方式將屬性新增至類別，以測試本教學課程。

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    如果您要存取檢查插入、更新和刪除作業，您必須以下列方式，僅設定這些方法的屬性。

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. 儲存 TodoItemController.cs 並建置行動服務，以驗證語法錯誤。
5. 將行動服務發行至您的 Azure 帳戶。


##測試用戶端的存取

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[將驗證新增至您的應用程式]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[註冊使用 Azure Active Directory 登入]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[存取 Azure Active Directory Graph 資訊]: mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info.md
[適用於 .NET 的 ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=July15_HO1-->