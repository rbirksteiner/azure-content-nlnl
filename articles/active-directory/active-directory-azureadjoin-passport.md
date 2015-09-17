<properties 
	pageTitle="透過初次執行體驗使用 Azure AD 設定新的裝置| Microsoft Azure" 
	description="說明使用者如何在初次執行體驗期間設定 Azure AD Join 的主題。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/02/2015" 
	ms.author="femila"/>

# 透過 Microsoft Passport 不需要密碼就能驗證身分識別

目前單獨驗證密碼的方法不足以保障使用者的安全。使用者會重複使用和忘記密碼。密碼是可破壞、可進行網路釣魚、易於破解且可猜測的。密碼也很難記住，而且易於遭受攻擊，例如「傳送雜湊」。

## 什麼是 Microsoft Passport
對於組織和消費者來說，Microsoft Passport 是超越密碼的新型私密金鑰/公開金鑰或憑證式驗證方法。這種形式的驗證依賴這些金鑰組認證，這類認證可以取代密碼且能抵禦漏洞、竊取及網路釣魚。Microsoft Passport 讓使用者能夠向 Microsoft 帳戶、Active Directory 帳戶、Microsoft Azure Active Directory (AD) 帳戶，或是支援線上快速識別碼 (FIDO) 驗證的非 Microsoft 服務進行驗證。在 Microsoft Passport 註冊期間進行最初的兩步驟驗證之後，就會在使用者的裝置上設定 Microsoft Passport，而該使用者需設定一個手勢，可能是 Windows Hello 或 PIN。使用者提供該手勢來驗證身分識別。Windows 接著會使用 Microsoft Passport 來驗證使用者，並協助他們存取受保護的資源和服務。

私密金鑰可以透過「使用者手勢」單獨使用，例如，PIN、生物識別技術、遠端裝置 (像是使用者用來登入裝置的智慧卡)，而此資訊會連結到憑證或非對稱式金鑰組。如果裝置具備信賴平台模組 (TPM) 晶片，這個私密金鑰就已通過硬體證明。私密金鑰永遠都不會離開裝置。

公開金鑰是使用 Azure Active Directory 和 Windows Server Active Directory (適用於內部部署) 進行登錄。身分識別提供者 (IDP) 會藉由將使用者的公開金鑰對應到私密金鑰來驗證該使用者，並透過單次密碼 (OTP)、PhoneFactor 或不同的通知機制來提供登入資訊。
## 為什麼企業應該採用 Microsoft Passport
藉由啟用 Microsoft Passport，企業可透過下列動作，使其資源更安全：

* 使用硬體慣用選項來設定 Microsoft Passport，這表示將在 TPM 1.2 或 TPM 2.0 可供使用時於其上產生金鑰，或者在 TPM 無法使用時透過軟體產生金鑰。 

* 定義 PIN 的複雜度和長度，以及是否要在組織中啟用 Hello 使用方式

* 設定 Microsoft Passport，使用憑證式信任來支援類似智慧卡的案例。

## 它的運作方式
1. 金鑰是在硬體上產生的。許多機器都會內建信賴平台模組 (TPM) 晶片，藉由將密碼編譯金鑰整合到裝置來保護硬體。TPM 1.2 或 TPM 2.0 可用來產生金鑰或憑證，將會在產生的金鑰範圍之外建立金鑰。

2. 這些硬體繫結的金鑰會通過 TPM 的證明，而企業可以

3. 單一解除鎖定手勢將解除裝置鎖定，而且如果裝置已加入網域或已加入 Azure AD，就允許這個手勢取得多個資源的存取權。

## Microsoft Passport 週期
Microsoft Passport 驗證週期![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)上圖說明私密與公開金鑰組，以及身分識別提供者所提供的驗證。以下詳細說明每個步驟：

1. 使用者透過多個內建校訂方法 (手勢、實體智慧卡、多重要素驗證) 證明其身分識別，並將此資訊傳送到類似 Azure Active Directory 或 Active Directory 的身分識別提供者 (IDP)。

2.  裝置接著會建立金鑰、證明金鑰、接受此金鑰的公開部分、為其附加站台聲明、登入並傳送至 IDP 以登錄此金鑰。

3. 一旦在 IDP 中登錄金鑰的公開部分之後，它就會要求裝置使用金鑰的私密部分進行簽署。IDP 接著會驗證並發出讓使用者能夠存取受保護資源的驗證權杖。

4. 一旦在 IDP 中登錄金鑰的公開部分之後，它就會要求裝置進行使用金鑰的私密部分進行簽署的挑戰。

5. IDP 接著會驗證並發出讓使用者和裝置能夠存取受保護資源的驗證權杖。IDP 可以撰寫跨平台的 App，或者透過 JS/Webcrypto API 使用瀏覽器支援，為其使用者建立和使用 Microsoft Passport 認證。

## 部署需求
在企業層級
---------------------------
* Azure 訂閱

在使用者層級
-------------------------------------------------------------
* 電腦必須執行 Windows 10 Professional 或 Enterprise SKU

## 其他資訊

* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->