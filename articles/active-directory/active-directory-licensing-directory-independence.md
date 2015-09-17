<properties
   pageTitle="新增和管理多個 Azure AD 目錄 | Microsoft Azure"
   description="新增和管理 Azure AD 目錄的指示和最佳作法，說明目錄為完全獨立的資源"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/16/2015"
   ms.author="curtand"/>

# 新增和管理多個 Azure AD 目錄

在 Azure AD 目錄中，每個目錄都是完全獨立的資源：對等、全功能，且在邏輯上獨立於您管理的其他目錄。目錄之間沒有任何父子關聯性。目錄之間的這項獨立性包括資源獨立性、系統管理獨立性和同步處理獨立性。

##資源獨立性

如果您在某個目錄中建立或刪除資源，則不會影響另一個目錄中的任何資源 (但外部使用者有部分例外狀況)，如下所述。如果您搭配使用自訂網域 'contoso.com' 與某個目錄，則它不能與任何其他目錄搭配使用。

##系統管理獨立性

如果是 'Contoso' 目錄的非系統管理使用者，請建立 'Test' 測試目錄，然後：- 根據預設，建立目錄的使用者會加入該新目錄中成為外部使用者，並且被指派該目錄的全域系統管理員角色。- 'Contoso' 目錄的系統管理員對 'Test' 目錄沒有直接的系統管理權限，除非 'Test' 的系統管理員特別授與他們這些權限。'Contoso' 的系統管理員如果控制建立 'Test' 的使用者帳戶，就可以控制 'Test' 目錄的存取權。- 如果您變更 (新增或移除) 一個目錄中使用者的系統管理員角色，變更並不會影響使用者在另一個目錄中可能擁有的任何系統管理員角色。

##同步處理獨立性

您可以獨立設定每個 Azure AD 目錄，讓資料可從下列的單一執行個體同步處理：- 目錄同步作業 (DirSync) 工具，與單一 AD 樹系同步處理資料。- 適用 Forefront Identity Manager 的 Azure Active Directory 連接器，與一或多個內部部署樹系和/或非 Azure AD 資料來源同步處理資料。

##新增 Azure AD 目錄

若要在 Azure 管理入口網站中新增 Azure AD 目錄，請選取左邊的 Active Directory 延伸模組，然後點選 [新增]。

> [AZURE.NOTE]與其他 Azure 資源不同，您的目錄不是 Azure 訂用帳戶的子資源。如果您取消或允許 Azure 訂用帳戶到期，則還是可以使用 Azure PowerShell、Azure Graph API 或其他介面 (例如 Office 365 系統管理中心) 存取目錄資料。您也可以關聯另一個訂用帳戶與目錄。

如需 Azure AD 授權問題和最佳作法的一般概觀，請參閱[什麼是 Azure Active Directory 授權？](active-directory-licensing-what-is.md)。

<!---HONumber=August15_HO6-->