<properties 
	pageTitle="Azure AD Connect 運作方式"
	description="深入了解 Azure AD Connect 運作方式。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure AD Connect 運作方式



Azure Active Directory Connect 由三個主要部分組成。它們是同步處理服務、選用性 Active Directory Federation Services 部分，以及使用 [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx) 所執行的監控部分。


<center>![Azure AD Connect 堆疊](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- 同步處理 - 此組件是由先前發行為 Dirsync 和 AAD Sync 的元件和功能所組成。這是負責建立使用者和群組的組件。它也會負責確保您內部部署環境中的使用者和群組資訊和雲端的符合。
- AD FS - 這是 Azure AD Connect 的選用性組件，而且可以用來使用內部部署 AD FS 基礎結構來設定混合環境。組織可以使用此組件來處理複雜部署，而複雜部署包括網域加入 SSO、AD 登入原則的強制執行以及智慧卡或協力廠商 MFA 這類項目。如需設定 SSO 的詳細資訊，請參閱[搭配單一登入的 DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)。
- 狀況監控 - 對於使用 AD FS 的複雜部署，Azure AD Connect Health 可以提供同盟伺服器的健全監控，並在 Azure 入口網站中提供提供此活動的中央位置。如需詳細資訊，請參閱 [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)。


## Azure AD Connect 支援元件

下列是每個必要條件和支援元件的清單，而 Azure AD Connect 將這些必要條件和支援元件安裝於在其上設定 Azure AD Connect 的伺服器。此清單適用於基本快速安裝。如果您選擇在 [安裝同步處理服務] 頁面上使用不同的 SQL Server，則不會安裝下面所列的 SQL Server 2012 元件。

- Azure AD Connect Azure AD 連接器
- Microsoft SQL Server 2012 命令列公用程式
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory Module for Windows PowerShell
- Microsoft Online Services Sign-In Assistant for IT Professionals
- Microsoft Visual C++ 2013 Redistribution Package




 

<!---HONumber=August15_HO9-->