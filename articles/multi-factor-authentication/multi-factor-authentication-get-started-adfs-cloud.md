<properties 
	pageTitle="使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源" 
	description="這是說明如何在雲端開始使用 Azure MFA 和 AD FS 的 Azure Multi-Factor Authentication 頁面。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# 使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源

如果您的組織與 Azure Active Directory 結盟，而且您有 Azure AD 所存取的資源，您可以使用 Azure Multi-factor Authentication 或 Active Directory Federation Services 來保護這些資源。使用下列程序可利用 Azure Multi-Factor Authentication 或 Active Directory Federation Services 來保護 Azure Active Directory 資源。

## 若要使用 AD FS 保護 Azure AD 資源，請執行下列作業： 



1. 使用[開啟 Multi-Factor Authentication](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) 中概述的步驟，讓使用者啟用帳戶。
2. 請使用下列程序來設定宣告規則：

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	啟動 AD FS 管理主控台。
- 	瀏覽至 [信賴憑證者信任]，並以滑鼠右鍵按一下 [信賴憑證者信任]。選取 [編輯宣告規則...]
- 	按一下 [新增規則…]
- 	從下拉式清單中，選取 [使用自訂規則傳送宣告] 並按 [下一步]。
- 	輸入宣告規則的名稱。
- 	在自訂規則之下：新增下列各項：


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	對應宣告：

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- 按一下 [確定]。按一下 [完成] (Finish)。關閉 AD FS 管理主控台。

使用者便可以使用內部部署方法 (例如智慧卡) 完成登入。


 

<!---HONumber=July15_HO2-->