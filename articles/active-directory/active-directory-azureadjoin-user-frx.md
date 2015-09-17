<properties 
	pageTitle="設定期間使用 Azure AD 設定新裝置 | Microsoft Azure" 
	description="此主題說明使用者如何在初次執行體驗期間設定 Azure AD Join。" 
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

# 設定期間使用 Azure AD 設定新裝置

在 Windows 10 中，使用者可以在初次執行體驗 (FRX) 中將其裝置加入至 Azure AD。這可讓組織將包含拆封授權的裝置散發給他們的員工或學生，或讓他們選擇自己的裝置 (CYOD)。如果您安裝 Windows 10 的 Professional 或 Enterprise SKU，體驗會預設為公司擁有裝置的安裝。

將裝置加入至 Azure AD
-----------------------------------------------------------------------

1. 在**準備就緒**階段之後，您將會看到安裝精靈。
2. 透過自訂其區域和語言，並接受 EULA 並連線網路來啟動。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. 選取要連線至網際網路的網路。
4. 選取適用於個人裝置或公司擁有裝置的選項：
5. 按一下 [**這個裝置屬於我的組織**]。如此會啟動 Azure AD Join 體驗。以下是您會在 Windows 10 Professional SKU 中看到的畫面。 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	輸入組織提供給您的認證。
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	一旦您輸入您的使用者名稱，相符的租用戶會位於 Azure AD。如果您位於同盟網域，系統會將您重新導向至內部部署安全權杖服務 (STS) 伺服器 (例如 AD FS)。如果您是位於非同盟網域中的使用者，您必須直接在 Azure AD 主控的頁面上輸入您的認證。您也會看到組織的標誌，且如果已設定 [公司商標] 則支援顯示文字。
8.	接著系統會提示您 Multi-Factor Authentication 挑戰。這可由 IT 人員進行設定。
9.	接著 Azure AD 會檢查這個使用者/裝置是否需要行動裝置管理 (MDM) 註冊。 
10.	接著 Windows 會在 Azure AD 的組織目錄中登錄裝置並在 MDM 中進行註冊。
11.	完成此動作後，如果您是受管理的使用者，Windows 會總結安裝程序並透過自動登入來引導使用者。
12.	如果您是同盟的使用者，您將會進入 Windows 登入畫面且必須輸入您的認證才能進行登入。

> [AZURE.NOTE]不支援在 Windows 全新體驗中加入內部部署 Active Directory 網域。因此，如果您打算將電腦加入網域，您應該選取連結「改為使用本機帳戶設定 Windows」。接著您可以從 [電腦設定] 加入網域，如同之前已完成的步驟。

## 其他資訊
* [了解適用於 Azure AD Join 的使用方式情節](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->