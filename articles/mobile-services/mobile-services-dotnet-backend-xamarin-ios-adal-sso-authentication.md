<properties 
	pageTitle="使用 Active Directory Authentication Library 單一登入驗證您的應用程式 (Xamarin.iOS) | 行動開發人員中心" 
	description="了解如何在 Xamarin.iOS 應用程式中驗證使用者的單一登入。" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="mahender"/>

# 使用 Active Directory Authentication Library 單一登入驗證您的應用程式

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##概觀

在本教學課程中，您會使用 Active Directory Authentication Library 將驗證新增至快速入門專案。

要能夠驗證使用者，您必須向 Azure Active Directory (AAD) 註冊您的應用程式。此作業有兩個階段。第一，您必須註冊行動服務，並公開其權限。第二，您必須註冊 Xamarin.iOS 應用程式，並為其授與對這些權限的存取權


>[AZURE.NOTE]本教學課程旨在協助您深入了解如何透過行動服務，為 Xamarin.iOS 應用程式執行單一登入 Azure Active Directory 驗證。如果這是您第一次接觸行動服務，請先完成教學課程[開始使用行動服務]。

##必要條件

本教學課程需要下列各項：

* XCode 4.5 和 iOS 6.0 (或更新版本) 
* 附有 [Xamarin 延伸]或 [Xamarin Studio] 的 Visual Studio (在 OS X 上)
* 完成[開始使用行動服務]或[開始使用資料]教學課程。
* Microsoft Azure 行動服務 SDK
* [Active Directory Authentication Library for iOS 的 Xamarin 繫結]。

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##將驗證程式碼新增至用戶端應用程式

1. 將 Active Directory Authentication Library 的 Xamarin 繫結新增至您的 Xamarin.iOS 專案。在 Visual Studio 2013 中，以滑鼠右鍵按一下 [參考]，然後選取 [新增參考]。接著，瀏覽至您的繫結程式庫，然後按一下 [新增]。請務必同時從 ADAL 來源新增腳本。

2. 將下列程式碼新增至 QSTodoService 類別：

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

6. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-AUTHORITY-HERE** 替換為您佈建應用程式的租用戶名稱，其格式應為 https://login.windows.net/tenant-name.onmicrosoft.com。此值可從 [Azure 管理入口網站]複製到 Azure Active Directory 的 [網域] 索引標籤以外。

7. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-RESOURCE-URI-HERE** 替換為您行動服務的「應用程式識別碼 URI」。如果您依照[如何向 Azure Active Directory 註冊]主題的內容操作，您的應用程式識別碼 URI 應類似於 https://todolist.azure-mobile.net/login/aad。

8. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-CLIENT-ID-HERE** 替換為您從原生用戶端應用程式中複製的用戶端識別碼。

9. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-REDIRECT-URI-HERE** 替換為您行動服務的 /login/done 端點。此項目應與 https://todolist.azure-mobile.net/login/done 類似。


3. 在 QSTodoListViewController 中，在 RefreshAsync(); 的呼叫前面新增下列程式碼，以修改 **ViewDidLoad**

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##使用驗證測試用戶端

1. 在 [執行] 功能表中按一下 [執行]，以啟動應用程式 
2. 您會看見登入 Azure Active Directory 的提示。  
3. 應用程式會驗證並傳回 todo 項目。

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[開始使用資料]: mobile-services-ios-get-started-data.md
[開始使用行動服務]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[如何向 Azure Active Directory 註冊]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Active Directory Authentication Library for iOS 的 Xamarin 繫結]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Xamarin 延伸]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download

<!---HONumber=July15_HO2-->