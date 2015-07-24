<properties 
	pageTitle="Azure AD 特殊權限身分識別管理" 
	description="說明何謂 Azure AD 特殊權限身分識別管理及其設定方式的主題" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="Justinha"/>

# Azure AD 特殊權限身分識別管理

Azure AD 特殊權限身分識別管理可讓您管理、控制和監視特殊權限身分識別，以及其在 Azure AD 和其他 Microsoft 線上服務 (如 Office 365 或 Microsoft Intune) 中的資源存取權。

若要讓使用者能夠執行特殊權限作業，組織通常需要賦予許多使用者在 Azure AD 中或對 Azure 或 Office 365 資源或其他 SaaS 應用程式的永久特殊權限存取權。對大多數客戶而言，這會使其雲端資源的安全性風險提高，因為他們無法充分監視這些使用者利用其管理員權限的所作所為。此外，擁有特殊權限存取權的使用者帳戶若遭到入侵，可能會影響其整體的雲端安全性。Azure AD 特殊權限身分識別管理有助於解決此風險。

本預覽版本中的 Azure AD 特殊權限身分識別管理可讓您：

- 探索哪些使用者是 Azure AD 管理員
- 啟用目錄資源的隨選、「即時」管理存取權
- 取得有關管理員存取歷程記錄以及有關管理員指派變更的報告 
- 取得有關特殊權限角色存取的警示 

在本預覽版本中，Azure AD 特殊權限身分識別管理可以管理內建的 Azure Active Directory 組織角色：

- 全域管理員 
- 計費管理員 
- 服務管理員  
- 使用者管理員 
- 密碼管理員 

## 即時管理員存取權 

在過去，您可以透過 Azure 管理入口網站或 Windows PowerShell 將使用者指派給管理員角色。因此，該使用者會成為**永久管理員**，其獲得指派的角色永遠處於作用狀態。此預覽版本新增了**暫時管理員**的支援，而這就是需要對指派的角色完成啟用程序的使用者。此啟用程序會將 Azure AD 中角色的使用者指派從非作用中變更為作用中。

## 針對目錄啟用特殊權限身分識別管理

存取 [Microsoft Azure 入口網站](https://portal.azure.com/)，即可開始使用 Azure AD 特殊權限身分識別管理。目前，Azure AD 特殊權限身分識別管理只會出現在 Microsoft Azure 入口網站中。您必須是全域的管理員才能針對目錄啟用 Azure AD 特殊權限身分識別管理。

![][1]

初始化此延伸模組之後，您就會自動成為目錄的第一個**安全性管理員**。只有安全性管理員可以存取此延伸模組來管理其他管理員的存取權。在初始化期間，Azure AD 特殊權限身分識別管理的動態磚會加入至 Azure 預覽入口網站的儀表板。

## 特殊權限身分識別管理儀表板 

Azure AD 特殊權限身分識別管理員有一個儀表板可提供重要資訊，例如：

- 指派給每個特殊權限角色的使用者數目  
- 暫時和永久管理員的數目 
- 管理員的存取歷程記錄 

![][2]

## 特殊權限角色管理 

利用 Azure AD 特殊權限身分識別管理，您可以藉由新增或移除每個角色的永久或暫存管理員來管理管理員。

![][3]

## 設定角色啟用設定 

您可以使用角色啟用設定來設定暫時的角色啟用屬性，包括：

- 角色啟用期間的持續時間
- 角色啟用通知 
- 使用者在角色啟用程序期間所需提供的資訊  

![][4]

## 角色啟用  

若要啟用角色，暫時管理員必須要求對角色進行有時效性的「啟用」。使用 Azure AD 特殊權限身分識別管理中的 [**啟用我的角色**] 選項，即可要求啟用。

想要啟用角色的管理員必須在 Azure 預覽入口網站中初始化 Azure AD 特殊權限身分識別管理。

任何類型的管理員均可使用 Azure AD 特殊權限身分識別管理來啟用自己的角色。
 
角色啟用是有時效性的。在 [角色啟用] 設定中，您可以設定啟用的長度，以及管理員必須提供才能啟用角色的必要資訊。

![][5]

## 角色啟用歷程記錄

使用 Azure AD 特殊權限身分識別管理，也可以追蹤特殊權限角色指派和角色啟用歷程記錄的變更。使用稽核記錄選項即可進行：

![][6]

## 後續步驟

[Microsoft Azure 部落格](http://azure.microsoft.com/blog/) [角色型存取控制](../role-based-access-control-configure.md)

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

 

<!---HONumber=62-->