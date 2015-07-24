<properties 
	pageTitle="什麼是 Azure MFA 中的應用程式密碼？" 
	description="本頁面將協助使用者了解什麼是應用程式密碼，以及它們在 Azure MFA 方面的用途。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# 什麼是 Azure Multi-Factor Authentication 中的應用程式密碼？

有些非瀏覽器應用程式 (例如使用 Exchange Active Sync 的 Apple 原生電子郵件用戶端) 目前不支援 Multi-Factor Authentication。Multi-Factor Authentication 會對每個使用者啟用。這表示如果使用者已啟用 Multi-Factor Authentication，並嘗試使用非瀏覽器應用程式，他們將無法這麼做。應用程式密碼允許發生此情形。

>[AZURE.NOTE]適用於 Office 2013 用戶端的新式驗證
>
> Office 2013 用戶端 (包括 Outlook) 現在支援新的驗證通訊協定，而且可以啟用 Multi-Factor Authentication 支援。這表示一旦啟用後，應用程式密碼就不需要使用於 Office 2013 用戶端。如需詳細資訊，請參閱[發表的 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。
 
## 如何使用應用程式密碼

以下是使用應用程式密碼的一些注意事項。

- 實際的密碼會自動產生，而不是由使用者提供。這是因為攻擊者比較難以猜中自動產生的密碼，因此比較安全。
- 每位使用者的密碼目前以 40 組為限。如果您嘗試在達到此限制後建立密碼，系統會提示您刪除其中一個現有的應用程式密碼，以便建立新密碼。
- 建議依照每個裝置 (而不是依照每個應用程式) 建立應用程式密碼。例如，您可以為膝上型電腦建立一個應用程式密碼，並將該應用程式密碼使用於該膝上型電腦上的所有應用程式。
- 您會在第一次登入時獲得一個應用程式密碼。如果您需要其他密碼，您可加以建立。
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

擁有應用程式密碼後，您可使用此密碼來取代這些非瀏覽器應用程式的原始密碼。所以舉例來說，如果您在電話上使用 Multi-Factor Authentication 和 Apple 原生電子郵件用戶端。使用應用程式密碼，以便略過 Multi-Factor Authentication 並繼續運作。

### 建立應用程式密碼
在初次登入期間，您會獲得可供使用的應用程式密碼。此外，您也可以稍後再建立應用程式密碼。您執行此動作的方式取決於您使用 Multi-Factor Authentication 的方式。選擇最適用您的方式。

如何使用多因素驗證|說明
:------------- | :------------- | 
[搭配 Office 365 使用](multi-factor-authentication-end-user-manage-o365.md)| 這表示您想要透過 Office 365 入口網站建立應用程式密碼。
[搭配 Microsoft Azure 使用](multi-factor-authentication-end-user-manage-azure.md)| 這表示您想要透過 Azure 入口網站建立應用程式密碼。
[我不知道](multi-factor-authentication-end-user-manage-myapps.md)|這表示您想要透過 [https://myapps.microsoft.com](https://myapps.microsoft.com) 建立應用程式密碼。




 

<!---HONumber=July15_HO2-->