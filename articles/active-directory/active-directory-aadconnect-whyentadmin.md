<properties 
	pageTitle="需要企業系統管理員帳戶的原因"
	description="自訂設定說明。"
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

# 設定 Azure AD Connect 時需要企業系統管理員帳戶連接至 AD DS 的原因

下表說明設定 Azure AD Connect 時需要企業系統管理員帳戶的原因。

在下列情況下 | 說明 
------------- | ------------- |
快速設定和 DirSync 升級 | <li>若為快速設定，我們必須建立本機 Active Directory 帳戶 (也稱作 AD Connector 帳戶)，以用於同步處理並指派正確的同步處理與密碼同步處理權限</li> <li>若為 DirSync 升級，我們必須在目前設定的 AD Connector 帳戶上重新設定密碼，並且設定新的 Azure AD Connect 同步處理服務以使用此帳戶。</li>



**其他資源**


* [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-account-summary.md)
* [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)
* [MSDN 上的 Azure AD Connect](active-directory-aadconnect.md) 

<!---HONumber=August15_HO9-->