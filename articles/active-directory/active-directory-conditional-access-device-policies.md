<properties
	pageTitle="Office 365 服務的條件式存取裝置原則 | Microsoft Azure"
	description="以裝置為基礎的條件如何控制 Office 365 服務存取的詳細資訊。雖然資訊工作者 (IW) 想要在公司或學校從其個人裝置存取 Office 365 服務，例如 Exchange 和 SharePoint Online，但其 IT 系統管理員卻希望安全的存取。IT 管理員可以佈建條件式存取裝置原則來保護公司資源，同時允許相容裝置上的 IW 存取服務。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2015"
	ms.author="femila"/>
# Office 365 服務的條件式存取裝置原則

「條件式存取」一詞有許多與其相關聯的條件，例如經過多因素驗證的使用者、經過驗證的裝置、相容的裝置等等。本主題主要著重於以裝置為基礎的條件如何控制 Office 365 服務的存取。雖然資訊工作者 (IW) 想要在公司或學校從其個人裝置存取 Office 365 服務，例如 Exchange 和 SharePoint Online，但其 IT 系統管理員卻希望安全的存取。IT 管理員可以佈建條件式存取裝置原則來保護公司資源，同時允許相容裝置上的 IW 存取服務。從 Microsoft Intune 條件式存取入口網站可以設定 Office 365 的條件式存取原則。

Azure Active Directory 會強制執行條件式存取原則，以確保 Office 365 服務的存取安全。租用戶管理員可以建立條件式存取原則，以阻止不相容裝置上的使用者存取 O365 服務。使用者必須先符合公司的裝置原則，才可以獲得服務的存取權。或者，系統管理員也可以建立原則，要求使用者註冊其裝置以取得 O365 服務的存取權。原則可能會套用至組織的所有使用者，或受限於少數幾個目標群組並隨著時間增加以包含其他目標群組。

強制對使用者執行裝置原則的先決條件是使用 Azure Active Directory 裝置註冊服務註冊其裝置。您可以選擇啟用 Multi-Factor Authentication (MFA)，以便使用 Azure Active Directory 裝置註冊服務註冊裝置。建議 Azure Active Directory 裝置註冊服務使用 MFA。啟用 MFA 後，使用 Azure Active Directory 裝置註冊服務註冊其裝置的使用者會面臨第二個因素驗證挑戰。

##條件式存取原則如何運作？

當使用者要求從支援的裝置平台存取 O365 服務時，Azure Active Directory 會驗證使用者及使用者啟動要求的裝置；只有在使用者符合為服務設定的原則時，才會授與服務存取權。系統會提供補救指示給未註冊裝置的使用者，告知如何註冊及符合規定以存取公司 O365 服務。iOS 和 Android 裝置上的使用者必須使用公司入口網站應用程式來註冊其裝置。當使用者註冊自己的裝置時，此裝置會向 Azure Active Directory 登錄並進行註冊，以便裝置管理與符合法規。客戶必須使用 Azure Active Directory 裝置註冊服務搭配 Microsoft Intune，以啟用 Office 365 服務的行動裝置管理。若已強制執行裝置原則，裝置註冊則是使用者存取 Office 365 服務的先決條件。

當使用者成功註冊其裝置時，此裝置會成為受信任的。Azure Active Directory 提供單一登入來存取公司應用程式，並強制執行條件式存取原則，不僅在使用者第一次要求存取時授與服務存取權，而在使用者每次要求更新存取權時也會授與存取權。當登入認證變更、裝置遺失/遭竊，或在要求更新時不符合原則時，使用者將被拒絕存取服務。

## 部署考量︰
必須使用 Azure Active Directory 裝置註冊服務進行裝置註冊。

在內部部署驗證使用者時，需要有 Active Directory Federation Services (AD FS) (1.0 和更高版本)。當身分識別提供者不能進行 Multi-Factor Authentication 時，「加入工作場所」的 Multi-Factor Authentication 將會失敗。例如，AD FS 2.0 不能夠進行 Multi-Factor Authentication。租用戶管理員必須先確定內部部署 AD FS 能夠進行 MFA 並已啟用有效的 MFA 方法，才能在 Azure Active Directory 裝置註冊服務上啟用 MFA。例如，Windows Server 2012 R2 上的 AD FS 具有 MFA 功能。您也必須先在 AD FS 伺服器上啟用其他有效的驗證 (MFA) 方法，才能在 Azure Active Directory 裝置註冊服務上啟用 MFA。如需有關 AD FS 中支援的 MFA 方法詳細資訊，請參閱設定 AD FS 的其他驗證方法。

## 常見問題集 (FAQ)

問：不支援的裝置平台的預設排除原則為何？

答：在現階段，條件式存取原則會選擇性地強制執行於 iOS 和 Android 裝置上的使用者。其他裝置平台上的應用程式預設不受 iOS 和 Android 裝置的條件式存取原則影響。不過，租用戶管理員可選擇覆寫全域原則，不允許存取不支援的平台上的使用者。我們已規劃將條件式存取原則延伸到其他平台 (包括 Windows) 上的使用者。

問：Office 365 服務的條件式存取原則何時會延伸到以瀏覽器為基礎的應用程式 (例如 OWA、以瀏覽器為基礎的 SharePoint)？

答：在現階段，Office365 服務的條件式存取受限於裝置上的豐富應用程式。我們已規劃將條件式存取原則延伸到從瀏覽器存取服務的使用者。

<!---HONumber=August15_HO9-->