<properties 
	pageTitle="在 Azure AD 中管理群組" 
	description="說明如何在 Azure AD 中管理群組的主題。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# 在 Azure AD 中管理群組

群組是可以當作單一單位管理的使用者和群組集合。屬於特定群組的使用者和群組稱為群組成員。使用群組可以簡化管理，其作法是一次將一組常用的權限和權利指派給許多帳戶，而不需個別指派權限和權利給每個帳戶。

目前在 Azure AD 中，您只可以建立安全性群組。

當您需要將許多使用者指派給相同的應用程式時，您可以使用群組作為簡便的方式來指派存取權給使用者的應用程式。設定其他可控制資源存取之線上服務 (例如，SharePoint Online) 的存取管理時，您也可以使用群組。

如果您已設定目錄同步作業，您可以在 [源自] 屬性中，看到已從本機內部部署 Windows Server Active Directory 同步處理的群組具有 [本機 Active Directory] 值。您必須繼續在本機 Active Directory 中管理這些群組；在 Azure 管理入口網站中無法管理或刪除這些群組。

如果您有 Office 365 時，您可以看到在 Office 365 的 Exchange 系統管理中心內建立及管理的通訊群組和啟用郵件功能的安全性群組。這些群組在 [源自] 屬性中具有值 [Office 365]，而且必須繼續在 Exchange 系統管理中心進行管理。

您也可以透過存取面板建立統一群組。在 [設定] 索引標籤的 [群組管理] 中，將 [**使用者可建立 O365 群組**] widget 設定為 [**是**]。如果您有在存取面板或 Office 365 中建立的 Office 365 統一群組，這些群組的 [源自] 數性會設定為 [Azure Active Directory]，並可透過存取面板進行管理。

如果您已為使用者啟用自助式群組管理 (如需詳細資訊，請參閱 Azure AD 中使用者的自助式群組管理)，身為租用戶系統管理員的您也可以透過 Azure 管理入口網站管理這些群組。您可以新增和移除群組成員、新增和移除群組擁有者、編輯群組屬性，以及檢視群組的歷程記錄活動報告 (該報告可顯示在群組內執行的動作、執行該動作的人員與時間)。

> [AZURE.NOTE]為了能夠將群組指派至應用程式，您必須使用 Azure AD Premium。如果您有 Azure AD Premium，您也可以使用群組來指派與 Azure AD 整合之 SaaS 應用程式的存取權。如需詳細資訊，請參閱〈在 Azure AD 中將群組存取權指派給 SaaS 應用程式〉。

## 後續步驟

- [管理 Azure AD](active-directory-administer.md)
- [在 Azure AD 中建立或編輯使用者](active-directory-create-users.md)
- [在 Azure AD 中管理密碼](active-directory-manage-passwords.md)
 

<!---HONumber=62-->