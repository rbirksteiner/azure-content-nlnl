<properties 
   pageTitle="Azure Active Directory 程式碼範例" 
   description="依案例的組織的 Azure Active Directory 程式碼範例的索引。" 
   services="active-directory" 
   documentationCenter="dev-center-name" 
   authors="msmbaldwin" 
   manager="mbaldwin" 
   editor=""/>

<tags
   ms.service="azure"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity" 
   ms.date="04/06/2015"
   ms.author="mbaldwin"/>

# Azure Active Directory 程式碼範例 

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

您可以使用 Microsoft Azure Active Directory (Azure AD)，將驗證和授權加入 Web 應用程式及 Web API。本節連結的程式碼範例將告訴您如何進行以及您可以在應用程式中使用的程式碼片段。在程式碼範例頁面上，您可以找到詳細的讀我主題，有助於符合需求、安裝及設定。而程式碼加上的註解可協助您了解重要區段。

若要了解每個範例類型的基本案例，請參閱「Azure AD 的驗證案例」。

歡迎對我們在 GitHub 的範例做出貢獻：[Microsoft Azure Active Directory 範例與文件](https://github.com/AzureADSamples)。

## Web 瀏覽器到 Web 應用程式 

這些範例示範如何撰寫將使用者的瀏覽器導向至 Azure AD 以進行登入的 Web 應用程式。



| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | 使用 OpenID Connect (ASP.Net OpenID Connect OWIN 中介軟體) 驗證來自 Azure AD 租用戶的使用者。
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | 多租用戶 .NET MVC Web 應用程式，其使用 OpenID Connect (ASP.Net OpenID Connect OWIN 中介軟體) 驗證來自多個 Azure AD 租用戶的使用者。
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | 使用 WS-Federation (ASP.Net WS-Federation OWIN 中介軟體) 驗證來自 Azure AD 租用戶的使用者。






## 單一頁面應用程式 (SPA)

這個範例示範如何撰寫使用 Azure AD 保護的單一頁面應用程式。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| JavaScript、C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | 使用 ADAL for JavaScript 和 Azure AD 來保護使用 ASP.NET Web API 後端實作的 AngularJS 式單一頁面應用程式。


## 原生應用程式到 Web API
 
這些程式碼範例示範如何建置原生用戶端應用程式，其呼叫由 Azure AD 保護的 Web API。它們使用 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 和 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
 
| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| Javascript | [NativeClient-MultiTarget-Cordova](https://github.com/AzureADSamples/NativeClient-MultiTarget-Cordova) | 使用 Apache Cordova 的 ADAL 外掛程式，建置一個可呼叫 Web API 並使用 Azure AD 進行驗證的 Apache Cordova 應用程式。
| C#/.NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | .NET WPF 應用程式，其呼叫使用 Azure AD 保護的 Web API。
| C#/.NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Windows 市集應用程式，其呼叫使用 Azure AD 保護的 Web API。
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Windows 市集應用程式，其呼叫使用 Azure AD 保護的多租用戶 Web API。
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | 原生用戶端應用程式，其呼叫的 Web API 會取得 Token 來代表原始使用者執行動作，然後使用 Token 來呼叫另一個 Web API。
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Windows Phone 8.1 適用的 Windows 市集應用程式，其呼叫由 Azure AD 保護的 Web API。
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | iOS 應用程式，其呼叫的 Web API 需要 Azure AD 進行驗證。
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | 原生用戶端應用程式，其中包含邏輯來處理 Web API 中的 JWT Token，而不使用 OWIN 中介軟體。
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Android 程式庫適用之原生 Azure AD 驗證程式庫 (ADAL) 的 Xamarin 繫結。
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | iOS 程式庫適用之原生 Azure AD 驗證程式庫 (ADAL) 的 Xamarin 繫結。
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | Xamarin 專案，以五個平台為目標，並呼叫由 Azure AD 保護的 Web API。
| C#/.NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | 原生應用程式，其執行非互動式驗證，並呼叫由 Azure AD 保護的 Web API。

   

## Web 應用程式到 Web API

這些程式碼範例示範如何使用 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) 建置 Web 應用程式，其呼叫由 Azure AD 保護的 Web API。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | 使用登入的使用者的權限呼叫 Web API。
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | 使用應用程式的權限呼叫 Web API。
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | 使用 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) 對現有 Web 應用程式新增授權，使應用程式能呼叫 Web API。
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | 設定與 Azure AD 整合的 REST API 服務以保護 API。包含 Node.js 伺服器與 Web API。
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | 多租用戶 MVC Web 應用程式，其使用 OpenID Connect (ASP.Net OpenID Connect OWIN 中介軟體) 驗證來自 Azure AD 租用戶的使用者。使用授權碼來叫用 Graph API。

## 伺服器或精靈應用程式到 Web API

這些程式碼範例示範如何建置精靈或伺服器應用程式，以使用 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 和 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) 從 Web API 取得資源。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | 呼叫 Web API 的主控台應用程式。用戶端認證是密碼。
| C#/.NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | 呼叫 Web API 的主控台應用程式。用戶端認證是憑證。


## 呼叫 Azure AD Graph API

