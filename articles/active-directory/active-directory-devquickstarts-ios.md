<properties
	pageTitle="開始使用 Azure AD iOS | Microsoft Azure"
	description="如何建置 iOS 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="brandwe"/>

# 將 Azure AD 整合至 iOS 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 提供 Active Directory 驗證程式庫 (ADAL) 給需要存取受保護資源的 iOS 用戶端。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範它有多麼容易使用，我們會在此建置可達行下列作業的目標 C 待辦事項清單應用程式：

-	使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)取得呼叫 Azure AD Graph API 的存取權杖。
-	在目錄中搜尋具有指定別名的使用者。

若要建立完整可用的應用程式，您必須：

2. 向 Azure AD 註冊您的應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## *1.決定您 iOS 的重新導向 URI*

為了安全地在特定 SSO 案例啟動您的應用程式，我們需要您以特定格式建立**「重新導向 URI」**。重新導向 URI 可確保應用程式要求的權杖會正確地傳回給它們。

iOS 格式的重新導向 URI：

```
<app-scheme>://<bundle-id>
```

- 	**aap-scheme** - 這已在您的 XCode 專案中註冊。它是其他應用程式呼叫您的方式。您可以在 Info.plist -> URL types -> URL Identifier 下找到此項目。如果您尚未設定任何一個，建議您建立一個。
- 	**bundle-id** - 這是在您的 XCode 專案設定中，[identity] 下可找到的 [Bundle Identifier]。
	
此 QuickStart 程式碼的範例為：******msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2.註冊 DirectorySearcher 應用程式*
若要讓您的應用程式取得權杖，您必須先在 Azure AD 租用戶中註冊這個應用程式，並授權它存取 Azure AD Graph API：

-	登入 Azure 管理入口網站
-	在左側導覽中按一下 **Active Directory**
-	選取要在其中註冊應用程式的租用戶。
-	按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [**新增**]。
-	遵照提示進行，並建立新的**原生用戶端應用程式**。
    -	應用程式的 [**名稱**] 將對使用者說明您的應用程式
    -	「**重新導向 URI**」是配置和字串的組合，Azure AD 可用它來傳回權杖回應。根據上面的資訊，針對您的應用程式輸入值。
-	完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值，所以請從 [**設定**] 索引標籤中複製此值。
- 此外，在 [**設定**] 索引標籤上找到 [其他應用程式的權限] 區段。在 [**委派權限**] 下，為 [Azure Active Directory] 應用程式新增 [**存取您的組織目錄**] 權限。這樣做可讓您的應用程式查詢 Graph API 的使用者。

## *3.安裝及設定 ADAL*
既然您在 Azure AD 中已經擁有應用程式，您可以安裝 ADAL，並撰寫身分識別相關程式碼。為了讓 ADAL 能夠與 Azure AD 進行通訊，您必須提供一些應用程式註冊相關資訊。從使用 Cocoapods 將 ADAL 加入 DirectorySearcher 專案開始。

```
$ vi Podfile
```
將下列加入此 Podfile：

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

現在使用 Cocoapods 載入該 Podfile。這會建立您將載入的新 XCode Workspace。

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-	在 QuickStart 專案中，開啟 plist 檔案 `settings.plist`。取代區段中的元素值，以反映您在 Azure 入口網站中所輸入的值。每當使用 ADAL 時，您的程式碼便會參考這些值。
    -	`tenant` 是指您的 Azure AD 租用戶網域，例如 contoso.onmicrosoft.com
    -	`clientId` 是指您從入口網站複製的應用程式 clientId。
    -	`redirectUri` 是您在入口網站中註冊的重新導向 url。

## *4.使用 ADAL 來取得 AAD 的權杖*
ADAL 的基本原則是每當您的應用程式需要存取權杖時，它只需呼叫 completionBlock `+(void) getToken : `，ADAL 就會進行其餘工作。

-	在 `QuickStart` 專案中，開啟 `GraphAPICaller.m` 並找出接近頂端的 `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` 註解。您在這裡將 ADAL 與 Azure AD 通訊透過 CompletionBlock 將座標傳給 ADAL，並告訴它如何快取權杖。

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }
    
    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];
    
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {
                              
                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- 現在我們需要使用此權杖搜尋圖形中的使用者。尋找 `// TODO: implement SearchUsersList` 註解。這個方法會對 Azure AD Graph API 提出 GET 要求，以查詢 UPN 開頭為指定搜尋詞彙的使用者。但為了能夠查詢 Graph API，要求的 `Authorization` 標頭必須包含 access\_token - ADAL 可以提供這方面的協助。

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    AppData* data = [AppData getInstance];
    
    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

    
    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {
         
         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {
             
             NSOperationQueue *queue = [[NSOperationQueue alloc]init];
             
             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
                 
                 if (error == nil && data != nil){
                     
                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];
                     
                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
                     
                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)
                     
                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];
                     
                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];
                         
                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];
                         
                         [Users addObject:s];
                     }
                     
                     completionBlock(Users, nil);
                 }
                 else
                 {
                     completionBlock(nil, error);
                 }
                 
             }];
         }
     }];
    
}

```
- 當您的應用程式透過呼叫 `getToken(...)` 要求權杖時，ADAL 會嘗試在不要求使用者認證的情況下傳回權杖。如果 ADAL 決定使用者需要登入才能取得權杖，它會顯示登入對話方塊、收集使用者的認證，並在成功驗證後傳回權杖。如果基於任何原因 ADAL 無法傳回權杖，則會擲回 `AdalException`。
- 請注意，`AuthenticationResult` 物件包含 `tokenCacheStoreItem` 物件，可用來收集您的應用程式可能需要的資訊。在 QuickStart 中，`tokenCacheStoreItem` 用來判斷驗證是否已經發生。 


## 步驟 5：建置並執行應用程式



恭喜！ 您現在有一個可運作的 iOS 應用程式，能夠驗證使用者、使用 OAuth 2.0 安全地呼叫 Web API，以及取得使用者的基本資訊。如果您還沒有這麼做，現在是將一些使用者植入租用戶的時候。執行 QuickStart 應用程式，並使用其中一個使用者登入。根據 UPN 搜尋其他使用者。關閉並重新執行應用程式。請注意，使用者工作階段會維持不變。

ADAL 可讓您輕鬆地將這些常見的身分識別功能全部納入您的應用程式。它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。您唯一需要知道的就是單一 API 呼叫，`getToken`。

[這裡](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)提供完成的範例供您參考 (不含您的設定值)。您現在可以繼續探索其他案例。您可以嘗試：

[使用 Azure AD 保護 Node.JS Web API >>](../active-directory-devquickstarts-webapi-nodejst.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=August15_HO6-->