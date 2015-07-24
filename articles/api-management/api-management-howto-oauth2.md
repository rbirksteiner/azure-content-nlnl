<properties 
	pageTitle="如何在 Azure API 管理中使用 OAuth 2.0 授權開發人員帳戶" 
	description="了解如何在 API 管理中使用 OAuth 2.0 來授權給使用者。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中使用 OAuth 2.0 授權開發人員帳戶

許多 API 都支援使用 [OAuth 2.0](http://oauth.net/2/) 來保護 API，並確保只有有效的使用者才能存取，而且他們只能存取獲授權的資源。為了搭配使用 Azure API 管理的互動式開發人員主控台與這類 API，服務可讓您設定服務執行個體來使用已啟用 OAuth 2.0 的 API。

## <a name="prerequisites"> </a>必要條件

本指南將示範如何設定 API 管理服務執行個體，以便使用開發人員帳戶適用的 OAuth 2.0 授權，但並未示範如何設定 OAuth 2.0 提供者。儘管步驟相似，且用來在 API 管理服務執行個體中設定 OAuth 2.0 所需的資訊也相同，但每個 OAuth 2.0 提供者的組態並不相同。本主題演示的範例將 Azure Active Directory 當做 OAuth 2.0 提供者。

>[AZURE.NOTE]如需使用 Azure Active Directory 設定 OAuth 2.0 的詳細資訊，請參閱 [WebApp-GraphAPI-DotNet][] (英文) 範例。

## <a name="step1"> </a>在 API 管理中設定 OAuth 2.0 授權伺服器

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>[AZURE.NOTE]如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

從左側的 [API 管理] 功能表按一下 [安全性]，然後依序按一下 [OAuth 2.0] 和 [Add authorization server]。

![OAuth 2.0][api-management-oauth2]

按一下 [Add authorization server] 後，即會出現新的授權伺服器表單。

![New server][api-management-oauth2-server-1]

在 [名稱] 和 [說明] 欄位中輸入名稱和選擇性的說明。

>[AZURE.NOTE]這些欄位可用來在目前的 API 管理服務執行個體中識別 OAuth 2.0 授權伺服器，且欄位的值並非來自 OAuth 2.0 伺服器。

輸入 [Client registration page URL]。此頁面可供使用者建立及管理帳戶，並且會因為使用的 OAuth 2.0 提供者不同而有所差異。[**用戶端註冊頁面 URL**] 指向使用者可用來建立和設定其對 OAuth 2.0 提供者之專屬帳戶的頁面，而提供者支援使用者帳戶管理。即使 OAuth 2.0 提供者支援這項功能，有些組織還是未設定或未使用這項功能。如果您的 OAuth 2.0 提供者未設定使用者帳戶管理，請在這裡輸入預留位置 URL (例如您公司的 URL) 或 `https://placeholder.contoso.com` 這類 URL。

表單的下一個區段含有 [Authorization code grant types]、[Authorization endpoint URL] 及 [Authorization request method] 等設定。

![New server][api-management-oauth2-server-2]

勾選需要的類型以指定 [Authorization code grant types]。依預設，系統會指定 [授權碼]。

輸入 [Authorization endpoint URL]。對於 Azure Active Directory，此 URL 將與下列 URL 相似；其中，`<client_id>` 將取代為向 OAuth 2.0 伺服器識別應用程式的用戶端識別碼。

    https://login.windows.net/<client_id>/oauth2/authorize

[Authorization request method] 能指定將授權要求傳送到 OAuth 2.0 伺服器的方法。依預設，系統會選取 **GET**。

下一個區段是指定 [Token endpoint URL]、[Client authentication methods]、[Access token sending method] 及 [預設範圍] 的位置。

![New server][api-management-oauth2-server-3]

對於 Azure Active Directory OAuth 2.0 伺服器，[**權杖端點 URL**] 將具有以下格式；其中，`<APPID>` 的格式為 `yourapp.onmicrosoft.com`。

    https://login.windows.net/<APPID>/oauth2/token

[Client authentication methods] 的預設設定是 [基本]，而 [Access token sending method] 則是 [授權標頭]。這些值和 [預設範圍] 均是在此表單區段設定的。

[用戶端認證] 區段含有 [用戶端識別碼] 和 [用戶端密碼]，這兩個項目可在 OAuth 2.0 伺服器的建立和組態程序中取得。一旦指定 [用戶端識別碼] 和 [用戶端密碼] 後，系統便會產生 [授權碼] 的 **redirect_uri**。此 URI 可用來設定 OAuth 2.0 伺服器組態中的回覆 URL。

![New server][api-management-oauth2-server-4]

如果將 [Authorization code grant types] 設定為 [Resource owner password]，您便需要使用 [Resource owner password credentials] 區段來指定認證，若不想這麼做，則可以將授與類型保持空白。

![New server][api-management-oauth2-server-5]

完成表單後，按一下 [儲存] 以儲存 API 管理 OAuth 2.0 授權伺服器組態。儲存伺服器組態後，您便可以設定 API 以使用此組態，如下一個小節所述。

## <a name="step2"> </a>設定 API 以使用 OAuth 2.0 使用者授權

從左側的 [API 管理] 功能表按一下 [API]，接著依序按一下所需之 API 的名稱和 [安全性]，然後勾選 [OAuth 2.0] 的方塊。

![User authorization][api-management-user-authorization]

從下拉式清單選取需要的 [授權伺服器]，然後按一下 [儲存]。

![User authorization][api-management-user-authorization-save]

## <a name="step3"> </a>在開發人員入口網站中測試 OAuth 2.0 使用者授權

設定好 OAuth 2.0 授權伺服器並將 API 設定為使用該伺服器後，您可以前往開發人員入口網站並呼叫 API 來進行測試。在右上角的功能表中按一下 [開發人員入口網站]。

![開發人員入口網站][api-management-developer-portal-menu]

在上方功能表中按一下 [API]，然後選取 [Echo API]。

![Echo API][api-management-apis-echo-api]

>[AZURE.NOTE]如果您的帳戶只設定或只看見一個 API，按一下 API 將帶您直接前往該 API 的作業。

選取 [GET Resource] 作業、按一下 [開啟主控台]，然後從下拉式功能表選取 [授權碼]。

![Open console][api-management-open-console]

選取 [授權碼] 時，系統會顯示含有 OAuth 2.0 提供者之登入表單的快顯視窗。在此範例中，登入表單是由 Azure Active Directory 提供。

>[AZURE.NOTE]如果已停用快顯視窗，瀏覽器會提示您加以啟用。啟用後，請再次選取 [授權碼]，系統就會顯示登入表單。

![Sign in][api-management-oauth2-signin]

登入後，系統會將授權要求的 `Authorization : Bearer` 標頭填入 [**要求標頭**]。

![Request header token][api-management-request-header-token]

此時，您可以針對剩餘的參數設定需要的值，然後再提交要求。

## 後續步驟

如需使用 OAuth 2.0 和 API 管理的詳細資訊，請參閱下列影片。

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[建立 API 管理服務執行個體]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

<!---HONumber=62-->