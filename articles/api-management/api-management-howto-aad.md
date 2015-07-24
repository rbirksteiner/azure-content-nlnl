<properties 
	pageTitle="如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶" 
	description="了解如何在 API 管理中使用 Azure Active Directory 授權使用者" 
	services="api-management" 
	documentationCenter="API Management" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶


## 概觀
本指南說明如何為一或多個 Azure Active Directory 中的所有使用者啟用開發人員入口網站的存取。本指南也說明如何管理 Azure Active Directory 的使用者，方法是加入包含 Azure Active Directory 的使用者的外部群組。

>若要完成本指南中的步驟，您必須先具備要在其中建立應用程式的 Azure Active Directory。

## 如何使用 Azure Active Directory 授權開發人員帳戶

若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

從左側的 [**API 管理**] 功能表按一下 [**安全性**]，然後按一下 [**外部身分識別**]。

![外部身分識別][api-management-security-external-identities]

按一下 [**Azure Active Directory**]。記下 [**重新導向 URL**]，然後切換到 Azure 入口網站中您的 Azure Active Directory。

![外部身分識別][api-management-security-aad-new]

按一下 [**加入**] 按鈕來建立新 Azure Active Directory 應用程式，並選擇 [**加入我的組織正在開發的應用程式**]。

![加入新的 Azure Active Directory 應用程式][api-management-new-aad-application-menu]

輸入應用程式的名稱，選取 [**Web 應用程式和/或 Web API**]，然後按 [下一步] 按鈕。

![新 Azure Active Directory 應用程式][api-management-new-aad-application-1]

針對 [**登入 URL**]，請從發行者入口網站 [**外部身分識別**] 索引標籤的 [**Azure Active Directory**] 區段，複製 [**重新導向 URL**]，並從 URL 結尾移除 **-aad** 尾碼。在此範例中，[**登入 URL**] 為 `https://aad03.portal.current.int-azure-api.net/signin`。

針對 [**應用程式識別碼 URL**]，請輸入 Azure Active Directory 的預設網域或自訂網域，並為其附加獨特的字串。在此範例中，使用 **https://contoso5api.onmicrosoft.com** 的預設網域時搭配指定了 **/api** 尾碼。

![新 Azure Active Directory 應用程式屬性][api-management-new-aad-application-2]

按一下核取記號按鈕來儲存並建立新應用程式，然後切換至 [**設定**] 索引標籤來設定新應用程式。

![新 Azure Active Directory 應用程式已建立][api-management-new-aad-app-created]

如果將對這個應用程式使用多個 Azure Active Directory，請對 [**應用程式是多租用戶**] 按一下 [**是**]。預設值為 [**否**]。

![應用程式為多租用戶][api-management-aad-app-multi-tenant]

從發行者入口網站 [**外部身分識別**] 索引標籤的 [**Azure Active Directory**] 區段，複製 [**重新導向 URL**]，並貼上至 [**回覆 URL**]。

![回覆 URL][api-management-aad-reply-url]

捲動至設定索引標籤的底端，選取 [**應用程式權限**] 下拉式清單，並勾選 [**讀取目錄資料**]。

![應用程式權限][api-management-aad-app-permissions]

選取 [**委派權限**] 下拉式清單，並勾選 [**啟用登入並讀取使用者的設定檔**]。

![委派的權限][api-management-aad-delegated-permissions]

>如需應用程式和委派的權限的詳細資訊，請參閱[存取 Graph API][]。

複製 [**用戶端識別碼**] 至剪貼簿。

![用戶端識別碼][api-management-aad-app-client-id]

切換回發行者入口網站，並將從 Azure Active Directory 應用程式組態複製的 [**用戶端識別碼**] 貼上。

![用戶端識別碼][api-management-client-id]

切換回 Azure Active Directory 組態，並按一下 [**金鑰**] 區段中的 [**選取期間**] 下拉式清單，然後指定期間。在此範例中使用 **1 年**。

![金鑰][api-management-aad-key-before-save]

按一下 [**儲存**] 以儲存組態並顯示金鑰。複製金鑰至剪貼簿。

>記下此金鑰。關閉 Azure Active Directory 組態視窗之後，即無法再次顯示金鑰。

![金鑰][api-management-aad-key-after-save]

