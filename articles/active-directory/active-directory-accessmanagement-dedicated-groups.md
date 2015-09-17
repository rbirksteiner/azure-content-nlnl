<properties 
	pageTitle="Azure Active Directory 中的專用群組 | Microsoft Azure" 
	description="說明如何在 Azure AD 中管理群組的主題。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

# Azure Active Directory 中的專用群組

在 Azure Active Directory 中，會自動建立專用群組，也會自動建立專用群組的群組成員資格。您無法透過 Azure 管理入口網站、Windows PowerShell Cmdlet，或以程式設計方式，在專用群組中新增或移除成員。若要啟用專用群組，在 Azure 管理入口網站的 [設定] 索引標籤上，將 [啟用專用群組] 參數設定為 [是]。

在目前的公開預覽版本中，一旦將 [啟用專用群組] 參數設定為 [是] 之後，您就可以將 [啟用 [All Users] 群組] 參數設定為 [是]，進一步讓目錄自動建立 [All Users] 專用群組。接著，您也可以在 [All Users] 群組欄位的 [顯示名稱] 中輸入此專用群組的名稱來加以編輯。

如果您想要將相同的權限指派給您目錄中的所有使用者，[All Users] 專用群組可能會很實用。例如，您可以將 [All Users] 專用群組的存取權指派給此應用程式，以便為您目錄中的所有使用者授與 SaaS 應用程式的存取權。

以下是將提供一些 Azure Active Directory 的其他相關資訊的部分主題

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->