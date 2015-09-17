<properties
	pageTitle="應用程式模型 v2.0 | Microsoft Azure"
	description="Azure AD v2.0 應用程式模型限制事項清單。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽：限制事項

在公開預覽期間，v2.0 應用程式模型有數個功能未獲支援。在 v2.0 應用程式模型正式運作之前，這些限制全部都會移除；但如果您在公開預覽期間建置應用程式，請務必注意這些限制。

> [AZURE.NOTE]此資訊適用於 v2.0 應用程式模型公開預覽。如需如何與正式運作之 Azure AD 服務整合的指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 支援生產環境應用程式
與 v2.0 應用程式模型整合的應用程式，不應該當成生產環境層級的應用程式公開發行。V2.0 應用程式模型目前仍處於公開預覽，隨時可能發布重大變更，此項服務也不提供任何 SLA 保證。並非所有發生的事件都會受到支援。如果您願意接受開發中服務帶來的相依性風險，請務必與我們 @AzureAD 連絡，討論您的應用程式或服務的範圍。

## 應用程式限制
v2.0 應用程式模型公開預覽目前不支援下列類型的應用程式。如需受支援的應用程式類型描述，請參閱[這篇文章](active-directory-v2-flows.md)。

##### 單一頁面應用程式 (Javascript)
許多新式的應用程式都有單一頁面應用程式前端，以 javascript 編碼為主，而且通常使用 AngularJS、Ember.js、Durandal 等 SPA 架構。正式運作的 Azure AD 服務支援使用 [OAuth 2.0 隱含流程](active-directory-v2-protocols.md#oauth2-implicit-flow)的應用程式，但 v2.0 應用程式模型尚不提供此流程。近期內可望提供。

如果您急於取得使用 v2.0 應用程式模型的 SPA，您可以使用 [Web 應用程式流程](active-directory-v2-flows.md#web-apps)實作驗證。但這不是建議的方法，而這個案例的相關文件也有所限制。如果想要了解 SPA 案例，請參閱[已正式運作的 Azure AD SPA 程式碼範例](active-directory-devquickstarts-angular.md)。

##### 精靈/伺服器端應用程式
包含長時執行處理序或不需要使用者操作的應用程式，也需他法存取受保護的資源，例如 Web API。這些應用程式可以使用 [OAuth 2.0 用戶端認證流程](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow)，驗證及取得使用應用程式身分識別 (而非使用者委派身分識別) 的權杖。

v2.0 應用程式模型目前不支援此流程，也就是說應用程式只能在互動式使用者登入流程之後取得權杖。近期內即會加入用戶端認證流程。如果想要在正式運作的 Azure AD 應用程式模型中查看用戶端認證流程，請參閱 [GitHub 上的精靈範例](https://github.com/AzureADSamples/Daemon-DotNet)。

##### 鏈結的 Web API (代理者)
許多架構包含需要呼叫另一個下游 Web API 的 Web API，而這兩者都受到 v2.0 應用程式模型的保護。有 Web API 後端的原生用戶端中常出現這種情況，而呼叫 Office 365 或 Graph API 等 Microsoft 線上服務。

使用 OAuth 2.0 的 Jwt Bearer 認證授與可支援此鏈結的 Web API，亦稱為[代理者流程](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)。不過，v2.0 應用程式模型預覽目前尚未施行代理者流程。若要查看此流程在正式運作的 Azure AD 服務中如何運作，請參閱 [GitHub 上的代理者程式碼範例](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)。

##### 獨立的 Web API
在 v2.0 應用程式模型預覽中，您有能力從 v2.0 端點[建置使用 OAuth 權杖保護的 Web API](active-directory-v2-flows.md#web-apis)。不過，該 Web API 只能接收來自共用相同應用程式 ID 之用戶端的權杖。不支援建置從數個不同用戶端存取的協力廠商 Web 服務。

若要查看如何建置 Web API，其接受來自應用程式 ID 相同之知名用戶端的權杖，請參閱[使用者入門](active-directory-appmodel-v2-overview.md#getting-started)一節中的 v2.0 應用程式模型 Web API 範例。

## 使用者限制
目前使用 v2.0 應用程式模型建置的每個應用程式，都會開放給具有 Microsoft 帳戶或 Azure AD 帳戶的所有使用者。具有任一種帳戶的任何使用者都能夠順利安裝或瀏覽至您的應用程式，在 v2.0 應用程式模型中輸入其認證，並同意您的應用程式權限。您可以撰寫應用程式程式碼，拒絕特定集合的使用者登入，但這無法阻止他們同意應用程式。

實際上，您的應用程式無法限制可以登入應用程式的使用者類型。您無法建置企業營運應用程式 (使用者必須從屬於同一個組織)、僅供企業使用者使用的應用程式 (有 Azure AD 帳戶的使用者)，或僅供消費者使用的應用程式 (有 Microsoft 帳戶的使用者)。

## 應用程式註冊限制
目前，所有想要與 v2.0 應用程式模型整合的應用程式，都必須在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 建立新的應用程式註冊。任何現有的 Azure AD 或 Microsoft 帳戶應用程式都與 v2.0 應用程式模型不相容，除新的應用程式註冊入口網站以外，在任何入口網站中註冊的應用程式也不相容。應用程式沒有任何可從正式運作的 Azure AD 服務移至 v2.0 應用程式模型的移轉路徑。

同樣地，在新的應用程式註冊入口網站中註冊的應用程式將會以獨佔方式使用 v2.0 應用程式模型。您無法使用應用程式註冊入口網站來建立將與 Azure AD 或 Microsoft 帳戶服務成功整合的應用程式。

在新的應用程式註冊入口網站中註冊的應用程式，目前僅限一組受限的 redirect\_uri 值。Web 應用程式和服務的 redirect\_uri 必須以配置或 `https` 開頭，而所有其他平台的 redirect\_uri 則必須使用 `urn:ietf:oauth:2.0:oob` 的硬式編碼值。

若要了解如何在新的應用程式註冊入口網站中註冊應用程式，請參閱[這篇文章](active-directory-v2-app-registration.md)。

## 服務和 API 限制
v2.0 應用程式模型目前支援登入所有在新的應用程式註冊入口網站註冊的應用程式，如[支援的驗證流程](active-directory-v2-flows.md)清單所列。不過，這些應用程式將只能取得 OAuth 2.0 存取權杖，獲得非常有限的資源。v2.0 端點只會為下列項目發行 access\_token：

- 要求權杖的應用程式。如果邏輯應用程式是由數個不同的元件或層級組成，應用程式就可以為自己取得 access\_token。若要查看此案例的執行狀況，請參閱[使用者入門](active-directory-appmodel-v2-overview.md#getting-started)教學課程。
- Outlook 郵件、行事曆和連絡人 REST API，全都位於 https://outlook.office.com。若要了解如何撰寫存取這些 API 的應用程式，請參閱 [Office 使用者入門](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)教學課程。

近期內將增加更多 Microsoft 線上服務，以及對您 Web API 和服務的支援。

## 程式庫與 SDK 限制
並非所有語言和平台都有支援 v2.0 應用程式模型預覽的文件庫。驗證程式庫集合目前僅適用於 .NET、iOS、Android、NodeJS 和 Javascript。[使用者入門](active-directory-appmodel-v2-overview.md#getting-started)一節提供各項的對應程式碼範例和教學課程。

如果您想要整合應用程式與使用其他語言或平台的 v2.0 應用程式模型，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定參考](active-directory-v2-protocols.md)，其中指示如何建構與 v2.0 端點進行通訊所需的 HTTP 訊息。

## 通訊協定限制
v2.0 應用程式模型支援 Open ID Connect 和 OAuth 2.0。不過，並非每個通訊協定的所有特性與功能都已納入 v2.0 應用程式模型。部分範例包括：

- 對 OpenID Connect `prompt` 參數的完整支援
- OpenID Connect `login_hint` 參數
- OpenID Connect `domain_hint` 參數
- OpenID Connect `end_sesssion_endpoint`

若要進一步了解 v2.0 應用程式模型支援的通訊協定功能範圍，請參閱 [OpenID Connect 與 OAuth 2.0 通訊協定參照](active-directory-v2-protocols.md)。

<!---HONumber=August15_HO7-->