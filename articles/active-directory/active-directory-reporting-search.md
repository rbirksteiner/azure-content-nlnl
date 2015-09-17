<properties
	pageTitle="Azure Active Directory 報告搜尋"
	description="如何搜尋 Azure Active Directory 的安全性、活動和稽核報告"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/07/2015"
	ms.author="kenhoff"/>

# Azure Active Directory 報告搜尋

Azure Active Directory 可以讓目錄管理員跨多個報告搜尋使用者安全性、活動和稽核事件。

若要尋找搜尋面板，請瀏覽至 [Azure 管理入口網站] -> 您的 Azure Active Directory -> [報告]。 您可以在報告清單頂端找到面板。

若要搜尋特定使用者的活動或稽核事件，請在 [從] 和 [到] 欄位中選取日期範圍、輸入使用者的 UPN 或顯示名稱，然後按一下核取記號按鈕。

## 搜尋中包含的報告

搜尋結果中不一定會包含所有報告。此表格指出會包含哪些報告。

|	報告 |	已包括 |
|	------												|	--------			|
|	從不明來源登入 |	否 |
|	在多次失敗後登入 |	否 |
|	從多個地理區域登入 |	否 |
|	從具有可疑活動的 IP 位址登入 |	否 |
|	從可能受感染的裝置登入 |	否 |
|	異常的登入活動 |	否 |
|	具有異常登入活動的使用者 |	否 |
|	認證外洩的使用者 |	否 |
|	稽核報告 |	是 |
|	密碼重設活動 |	是 |
|	密碼重設註冊活動 |	是 |
|	自助服務群組活動 |	是 |
|	應用程式使用情況 |	否 |
|	帳戶佈建活動 |	是 |
|	密碼變換狀態 |	否 |
|	帳戶佈建錯誤 |	否 |
|	RMS 使用量 |	否 |
|	最活躍的 RMS 使用者 |	否 |
|	RMS 裝置使用量 |	否 |

## 詳細資訊

 - [Azure Active Directory 報告](active-directory-view-access-usage-reports.md)
 - [Azure Active Directory 報告稽核事件](active-directory-reporting-audit-events.md)

<!---HONumber=August15_HO6-->