切換回發行者入口網站，並將金鑰貼上至 [**用戶端密碼**] 文字方塊。

![用戶端密碼][api-management-client-secret]

[**允許的租用戶**] 指定哪些目錄可存取 API 管理服務執行個體的 API。指定您要授與存取的 Azure Active Directory 執行個體的網域。您可以使用換行符號、空格或逗號來分隔多個網域。

![允許的租用戶][api-management-client-allowed-tenants]

可以在 [**允許的租用戶**] 區段中指定多個網域。在使用者可透過與註冊應用程式之原始網域不同的網域登入前，不同網域的全域管理員必須授與權限，應用程式才能存取目錄資料。若要授與權限，全域管理員必須登入應用程式，然後按一下 [**接受**]。在以下的範例中，`miaoaad.onmicrosoft.com` 已被加入至 [**允許的租用戶**]，並且來自該網域的全域管理員正在第一次登入。

![權限][api-management-permissions-form]

>如果非全域管理員在全域管理員授與其權限之前便嘗試登入，登入嘗試會失敗，並且顯示錯誤畫面。

指定需要的組態之後，按一下 [**儲存**]。

![Save][api-management-client-allowed-tenants-save]

儲存變更之後，在指定 Azure Active Directory 中的使用者透過遵循[使用 Azure Active Directory 帳戶登入開發人員入口網站][]中的步驟，即可登入開發人員入口網站。

## 如何加入外部 Azure Active Directory 群組

為 Azure Active Directory 中的使用者啟用存取之後，您可以將 Azure Active Directory 群組加入至 API 管理，以更輕鬆管理所需產品的群組中開發人員的關聯。

> 為了設定外部 Azure Active Directory 群組，必須先遵循上一節中的程序，在 [身分識別] 索引標籤中設定 Azure Active Directory。

外部 Azure Active Directory 群組是從您要授與群組存取之產品的 [**可見性**] 索引標籤中加入。按一下 [**產品**]，然後按一下所需產品的名稱。

![Configure product][api-management-configure-product]

切換至 [**可用性**] 索引標籤，然後按一下 [**從 Azure Active Directory 加入群組**]。

![加入群組][api-management-add-groups]

從下拉式清單選取 [**Azure Active Directory 租用戶**]，然後在要加入的 [**群組**] 文字方塊中輸入所需群組的名稱。

![選取群組][api-management-select-group]

此群組名稱可在您的 Azure Active Directory 的 [**群組**] 清單中找到，如下列範例所示。

![Azure Active Directory 群組清單][api-management-aad-groups-list]

按一下 [**加入**] 可驗證群組名稱並加入群組。在此範例中會加入 **Contoso 5 Developers** 外部群組。

![Group added][api-management-aad-group-added]

按一下 [**儲存**] 以儲存新群組選項。

透過一個產品設定 Azure Active Directory 群組之後，即可在 API 管理服務執行個體中其他產品的 [**可見性**] 索引標籤加以查看。

在加入外部群組之後，若要檢查並設定其屬性，請從 [**群組**] 索引標籤按一下群組的名稱。

![管理群組][api-management-groups]

從此處，您可以編輯群組的 [**名稱**] 和 [**說明**]。

![編輯群組][api-management-edit-group]

來自所設定 Azure Active Directory 的使用者可以登入開發人員入口網站，並透過下一節中的指示，以檢視及訂閱他們看的見的任何群組。

## 如何使用 Azure Active Directory 帳戶登入開發人員入口網站

若要使用前一節設定的 Azure Active Directory 帳戶登入開發人員入口網站，請使用來自 Active Directory 應用程式組態的 [**登入 URL**] 開啟新瀏覽器視窗，然後按一下 [**Azure Active Directory**]。

![開發人員入口網站][api-management-dev-portal-signin]

輸入您的 Azure Active Directory 中其中一個使用者的認證，然後按一下 [**登入**]。

![Sign in][api-management-aad-signin]

如果需要其他資訊，可能會出現註冊表單的提示。完成註冊表單，然後按一下 [**註冊**]。

![註冊][api-management-complete-registration]

您的使用者現在已登入您的 API 服務執行個體的開發人員入口網站。

![註冊完成][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[存取 Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[使用 Azure Active Directory 帳戶登入開發人員入口網站]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

<!---HONumber=62-->