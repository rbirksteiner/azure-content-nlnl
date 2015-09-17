<properties 
	pageTitle="Azure AD Connect - SSL 憑證需求"
	description="搭配 AD FS 使用的 Azure AD Connect SSL 憑證需求。"
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

# Azure AD Connect - SSL 憑證需求

**重要事項：**強烈建議您在 AD FS 伺服器陣列的所有節點以及所有 Web 應用程式 Proxy 伺服器上使用相同的 SSL 憑證。

- 此憑證必須是 X509 憑證。 
- 您可以在測試實驗室環境中的同盟伺服器上使用自我簽署憑證；但是，如果您在生產環境中，則建議從公開的 CA 中取得憑證。 
	- 如果使用非公開信任的憑證，請確定每個 Web 應用程式 Proxy 伺服器上安裝的憑證已取得本機伺服器和所有同盟伺服器的信任 
- 不支援以 CryptoAPI 新一代 (CNG) 金鑰和金鑰儲存體為基礎的憑證。這表示您必須使用以 CSP (密碼編譯服務提供者) 為基礎的憑證，而不是 KSP (金鑰儲存體提供者)。 
- 憑證的身分識別必須與 Federation Service 名稱相符 (例如 fs.contoso.com)。 
	- 身分識別可以是 dNSName 類型的主體別名 (SAN) 副檔名；或如果沒有 SAN 項目，則會將主體名稱指定為通用名稱。  
	- 憑證中可顯示多個 SAN 項目，前提是其中一個項目與 Federation Service 名稱相符。 
	- 如果您打算使用「加入工作場所」，則您需要其他帶有 “enterpriseregistration” 值的 SAN，後面接者組織的使用者主體名稱 (UPN) 尾碼 (例如，enterpriseregistration.contoso.com)。 

支援萬用字元憑證。
 

<!---HONumber=August15_HO9-->