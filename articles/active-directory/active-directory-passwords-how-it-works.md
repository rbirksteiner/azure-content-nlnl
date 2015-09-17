<properties 
	pageTitle="它的運作方式：Azure AD 密碼管理 | Microsoft Azure" 
	description="了解 Azure AD 密碼管理的不同元件，包括使用者註冊、重設及變更其密碼的位置，以及系統管理員設定、報告及啟用內部部署 Active Directory 密碼管理的位置。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# 密碼管理如何運作
Azure Active Directory 中的密碼管理是由以下所述的數個邏輯元件所組成。按一下每個連結以深入了解該元件。

- [**密碼管理組態入口網站**](#password-management-configuration-portal) – 系統管理員可以控制如何在其租用戶中管理密碼的方式的不同面向，方法瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)中其目錄的 [設定] 索引標籤。
- [**使用者註冊入口網站**](#user-registration-portal) – 使用者可以透過此 Web 入口網站自行註冊密碼重設。
- [**使用者密碼重設入口網站**](#user-password-reset-portal) – 使用者可以根據系統管理員控制密碼重設原則，使用數個不同的挑戰來重設自己的密碼。
- [**使用者密碼變更入口網站**](#user-password-change-portal) – 使用者可以藉由使用此 Web 入口網站，輸入舊密碼並選取新的密碼，隨時變更自己的密碼
- [**密碼管理報告**](#password-management-reports) – 系統管理員可以檢視及分析其租用戶中的密碼重設和註冊活動，方法是瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)中他們的目錄的 [報表] 索引標籤中的 [活動報告] 區段
- [**Azure AD Connect 的密碼回寫元件**](#password-writeback-component-of-azure-ad-connect) – 安裝 Azure AD Connect 以從雲端啟用同盟或密碼同步處理使用者密碼管理時，系統管理員可以選擇性地啟用密碼回寫功能。

## 密碼管理組態入口網站
您可以設定特定目錄的密碼管理原則，方法是使用 [Azure 管理入口網站](https://manage.windowsazure.com)，瀏覽至目錄的 [**設定**] 索引標籤中的 [**使用者密碼重設原則**] 區段。您可以從此組態頁面控制如何在您的組織中管理密碼的許多層面，包括：

- 啟用和停用目錄中所有使用者的密碼重設
- 設定使用者必須通過才能重設其密碼的挑戰數目 (一個或兩個)
- 從以下選項中設定您想要對組織中的使用者啟用的特定類型挑戰：
 - 行動電話 (透過文字或語音通話的驗證碼)
 - 辦公室電話 (語音通話)
 - 備用電子郵件 (透過電子郵件的驗證碼)
 - 安全性問題 (以知識為基礎的驗證)
- 設定使用者必須註冊才能使用安全性問題驗證方法的問題數目 (只有在已啟用安全性問題時才會看見)
- 設定使用者在重設期間必須提供才能使用安全性問題驗證方法的問題數目 (只有在已啟用安全性問題時才會看見)
- 定義使用者可以選擇註冊以使用安全性問題驗證方法的自訂安全性問題 (只有在已啟用安全性問題時才會看見)
- 要求使用者在前往位於 [http://myapps.microsoft.com](http://myapps.microsoft.com) 的應用程式存取面板時，註冊密碼重設。
- 要求使用者在經過可設定的天數之後重新確認先前已註冊的資料 (只有在已啟用強制執行註冊時才會看見)
- 提供自訂技術支援中心電子郵件或 URL，在使用者遇到重設密碼的問題時向其顯示
- 啟用或停用密碼回寫功能 (當使用 AAD Connect 部署密碼回寫時)
- 檢視密碼回寫代理程式的狀態 (當使用 AAD Connect 部署密碼回寫時)
- 當使用者自己的密碼已重設時，對使用者啟用電子郵件通知 (位於 [Azure 管理入口網站](https://manage.windowsazure.com)的 [**通知**] 區段)
- 當其他系統管理員重設自己的密碼時，對使用者啟用電子郵件通知 (位於 [Azure 管理入口網站](https://manage.windowsazure.com)的 [**通知**] 區段)
- 以您的組織的標誌和名稱設定使用者密碼重設入口網站和密碼重設電子郵件的商標，方法是使用租用戶商標自訂功能 (位於 [Azure 管理入口網站](https://manage.windowsazure.com)的 [**目錄內容**] 區段

若要深入了解在您的組織中設定密碼管理，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##使用者註冊入口網站
使用者可以使用密碼重設之前，必須使用正確的驗證資料更新其雲端使用者帳戶，以確保他們可以通過由他們的系統管理員定義的適當數目密碼重設挑戰。系統管理員也可以代表使用者定義此驗證資訊，方法是使用 Azure 或 Office Web 入口網站、DirSync / Azure AD Connect 或 Windows PowerShell。

不過，如果您想讓使用者註冊自己的資料，我們也提供網頁讓使用者可以前往並提供這項資訊。此頁面可讓使用者根據其組織中啟用的密碼重設原則，指定驗證資訊。一旦此資料經過驗證之後，它會儲存在其雲端使用者帳戶中，於稍後用於帳戶復原。以下是註冊入口網站的外觀：

  ![][001]

如需詳細資訊，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)和[最佳作法：Azure AD 密碼管理](active-directory-passwords-best-practices.md)。

##使用者密碼重設入口網站
一旦您啟用自助式密碼重設、設定組織的自助式密碼重設原則，並確保您的使用者在目錄中有適當的連絡資料，您的組織中的使用者將能夠自動從使用工作或學校帳戶進行登入 (例如 [portal.microsoftonline.com](https://portal.microsoftonline.com)) 的任何網頁重設自己的密碼。在這類頁面中，使用者會看到「**無法存取您的帳戶嗎？**」連結。

  ![][002]

按一下此連結會啟動自助式密碼重設入口網站。

  ![][003]

若要深入了解使用者可以如何重設自己的密碼，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##使用者密碼變更入口網站
如果使用者想要變更自己的密碼，也可以隨時使用密碼變更入口網站來完成。使用者可以透過存取面板設定檔頁面，或按一下 Office 365 應用程式中的 [變更密碼] 連結，來存取密碼變更入口網站。在其密碼過期的案例中，使用者也會在登入時被系統要求自動變更它們。

  ![][004]

在這兩種情況下，如果已啟用密碼回寫，且使用者為同盟或密碼同步處理，則這些變更的密碼會回寫至您的內部部署 Active Directory。以下是密碼變更入口網站的外觀：

  ![][005]

若要深入了解使用者可以如何變更自己的內部部署 Active Directory 密碼，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##密碼管理報告
瀏覽至 [**報告**] 索引標籤，並且在 [**活動記錄檔**] 區段底下尋找，您會看到兩個密碼管理報告：[**密碼重設活動**] 和 [**密碼重設註冊活動**]。您可以使用這兩份報告，取得使用者在您的組織中註冊和使用密碼重設的檢視。以下是這些報告在 [Azure 管理入口網站](https://manage.windowsazure.com)中的外觀：

  ![][006]

如需詳細資訊，請參閱[取得深入了解：Azure AD 密碼管理報告](active-directory-passwords-get-insights.md)。

##Azure AD Connect 的密碼回寫元件
如果您的組織中的使用者密碼源自於您的內部部署環境 (透過同盟或密碼同步處理)，您可以安裝最新版的 Azure AD Connect，以便直接從雲端更新這些密碼。這表示當使用者忘記或想要修改其 AD 密碼時，它們可以直接從 Web 進行。以下是 Azure AD Connect 安裝精靈中，密碼回寫的位置：

  ![][007]

如需有關 Azure AD Connect 的詳細資訊，請參閱[開始使用：Azure AD Connect](active-directory-aadconnect.md)。如需有關密碼回寫的詳細資訊，請參閱[開始使用：Azure AD 密碼管理](active-directory-passwords-getting-started.md)。


<br/> <br/> <br/>

**其他資源**


* [密碼管理是什麼](active-directory-passwords.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
 

<!---HONumber=August15_HO6-->