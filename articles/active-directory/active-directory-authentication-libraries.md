<properties
   pageTitle="Azure Active Directory 驗證程式庫 | Microsoft Azure"
   description="Azure AD 驗證程式庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆地向雲端或內部部署 Active Directory (AD) 驗證使用者，然後取得存取權杖來保護 API 呼叫。"
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/11/2015"
   ms.author="mbaldwin" />

# Azure Active Directory 驗證程式庫

Azure AD 驗證程式庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆地向雲端或內部部署 Active Directory (AD) 驗證使用者，然後取得存取權杖來保護 API 呼叫。ADAL 有許多功能可使開發人員的驗證更容易，例如非同步支援、儲存存取權杖和更新權杖的可設定權杖快取、當存取權杖到期並且更新權杖可供使用時自動更新權杖等等。藉由處理大部分的複雜度，ADAL 可以幫助開發人員專注於他們的應用程式中的商務邏輯，並輕鬆地保護資源而不需成為安全性方面的專家。

ADAL 可用於各種平台上。

|平台|封裝名稱|用戶端/伺服器|下載|原始程式碼|範例|
|---|---|---|---|---|---|
|.NET 用戶端、Windows 市集、Windows Phone (8.1)|.NET 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[.NET 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)||
|JavaScript|JavaScript 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[JavaScript 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[JavaScript 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X、iOS|Objective-C 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[Objective-C 適用的 ADAL (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[Objective-C 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|[NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Android 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[ Android 適用的 ADAL (中央儲存機制)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[Android 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|[NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Node.js 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[Node.js 適用的 ADAL (npm)](https://www.npmjs.com/package/adal-node)|[Node.js 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|[WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Node 適用的 Windows Azure Active Directory Passport 驗證中介軟體|用戶端|[Node.js 適用的 Azure Active Directory Passport (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Node.js 適用的 Azure Active Directory (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Java 適用的 Active Directory 驗證程式庫 (ADAL)|用戶端|[Java 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[Java 適用的 ADAL (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Microsoft .NET Framework 4.5 的身分識別通訊協定延伸模組|伺服器|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[.NET 適用的 Azure AD 身分識別模型延伸模組 (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Microsoft .Net Framework 4.5 的 JSON Web 權杖處理常式|伺服器|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[.NET 適用的 Azure AD 身分識別模型延伸模組 (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|OWIN 中介軟體可讓應用程式使用 Microsoft 技術進行驗證。|伺服器|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|OWIN 中介軟體可讓應用程式使用 OpenIDConnect 進行驗證。|伺服器|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|[WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|OWIN 中介軟體可讓應用程式使用 WS-同盟進行驗證。|伺服器|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|[WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## 案例

以下是可以使用 ADAL 進行驗證的三種常見案例。

### 向遠端資源驗證用戶端應用程式的使用者

在此案例中，開發人員會有 WPF 應用程式之類的用戶端，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道 Web API 使用的 Azure AD 租用戶。如此一來，他可以使用 ADAL 來協助向 Azure AD 進行驗證，完全將驗證經驗委派給 ADAL 或明確地處理使用者認證。ADAL 使得驗證使用者變得輕鬆，只要取得存取權杖並對 Azure AD 更新權杖，然後使用該存取權杖向 Web API 提出要求。

如需對 Azure AD 使用驗證來示範此案例的程式碼範例，請參閱 [Web API 的原生用戶端 WPF 應用程式](https://github.com/azureadsamples/nativeclient-dotnet)。

### 向遠端資源驗證伺服器應用程式

在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。他有 Azure 訂用帳戶、知道如何叫用下游服務，並知道 Web API 使用的 Azure AD 租用戶。如此一來，他可以透過明確地處理應用程式的認證，使用 ADAL 來協助向 Azure AD 進行驗證。ADAL 使得從 Azure AD 擷取權杖變得輕鬆，只需使用應用程式的用戶端認證，然後使用該權杖向 Web API 提出要求。ADAL 也會處理存取權杖的存留期的管理，方法是快取它並視需要更新。如需示範此案例的程式碼範例，請參閱 [Web API 的主控台應用程式](https://github.com/AzureADSamples/Daemon-DotNet)。

### 代表使用者驗證伺服器應用程式以存取遠端資源

在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。要求也必須在 Azure AD 中代表使用者提出。他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道服務使用的 Azure AD 租用戶。一旦向 Web 應用程式驗證了使用者，應用程式便可以從 Azure AD 取得使用者的授權碼。然後，Web 應用程式可以使用 ADAL 代表使用者，利用與應用程式相關聯的授權碼和用戶端認證從 Azure AD 取得存取權杖和更新權杖。在 Web 應用程式取得存取權杖之後，它可以呼叫 Web API，直到權杖到期。權杖到期時，Web 應用程式可以使用 ADAL 透過先前收到的更新權杖來取得新的存取權杖。


## 另請參閱

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[Azure Active Directory 的驗證案例](active-directory-authentication-scenarios.md)

[Azure Active Directory 程式碼範例](active-directory-code-samples.md)

<!---HONumber=August15_HO8-->