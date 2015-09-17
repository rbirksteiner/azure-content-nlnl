<properties
	pageTitle="應用程式模型 v2.0 | Microsoft Azure"
	description="建置具備 Microsoft 帳戶和 Azure Active Directory 登入之應用程式的簡介。"
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

# 應用程式模型 v2.0 預覽版：以單一應用程式登入 Microsoft 帳戶和 Azure AD 使用者

> [AZURE.NOTE]這項資訊適用於 v2.0 應用程式模型公開預覽版。如需如何與正式運作之 Azure AD 服務整合的相關指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

在過去，想要同時支援 Microsoft 帳戶和 Azure Active Directory 的應用程式開發人員必須整合這兩個不同的系統。透過 v2.0 應用程式模型，您現在可以使用這兩種帳戶類型登入使用者。一個簡單的整合便可讓您觸達橫跨個人和工作/學校帳戶的數百萬名使用者對象。

您的應用程式也可以透過任何一種帳戶類型，來使用[一組 Office 365 REST API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)。這些 API 目前包含 Outlook 的郵件、連絡人和行事曆 API。在不久的將來會新增其他服務。
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

V2.0 應用程式模型為預覽狀態。在預覽期間，歡迎您提供使用新應用程式模型進行試驗的意見反應和經驗。我們可能會根據這些意見反應進行重大變更，以改善服務。在這段期間，請勿發行使用 v2.0 應用程式模型的生產環境應用程式。
<!-- TODO: Get approval on how it looks  -->

## 開始使用
有兩種方式可以啟動應用程式並搭配 v2.0 應用程式模型執行。您可以選擇使用 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 或 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) 直接傳送通訊協定訊息，也可以使用我們的程式庫為您執行工作 - 選擇下列您最愛的平台並開始使用。
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 新功能
經常查看這裡，以了解 v2.0 應用程式模型公開預覽版的未來變更。我們也會使用 @AzureAD 發佈任何更新的相關推文。

- 了解[您可以使用應用程式模型 v2.0 建置的應用程式類型](active-directory-v2-flows.md)。
- 若是熟悉 Azure Active Directory 的開發人員，您應該查看 [v2.0 應用程式模型中的通訊協定更新和差異](active-directory-v2-compare.md)。
- 目前[預覽版的限制和條件約束](active-directory-v2-limitations.md)。

## 參考
下列連結有助於深入探索平台：

- 取得使用 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標記之堆疊溢位的相關說明。
- 使用 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) 告訴我們您對預覽版的想法 - 我們很想知道您的想法！ 請在您的貼文標題中使用 "AppModelv2:" 字詞，以方便我們尋找。
- [應用程式模型 v2.0 通訊協定參考](active-directory-v2-protocols.md)
- [應用程式模型 v2.0 權杖參考](active-directory-v2-tokens.md)
- [Office 365 REST API 參考](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [v2 端點的範圍和同意](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!----HONumber=August15_HO7-->