<properties 
	pageTitle="開始使用自訂驗證 | 行動開發人員中心" 
	description="了解如何藉由使用者名稱及密碼驗證使用者。" 
	documentationCenter="Mobile" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="mahender"/>

# 開始使用自訂驗證

## 概觀
本主題說明如何簽發您自己的行動服務驗證權杖，以在 Azure 行動服務 .NET 後端中驗證使用者。在本教學課程中，您會使用應用程式的自訂使用者名稱和密碼，將驗證新增至快速入門專案。

>[AZURE.NOTE]本教學課程將說明以自訂認證驗證行動服務的進階方法。有許多應用程式應改用內建社交身分識別提供者最為適當，以讓使用者能夠透過 Facebook、Twitter、Google、Microsoft 帳戶和 Azure Active Directory 進行登入。如果這是您第一次接觸行動服務中的驗證，請參閱[將驗證加入您的應用程式]教學課程。

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

>[AZURE.IMPORTANT]本教學課程的目的是要說明如何簽發行動服務的驗證權杖。請不要將其視為安全性指引。在開發應用程式時，您必須留意密碼儲存的安全性問題，且必須要有管理暴力密碼破解攻擊的策略。

## 設定帳戶資料表

由於您使用自訂驗證，且未仰賴其他身分識別提供者，因此您必須儲存使用者的登入資訊。在本節中，您將為帳戶建立資料表，並設定基本的安全性機制。帳戶資料表將包含使用者名稱和加料式雜湊密碼 (salted and hashed passwords)，且您也可以視需要加入其他使用者資訊。

1. 在後端專案的 **DataObjects** 資料夾中，加入名為 `Account` 的新實體。

2. 新增下列 `using` 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service;  

3. 以下列程式碼取代類別定義：

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }
    
    這代表新「帳戶」資料表中的一行資料列，其中含有使用者名稱，以及該使用者的 Salt 和安全儲存密碼。

2. 在 **Models** 資料夾下，您會看見以行動服務名稱命名的 **DbContext** 衍生類別。請開啟您的內容並加入下列程式碼，將帳戶資料表新增至您的資料模型：

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]本教學課程中的程式碼片段使用 `todoContext` 做為內容名稱。您必須更新專案內容的程式碼片段。接著，您將設定使用這項資料所需的安全性功能。
 
5. 建立名為 `CustomLoginProviderUtils` 的類別，然後新增下列 `using` 陳述式：

		using System.Security.Cryptography;

6. 將下列程式碼方法加入至新的類別：


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	這可讓您產生新的長型 Salt，新增將 salted 密碼雜湊化的能力，並提供比較兩個雜湊的安全方式。

## 建立註冊端點

至此，建立使用者帳戶的各項準備皆已完成。在本節中，您將設定註冊端點以處理新的註冊要求。您將在此處強制執行新的使用者名稱和密碼原則，並確保使用者名稱不會遭到取用。接著，您會將使用者資訊安全地儲存在資料庫中。

1. 建立以下新類別以代表傳入的註冊嘗試：

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    如果必須收集和儲存註冊期間的其他資訊，您應該在此處進行。

2. 在行動服務後端專案中，以滑鼠右鍵按一下 [控制器]，再依序按一下 [加入]、[控制器]，以建立名為 `CustomRegistrationController` 的新 **Microsoft Azure 行動服務自訂控制器**，然後新增下列 `using` 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	在上方程式碼中，以您專案的命名空間取代預留位置。
 
4. 以下列程式碼取代類別定義：

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }
	
	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }
	
	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }   

    請記得要以 **DbContext** 專案的名稱取代 *todoContext* 變數。請注意，此控制器會使用以下屬性允許此端點的所有流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]任何用戶端均可經由 HTTP 存取此註冊端點。將這項服務發行至實際執行環境之前，您應該實作某種配置來驗證註冊，例如以簡訊或電子郵件為基礎的驗證。這有助於防止惡意使用者建立詐騙註冊。

## 建立 LoginProvider

**LoginProvider** 是行動服務驗證管線中的基礎建構之一。在本節中，您將建立自己的 `CustomLoginProvider`。它並不會像內建提供者一樣插入管線中，但會為您提供方便的功能。

