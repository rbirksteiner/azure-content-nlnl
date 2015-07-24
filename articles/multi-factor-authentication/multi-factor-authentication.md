<properties 
	pageTitle="什麼是 Azure Multi-Factor Authentication？" 
	description="Azure Multi-Factor Authentication 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證您的身份的驗證方法。它可以為使用者登入和交易提供額外層級的安全性。" 
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

# 為連線過的項目提供額外的安全性

多因素驗證 (MFA) 是需要使用多種驗證方法，並在使用者登入和交易中新增重要的第二層安全性的驗證方法。其運作方式需要下列其中任何二或多個驗證方法：

- 您知道的某些資訊 (通常是密碼)
- 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
- 您身上的某些特徵 (生物識別技術)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>

## 什麼是 Azure Multi-Factor Authentication？

Azure Multi-Factor Authentication 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證您的身份的驗證方法。它可以為使用者登入和交易提供第二層安全性。

Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它可以透過一些簡單的驗證選項 (例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OATH 權杖) 來提供強大的驗證功能。

如需 Azure Multi-Factor Authentication 運作方式的概觀，請參閱以下影片。


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##為何使用 Azure Multi-Factor Authentication？

與以前比較起來，現在人們連線網路的時間越來越長。透過智慧型手機、平板電腦、膝上型電腦以及電腦，人們有幾種不同選擇可隨時用來連線網路並維持連線。人們可以從任何地方存取他們的帳戶與應用程式，這表示他們可以完成更多工作並為客戶提供更好的服務。

Azure Multi-Factor Authentication 是一個容易使用、可調整且可靠的解決方案，可提供第二種驗證方法讓您的使用者永遠受到保護。

![容易使用](./media/multi-factor-authentication/simple.png)| ![可調整](./media/multi-factor-authentication/scalable.png)| ![永遠受到保護](./media/multi-factor-authentication/protected.png)|![可靠](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**容易使用**|**可調整**|**永遠受到保護**|**可靠**

- **容易使用** - Azure Multi-Factor Authenticaton 很容易設定及使用。Azure Multi-Factor Authentication 提供的額外保護功能可讓使用者使用及管理他們自己的裝置，而且在許多情況下，只要簡單地按幾下就可以設定完成。
- **可調整** - Azure Multi-Factor Authenticaton 採用雲端技術且與您內部部署的 AD 與自訂應用程式整合。此保護功能甚至可以擴充以因應您高任務關鍵性的狀況。
- **永遠受到保護** - Azure Multi-Factor Authentication 使用最高工業標準提供強大驗證功能。
- **可靠** - 我們保證 Azure Multi-Factor Authentication 的可用性可達到 99.9%。當服務無法接收或處理多因素驗證的驗證要求時，服務會被視為無法使用。  

如需為何使用 Azure Multi-Factor Authentication 的其他資訊，請參閱以下影片。

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**其他資源**

* [適用於使用者](multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO1-->