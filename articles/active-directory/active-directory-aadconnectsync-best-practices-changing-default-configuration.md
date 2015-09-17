<properties
	pageTitle="變更預設組態的最佳作法 | Microsoft Azure"
	description="提供變更 Azure AD Connect 同步處理預設組態的最佳作法。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：變更預設組態的最佳作法

本主題的目的旨在說明支援及不支援的 Azure AD Connect 同步處理變更。

Azure AD Connect 所建立的組態適用於大部分同步內部部署 Active Directory 與 Azure AD 的「現狀」環境。不過，在某些情況下，組態必須套用某些變更以滿足特定需要或需求。

## 服務帳戶的變更
Azure AD Connect 同步處理會使用安裝精靈所建立的服務帳戶執行。這個服務帳戶會存放同步處理所使用的資料庫加密金鑰。它是使用 127 個字元長的密碼所建立的，而且密碼已設定為永不到期。

- 它**不支援**變更或重設服務帳戶的密碼。這樣做將會損毀加密金鑰，服務將無法存取資料庫且無法啟動。

## 排程器的變更
Azure AD Connect 同步處理已設定為每 3 小時同步處理身分識別資料。安裝期間會建立一個使用服務帳戶執行的排定工作，此帳戶會具備操作同步處理伺服器的權限。

- 它**不支援**對排定工作進行變更。不知道服務帳戶的密碼。請參閱[服務帳戶的變更](#changes-to-the-service-account)
- 它**不支援**比預設值 3 小時更頻繁的同步處理。

## 同步處理規則的變更

您雖然可以變更 Azure AD Connect 同步處理組態，但是請您務必小心，因為 Azure AD Connect 同步處理應該要越接近設備越好。

以下是預期的行為清單：

- 將 Azure AD Connect 升級到較新版本之後，大部分的設定將會重設回預設值。
- 套用升級之後，「非預設」同步處理規則的變更都會遺失。
- 在升級至較新版本期間，會重建已刪除的「非預設」同步處理規則。
- 套用較新版本的升級時，您已建立的自訂同步處理規則會保持不變。



需要變更預設組態時，請執行下列動作：

- 需要修改「非預設」同步處理規則的屬性流程時，請不要進行變更。而是，建立具有較高優先順序 (較低數值) 且包含必要屬性流程的新同步處理規則。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。
- 如果您需要變更「預設」同步處理規則中的範圍或加入設定，請記錄該項目，並在升級至較新版本的 Azure AD Sync 之後重新套用變更。



## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=August15_HO9-->