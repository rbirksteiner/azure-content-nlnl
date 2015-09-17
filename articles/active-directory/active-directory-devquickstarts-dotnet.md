<properties
	pageTitle="開始使用 Azure AD .NET | Microsoft Azure"
	description="如何建置 .NET Windows 桌面應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="dastrock"/>


# 將 Azure AD 整合至 Windows 桌面 WPF 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您正在開發桌面應用程式，Azure AD 讓使用 Active Directory 帳戶驗證您的使用者變得簡單明瞭。它也可讓您的應用程式安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

對於需要存取受保護資源的 .NET 原生用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 存在的唯一目的是讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將建置一個執行下列動作的 .NET WPF 待辦事項清單應用程式：

-	使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)取得呼叫 Azure AD Graph API 的存取權杖。
-	在目錄中搜尋具有指定別名的使用者。
-	將使用者登出。

若要建立可完整運作的應用程式，您必須：

2. 向 Azure AD 註冊您的應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)。您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## *1.註冊 DirectorySearcher 應用程式*
若要讓您的應用程式取得權杖，您必須先在 Azure AD 租用戶中註冊這個應用程式，並授權它存取 Azure AD Graph API：

-	登入 Azure 管理入口網站
-	在左側導覽中按一下 **Active Directory**
-	選取要在其中註冊應用程式的租用戶。
-	按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [**新增**]。
-	遵照提示進行，並建立新的**原生用戶端應用程式**。
    -	應用程式的 [**名稱**] 將對使用者說明您的應用程式
    -	「**重新導向 URI**」是配置和字串的組合，Azure AD 可用它來傳回權杖回應。輸入應用程式特定的值，例如 `http://DirectorySearcher`。
-	完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值，所以請從 [**設定**] 索引標籤中複製此值。
- 此外，在 [**設定**] 索引標籤上找到 [其他應用程式的權限] 區段。在 [**委派權限**] 下，為 [Azure Active Directory] 應用程式新增 [**存取您的組織目錄**] 權限。這樣做可讓您的應用程式查詢 Graph API 的使用者。

## *2.安裝及設定 ADAL*
既然您在 Azure AD 中已經擁有應用程式，您可以安裝 ADAL，並撰寫身分識別相關程式碼。為了讓 ADAL 能夠與 Azure AD 進行通訊，您必須提供一些應用程式註冊相關資訊。從使用封裝管理員主控台將 ADAL 加入 DirectorySearcher 專案開始。

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	在 DirectorySearcher 專案中，開啟 `app.config`。取代 `<appSettings>` 區段中的元素值，以反映您在 Azure 入口網站中所輸入的值。每當使用 ADAL 時，您的程式碼便會參考這些值。
    -	`ida:Tenant` 是指您的 Azure AD 租用戶網域，例如 contoso.onmicrosoft.com
    -	`ida:ClientId` 是指您從入口網站複製的應用程式 clientId。
    -	`ida:RedirectUri` 是您在入口網站中註冊的重新導向 url。

## *3.使用 ADAL 來取得 AAD 的權杖*
ADAL 的基本原則是每當您的應用程式需要存取權杖時，它只需呼叫 `authContext.AcquireToken(...)`，ADAL 就會進行其餘工作。

-	在 `DirectorySearcher` 專案中，開啟 `MainWindow.xaml.cs` 並找出 `MainWindow()` 方法。第一步是初始化應用程式的 `AuthenticationContext` - ADAL 的主要類別。您在這裡將 ADAL 與 Azure AD 通訊所需的座標傳給 ADAL，並告訴它如何快取權杖。

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());
    ...
}
```

- 現在請找到 `Search(...)` 方法，這是在使用者按一下應用程式 UI 的 [搜尋] 按鈕時所叫用的方法。這個方法會對 Azure AD Graph API 提出 GET 要求，以查詢 UPN 開頭為指定搜尋詞彙的使用者。但為了能夠查詢 Graph API，要求的 `Authorization` 標頭必須包含 access\_token - ADAL 可以提供這方面的協助。

```C#
private void Search(object sender, RoutedEventArgs e)
{
    ...

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri);
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- 當您的應用程式透過呼叫 `AcquireToken(...)` 要求權杖時，ADAL 會嘗試在不要求使用者認證的情況下傳回權杖。如果 ADAL 決定使用者需要登入才能取得權杖，它會顯示登入對話方塊、收集使用者的認證，並在成功驗證後傳回權杖。如果基於任何原因 ADAL 無法傳回權杖，則會擲回 `AdalException`。
- 請注意，`AuthenticationResult` 物件包含 `UserInfo` 物件，可用來收集您的應用程式可能需要的資訊。在 DirectorySearcher 中，`UserInfo` 用來以使用者的識別碼自訂應用程式的 UI。

- 當使用者按一下 [登出] 按鈕時，我們想要確保下次呼叫 `AcquireToken(...)` 時會要求使用者登入。有了 ADAL，這會和清除權杖快取一樣簡單：

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- 不過，如果使用者未按一下 [登出] 按鈕，您會想要維護使用者的工作階段，供他們下一次執行 DirectorySearcher 繼續使用。當應用程式啟動時，您可以在 ADAL 的權杖快取中檢查現有的權杖，並據此更新 UI。回到 `MainWindow()`, ，再呼叫一次 `AcquireToken(...)`這次要傳入 `PromptBehavior.Never` 參數。`PromptBehavior.Never` 告訴 ADAL 不要提示使用者進行登入，而如果 ADAL 無法傳回權杖，則應該改為擲回例外狀況。

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri, PromptBehavior.Never);
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

恭喜！ 您現在有一個可運作的 .NET WPF 應用程式，能夠驗證使用者、使用 OAuth 2.0 安全地呼叫 Web API，以及取得使用者的基本資訊。如果您還沒有這麼做，現在是將一些使用者植入租用戶的時候。執行 DirectorySearcher 應用程式，並使用其中一個使用者登入。根據 UPN 搜尋其他使用者。關閉並重新執行應用程式。請注意，使用者工作階段會維持不變。登出，再以另一個使用者身分重新登入。

ADAL 可讓您輕鬆地將這些常見的身分識別功能全部納入您的應用程式。它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。您唯一需要知道的就是單一 API 呼叫，`authContext.AcquireToken(...)`。

[這裡](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)提供完成的範例供您參考 (不含您的設定值)。您現在可以繼續探索其他案例。您可以嘗試：

[使用 Azure AD 保護 .NET Web API >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=August15_HO6-->