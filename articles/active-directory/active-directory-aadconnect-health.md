<properties 
	pageTitle="在雲端中監視內部部署身分識別基礎結構。" 
	description="這是 Azure AD Connect Health 頁面，其中說明它的功能，以及您可能會使用它的原因。" 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# 在雲端中監視內部部署身分識別基礎結構

Azure AD Connect Health 可協助您監視和了解內部部署身分識別基礎結構。它可讓您檢視警示、效能、使用模式、組態設定，並讓您穩定連線至 Office 365 ，還有更多功能。這些功能必須使用安裝在目標伺服器上的代理程式才可完成。若要深入了解，請參閱 [MSDN 上的 Azure AD Connect Health。](https://msdn.microsoft.com/library/azure/dn906722.aspx)

![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


這些資訊會全部呈現在 Azure AD Connect Health 入口網站中。您可以使用 Azure AD Connect Health 入口網站檢視警示、效能監視和使用量分析。這些資訊會全部呈現在容易找到的同一位置中，您便不必浪費時間尋找需要的資訊。

![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/usage.png)

Azure AD Connect Health 未來更新的內容包括額外監視，以及其他身分識別元件和服務 (例如 Azure AD Connect 同步處理服務) 的深入資訊。因此，它透過身分識別透鏡提供單一儀表板，讓您能擁有更穩健、良好的整合式環境，使用者便能夠充分利用以增進工作能力。

若要深入了解，請參閱 [MSDN 上的 Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)。


![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/logo1.png)




## 使用 Azure AD Connect Health 的原因

將內部部署目錄與 Azure AD 整合可提供通用身分識別供存取雲端和內部部署資源，進而讓您的使用者更具生產力。然而，整合後帶來的挑戰是，必須確保此環境良好健全，才能讓使用者從任何裝置可靠地存取內部部署和雲端中的資源。Azure AD Connect Health 提供簡易的雲端方法，監視及深入了解用來存取 Office 365 或其他 Azure AD 應用程式的內部部署身分識別基本結構。使用方式相當簡單，您只需將代理程式安裝在各個內部部署身分識別伺服器中即可。

適用於 AD FS 的 Azure AD Connect Health 支援 Windows Server 2008/2008 R2 中的 AD FS 2.0，以及 Windows Server 2012/2012 R2 中的 AD FS。其中也包括提供外部網路存取驗證支援的任何 AD FS Proxy 或 Web 應用程式 Proxy 伺服器。適用於 AD FS 的 Azure AD Connect Health 提供下列一組主要功能：

- 檢視警示並據此採取行動，以穩定存取受 AD FS 保護的應用程式 (包括 Azure AD)
- 重大警示的電子郵件通知
- 檢視效能資料以判斷容量規劃
- 詳細檢視 AD FS 登入模式，以判斷異常狀況或建立容量規劃基準



----------------------------------------------------------------------------------------------------------
## 下載 Azure AD Connect Health 代理程式

若要開始使用 Azure AD Connect Health，您可以在此下載最新版的代理程式：[下載 Azure AD Connect Health 代理程式](http://go.microsoft.com/fwlink/?LinkID=518973)。 請確定您已從 Marketplace 中加入服務，才可安裝代理程式。

----------------------------------------------------------------------------------------------------------





**其他資源**

* [MSDN 上的 Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=62-->