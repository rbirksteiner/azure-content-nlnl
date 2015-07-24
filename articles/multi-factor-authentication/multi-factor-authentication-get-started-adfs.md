<properties 
	pageTitle="開始使用 Azure Multi-Factor Authentication Server 和 Active Directory Federation Services" 
	description="這是說明如何開始使用 Azure MFA 和 AD FS 的 Azure Multi-Factor Authentication 頁面。" 
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

# 開始使用 Azure Multi-Factor Authentication Server 和 Active Directory Federation Services



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

如果貴組織已使用 AD FS 讓內部部署 Active Directory 與 Azure Active Directory 同盟，便可以使用以下 2 個 Azure Multi-Factor Authentication 使用選項。

- 使用 Azure Multi-Factor Authentication 或 Active Directory Federation Services 保護雲端資源 
- 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源 

下表摘要說明保護使用 Azure Multi-Factor Authentication 和 AD FS 兩者保護資源的驗證體驗

|驗證體驗 - 瀏覽器型應用程式|驗證體驗 - 非瀏覽器型應用程式
:------------- | :------------- | :------------- |
使用 Azure Multi-Factor Authentication 保護 Azure AD 資源 |<li>驗證的第一個因素是使用 AD FS 在內部部署。</li> <li>第二個因素是使用雲端驗證執行的電話式方法。</li>|使用者可以使用應用程式密碼來登入。
使用 Active Directory Federation Services 保護 Azure AD 資源 |<li>驗證的第一個因素是使用 AD FS 在內部部署。</li><li>第二個因素是遵守宣告在內部部署。</li>|使用者可以使用應用程式密碼來登入。

有關同盟使用者之應用程式密碼的警告：

- 應用程式密碼乃使用雲端驗證進行驗證，因此會略過同盟。唯有在設定應用程式密碼時才能主動使用同盟。
- 應用程式密碼不會遵守內部部署用戶端存取控制設定。
- 您會失去應用程式密碼的內部部署驗證記錄功能。
- 帳戶停用/刪除最長可能需要 3 個小時才能完成目錄同步處理，導致雲端身分識別中的應用程式密碼停用/刪除延遲。

如需使用 AD FS 設定 Azure Multi-Factor Authentication 和 Azure Multi-Factor Authentication Server 的詳細資訊，請參閱下列各項：

- [使用 Azure Multi-Factor Authentication 和 AD FS 保護雲端資源](multi-factor-authentication-get-started-adfs-cloud)
- [搭配 Windows Server 2012 R2 AD FS 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源](multi-factor-authentication-get-started-adfs-w2k12.md)
- [搭配 AD FS 2.0 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=July15_HO2-->