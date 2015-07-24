<properties 
	pageTitle="使用 Azure AD Connect 啟用目錄的混合式管理。" 
	description="這就是 Azure AD Connect，說明它是什麼及使用的理由。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# 使用 Azure AD Connect 啟用目錄的混合式管理

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="它是什麼" class="current">它是什麼</a> <a href="../active-directory-aadconnect-how-it-works/" title="運作方式">運作方式</a> <a href="../active-directory-aadconnect-get-started/" title="開始使用">開始使用</a> <a href="../active-directory-aadconnect-whats-next/" title="後續步驟">後續步驟</a> <a href="../active-directory-aadconnect-learn-more/" title="深入了解">深入了解</a>
</div>


現在，使用者希望在內部部署和雲端都能存取應用程式。他們希望能夠從任何裝置上這樣做，像是膝上型電腦、智慧型手機或平板電腦。為了實現這個理想，您和您的組織必須設法讓使用者存取這些應用程式，而完全轉向雲端不見得總是可行。

<center>![什麼是 Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

隨著 Azure Active Directory Connect 推出，存取這些應用程式和轉向雲端再簡單不過了。Azure AD Connect 提供下列優點：

- 您的使用者在雲端和內部部署都能使用一個通用身分識別登入。他們不需要記住多個密碼或帳戶，而系統管理員不必擔心多個帳戶可能帶來的額外負擔。
- 單一工具和引導式體驗，串連您的內部部署目錄與 Azure Active Directory。此精靈安裝後可部署及設定所有必要元件，讓您的目錄整合開始運作，包括同步服務、密碼同步或 AD FS，以及必要元件，例如 Azure AD PowerShell 模組。



<center>![什麼是 Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## 使用 Azure AD Connect 的理由 

將內部部署目錄與 Azure AD 整合可提供一個通用身分識別來存取雲端和內部部署資源，讓使用者變得更有生產力。透過此整合，使用者和組織可以享受到下列好處：
	
* 組織可以運用 Windows Server Active Directory，然後連線到 Azure Active Directory，提供跨內部部署或雲端架構服務的通用混合式身分識別給使用者。 
* 系統管理員可以根據應用程式資源、裝置和使用者身分識別、網路位置及多因素驗證，提供條件式存取。
* 使用者可以透過 Azure AD 中的帳戶，將通用身分識別運用到 Office 365、Intune、SaaS 應用程式和協力廠商應用程式。  
* 開發人員可以利用通用身分識別模型來建置應用程式，將應用程式整合到 Active Directory 內部部署或 Azure，成為雲端架構應用程式

Azure AD Connect 可輕鬆達成這項整合，並簡化管理內部部署和雲端身分識別基礎結構。



----------------------------------------------------------------------------------------------------------
## 下載 Azure AD Connect

若要開始使用 Azure AD Connect，您可以使用下列連結下載最新版本：[下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->