<properties 
    pageTitle="在 RemoteApp 中新增使用者" 
    description="了解如何在 RemoteApp 中新增使用者" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />

# 如何在 RemoteApp 中新增使用者

您必須先授與使用者您集合的存取權，他們才能在 RemoteApp 中看到和使用應用程式。這是最簡單的部分：在**使用者存取**索引標籤上，輸入使用者的帳戶資訊，然後按一下核取記號。

您需要什麼帳戶資訊？ 這取決於您建立的收藏類型 (雲端或混合式)，還有您是否正在該收藏中使用 Office 365 ProPlus。

## 支援的使用者識別

不同的集合類型 (雲端或混合式) 支援使用不同的使用者身分識別，供使用者存取應用程式。

對於 RemoteApp 的混合式收藏，您必須設定內部部署的 Active Directory 網域基礎結構和具備目錄整合的 Azure Active Directory 租用戶 (及選擇性的單一登入)。此外，您必須在內部部署的目錄中建立一些 Active Directory 物件。

對於 RemoteApp 的雲端收藏，具有 Azure Active Directory 支援識別的任何使用者都可以被授與 RemoteApp 的使用者存取權以包含 Microsoft 帳戶。請參閱下表。

Office 365 使用者為 Azure Active Directory 使用者。如果這些使用者有 Azure Active Directory 混合式、目錄同步處理的帳戶，就可以在 RemoteApp 混合式部署中授與他們使用者存取權。

您可以使用這個表格快速參考在您的收藏中支援何種識別，以及 Active Directory 的需求為何。

|使用者帳戶 |雲端 |混合式|
|--------------|--------|------|
|Microsoft 帳戶| 	是|	否|
|Azure Active Directory (Azure AD)| | |	
|僅 Azure AD 雲端 |是 |否 |
|具有密碼同步的 ADsync |是 |是 |
|不具密碼同步的 ADsync|	是 |否 |
|具 AD FS 的 ADsync |是 |是 |
|Azure 支援的第三方識別提供者 (例如 Ping) |是 |否|	
|Multi-Factor Authentication |是 |是 |

請查看有關設定 RemoteApp 的 Active Directory 的[詳細資訊](remoteapp-ad.md)。


> [AZURE.NOTE]Azure Active Directory 使用者必須來自於與您的訂用帳戶相關聯的租用戶。(您可以在入口網站的 [設定] 索引標籤上檢視和修改您的訂用帳戶。如需詳細資訊，請參閱[變更 RemoteApp 所使用的 Azure Active Directory 租用戶](remoteapp-changetenant.md)。)

## Office 365 ProPlus 使用者帳戶資訊
如果您的收藏中使用 Office 365 ProPlus 範本映像，*或者*如果您建立了使用 Office 365 的自訂映像，則您只能新增在您的訂用帳戶的預設網域中擁有 Office 365 訂用帳戶的 Azure Active Directory 使用者。如需詳細資訊，請參閱[透過 Azure RemoteApp 使用 Office 365](remoteapp-o365.md)。
 

<!---HONumber=62-->