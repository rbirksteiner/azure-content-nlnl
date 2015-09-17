<properties
	pageTitle="Azure AD Connect 同步處理 - 實作密碼同步處理 | Microsoft Azure"
	description="提供您所需的資訊，以了解密碼同步處理的運作方式，及如何在您的環境中啟用此功能。"
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
	ms.date="08/24/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：實作密碼同步處理

藉由密碼同步處理，您可讓使用者使用與登入您的內部部署 Active Directory 時使用的相同密碼登入 Azure Active Directory。

本主題的目標是提供您所需的資訊，以了解密碼同步處理的運作方式，及如何在您的環境中啟用此功能。

## 什麼是密碼同步處理

密碼同步處理是 Azure Active Directory Connect 同步處理服務 (Azure AD Connect 同步處理) 的一項功能，可將使用者密碼從您的內部部署 Active Directory 同步至 Azure Active Directory (Azure AD)。此功能可讓您的使用者以登入您內部部署網路的相同密碼登入其 Azure Active Directory 服務 (如 Office 365、Microsoft Intune 和 CRM Online)。

> [AZURE.NOTE]如需針對 FIPS 和密碼同步處理設定的 Active Directory 網域服務的詳細資訊，請參閱＜在 FIPS 相容系統中密碼同步處理失敗＞。

## 密碼同步處理的可用性

任何 Azure Active Directory 的客戶都有權執行密碼同步處理。請參閱以下有關密碼同步處理相容性，及同盟驗證等其他功能的資訊。

## 密碼同步處理如何運作

密碼同步處理是 Azure AD Connect 同步處理實作的目錄同步作業功能的延伸。因此，這項功能需要設定您的內部部署與 Azure Active Directory 之間的目錄同步作業。

Active Directory 網域服務是以代表使用者實際密碼的雜湊值格式儲存密碼。密碼雜湊無法用來登入您的內部部署網路。它也被設計為可反轉以藉此取得使用者的純文字密碼。為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 擷取使用者的密碼。密碼在同步到 Azure Active Directory 驗證服務之前，會先套用額外的安全性處理。密碼同步處理程序的實際資料流程，就類似同步處理 DisplayName 或電子郵件地址等使用者資料。

相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。密碼是以每個使用者為基礎來同步，且通常會依照時間先後順序進行。當使用者的密碼從內部部署 AD 同步到雲端時，會覆寫現有的雲端密碼。

您第一次啟用密碼同步處理功能時，系統會執行初始同步處理，將所有範圍內使用者的密碼從內部部署 Active Directory 同步到 Azure Active Directory。您無法明確定義一組使用者來指定將他們的密碼同步到雲端。此後，當內部部署使用者變更密碼時，通常在幾分鐘內密碼同步處理功能就會偵測到變更的密碼並加以同步。密碼同步處理功能會自動重試失敗的使用者密碼同步。若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。如果使用者已登入雲端服務並變更內部部署密碼，則雲端服務工作階段將會繼續運作不受干擾。不過，一旦雲端服務要求使用者重新驗證，就需要提供新的密碼。此時，使用者必須提供新的密碼 – 也就是最近從內部部署 Active Directory 同步至雲端的密碼。

## 安全性考量

同步密碼的時候，純文字版本的使用者密碼不會向密碼同步處理功能及 Azure AD 或任何相關服務公開。

此外，內部部署 Active Directory 不需使用可反轉加密的格式儲存密碼。Active Directory 密碼雜湊的摘要會用於內部部署 AD 和 Azure Active Directory 之間的傳輸。密碼雜湊的摘要無法用來存取客戶的內部部署環境。

## 密碼原則考量

啟用密碼同步處理會影響兩種類型的密碼原則：

1. 密碼複雜性原則
2. 密碼到期原則

### 密碼複雜性原則

當您啟用密碼同步處理之後，內部部署 Active Directory 中設定的密碼複雜性原則，會覆寫任何可能針對已同步使用者在雲端定義的複雜性原則。這表示任何在客戶內部部署 Active Directory 環境中有效的密碼，都可用來存取 Azure AD 服務。


> [AZURE.NOTE]直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

### 密碼到期原則

如果使用者位於密碼同步處理範圍內，則雲端帳戶的密碼會設為「永不到期」。這表示使用者的密碼可能在內部部署環境中已到期，但他們仍可以繼續使用到期的密碼登入雲端服務。

雲端密碼會於下一次使用者在內部部署環境中變更密碼時更新。


## 覆寫已同步的密碼

系統管理員可以使用 Azure Active Directory PowerShell 手動重設使用者的密碼。

在此情況下，新的密碼會覆寫使用者已同步處理的密碼，且雲端中定義的所有密碼原則會都套用到新的密碼。

如果使用者再次變更內部部署密碼，則新的密碼會同步到雲端，並且會覆寫手動更新的密碼。


## 準備密碼同步處理

您的 Azure Active Directory 租用戶必須先啟用目錄同步作業，才能啟用密碼同步處理。


## 啟用密碼同步處理

您可以在執行 Azure AD Connect 設定精靈時啟用密碼同步處理。

在 [選用功能] 對話方塊頁面上，選取 [密碼同步處理]“****”。

![選用功能][1]


> [AZURE.NOTE]此程序會觸發完整的同步處理。完整同步處理循環通常需要比其他同步循環更長的時間來完成。


## 管理密碼同步處理

您可以透過執行 Azure AD Connect 的電腦的事件記錄檔，監視密碼同步處理的進度。


### 判斷密碼同步處理狀態

您可以檢閱符合以下條件的事件，來判斷哪些使用者已成功同步密碼：

| 來源| 事件識別碼 |
| --- | --- |
| 目錄同步處理| 656|
| 目錄同步處理| 657|

事件識別碼 656 的事件可提供處理密碼變更要求的報告：

![事件識別碼 656][2]

識別碼 657 的相對應事件則提供這些要求的結果：

![事件識別碼 657][3]

在事件中，受影響的物件是藉由其錨點和 DN 值識別。錨點值會與透過 Get-MsoUser Cmdlet 針對使用者傳回的 **ImmutableId** 值相對應。

除了物件識別碼之外，**事件識別碼 656** 也會提供在內部部署 Active Directory 中變更使用者密碼的日期：

![密碼變更要求][4]

「事件識別碼 657」除了來源物件識別碼外還有一個 [ 結果] 欄位，用來指示該使用者物件的同步處理狀態。

成功同步的密碼位於「事件識別碼 657」的事件中，並以 [結果] 屬性的 [成功] 值來表示。當密碼同步處理嘗試失敗時，[結果] 屬性的值為 [失敗]。

![密碼變更結果][5]

### 觸發所有密碼的完整同步處理
如果您變更了篩選器設定，則您必須觸發所有密碼的完整同步處理，以便目前在範圍中的使用者進行其密碼的同步處理。

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true


## 停用密碼同步處理

您可以重新執行 Azure AD Connect 設定精靈以停用密碼同步處理。當精靈顯示提示時，取消選取 [密碼同步處理] 核取方塊。


> [AZURE.NOTE]此程序會觸發完整的同步處理。完整同步處理循環通常需要比其他同步循環更長的時間來完成。

執行組態精靈之後，您的租用戶將不再同步密碼。新的密碼變更將不會同步到雲端。先前同步其密碼的使用者將可繼續使用這些密碼登入，直到他們在雲端手動變更其密碼為止。



## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=August15_HO9-->