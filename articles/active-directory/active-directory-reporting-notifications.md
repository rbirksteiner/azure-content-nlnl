<properties
	pageTitle="Azure Active Directory 報告通知"
	description="如何使用 Azure Active Directory 報告通知找出可疑登入。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Azure Active Directory 報告通知

## 哪些報告會產生電子郵件通知

目前，只有「異常登入活動」報告和「具有異常登入活動的使用者」報告使用電子郵件通知系統。

## 什麼會觸發要傳送的電子郵件通知？

根據預設，Azure Active Directory 設為自動傳送電子郵件通知給所有全域管理員。在下列情況下會針對每份報告傳送電子郵件。

異常登入活動報告：

- 不明來源：10 個事件
- 多次失敗：10 個事件
- 具有可疑活動的 IP 位址：10 個事件
- 受感染的裝置：10 個事件

具有異常登入活動的使用者報告：

- 不明來源：10 個事件
- 多次失敗：10 個事件
- 具有可疑活動的 IP 位址：10 個事件
- 受感染的裝置：5 個事件
- 異常登入報告：15 個事件

如果在 30 天內或自從上次電子郵件傳送後 (若少於 30 天) 符合上述任何條件，就會傳送電子郵件。

異常登入是指我們的機器學習演算法根據未預期的登入位置、當日時間和位置或這些項目的組合，而識別為「異常」的登入。這可能表示駭客已嘗試使用此帳戶進行登入。您可以在上面的表格中找到有關此報告的詳細資訊。

## 誰會收到電子郵件通知？

電子郵件會傳送給所有已獲指派 Active Directory Premium 授權的全域管理員。為了確保能夠送達，我們也會將電子郵件傳送到管理員的備用電子郵件地址。管理員應將 aad-alerts-noreply@mail.windowsazure.com 納入其安全寄件者清單中，以免遺漏電子郵件。

## 這些電子郵件的傳送頻率為何？

傳送電子郵件後，只有在傳送該電子郵件的 30 天內發生 10 個或更多新的異常登入事件時，才會傳送下一封電子郵件。如何存取電子郵件中提到的報告？

當您按一下連結時，您將會重新導向至 Azure 管理入口網站中的報告頁面。若要存取報告，您必須同時是：

- 您的 Azure 訂用帳戶的管理員或共同管理員
- 目錄中的全域管理員，並獲得指派的 Active Directory Premium 授權。如需詳細資訊，請參閱 Azure Active Directory 版本。

## 我可以關閉這些電子郵件嗎？

是，若要關閉 Azure 管理入口網站中異常登入的相關通知，請按一下 [**設定**]，然後選取 [**通知**] 區段之下的 [**已停用**]。

## 後續步驟
- 想知道有哪些安全性、稽核及活動報告可用嗎？ 請查看 [Azure AD 安全性、稽核及活動報告](active-directory-view-access-usage-reports.md)
- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)

<!---HONumber=August15_HO8-->