<properties 
	pageTitle="為使用者設定 Azure AD Join| Microsoft Azure" 
	description="說明系統管理員如何為其使用者 (員工、學生、其他使用者) 設定 Azure AD Join 的主題。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/02/2015" 
	ms.author="femila"/>

# 在組織中設定 Azure AD Join

設定 Azure AD Join 之前，您需要將使用者的內部部署目錄同步處理至雲端，或在 Azure AD 中手動建立受管理的帳戶。

如需將內部部署使用者同步處理至 Azure AD 的詳細指示，請參閱[將內部部署身分識別與 Azure Active Directory 整合](active-directory-aadconnect.md)。


若要在 Azure AD 中手動建立和管理使用者，請參閱 [Azure AD 中的使用者管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)。

## 設定裝置註冊 
1. 以系統管理員身分登入 Azure 入口網站。
2. 在左窗格中選取 [Active Directory]。
3. 在 [目錄] 索引標籤中，選取您的目錄。
4. 選取 [設定] 索引標籤。
5. 捲動至名為**裝置**的區段。
6. 在 [裝置] 索引標籤上，設定下列內容：  
   * **每個使用者的裝置數目上限**：選取使用者可在 Azure AD 中擁有的裝置數目上限。如果使用者達到此配額限制，則在移除一或多個現有的裝置之前，將無法新增其他裝置。
   * **需要 Multi-factor Auth 才能聯結裝置**：當使用者應該提供第二個驗證要素，才能將其裝置加入 Azure AD 時啟用。如需 Multi-factor Auth 的詳細資訊，請參閱[開始在雲端中使用 Azure Multi-Factor Authentication](multi-factor-authentication-get-started-cloud/)
   * **使用者可以將裝置加入 Azure AD**：選取允許將裝置加入 Azure AD 的使用者和群組。
   * **加入 Azure AD 之裝置的其他系統管理員**：使用 Azure AD Premium 或 Enterprise Mobility Suite (EMS)，您可以選擇要為哪些使用者授與裝置的本機系統管理員權限。全域系統管理員和裝置擁有者預設會授與本機系統管理員權限。

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
為使用者設定 Azure AD Join 之後，他們就能透過其公司或個人裝置連接到 Azure AD。

以下是如何讓使用者能夠設定 Azure AD Join 的三種案例：

- 使用者可將公司擁有的裝置直接加入 Azure AD
- 使用者可將公司擁有的裝置網域加入內部部署 Active Directory，並擴充至 Azure AD
- 使用者可在個人裝置上將工作帳戶新增至 Windows 

## 其他資訊
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->