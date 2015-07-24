<properties
	pageTitle="Azure AD 如何運作？"
	description="Azure AD 會在雲端中建立您的身分識別景象。它可以連接到您的內部部署身分識別系統或單獨使用。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="curtand"/>



# Azure Active Directory 如何運作？


###本主題的其他文章
[什麼是 Azure AD？](active-directory-whatis.md)<br> [運作方式](active-directory-works.md)<br> [入門](active-directory-get-started.md)<br> [後續步驟](active-directory-next-steps.md)<br> [深入了解](active-directory-learn-map.md)


Azure AD 會在雲端中建立您的身分識別景象。它可以連接到您的內部部署身分識別系統或單獨使用。

您可以將 Azure AD 中的帳戶視為您的驅動程式在雲端的授權：這是您在線上存取服務的唯一識別碼。如此一來，Azure AD 的作用就好像您在雲端取得這些驅動程式授權的專屬私人登錄器。它可讓身分識別使用於雲端的任何地方，並改善存取內部部署資源之使用者的行動力。

> [AZURE.NOTE]若要使用 Azure Active Directory，您需要 Azure 帳戶。如果您沒有帳戶，您可以[註冊免費的 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## Azure AD 如何支援 Office 365、Microsoft Intune 和其他 Azure 服務？
Azure 入口網站、Office 365 系統管理中心、Microsoft Intune 帳戶入口網站，以及 Azure AD PowerShell 模組中的 Cmdlet 全都會讀取和寫入與您的目錄相關聯的單一 Azure AD 共用執行個體。入口網站 (或 Cmdlet) 可做為提取或變更您的目錄資訊的前端介面。[深入了解其他服務的支援](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Azure AD 如何支援協力廠商應用程式？
Azure AD 提供身分識別即服務來簡化開發人員的驗證工作，以及提供適用於不同平台的開放原始碼程式庫，協助您開始快速撰寫程式碼。[深入了解 Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。


## Azure AD 如何擴充我的內部部署目錄？
Azure AD 支援數個最常用的驗證和授權通訊協定。[深入了解 Azure Active Directory 驗證通訊協定](active-directory-authentication-scenarios.md)。

## Azure 如何協助我管理身分識別？
想要進一步了解如何管理 Azure AD 嗎？ 如何取得目錄？ 如何刪除目錄？ 如何管理目錄資料？ 深入了解 Azure AD 目錄的管理。[深入了解如何管理 Azure AD](active-directory-administer.md)。

## 其他資源

* [以組織身分註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)
 

<!---HONumber=62-->