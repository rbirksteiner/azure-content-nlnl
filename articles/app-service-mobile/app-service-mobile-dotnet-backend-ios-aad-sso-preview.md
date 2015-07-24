<properties 
        pageTitle="使用 Azure Active Directory 單一登入驗證您的 iOS 應用程式使用者" 
        description="了解如何使用 Active Directory Authentication Library 讓使用者登入您的 iOS 應用程式" 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede" />

<tags ms.service="app-service"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios" 
ms.devlang="objective-c" 
ms.topic="article" 
ms.date="05/19/2015" 
ms.author="mahender" />

# 將 Azure Active Directory 單一登入新增至您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

在本教學課程中，您會使用 Active Directory Authentication Library 將驗證新增至快速入門專案。

為了能夠驗證使用者，您必須向 Azure Active Directory (AAD) 租用戶註冊您的應用程式。此作業有兩個階段。第一，您必須註冊應用程式服務，並公開其權限。第二，您必須註冊 iOS 應用程式，並為其授與對這些權限的存取權。

本教學課程需要下列各項：

* XCode 4.5 和 iOS 6.0 (或更新版本)
* 完成[開始使用行動應用程式]教學課程。
* Microsoft Azure 行動服務 SDK
* [Active Directory Authentication Library for iOS]

## <a name="register-application"></a>向 Azure Active Directory 註冊您的應用程式

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>將應用程式設定為需要驗證

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>新增 Active Directory Authentication Library 的參考

1. 下載 [Active Directory Authentication Library for iOS]。

2. 在 Xcode Navigator 中選取您的專案，然後按一下 [**檔案**]，再選擇 [**將檔案新增至...**]。導覽至您下載驗證庫的位置，然後選取 [**ADALiOS.xcodeproj**]。

3. 再次選取您的專案，並開啟 [**建置設定**] 索引標籤。導覽至 [**連結**] 區段，然後將 `-ObjC` 新增至 [**其他連結器旗標**]。

4. 選取 [**建置階段**] 索引標籤。在 [**目標相依性**] 下，新增 `ADALiOS`。

5. 在 [**連結二進位檔與程式庫**] 下，新增 `libADALiOS.a`。

您現在將可在專案中參考 Active Directory Authentication Library。

## <a name="add-authentication-code"></a>將驗證程式碼新增至用戶端應用程式

2. 在 QSTodoListViewController 中，使用下列程式碼納入 ADAL：

        #import "ADALiOS/ADAuthenticationContext.h"

3. 然後，新增下列方法：

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }

4. 在上述 `loginAndGetData` 方法的程式碼中，將 **INSERT-AUTHORITY-HERE** 替換為您佈建應用程式的租用戶名稱，其格式應為 https://login.windows.net/tenant-name.onmicrosoft.com。此值可從 [Azure 管理入口網站]複製到 Azure Active Directory 的 [網域] 索引標籤以外。

5. 在前述 `loginAndGetData` 方法的程式碼中，將 **INSERT-RESOURCE-URI-HERE** 取代為您行動應用程式的 [**應用程式識別碼 URI**]。如果您依照[如何向 Azure Active Directory 設定您的行動應用程式]主題的內容操作，您的應用程式識別碼 URI 應類似於 https://contosogateway.azurewebsites.net/login/aad。

6. 在前述 `loginAndGetData` 方法的程式碼中，將 **INSERT-CLIENT-ID-HERE** 取代為您從原生用戶端應用程式中複製的用戶端識別碼。

7. 在前述 `loginAndGetData` 方法的程式碼中，將 **INSERT-REDIRECT-URI-HERE** 取代為您應用程式服務閘道的 /login/done 端點。此項目應與 https://contosogateway.azurewebsites.net/login/done 類似。

8. 在 QSTodoListViewController 中，使用下列程式碼取代 `[self refresh]`，以修改 `viewDidLoad`：

        [self loginAndGetData];

## <a name="test-client"></a>使用驗證測試用戶端

1. 從 [產品] 功能表中按一下 [執行]，以啟動應用程式
2. 您會看見登入 Azure Active Directory 的提示。  
3. 應用程式會驗證並傳回 todo 項目。

<!-- URLs. -->
[如何向 Azure Active Directory 設定您的行動應用程式]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Active Directory Authentication Library for iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [開始使用行動應用程式]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 

<!---HONumber=62-->