<properties
	pageTitle="開始使用 Azure AD .NET | Microsoft Azure"
	description="如何建立可整合 Azure AD 以進行登入的 .NET MVC Web App。"
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
	ms.date="04/28/2015"
	ms.author="dastrock"/>

# 使用 Azure AD 進行 Web 應用程式登入與登出

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 讓您外包 Web 應用程式的身分識別管理變得既簡單又直接，只需幾行的程式碼便可提供單一登入和登出。在 Asp.NET Web 應用程式中，您可以使用 Microsoft 的社群導向 OWIN 中介軟體 (隨附於 .NET Framework 4.5) 實作來完成這個作業。現在，我們將使用 OWIN 來執行下列動作：- 使用 Azure AD 作為身分識別提供者將使用者登入應用程式。- 顯示一些使用者的相關資訊。- 將使用者登出應用程式。

為執行此作業，您必須執行下列動作：

1. 向 Azure AD 註冊應用程式
2. 設定您的應用程式使用 OWIN 驗證管線。
3. 使用 OWIN 向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)。您還需要一個可以註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## *1.向 Azure AD 註冊應用程式*
若要啟用應用程式來驗證使用者，您必須先要在您的租用戶中註冊這個新的應用程式。

- 登入 Azure 管理入口網站。
- 在左側導覽中按一下 **Active Directory**。
- 選取您要註冊應用程式的租用戶。
- 按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [新增]。
- 遵照提示進行，並建立新的 **Web 應用程式和/或 WebAPI**。
    - 應用程式的 [**名稱**] 將對使用者說明您的應用程式
    -	[**登入 URL**] 是指應用程式的基底 URL。基本架構的預設值是 `https://localhost:44320/`。
    - [**應用程式識別碼 URI**] 是指應用程式的唯一識別碼。慣例會使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`
- 完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值，所以請從 [設定] 索引標籤中複製此值。

## *2.設定您的應用程式使用 OWIN 驗證管線*
在這裡，我們將設定 OWIN 中介軟體使用 OpenID Connect 驗證通訊協定。OWIN 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-	若要開始，請將 OWIN 中介軟體 NuGet 封裝加入使用封裝管理員主控台的專案。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	將 OWIN 啟動類別加入名為 `Startup.cs` 的專案。以滑鼠右鍵按一下專案 --> [**新增**] --> [ **新增項目**] --> 搜尋 "OWIN"。OWIN 中介軟體將會在應用程式啟動時叫用 `Configuration(...)` 方法。
-	將類別宣告變更為 `public partial class Startup`，我們已為您在另一個檔案中實作了此類別的一部分。在 `Configuration(...)` 方法中，請呼叫 ConfgureAuth(...) 以設定您的 Web 應用程式驗證。  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	開啟檔案 `App_Start\Startup.Auth.cs` 並實作 `ConfigureAuth(...)` 方法。您在 `OpenIDConnectAuthenticationOptions` 中所提供的參數將會做為您的應用程式與 Azure AD 進行通訊的座標使用。您還必須設定 Cookie 驗證，OpenID Connect 中介軟體會在表面下使用 Cookie。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	最後，請開啟專案根目錄中的 `web.config` 檔案，並在 [`<appSettings>`] 區段中輸入您的設定值。
    -	應用程式的 `ida:ClientId` 是指您在步驟 1 中從 Azure 入口網站複製的 GUID。
    -	`ida:Tenant` 是指您的 Azure AD 租用戶名稱，例如 "contoso.onmicrosoft.com"。
    -	您的 `ida:PostLogoutRedirectUri` 指出在登出要求成功完成之後，使用者應該要被重新導向至 Azure AD。

## *3.使用 OWIN 向 Azure AD 發出登入和登出要求*
您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 Azure AD 進行通訊。OWIN 已經處理所有製作驗證訊息、驗證 Azure AD 的權杖和維護使用者工作階段的瑣碎詳細資料。剩餘的工作就是提供使用者一個登入和登出的方式。

- 您可以在控制器中使用授權標記，要求使用者在存取特定頁面時登入。開啟 `Controllers\HomeController.cs`，並在 [關於] 控制器中加入 `[Authorize]` 標記。

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	您也可以使用 OWIN 從程式碼中直接發出驗證要求。開啟 `Controllers\AccountController.cs`。在 SignIn() 和 SignOut() 動作中，將分別發出 OpenID Connect 挑戰和登出要求。

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	現在，請開啟 `Views\Shared_LoginPartial.cshtml`。這裡是您向使用者顯示應用程式的登入和登出連結，以及在檢視中列印出使用者名稱的位置。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4.顯示使用者資訊*
使用 OpenID Connect 驗證使用者時，Azure AD 會將包含「宣告」或有關使用者判斷提示的 id_token 傳回給應用程式。您可以使用這些宣告來個人化應用程式：

- 開啟 `Controllers\HomeController.cs` 檔案。您可以透過 `ClaimsPrincipal.Current` 安全性主體物件來存取控制器中的使用者宣告。

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

最後，建置並執行您的應用程式！ 如果您還沒有這麼做，現在正是使用 *.onmicrosoft.com 網域在租用戶中建立新使用者的好時機。使用該名使用者登入，並注意上方導覽列如何反映使用者的身分識別。登出，再以租用戶中的另一個使用者身分重新登入。如果覺得還不夠，您可以註冊並執行此應用程式的另一個執行個體 (有自己的 clientId)，和監看作用中的單一登入。

[這裡提供](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)完整的範例供您參考 (不含您的設定值)。

您現在可以進入更進階的主題。您可以嘗試：

[使用 Azure AD 保護 Web API >>](active-directory-devquickstarts-webapi-dotnet.md)

如需其他資源，請參閱：- [GitHub 上的 AzureADSamples >>](https://github.com/AzureAdSamples) - [CloudIdentity.com >>](https://cloudidentity.com) - [Azure.com 上的 Azure AD 文件 >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->