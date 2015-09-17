<properties 
                pageTitle="什麼是 Azure Active Directory？" 
                description="使用 Azure Active Directory 將現有的內部部署身分識別延伸至雲端，或開發 Azure AD 整合式應用程式。" 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="08/10/2015" 
                ms.author="markusvi"/>


# 什麼是 Azure Active Directory？





Azure Active Directory (Azure AD) 是 Microsoft 的多租用戶雲端型目錄和身分識別管理服務。

對於 IT 系統管理員而言，Azure AD 提供經濟實惠、易於使用的解決方案，讓員工和企業合作夥伴單一登入 (SSO) 存取[數千個雲端 SaaS 應用程式](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx)，例如 Office365、Salesforce.com、DropBox 和 Concur。

對於應用程式開發人員而言，Azure AD 可讓您專注於建置應用程式，快速而簡單地整合數百萬個全球各地組織所使用的世界級身分識別管理解決方案。

Azure AD 也包含一組完整的身分識別管理功能，包括多重要素驗證、裝置註冊、自助式密碼管理、自助式群組管理、特殊權限的帳戶管理、角色型存取控制、應用程式使用量監視、豐富的稽核，以及安全性監視和警示。這些功能可以協助保護雲端型應用程式的安全、簡化 IT 程序、降低成本，以及協助確保達到公司的規範目標。

此外，只要按[四次按鍵](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)，Azure AD 就可以與現有的 Windows Server Active Directory 整合，讓組織能夠運用現有的內部部署身分識別投資，來管理雲端型 SaaS 應用程式的存取權。

如果您是 Office365、Azure 或 Dynamics CRM Online 的客戶，您可能不知道您已經在使用 Azure AD。每個 Office365、Azure 和 Dynamics CRM 租用戶實際上都已經是 Azure AD 租用戶。只要您願意，您就可以開始使用該租用戶來管理整合 Azure AD 的上千種其他雲端應用程式的存取權！





<center>![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png) </center>


## Azure AD 有多可靠？

Azure AD 有多租用戶、地理位置分散且具有高可用性的設計，表示您可以依賴 Azure AD 來應付最重要的商務需求。使用世界各地具自動容錯移轉的 28 個資料中心，您能夠輕易感受到 Azure AD 非常可靠，即使資料中心中斷，您的目錄資料至少在兩個以上地域分散的資料中心都有留存複本，而且可讓您立即存取。

如需詳細資料，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)。



## Azure AD 有哪些優點？

您的組織可以使用 Azure AD 在許多方面提升員工生產力、簡化 IT 程序、改善安全性並降低成本：

-	快速採用雲端服務，提供員工和合作夥伴簡單的單一登入體驗，這是由 Azure AD 提供、完全自動的 SaaS 應用程式存取管理和佈建服務功能。
-	讓員工可以在任何地方，使用他們所喜愛的裝置工作，存取世界級雲端應用程式和服務，以及各種自助式功能。
-	輕鬆安全地管理員工和廠商存取您公司的社交媒體帳戶。 
-	利用 Azure AD 多重要素驗證和條件式存取，改善應用程式的安全性。
-	實作一致、自助式的應用程式存取管理，讓企業主能夠快速採取行動，同時能夠降低 IT 成本和額外負荷。
-	利用安全性報告和監視，監視應用程式使用量並保護您的企業免於受到嚴重的威脅。
-	保護行動 (遠端) 存取內部部署應用程式的安全。






## 如何開始使用？
-	如果您是 IT 管理員：
 - [試試看！](https://azure.microsoft.com/trial/get-started-active-directory/) - 您可以立即註冊免費 30 天的試用版，使用此連結不到 5 分鐘即可部署第一個雲端解決方案
 - 閱讀我們的〈開始使用 Azure AD〉了解秘訣和訣竅，獲得 Azure AD 租用戶並快速執行
-	如果您是開發人員：
 - 請查看我們的 Azure Active Directory 的[開發人員手冊](https://msdn.microsoft.com/library/azure/ff800682.aspx)<need link>
 - [開始使用試用版](https://azure.microsoft.com/trial/get-started-active-directory/) – 立即註冊免費 30 天的試用版，並開始與 Azure AD 整合您的應用程式 



## 哪裡可以深入了解？

我們有無數的絕佳線上資源，協助您詳細了解 Azure AD。以下是一些很棒的文章，讓您快速入門：


- [使用 Azure AD Connect 啟用目錄的混合式管理](active-directory-aadconnect.md)

- [為連線過的項目提供額外的安全性](multi-factor-authentication.md)

- [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)

- [開始使用 Azure AD 報告](active-directory-reporting-getting-started.md)

- [從任何地方管理您的密碼](articles/active-directory-passwords.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

- [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)

- [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)

- [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)

- [什麼是 Microsoft Azure Active Directory 授權？](active-directory-licensing-what-is.md)

- [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)

<!---HONumber=August15_HO7-->