1. 請建立衍生自 **LoginProvider** 的新類別 `CustomLoginProvider`，並新增下列 `using` 陳述式：

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;
 
3. 以下列程式碼取代 **CustomLoginProvider** 類別定義：

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       如果您現在試著建置專案，建置會失敗。`LoginProvider` 有三種必須實作的抽象方法，您會在稍後實作。

2. 在同一個程式碼範例檔中建立名為 `CustomLoginProviderCredentials` 的新類別。

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	這代表使用者的相關資訊，而且您將可以在後端透過 [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx) 取得此資訊。如果您要新增自訂宣告，請確定這些宣告可從這個物件中擷取。

3. 將以下 `ConfigureMiddleware` 抽象方法的實作加入 **CustomLoginProvider**。

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	此方法並未實作，因為 **CustomLoginProvider** 沒有與驗證管線整合。

4. 將以下 `ParseCredentials` 抽象方法的實作加入 **CustomLoginProvider**。

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	此方法將使後端能夠從傳入的驗證權杖將使用者資訊還原序列化。

5. 將以下 `CreateCredentials` 抽象方法的實作加入 **CustomLoginProvider**。

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	此方法會將 [ClaimsIdentity] 轉譯為在驗證權杖發行階段使用的 [ProviderCredentials] 物件。此時，您可以再次以此方法擷取任何其他宣告。
	
6. 在 **ConfigOptions** 建立之後開啟 App_Start 資料夾中的 WebApiConfig.cs 專案檔案及下列程式碼行：
		
		options.LoginProviders.Add(typeof(CustomLoginProvider));

	

## 建立登入端點

接著，請建立供使用者登入的端點。系統會根據資料庫檢查您所收到的使用者名稱和密碼；方法是先套用使用者的 Salt、將密碼雜湊化，然後確定傳入的值符合資料庫的值。如果符合，即可建立 [ClaimsIdentity]，並將其傳遞至 **CustomLoginProvider**。用戶端應用程式會接收使用者識別碼和驗證權杖，藉此進一步存取您的行動服務。

1. 在行動服務後端專案中，建立下列新 `LoginRequest` 類別：

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	此類別代表傳入的登入嘗試。

2. 建立下列新 `CustomLoginResult` 類別：

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }
	
	    }

	此類別代表以使用者識別碼和驗證權杖成功登入。請注意，此類別和用戶端的 MobileServiceUser 類別具有相同形式，所以此類別能夠輕易地將登入回應傳給強型別用戶端。

2. 以滑鼠右鍵按一下 [控制器]，再依序按一下 [加入]、[控制器]，以建立名為 `CustomLoginController` 的新 **Microsoft Azure 行動服務自訂控制器**，然後新增下列 `using` 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. 以下列程式碼取代 **CustomLoginController** 類別定義：
 
	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }
	
	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);
	
	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       請記得要以 **DbContext** 專案的名稱取代 *todoContext* 變數。請注意，此控制器會使用以下屬性允許此端點的所有流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]您用於生產環境的 `CustomLoginController` 也應包含暴力密碼破解攻擊的偵測策略。否則，您的登入解決方案可能會容易受到攻擊。

## 將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## 使用測試用戶端測試登入流程

您必須在用戶端應用程式中開發自訂登入畫面，此畫面會擷取使用者名稱和密碼的，並將這些資訊以 JSON 裝載的形式傳送至註冊和登入端點。要完成此教學課程，您只需使用行動服務 .NET 後端的內建測試用戶端。

1. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務專案，然後依序按一下 [偵錯] 和 [開始新執行個體]。  

	這麼做會啟動行動服務後端專案的新偵錯執行個體。成功啟動服務之後，您會看到說明「此行動服務已啟動且在執行中」的起始頁。

2. 在服務起始頁中，按一下 [試用]，然後在驗證對話方塊中，輸入您於 web.config 檔案的 **MS_ApplicationKey** 應用程式設定中設定的密碼，並將使用者名稱保留空白。

3. 在說明頁面中，按一下 [CustomRegistration] 端點，然後按一下 [試用]。

    ![][2]

4. 在本文中，使用與您先前指定的準則相符的使用者名稱和密碼取代範例字串，然後按一下 [傳送]。

    ![][3]

	回應應為 **201/Created**。

