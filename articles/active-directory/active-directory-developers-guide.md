<properties
   pageTitle="Azure Active Directory 開發人員指南"
   description="Azure Active Directory 開發人員導向資源的完整指南"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 開發人員指南

## 概觀
Azure Active Directory 是身分識別管理即服務 (IDMaaS) 平台，提供有效的方法，協助開發人員將身分識別管理整合到他們的應用程式中。下列文章提供實作的概觀和 Azure Active Directory 的重要功能。我們建議您依序閱讀，如果您尚未準備好開始進行，請跳到[開始使用](#getting-started)。


1. **[Azure AD 整合的優點](active-directory-how-to-integrate.md)**：探索為什麼與 Azure Active Directory 整合是提供安全登入和授權的最佳解決方案。

1. **[使用 Azure AD 進行登入](active-directory-authentication-scenarios.md)**：利用 Azure Active Directory 的簡易驗證來提供登入功能給您的應用程式。

1. **[查詢目錄](https://msdn.microsoft.com/library/azure/hh974476.aspx)**：使用 Azure Active Directory Graph API 以程式設計方式透過 REST API 端點存取 Azure AD。

1. **[了解應用程式模型](https://msdn.microsoft.com/library/azure/dn151122.aspx)**：了解註冊您的應用程式和多租用戶應用程式的商標指導方針。

1. **[程式庫](https://msdn.microsoft.com/library/azure/dn151135.aspx)**：使用 Azure 驗證程式庫輕鬆地驗證使用者以取得存取權杖。

若要檢視在 Build 2015 會議發表的 AAD 概觀，請參閱下面的[影片](#videos)區段。


## 開始使用

這些教學課程適用於多種平台，可讓您快速開始使用 Azure Active Directory 進行開發。先決條件是您必須[取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)。

#### 行動裝置或電腦應用程式快速入門指南

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows 市集](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Web 應用程式或 Web API 快速入門指南

- [.NET Web 應用程式](active-directory-devquickstarts-webapp-dotnet.md)
- [.NET Web API](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## 作法

這些文章描述如何使用 Azure Active Directory (AD) 執行特定工作。

- [如何取得 Azure AD 租用戶](active-directory-howto-tenant.md)
- [如何在 Azure AD 應用程式庫中列出您的應用程式](active-directory-app-gallery-listing.md)
- [如何在應用程式中開始使用 Office 365 API](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [如何提交 Office 365 的 Web 應用程式到賣方儀表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## 參考

這些文章提供 REST 和驗證程式庫 API、通訊協定、錯誤、程式碼範例和端點的基礎參考。

###  支援
- **[何處取得支援](http://stackoverflow.com/questions/tagged/azure-active-directory)**：在 Stack Overflow 上搜尋標記 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 和 [adal](http://stackoverflow.com/questions/tagged/adal)，以尋找 Azure AD 解決方案。

### 代碼

- **[Azure AD 開放原始碼程式庫](http://github.com/AzureAD)**：尋找程式庫原始碼最簡單的方式是使用我們的[程式庫清單](https://msdn.microsoft.com/library/azure/dn151135.aspx)。

- **[Azure AD 範例](http://github.com/AzureADSamples)**：瀏覽範例清單最簡單的方式是使用 [程式碼範例索引](active-directory-code-samples.md)。


### Graph API

- **[Graph API 參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)**：Azure Active Directory Graph API 的 REST 參考。[檢視新的互動式 Graph API 參考體驗](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- **[Graph API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**：OAuth 2.0 權限範圍，用來控制應用程式對租用戶中的目錄資料有何存取權。


### 驗證通訊協定

- **[SAML 2.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn195591.aspx)**：SAML 2.0 通訊協定可讓應用程式提供單一登入體驗給使用者。


- **[OAuth 2.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn645545.aspx)**：OAuth 2.0 通訊協定可讓您授權存取 Azure AD 租用戶中的 Web 應用程式和 Web API。


- **[OpenID Connect 1.0 通訊協定參照](https://msdn.microsoft.com/library/azure/dn645541.aspx)**：OpenID Connect 1.0 通訊協定擴充 OAuth 2.0 以做為驗證通訊協定。


- **[WS-同盟 1.2 通訊協定參照](https://msdn.microsoft.com/library/azure/dn903702.aspx)**：Web 服務同盟 1.2 版規格中所指定的 WS-同盟 1.2 通訊協定。

- **[支援的安全性權杖與宣告](active-directory-token-and-claims.md)**：了解與評估 SAML 2.0 和 JSON Web Token (JWT) 權杖中的宣告的指南。

## 影片

### Build 2015

這些關於配合 Azure Active Directory 開發應用程式的概觀簡報，是由直接參與工程團隊的演講者主講，並涵蓋基本主題，包括 IDMaaS、驗證、身分識別同盟和單一登入。

- **[Azure Active Directory：現代應用程式的「身分識別管理即服務」](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[使用 Azure Active Directory 開發現代 Web 應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[使用 Azure Active Directory 開發現代原生應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) 是每星期五的一對一影片系列，專門提供簡短的 10-15 分鐘訪談，由專家就各種 Azure 主題發表意見。請使用頁面上的 [服務篩選] 功能，查看所有 Azure Active Directory 影片。

- **[Azure 身分識別 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Azure 身分識別 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Azure 身分識別 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## 社交

- **[Active Directory 團隊部落格](http://blogs.technet.com/b/ad/)**：隨時掌握 Azure AD 領域的最新發展。

- **[Azure AD Graph 部落格](http://blogs.msdn.com/b/aadgraphteam)**：Azure AD Graph API 的特定資訊。

- **[雲端識別](http://www.cloudidentity.net)**：一位主要 Azure Active Directory PM 對於「身分識別管理即服務」的見解。

- **[Twitter 上的 Azure AD](https://twitter.com/azuread)**：140 個字元以內的 Azure AD 公告。

<!---HONumber=62-->