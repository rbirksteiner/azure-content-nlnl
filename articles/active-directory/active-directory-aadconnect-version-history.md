<properties
   pageTitle="Azure AD Connect︰版本發行歷程記錄 | Microsoft Azure"
	description="本主題列出 Azure AD Connect 和 Azure AD Sync 的所有版本"
	services="active-directory"
	documentationCenter=""
	authors="AndKjell"
	manager="stevenpo"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/24/2015"
	ms.author="andkjell"/>

# Azure AD Connect︰版本發行歷程記錄

Azure Active Directory 團隊會定期以新的特性和功能更新 Azure AD Connect。並非所有新增項目都適用於所有的對象。

本文旨在協助您追蹤已發行的版本，以及了解您是否需要更新為最新版本。

## 1\.0.8667.0
發行日期：2015 年 8 月

**新功能︰**

- Azure AD Connect 安裝精靈現在已進行所有 Windows Server 語言的當地語系化。
- 新增在使用 Azure AD 密碼管理時的帳戶解除鎖定支援。

**已修正的問題：**

- 如果另一位使用者繼續安裝，而不是最先開始安裝的人，則 Azure AD Connect 安裝精靈會當機。
- 如果 Azure AD Connect 的先前解除安裝作業無法將 Azure AD Connect Sync 完全解除安裝，則不可能重新安裝。
- 如果使用者不在樹系的根網域中或已使用非英文版的 Active Directory，則無法使用 Express 安裝 Azure AD Connect。
- 如果無法解析 Active Directory 使用者帳戶的 FQDN，則會顯示「無法認可結構描述」的誤導錯誤訊息。
- 如果 Active Directory 連接器上使用的帳戶已在精靈外部變更，精靈將無法進行後續的執行。
- Azure AD Connect 有時無法在網域控制站上安裝。
- 如果已加入擴充屬性，則無法啟用和停用「預備模式」。
- 因為 Active Directory 連接器上的密碼不正確，所以有些設定的密碼回寫失敗。
- 如果 dn 用於屬性篩選，則無法升級 DirSync。

## 1\.0.8641.0
發行日期：2015 年 6 月

**Azure AD Connect 的最初發行版本。**

名稱從 Azure AD Sync 變更為 Azure AD Connect。

## 1\.0.494.0501
發行日期：2015 年 5 月

**新需求︰**

- Azure AD Sync 現在需要安裝 .Net framework 4.5.1 版。

**已修正的問題：**

- Azure AD 密碼回寫出現服務匯流排連接錯誤。

## 1\.0.491.0413
發行日期：2015年 4 月

**已修正的問題和改進︰**

- 如果已啟用資源回收筒且樹系中有多個網域，則 Active Directory 連接器不會正確處理刪除。
- Azure Active Directory 連接器的匯入作業效能已改善。
- 當群組已超過成員資格限制 (預設限制已設為 50k 個物件)，則會在 Azure Active Directory 中刪除此群組。新的行為是保留群組、擲回錯誤，而且不會匯出新的成員資格變更。
- 如果連接器空間已存在具有相同 DN 的分段刪除，則無法佈建新的物件。
- 在差異同步處理期間，儘管物件上沒有預備的變更，某些物件仍會被標示為同步處理中。
- 強制執行密碼同步也會移除慣用的 DC 清單。
- CSExportAnalyzer 有一些物件狀態的問題。

**新功能︰**

- 聯結現在可以連接到 MV 中的「任何」物件類型。

## 1\.0.485.0222
發行日期：2015 年 2 月

**改進：**

- 改進匯入效能。

**已修正的問題：**

- 密碼同步會接受屬性篩選所用的 cloudFiltered 屬性。經過篩選的物件不再於密碼同步處理的範圍中。
- 在拓撲有很多網域控制站的少數情況下，無法進行密碼同步處理。
- 在 Azure AD/Intune 中啟用裝置管理之後，從 Azure AD 連接器匯入時的「停止的伺服器」。
- 從相同樹系中的多個網域加入外部安全性主體 (FSP) 會造成模稜兩可的加入錯誤。

## 1\.0.475.1202
發行日期︰2014 年 12 月

**新功能︰**

- 現在支援透過以屬性為基礎的篩選進行密碼同步處理。如需詳細資訊，請參閱透過篩選進行密碼同步處理。
- msDS ExternalDirectoryObjectID 屬性會寫回至 AD。這可支援 Office 365 應用程式使用 OAuth2 存取混合式 Exchange 部署中的線上和內部部署信箱。

**已修正的升級問題︰**

- 在伺服器上可使用較新版的登入小幫手。
- 自訂安裝路徑用來安裝 Azure AD Sync。
- 無效的自訂加入準則會封鎖升級。

**其他修正︰**

- 已修正適用於 Office Pro Plus 的範本。
- 已修正以破折號開頭的使用者名稱所造成的安裝問題。
- 已修正第二次執行安裝精靈時遺失 sourceAnchor 設定的問題。
- 已修正密碼同步處理的 ETW 追蹤

## 1\.0.470.1023
發行日期：2014 年 10 月

**新功能︰**

- 從多個內部部署 AD 至 Azure AD 的密碼同步處理。
- 所有 Windows Server 語言的當地語系化安裝 UI。

**從 AADSync 1.0 GA 升級**

如果您已安裝 Azure AD Sync，您必須進行一個額外步驟，以免您已變更任何現成可用的同步規則。在升級至 1.0.470.1023 版本之後，您已修改的同步規則會重複。針對每個已修改的同步規則，執行下列作業︰

- 找出您已修改的同步規則並記下變更。
- 刪除同步規則。
- 找出 Azure AD Sync 建立的新同步規則並重新套用變更。

**AD 帳戶的權限**

AD 帳戶必須獲得其他權限，才能讀取來自 AD 的密碼雜湊。要授與的權限名為「複寫目錄變更」和「複寫所有目錄變更」。需要有這兩個權限才能讀取密碼雜湊

## 1\.0.419.0911
發行日期：2014 年 9 月

**Azure AD Sync 的最初發行版本。**

## 其他資源
[Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)

[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO9-->