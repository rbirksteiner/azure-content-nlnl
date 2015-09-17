<properties 
	pageTitle="設定 Azure AD Connect 時需要 Azure AD 全域系統管理員帳戶的原因"
	description="為何需要全域系統管理員帳戶的自訂設定說明。"
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

# 設定 Azure AD Connect 時需要 Azure AD 全域系統管理員帳戶的原因

下表說明設定 Azure AD Connect 時需要 Azure AD 全域系統管理員帳戶的原因。

在下列情況下 | 說明 
------------- | ------------- |
快速設定和 DirSync 升級 | 我們會在 Azure AD 目錄中啟用同步處理 (如有必要)，並建立將用於持續同步處理作業的 Azure AD 帳戶 (Azure AD Connector 帳戶)。 
自訂設定 | 我們會在 Azure AD 目錄中啟用同步處理，並建立將用於持續同步作業的 Azure AD 帳戶 (Azure AD Connector 帳戶)。若是使用 AD FS 選項的單一登入，我們會在 Azure AD 中讀取和設定同盟屬性。



**其他資源**


* [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-account-summary.md)
* [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)

<!---HONumber=August15_HO9-->