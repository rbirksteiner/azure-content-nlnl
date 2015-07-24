<properties 
	pageTitle="在 Azure AD 中管理密碼" 
	description="說明如何在 Azure AD 中管理密碼的主題。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# 在 Azure AD 中管理密碼

如果您是系統管理員，可以在 Azure 傳統入口網站的 Azure 中重設使用者的密碼。按一下您的目錄名稱並在 [使用者] 頁面上按一下使用者的名稱，然後按一下入口網站底部的 [**重設密碼**]。

本主題的其餘部分涵蓋 Azure Active Directory 支援的完整密碼管理功能，包括：

- **自助式密碼**變更可讓使用者或系統管理員變更過期或未過期密碼，而不需向系統管理員或服務台請求支援。
- **自助式密碼**重設可讓使用者或系統管理員自動重設密碼，而不需向系統管理員或服務台請求支援。自助式密碼重設需要 Azure AD Premium 或 Basic。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
- **由管理員起始的密碼重設**可讓系統管理員在 Azure 管理入口網站中重設一般使用者或另一個系統管理員的密碼。
- **密碼管理活動報告**讓系統管理員得以深入了解其組織內發生的密碼重設和註冊活動。 
- **密碼回寫**允許從雲端管理內部部署密碼，以便代表或由同盟或已同步處理密碼的使用者執行上述所有案例。密碼回寫需要 Azure AD Premium。如需詳細資訊，請參閱[開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。

> [AZURE.NOTE]Azure AD Premium 可用於使用 Azure AD 全球執行個體的中國客戶。由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure AD Premium。如需詳細資訊，請透過 [Azure Active Directory 論壇](http://feedback.azure.com/forums/169401-azure-active-directory)與我們連絡。

使用下列連結可前往您最感興趣的文件：

- [概觀：Azure AD 中的密碼管理](https://msdn.microsoft.com/library/azure/dn683880.aspx)
- [Azure AD 中的自助式密碼重設：如何啟用、設定及測試自助式密碼重設](https://msdn.microsoft.com/library/azure/dn683881.aspx)
- [Azure AD 中的自助式密碼重設：如何自訂密碼重設以符合您的需求](https://msdn.microsoft.com/library/azure/dn688249.aspx)
- [Azure AD 中的自助式密碼重設：部署和管理的最佳作法](https://msdn.microsoft.com/library/azure/dn903643.aspx)
- [Azure AD 中的密碼管理報告：如何在您的租用戶中檢視密碼管理活動](https://msdn.microsoft.com/library/azure/dn903641.aspx)
- [密碼回寫：如何設定 Azure AD 來管理內部部署密碼](https://msdn.microsoft.com/library/azure/dn903642.aspx)
- [Azure AD 密碼管理的常見問題集/疑難排解](https://msdn.microsoft.com/library/azure/dn683878.aspx)

## 後續步驟

- [管理 Azure AD](active-directory-administer.md)
- [在 Azure AD 中建立或編輯使用者](active-directory-create-users.md)
- [在 Azure AD 中管理群組](active-directory-manage-groups.md)
 

<!---HONumber=62-->