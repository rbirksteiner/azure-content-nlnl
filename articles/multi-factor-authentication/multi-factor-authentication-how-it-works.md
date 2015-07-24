<properties 
	pageTitle="Azure Multi-Factor Authentication -它的作用" 
	description="Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

#Azure Multi-Factor Authentication 的作用

多因素驗證的安全性仰賴其分層方法。使用多重驗證因素會為攻擊者帶來相當程度的挑戰。即使攻擊者試圖打探使用者的密碼，在不持有信任裝置的情況下便沒有任何意義。如果使用者遺失裝置，拾獲該裝置的人仍然無法使用此裝置，除非他或她剛好知道使用者的密碼。

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證：

- 撥打電話 
- 簡訊
- 行動應用程式通知，讓使用者選擇偏好的方法
- 行動應用程式驗證碼
- 協力廠商 OATH 權杖

如需其作用的詳細資訊，請觀看以下影片。

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##適用於 Multi-Factor Authentication 的方法
當使用者登入時，系統會將額外的驗證傳送給使用者。以下是適用於這個第二次驗證的方法清單。

驗證方法 | 說明 
------------- | ------------- |
撥打電話 | 撥打使用者的智慧型手機，要求他們按 # 符號來驗證登入。如此將可完成驗證程序。這是可設定的選項，您可以變更為指定的代碼。
簡訊 | 傳送含 6 位數代碼的簡訊到使用者智慧型手機。輸入此代碼可完成驗證程序。
行動應用程式通知 | 將驗證要求傳送到使用者的智慧型手機，要求他們在行動應用程式中選取 [驗證] 來完成驗證。如果您將應用程式通知選為主要驗證方法，這種情況便會發生。如果它們在未登入時收到通知，他們可以選擇提報詐騙。
透過行動應用程式的驗證碼 | 將驗證碼傳送到在使用者智慧型手機中運作的行動應用程式。如果您將驗證碼選為主要驗證方法，這種情況便會發生。


##可用的 Azure Multi-Factor Authentication 版本
Azure Multi-Factor Authentication 備有三個不同的版本。下表是每種版本的詳述。

版本 | 說明 
------------- | ------------- |
Multi-Factor Authentication for Office 365 | 這個版本專門搭配 Office 365 應用程式運作，並且可從 Office 365 入口網站管理。系統管理員現在可以使用 Multi-Factor Authentication 來保護其 Office 365 資源的安全。此版本隨附於 Office 365 訂用帳戶。
適用於 Azure 系統管理員的 Multi-Factor Authentication | 適用於 Office 365 的 Multi-Factor Authentication 功能的相同子集將免費提供給所有 Azure 系統管理員。Azure 訂用帳戶的每個管理帳戶現可透過啟用此 Multi-Factor Authentication 核心功能來取得額外防護。因此想要存取 Azure 入口網站以建立 VM、網站、管理儲存體、行動服務或任何其他 Azure 服務的系統管理員，可在其系統管理員帳戶中新增多因素驗證。
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication 提供最豐富的功能。它能透過 Azure 管理入口網站、進階報告及支援一系列內部部署和雲端應用程式來提供其他組態選項。Azure Multi-Factor Authentication 隨附於 Azure Active Directory Premium 中。

##版本的功能比較
下表提供 Azure Multi-Factor Authentication 各版本中可用的功能清單。


功能 | Multi-Factor Authentication for Office 365 (隨附於 Office 365 SKU 中)|適用於 Azure 系統管理員的 Multi-Factor Authentication (隨附於 Azure 訂用帳戶中) | Azure Multi-Factor Authentication (隨附於 Azure AD Premium 和 Enterprise Mobility Suite 中) 
------------- | :-------------: |:-------------: |:-------------: |
系統管理員可以透過 MFA 保護帳戶| * | * (僅適用於 Azure 系統管理員帳戶)|*
以行動應用程式做為第二個因素|* | * | *
以撥打電話做為第二個因素|* | * | *
以 SMS 做為第二個因素|* | * | *
用戶端應用程式密碼不支援 MFA|* | * | *
系統管理員控制驗證方法| | | *
PIN 模式| | | *
詐騙警示| | | *
MFA 報告| | | *
一次性略過| | | *
通話的自訂問候語| | | *
自訂通話來電者 ID| | | *
事件確認| | | *
信任的 IP| | | *
暫停對已記住的裝置進行 MFA (公開預覽)| | | *
MFA SDK| | | *
對使用 MFA Server 的內部部署應用程式進行 MFA| | | *


##如何取得 Azure Multi-Factor Authentication

Azure Multi-Factor Authentication 隨附於 Azure Active Directory Premium 和 Enterprise Mobility Suite 中。如果您已經擁有上述產品，代表您也擁有 Azure Multi-Factor Authentication。

如果您是 Office 365 使用者或 Azure 訂閱者，並且想要充分利用 Azure Multi-Factor Authentication 提供的其他功能，請繼續閱讀。

如果您沒有任何上述任何產品，若要開始使用 Azure Multi-Factor Authentication，首先您需要 Azure 訂用帳戶或 [Azure 試用版訂用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

使用 Azure Multi-Factor Authentication 時有兩個可用的計費選項：

- **每位使用者**。通常適用於想要為一群定期需要驗證之固定數量員工啟用 Multi-Factor Authentication 的企業。
- **每次驗證**。通常適用於想要為大量不定期需要驗證之外部使用者啟用 Multi-Factor Authentication 的企業。

如需定價詳細資料，請參閱 [Azure MFA 定價](http://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

選擇最適合組織的模式。接下來，若要開始使用，請參閱[開始使用](multi-factor-authentication-get-started.md)



 

<!---HONumber=July15_HO2-->