5. 按一下瀏覽器的上一頁按鈕，並使用先前步驟中註冊的同一組使用者名稱和密碼，對 **CustomLogin** 端點重複步驟 2 和 3。

    ![][4]

	您應該會收到回應訊息，此訊息本文會包含一個具有 *userId* 和 *authenticationToken* 的 **user** JSON 物件，此物件是您的自訂驗證所產生的行動服務驗證權杖。此權杖已足以授與用戶端應用程式存取 TodoItem 端點的權限。

	複製 *authenticationToken* 的值。您會用此值存取受限制的 TodoItem 端點。

6. 按一下瀏覽器的上一頁按鈕，然後在 API 文件頁面中，依序按一下 [GetTables]、[試用]。

7. 在 GET 的要求對話方塊中，按一下 [標頭] 旁邊的加號，在左邊方塊中輸入 `X-ZUMO-AUTH` 值，並在右邊方塊中貼上複製的 *authenticationToken* 值，然後按一下 [傳送]。

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	行動服務應該會授與端點的存取權，並傳回 **200/OK** 狀態，以及資料表中的 TodoItems 清單。

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]如果您選擇也將此行動服務專案發佈至 Azure 以供測試，請記住，登入和驗證提供者也會很容易受到攻擊。請確認這些提供者已經過適當強化，或是受保護的測試資料對您而言並不重要。使用自訂的驗證配置前請務必小心，才能保護生產環境的服務。

## 使用自訂驗證從用戶端登入

本節說明從用戶端存取自訂驗證端點，以取得存取行動服務所需的驗證權杖時，所需的步驟。因為您需要的特定用戶端程式碼是取決於用戶端，所以此處提供的指引是針對未知的平台。

>[AZURE.NOTE]行動服務用戶端程式庫會經由 HTTPS 與服務通訊。由於此解決方案需要您以純文字傳送密碼，因此請務必在透過直接 REST 要求呼叫這些端點時，使用 HTTPS。

1. 在用戶端程式中建立所需的 UI 項目，藉此讓使用者輸入使用者名稱和密碼。

2. 在用戶端程式庫的 **MobileServiceClient** 上使用適當的 **invokeApi** 方法，以呼叫 **CustomRegistration** 端點，藉此在訊息本文中傳遞於執行階段提供的使用者名稱和密碼。

	如果您在「帳戶」資料表中保留了使用者登入資訊，則您只需要呼叫 **CustomRegistration** 端點一次，即可建立指定使用者的帳戶。如需如何在各種支援的用戶端平台上呼叫自訂 API 的範例，請參閱 [Azure 行動服務中的自訂 API – 用戶端 SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) 文章 (英文)。
	 
	> [AZURE.IMPORTANT]由於這個佈建使用者的步驟只會發生一次，因此您可以考慮以某種頻外方式建立使用者帳戶。若是公用註冊端點，您應該也要考慮實作以簡訊或電子郵件為基礎的驗證程序，或是其他保護措施，以避免產生詐騙帳戶。您可以使用 Twilio 透過行動服務傳送手機簡訊。如需詳細資訊，請參閱[如何傳送手機簡訊](partner-twilio-mobile-services-how-to-use-voice-sms.md#howto_send_sms)。您也可以使用 SendGrid 透過行動服務傳送電子郵件。如需詳細資訊，請參閱[使用 SendGrid 透過行動服務傳送電子郵件](store-sendgrid-mobile-services-send-email-scripts.md)。
	
3. 再次使用適當的 **invokeApi** 方法，但這次改為呼叫 **CustomLogin** 端點，藉此在訊息本文中傳遞於執行階段提供的使用者名稱和密碼。

	這次您必須在成功登入後，擷取回應物件中傳回的 *userId* 和 *authenticationToken* 值。
	
4. 使用傳回的 *userId* 和 *authenticationToken* 值建立新的 **MobileServiceUser** 物件，並將該物件設為 **MobileServiceClient** 執行個體的目前使用者，如[將驗證加入現有應用程式](mobile-services-dotnet-backend-ios-get-started-users.md)主題中所示。因為 CustomLogin 的結果與 **MobileServiceUser** 物件具有相同形式，所以您應該能夠直接轉換結果。

本教學課程到此完成。


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[將驗證加入您的應用程式]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[開始使用行動服務]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx
 

<!---HONumber=July15_HO2-->