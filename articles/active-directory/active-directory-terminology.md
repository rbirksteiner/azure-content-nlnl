<properties
	pageTitle="Azure AD 詞彙 | Microsoft Azure"
	description="與 Azure Active Directory 相關的詞彙和定義。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="TerryLan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="curtand"/>

# Azure AD 術語

Microsoft Azure Active Directory (Azure AD) 有一組與雲端、混合和內部部署案例相關的獨特術語。下表定義了一些詞彙，可讓您對其使用方式有基本的認識。

 詞彙 | 定義
------------- | -------------
其他安全性驗證 | 全域管理員可以在組織目錄中的使用者帳戶上設定以要求使用者的密碼及其電話回應的安全性設定，必須用來向 Azure Active Directory 驗證系統驗證身分識別。
Azure Active Directory | Azure 中可透過 REST API 提供身分識別管理及存取控制功能的身分識別服務。
Azure Active Directory 存取控制 | 可為 REST Web 服務提供同盟驗證和規則驅動、宣告型授權的 Azure 服務。
Azure Active Directory 驗證系統 | 雲端中用來驗證和授權公司或學校帳戶的 Microsoft 身分識別服務。
Azure Active Directory 圖形 | 用以存取社交企業圖中使用者、群組和角色物件的 Azure Active Directory 功能，可輕鬆揭露使用者資訊和關聯性。
適用於 Windows PowerShell 的 Azure Active Directory 模組 | 一組用來管理 Azure Active Directory 的 Cmdlet。您可以使用這些 Cmdlet 來管理使用者、群組、網域、雲端服務訂閱、授權、目錄同步作業、單一登入等等。
Azure Active Directory Connect | Azure Active Directory Connect 精靈是單一工具，其引導式體驗可將您的內部部署目錄與 Azure Active Directory 連線。此精靈會部署及設定讓您的目錄整合啟動並執行所需的所有元件，包括同步服務、密碼同步或 Active Directory Federation Services (AD FS) 和必要元件，例如 Azure AD PowerShell 模組。
Azure Active Directory 同步作業工具 | 此應用程式可提供從公司的內部部署 Active Directory 服務到 Azure Active Directory 的目錄物件單向同步處理。
目錄整合 | 您可以設定的 Azure Active Directory 功能，用以改善與您在內部部署目錄和雲端目錄中維護身分識別相關聯的管理經驗。目錄整合案例包括目錄同步作業，以及採用單一登入的目錄同步作業。
目錄同步作業 | 用來將內部部署目錄物件 (使用者、群組、連絡人) 同步至雲端，協助降低管理負擔。目錄同步作業在 Azure AD 入口網站和 Azure 管理入口網站中也稱為目錄同步。一旦設定目錄同步作業，系統管理員即可將 Active Directory 中內部部署的目錄物件佈建至 Azure AD 執行個體。
Microsoft Online Services 登入小幫手 | 登入小幫手是安裝在用戶端電腦上的應用程式，可讓使用者在該電腦上登入一次，然後在登入階段期間存取服務無數次。若沒有登入小幫手，使用者必須在每次嘗試存取服務時提供名稱和密碼。登入小幫手不應與單一登入混淆，後者是 Azure Active directory 的目錄整合功能，加以部署即可利用使用者的現有內部部署公司認證來順暢地存取 Microsoft 雲端服務。
多因素驗證 (也稱為雙因素驗證或 2FA) | 多重要素驗證會為使用者登入和交易加入重要的第二層安全性。當您在 Azure AD 中啟用使用者帳戶的多因素驗證時，該使用者必須接著使用其電話 (除了標準密碼認證以外) 作為每次需要登入及使用任何貴組織訂閱的 Microsoft 雲端服務時的額外安全性驗證方法。
單一登入 | 用來為已登入公司網路的使用者提供存取 Microsoft 雲端服務時更順暢的驗證體驗。若要設定單一登入，組織必須在內部部署環境中部署安全性權杖服務。一旦設定單一登入，使用者就可以使用其 Active Directory 公司認證 (使用者名稱和密碼) 來存取雲端中的服務與其現有的內部部署資源。
使用者識別碼 | 使用者識別碼會使用者在 [登入] 頁面上提供的唯一識別碼，用以存取您的組織已訂閱的 Microsoft 雲端服務。
公司帳戶或學校帳戶 | 由組織 (公司、學校、非營利組織) 指派給其中一個成員 (員工、學生、客戶) 的使用者帳戶可供登入一或多個組織的 Microsoft 雲端服務訂閱，例如 Office 365 或 Azure。這些帳戶會儲存在組織的 Azure AD 目錄中，而且通常會在使用者離開組織時刪除。公司或學校帳戶與 Microsoft 帳戶不同，這類帳戶是由組織的系統管理員 (而非由使用者) 所建立及管理。

## 後續步驟
- [以組織方式註冊 Azure](sign-up-organization.md)
- [Azure 訂用帳戶如何與 Azure AD 產生關聯](active-directory-how-subscriptions-associated-directory.md)
- [Azure AD 服務限制](active-directory-service-limits-restrictions.md)

<!---HONumber=August15_HO6-->