這些程式碼範例示範如何建立應用程式，其呼叫 Azure AD Graph API 來讀取和寫入目錄資料。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | 使用 Graph API 來存取 Azure AD 目錄資料的 Web 應用程式。
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | 使用 Graph API 來存取 Azure AD 目錄資料的 Web 應用程式。
| C#/.NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | 使用 Graph API 來存取 Azure AD 目錄資料的 Web 應用程式。
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | 此主控台應用程式示範對 Graph API 的一般讀取和寫入呼叫，並示範如何執行使用者授權指派及更新使用者的縮圖相片和連結。
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | 主控台應用程式，其在 Graph API 中使用差異查詢來取得 Azure AD 租用戶中使用者物件的定期變更。
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet) | MVC 應用程式，使用 Graph API 查詢來產生簡易的公司組織圖。
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | PHP 應用程式，其呼叫 Graph API 來註冊延伸模組，然後讀取、更新和刪除延伸模組屬性中的值。


## Authorization

這些程式碼範例示範如何使用 Azure AD 進行授權。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | 在與 Azure AD 整合的應用程式中，使用 Azure Active Directory 群組宣告執行角色型存取控制 (RBAC)。
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | 在與 Azure AD 整合的應用程式中，使用 Azure Active Directory 應用程式角色執行角色型存取控制 (RBAC)。


## 舊版逐步解說

這些逐步解說使用稍早的技術，不過您也許會感興趣。

| 語言/平台 | 範例 | 說明
| ----------------- | ------ | -----------
| C#/.NET | [Microsoft Azure AD 應用程式中的角色型和 ACL 型授權](http://go.microsoft.com/fwlink/?LinkId=331694) | 在與 Azure AD 整合的應用程式中，執行角色型授權 (RBAC) 和 ACL 型授權。
| C#/.NET | [AAL - Windows 市集應用程式對 REST 服務 - 驗證](http://go.microsoft.com/fwlink/?LinkId=330605) | 使用 Windows 市集 Beta 適用的 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) (先前稱為 AAL)，將使用者驗證功能新增至 Windows 市集應用程式。
| C#/.NET | [ADAL - 原生應用程式對 REST 服務 - 透過瀏覽器對話方塊使用 AAD 進行驗證](http://go.microsoft.com/fwlink/?LinkId=259814) | 使用 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 將使用者驗證功能新增至 WPF 用戶端。
| C#/.NET | [ADAL - 原生應用程式對 REST 服務 - 透過瀏覽器對話方塊使用 ACS 進行驗證](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | 使用 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 和 [存取控制服務 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) 將使用者驗證功能新增至 WPF 用戶端。
| C#/.NET | [ADAL - 伺服器對伺服器驗證](http://go.microsoft.com/fwlink/?LinkId=259816) | 使用 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 來保護從伺服器端程序至 MVC4 Web API REST 服務的服務呼叫。
| C#/.NET | [使用 Azure AD 將登入新增至 Web 應用程式](https://msdn.microsoft.com/library/azure/dn151790.aspx) | 設定 .NET 應用程式對您的 Azure AD 企業目錄執行 Web 單一登入。
| C#/.NET | [使用 Azure AD 開發多租用戶 Web 應用程式](https://msdn.microsoft.com/library/azure/dn151789.aspx) | 使用 Azure AD 新增 .NET 應用程式的單一登入和目錄存取功能，以跨多個組織運作。
JAVA | [Azure AD Graph API 的 Java 範例應用程式](http://go.microsoft.com/fwlink/?LinkId=263969) | 使用 Graph API 從 Azure AD 存取目錄資料。
PHP | [Azure AD Graph API 的 PHP 範例應用程式](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | 使用 Graph API 從 Azure AD 存取目錄資料。
| C#/.NET | [Azure AD Graph API 的範例應用程式](http://go.microsoft.com/fwlink/?LinkID=262648) | 使用 Graph API 從 Azure AD 存取目錄資料。
| C#/.NET | [Azure AD Graph 差異查詢的範例應用程式](http://go.microsoft.com/fwlink/?LinkId=275398) | 在 Graph API 中使用差異查詢來取得 Azure AD 租用戶中使用者物件的定期變更。
| C#/.NET | [整合 Azure AD 多租用戶雲端應用程式的範例應用程式](http://go.microsoft.com/fwlink/?LinkId=275397) | 將多租用戶應用程式整合至 Azure AD。
| C#/.NET | [使用 Azure AD (預覽) 保護 Windows 市集應用程式和 REST Web 服務](https://msdn.microsoft.com/library/azure/dn169448.aspx) | 使用 Azure AD 和 [Azure AD 驗證程式庫 (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) 建立簡單 Web API 資源和 Windows 市集用戶端應用程式。
| C#/.NET| [使用 Graph API 來查詢 Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) | 設定 Microsoft .NET 應用程式，以使用 Azure AD Graph API 從 Azure AD 租用戶目錄存取資料。

## 另請參閱

##### 其他資源


[Azure AD Graph API 協助程式程式庫](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18)

[使用 OAuth 和 Active Directory Federation Services 開發現代化的應用程式](http://msdn.microsoft.com/library/dn633593.aspx)




 

<!---HONumber=62-->