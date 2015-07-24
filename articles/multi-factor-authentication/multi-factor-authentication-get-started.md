<properties 
	pageTitle="Azure Multi-Factor Authentication - 開始使用" 
	description="藉由提出我要保護什麼和使用者所在位置等問題，選擇最合適的 Multi-Factor Authentication 安全性解決方案。然後選擇雲端、MFA Server 或 AD FS。" 
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

#選擇合適的多重因素安全性解決方案

因為 Azure Multi-Factor Authentication 的種類繁多，我們必須從幾個重點來判斷，以便找出最合適的版本。這些重點包括：

-	[我要保護什麼](#what-am-i-trying-to-secure)
-	[使用者位於何處](#where-are-the-users-located)

下列小節將提供如何判斷以上重點的指引。

## 我要保護什麼？

為了判斷出正確的 Multi-Factor Authentication 解決方案，首先我們必須回答一個問題：您試圖使用第二個驗證方法來保護什麼？它是 Azure 中的應用程式嗎？ 或者是遠端存取系統之類的系統。藉由判斷我們嘗試保護的東西，我們可以找出該在何處啟用 Multi-Factor Authentication 這個問題的答案。



您想要保護什麼| 雲端中的 Multi-Factor Authentication|Multi-Factor Authentication Server 
------------- | :-------------: | :-------------: |
第一方 Microsoft 應用程式|* |* |
應用程式資源庫中的 Saas 應用程式|* |* |
透過 Azure AD App Proxy 發佈的 IIS 應用程式|* |* |
非透過 Azure AD App Proxy 發佈的 IIS 應用程式 | |* |
VPN、RDG 等遠端存取| |* |



## 使用者位於何處

接下來，不論是雲端中的 Multi-Factor Authentication 或使用 MFA Server 的內部部署 Multi-Factor Authentication，只要根據使用者的所在位置，我們就可以判斷出合適的解決方案。



使用者位置| 方案
------------- | :------------- | 
Azure Active Directory| 雲端中的 Multi-Factor Authentication|
Azure AD 和使用 AD FS 同盟的內部部署 AD| 雲端中的 MFA 和 MFA Server 都是可用選項 
Azure AD 和使用 DirSync、Azure AD Sync、Azure AD Connect 的內部部署 AD - 而沒有密碼同步|雲端中的 MFA 和 MFA Server 都是可用選項 
Azure AD 和使用 DirSync、Azure AD Sync、Azure AD Connect 的內部部署 AD - 使用密碼同步|雲端中的 Multi-Factor Authentication
內部部署 Active Directory|Multi-Factor Authentication Server

下表是雲端中 Multi-Factor Authentication 和 Multi-factor Authentication Server 的功能比較。

 | 雲端中的 Multi-Factor Authentication | Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
以行動應用程式通知做為第二個因素 | ● | ● |
以行動應用程式驗證碼做為第二個因素 | ● | ●
以撥打電話做為第二個因素 | ● | ● 
以單向 SMS 做為第二個因素 | ● | ●
以雙向 SMS 做為第二個因素 | | ● 
以硬體權杖做為第二個因素 | | ● 
用戶端應用程式密碼不支援 MFA | ● |  
系統管理員控制驗證方法 | | ● 
PIN 模式 | | ●
詐騙警示 | ● | ●
MFA 報告 | ● | ● 
一次性略過 | ● | ● 
通話的自訂問候語 | ● | ● 
可自訂的通話來電者 ID | ● | ● 
信任的 IP | ● | ● 
暫停對已記住的裝置進行 MFA (公開預覽) | ● |  
條件式存取 | ● | ● 
快取 | ● | ● 

既然我們已判斷出要使用雲端 Multi-Factor Authentication 或內部部署 MFA Server，接下來可以開始設定及使用 Azure Multi-Factor Authentication。**選取代表您案例的圖示！**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

**其他資源**

* [適用於使用者](multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO2-->