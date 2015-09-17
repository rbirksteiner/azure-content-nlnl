<properties
   pageTitle="Azure AD 報告：開始使用"
   description="Azure AD 報告：開始使用"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2015"
   ms.author="curtand;kenhoff"/>

# 開始使用 Azure AD 報告

## 內容

Azure Active Directory 包括您的目錄的安全性、活動和稽核報告。以下是包含的報告清單：

### 安全性報告

- 從不明來源登入
- 在多次失敗後登入
- 從多個地理區域登入
- 從具有可疑活動的 IP 位址登入
- 異常的登入活動
- 從可能受感染的裝置登入
- 具有異常登入活動的使用者

### 活動報告

- 應用程式使用情況：摘要
- 應用程式使用情況：詳細
- 應用程式儀表板
- 帳戶佈建錯誤
- 個別使用者裝置
- 個別使用者活動
- 群組活動報告
- 密碼重設登錄活動報告
- 密碼重設活動

### 稽核報告

- 目錄稽核報告

> [AZURE.TIP]如需有關 Azure AD 報告的更多文件，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。



## 運作方式


### 報告管線

報告管線包含三個主要步驟。每次使用者登入或進行驗證時，就會發生以下狀況：

- 首先，使用者會經過驗證 (成功或失敗)，結果會儲存在 Azure Active Directory 服務資料庫。
- 每隔一段固定時間，就會處理所有最近的登入。此時，我們的安全性和異常活動演算法會搜尋所有最近的登入找出是否有可疑的活動。
- 處理之後，就會寫入報告、快取報告，然後在 Azure 管理入口網站中提供報告。

### 報告產生時間

由於 Azure AD 平台需處理大量的驗證和登入，所處理的最近登入平均而言為過去一小時。在罕見的情況下，可能需要花費多達 8 小時處理最近的登入。

您可以查看每個報表頂端的說明文字，找到最近處理的登入。

![每個報告頂端的說明文字](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP]如需有關 Azure AD 報告的更多文件，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。



## 開始使用


### 登入 Azure 管理入口網站

首先，您必須以全域或相容性管理員身分登入 [Azure 管理入口網站](https://manage.windowsazure.com)。您也必須是 Azure 訂用帳戶服務管理員或共同管理員，或使用「存取 Azure AD」的 Azure 訂用帳戶。

### 瀏覽至報告

若要檢視報告，請瀏覽至目錄頂端的 [報告] 索引標籤。

如果這是您第一次檢視報告，您必須先同意對話方塊，才能檢視報告。這是為了確保在您的組織中可接受讓管理員檢視這項資料，在某些國家/地區，這些資料可能會被視為隱私資訊。

![對話方塊](./media/active-directory-reporting-getting-started/dialogBox.png)

### 瀏覽每個報告

瀏覽每個報告以查看收集的資料，以及處理的登入。您可以[在此找到所有報告的清單](active-directory-reporting-what-it-is.md)。

![所有報告](./media/active-directory-reporting-getting-started/reportsMain.png)

### 下載報告為 CSV

每個報告可以下載為 CSV (逗號分隔值) 檔案。您可以在 Excel、PowerBI 或協力廠商分析程式中使用這些檔案，進一步分析您的資料。

若要下載任何報告為 CSV，請瀏覽至報告並按一下底部的 [下載]。

![[下載] 按鈕](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP]如需有關 Azure AD 報告的更多文件，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。





## 後續步驟

### 自訂異常登入活動的警示

瀏覽至您目錄的 [設定] 索引標籤。

捲動到 [通知] 區段。

啟用或停用 [惡意登入的電子郵件通知] 區段。

![[通知] 區段](./media/active-directory-reporting-getting-started/notificationsSection.png)

### 與 Azure AD 報告 API 整合

請參閱[開始使用報告 API](active-directory-reporting-api-getting-started.md)。

### 對使用者採取 Multi-Factor Authentication

在報告中選取使用者。

按一下畫面底部的 [啟用 MFA] 按鈕。

![畫面底部的 [Multi-Factor Authentication] 按鈕](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP]如需有關 Azure AD 報告的更多文件，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。




## 詳細資訊


### 稽核事件

如需了解目錄中的哪些事件會進行稽核，請參閱 [Azure Active Directory 報告稽核事件](active-directory-reporting-audit-events.md)。

### API 整合

請參閱[開始使用報告 API](active-directory-reporting-api-getting-started.md) 和 [API 參考文件](https://msdn.microsoft.com/library/azure/mt126081.aspx)。

### 取得聯繫

如有任何意見回饋、需要說明，或有任何問題，請寄送電子郵件給 [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com)。

> [AZURE.TIP]如需有關 Azure AD 報告的更多文件，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。

<!---HONumber=August15_HO6-->