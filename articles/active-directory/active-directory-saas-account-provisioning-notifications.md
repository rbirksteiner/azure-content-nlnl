<properties
	pageTitle="帳戶佈建通知"
	description="藉由啟用帳戶佈建通知，了解如何確保您會收到與使用者佈建相關問題的通知要您特別注意。"
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
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# 帳戶佈建通知

透過使用者佈建，您可以自動化在協力廠商 SaaS 應用程式中管理使用者的程序。<br>雖然這是自動化的程序，但是您還是不時需要與此程序互動。<br>例如以下這個範例，當您設定與協力廠商 SaaS 應用程式交換資料的帳戶密碼已經到期。

藉由啟用帳戶佈建通知，您就可以確保您會收到與使用者佈建相關問題的通知要您特別注意。

您在為協力廠商 SaaS 應用程式設定使用者佈建時，就可以啟用或停用帳戶佈建通知。

![使用者佈建][1]



若要啟用帳戶佈建通知，請在 [確認] 對話方塊頁面上選取相關的核取方塊，然後輸入收件者的電子郵件別名。

![帳戶佈建通知][2]
 


您可以輸入通訊群組清單做為收件者。不過請務必注意，通知電子郵件所包含的報表連結，只能由 Azure AD 系統管理員存取。

如果您啟用了帳戶佈建通知，您將會收到電子郵件，通知您與使用者佈建相關的重大問題。不過為了避免電子郵件多載，對於已啟用電子郵件通知的每個 SaaS 應用程式，您每天只會收到一封通知電子郵件。


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=August15_HO6-->