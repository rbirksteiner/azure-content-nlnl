<properties
	pageTitle="檢視存取和使用情況報告"
	description="說明如何檢視存取和使用情況報告來深入了解貴組織之目錄完整性和安全性的主題。"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="kenhoff;Justinha"/>

# 檢視存取和使用情況報告

您可以使用 Azure Active Directory 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

在 Azure 管理入口網站中，報告會以下列方式分類：

- 異常報告 – 包含我們發現異常的登入事件。我們的目標在於使您注意這類活動，並讓您能夠判斷事件是否可疑。
- 整合式應用程式報告 – 可供深入了解雲端應用程式在組織中的使用方式。Azure Active Directory 提供與數千個雲端應用程式的整合。
- 錯誤報告 – 指出將帳戶佈建至外部應用程式時可能發生的錯誤。
- 使用者特定報告 – 顯示特定使用者的裝置/登入活動資料。
- 活動記錄檔 – 包含過去 24 小時、過去 7 天或過去 30 天內所有稽核事件的記錄，以及群組活動變更、密碼重設和登錄活動。

> [AZURE.NOTE]
>
- 某些進階的異常和資源使用情況報告僅適用於您啟用 [Azure Active Directory Premium](active-directory-get-started-premium.md) 時。進階報告可協助您改善存取安全性、回應潛在威脅，以及存取裝置存取與應用程式使用情況的分析資料。
- Azure Active Directory Premium 和 Basic 版本適用於使用 Azure Active Directory 全球執行個體的中國客戶。由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。如需詳細資訊，請透過 [Azure Active Directory 論壇](http://feedback.azure.com/forums/169401-azure-active-directory)與我們連絡。

## 報告

|	報告 |	說明 |
|	------												|	-----																						|
|	**異常活動報告**
|	[從不明來源登入](active-directory-reporting-sign-ins-from-unknown-sources.md) |	可能表示使用者嘗試在不被追蹤的情況下登入。 |
|	[在多次失敗後登入](active-directory-sign-ins-after-multiple-failures.md) |	可能表示暴力密碼破解攻擊成功。 |
|	[從多個地理區域登入](active-directory-sign-ins-from-multiple-geographies.md) |	可能表示多個使用者登入相同帳戶。 |
|	[從具有可疑活動的 IP 位址登入](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	可能表示使用者嘗試多次入侵後成功登入。 |
|	[從可能受感染的裝置登入](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	可能表示使用者嘗試用於登入的裝置可能已受到感染。 |
|	[異常的登入活動](active-directory-reporting-irregular-sign-in-activity.md) |	可能表示違背使用者平常登入習慣的事件。 |
|	[具有異常登入活動的使用者](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	指示帳戶可能已受到危害的使用者。 |
|	認證外洩的使用者 |	認證外洩的使用者 |
|	**活動記錄檔**
|	稽核報告 |	目錄中的已稽核事件 |
|	密碼重設活動 |	提供您組織中所執行之密碼重設的詳細檢視。 |
|	密碼重設註冊活動 |	提供您組織中所執行之密碼重設登錄的詳細檢視。 |
|	自助服務群組活動 |	提供活動記錄給您目錄中所有的群組自助活動 |
|	**整合式應用程式**
|	應用程式使用情況 |	針對與您目錄整合的所有 SaaS 應用程式提供使用摘要。 |
|	帳戶佈建活動 |	提供嘗試將帳戶佈建到外部應用程式的歷程記錄。 |
|	密碼變換狀態 |	提供 SaaS 應用程式自動密碼變換狀態的詳細概觀。 |
|	帳戶佈建錯誤 |	指示對使用者的外部應用程式存取造成的影響。 |
|	**版權管理**
|	RMS 使用量 |	提供 Rights Management 使用量的摘要 |
|	最活躍的 RMS 使用者 |	列出已存取受 RMS 保護之檔案的前 1000 名有效使用者 |
|	RMS 裝置使用量 |	列出存取受 RMS 保護的檔案所使用的裝置 |
|	啟用 RMS 的應用程式使用量 |	提供啟用 RMS 之應用程式的使用量 |

## 報告版本

|	報告 |	免費 |	基本 |	高級 |
|	------												|	----	|	-----	|	--------	|
|	**異常活動報告**
|	從不明來源登入 |	✓ |	✓ |	✓ |
|	在多次失敗後登入 |	✓ |	✓ |	✓ |
|	從多個地理區域登入 |	✓ |	✓ |	✓ |
|	從具有可疑活動的 IP 位址登入 | | |	✓ |
|	從可能受感染的裝置登入 | | |	✓ |
|	異常的登入活動 | | |	✓ |
|	具有異常登入活動的使用者 | | |	✓ |
|	認證外洩的使用者 | | |	✓ |
|	**活動記錄檔**
|	稽核報告 | | |	✓ |
|	密碼重設活動 | | |	✓ |
|	密碼重設註冊活動 | | |	✓ |
|	自助服務群組活動 | | |	✓ |
|	**整合式應用程式**
|	應用程式使用情況 | | |	✓ |
|	帳戶佈建活動 |	✓ |	✓ |	✓ |
|	密碼變換狀態 | | |	✓ |
|	帳戶佈建錯誤 |	✓ |	✓ |	✓ |
|	**版權管理**
|	RMS 使用量 | | |	僅 RMS |
|	最活躍的 RMS 使用者 | | |	僅 RMS |
|	RMS 裝置使用量 | | |	僅 RMS |
|	啟用 RMS 的應用程式使用量 | | |	僅 RMS |









## 異常活動報告
異常登入活動報告包含 Azure 管理入口網站、Azure AD 存取面板、Office365、Sharepoint Online、Dynamics CRM Online 和其他 Microsoft 線上服務 (無論是否為同盟提供者) 的可疑登入活動。<p>提供下列報告：</p><ul><li>[從不明來源登入](active-directory-reporting-sign-ins-from-unknown-sources.md)。</li> <li>[在多次失敗後登入](active-directory-reporting-sign-ins-after-multiple-failures.md)。</li> <li>[從多個地理區域登入](active-directory-reporting-sign-ins-from-multiple-geographies.md)。</li> <li>[從具有可疑活動的 IP 位址登入](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)。</li> <li>[異常的登入活動](active-directory-reporting-irregular-sign-in-activity.md)。</li> <li>[從可能受感染的裝置登入](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)。</li> <li>[具有異常登入活動的使用者](active-directory-reporting-users-with-anomalous-sign-in-activity.md)。</li> <li>認證外洩的使用者</li></ul>










## 活動記錄檔

### 稽核報告

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 顯示過去 24 小時、過去 7 天或過去 30 天內的所有稽核事件記錄。<br /> 如需詳細資訊，請參閱 [Azure Active Directory 稽核報告事件](active-directory-reporting-audit-events.md) | 目錄 > 報告索引標籤 |

![稽核報告](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### 密碼重設活動

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 顯示您的組織中發生的所有密碼重設嘗試 | 目錄 > 報告索引標籤 |

![密碼重設活動](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### 密碼重設註冊活動

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 顯示您的組織中發生的所有密碼重設登錄 | 目錄 > 報告索引標籤 |

![密碼重設註冊活動](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### 自助服務群組活動

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 顯示您的目錄中自助式管理群組的所有活動。 | 目錄 > 使用者 > <i>使用者</i> > 裝置索引標籤 |

![自助服務群組活動](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## 整合式應用程式報告

### 應用程式使用情況：摘要

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 使用這份報告可以查看您的目錄中所有 SaaS 應用程式的使用情況。這份報告是以使用者在 [存取面板] 中點選應用程式的次數為基礎。 | 目錄 > 報告索引標籤 |

![應用程式使用情況摘要](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### 應用程式使用情況：詳細

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 使用這份報告可以查看特定 SaaS 應用程式目前的使用量。這份報告是以使用者在 [存取面板] 中點選應用程式的次數為基礎。 | 目錄 > 報告索引標籤 |

### 應用程式儀表板

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 此報告指出在一段選取的時間間隔內，您組織中的使用者對應用程式進行的累計登入。儀表板頁面上的圖表可協助您識別該應用程式的所有使用趨勢。 | 目錄 > 應用程式 > 儀表板索引標籤 |

## 錯誤報告

### 帳戶佈建錯誤

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 使用這份報告來監控將帳戶從 SaaS 應用程式同步處理至 Azure Active Directory 期間發生的錯誤。 | 目錄 > 報告索引標籤 |

![帳戶佈建錯誤](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## 特定使用者報告

### 裝置

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 使用此報告可查看特定使用者用來存取 Azure Active Directory 的 IP 位址和裝置地理位置。 | 目錄 > 使用者 > <i>使用者</i> > 裝置索引標籤 |

### 活動

| 說明 | 報告位置 |
| :-------------     | :-------        |
| 顯示使用者的登入活動。此報告包含登入的應用程式、使用的裝置、IP 位址和位置等資訊。我們不會收集以 Microsoft 帳戶登入的使用者歷程記錄。 | 目錄 > 使用者 > <i>使用者</i> > 活動索引標籤 |

#### 使用者活動中包含的登入事件報告

只有某些類型的登入事件會出現在「使用者活動」報告中。

| 事件類型 | 已包含？ |
| ----------------------								| ---------		|
| 對[存取面板](http://myapps.microsoft.com/)進行的登入 | 是 |
| 對 [Azure 管理入口網站](https://manage.windowsazure.com/)進行的登入 | 是 |
| 對 [Microsoft Azure 入口網站](http://portal.azure.com/)進行的登入 | 是 |
| 對 [Office 365 入口網站](http://portal.office.com/)進行的登入 | 是 |
| 對原生應用程式進行的登入，例如 Outlook (請參閱底下的例外狀況) | 是 |
| 透過存取面板 (例如 Salesforce) 對同盟/佈建應用程式進行的登入 | 是 |
| 透過存取面板 (例如 Twitter) 對密碼型應用程式進行進行的登入 | 是 |
| 對已新增至目錄的自訂商務應用程式進行的登入 | 否 (敬請期待) |
| 登入已加入目錄的 Azure AD 應用程式 Proxy 應用程式 | 否 (敬請期待) |

> 注意：為了減少此報告中的雜訊量，不會顯示經由 [Microsoft Online Services 登入小幫手](http://community.office365.com/zh-TW/w/sso/534.aspx)對 [Lync/Skype for Business](http://products.office.com/zh-TW/skype-for-business/online-meetings) 原生應用程式進行的登入。










## 您懷疑有安全性缺口時的考慮事項

如果您懷疑使用者帳戶可能遭到入侵，或有任何可能導致雲端中您的目錄資料出現安全性缺口的可疑使用者活動，您可能要考慮下列其中一或多個動作：

- 連絡使用者來確認活動
- 重設使用者的密碼
- [啟用多因素驗證](http://go.microsoft.com/fwlink/?linkid=335774)以提供額外的安全性

## 檢視或下載報告

1. 在 Azure 管理入口網站中，按一下 [**Active Directory**]，按一下您組織的目錄名稱，後按一下 [**報告**]。
2. 在 [報告] 頁面上，按一下您要檢視和/或下載的報告。

    > [AZURE.NOTE]如果這是您第一次使用 Azure Active Directory 的報告功能，您會看到「選擇加入」的訊息。如果您同意，請按一下核取記號圖示繼續進行。

3. 按一下 [間隔] 旁邊的下拉式功能表，然後選取在產生此報告時所應使用的其中一個時間範圍：
    - 過去 24 小時
    - 過去 7 天
    - 過去 30 天
4. 按一下核取記號圖示來執行報告。
	- Azure 管理入口網站最多會顯示 1000 個事件。
5. 如果適用的話，按一下 [**下載**] 可將報告下載為逗號分隔值 (CSV) 格式的壓縮檔，以供離線檢視或封存。
	- 最多 75,000 個事件會包含在下載的檔案中。

## 忽略事件

如果正在檢視任何異常報告，您可能會注意到您可以忽略顯示在相關報告中的各種事件。若要忽略事件，只要將報告中的事件反白，然後按一下 [**忽略**] 即可。[**忽略**] 按鈕將會永久移除報告中反白顯示的事件，而且只能由獲得授權的全域管理員使用。

## 自動電子郵件通知

如需有關 Azure AD 的報告通知的詳細資訊，請參閱 [Azure Active Directory 報告通知](active-directory-reporting-notifications.md)。

## 後續步驟

- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)

<!---HONumber=August15_HO9-->