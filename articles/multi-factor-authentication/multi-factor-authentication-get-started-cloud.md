<properties 
	pageTitle="開始在雲端中使用 Azure Multi-Factor Authentication" 
	description="這是說明如何開始在雲端中使用 Azure MFA 的 Azure Multi-Factor Authentication 頁面。" 
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

# 開始在雲端中使用 Azure Multi-Factor Authentication



<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/cloud2.png)</center>

既然我們已經決定要在雲端中使用 Multi-Factor Authentication，現在就讓我們開始吧！ 請注意，如果您使用 Multi-Factor Authentication for Office 365 或適用於 Azure 系統管理員的 Multi-Factor Authentication，可以跳到步驟 3。


1. **註冊 Azure 訂用帳戶**
	- 如果您還沒有 Azure 訂用帳戶，可以免費註冊。如果您剛開始使用，並且正在摸索如何使用 Azure MFA，可以使用試用版訂用帳戶
2. **建立 Multi-Factor Auth Provider，或將 Azure AD Premium 或 Enterprise Mobility Suite 授權指派給使用者**
	- 您需要建立 Azure Multi-Factor Auth Provider 並將它指派給目錄，或將授權指派給 Azure AD Premium 或 EMS 使用者。Azure Multi-Factor Authentication 隨附於 Azure Active Directory Premium 中，因此也隨附於 Enterprise Mobility Suite 中。如果您擁有 Azure AD Premium 或 EMS，不需要建立多因素驗證提供者，而是要為 Azure AD Premium 或 EMS 使用者啟用 MFA。您需要將 Azure AD Premium 或 EMS 授權指派給使用者，系統管理員才可以透過管理入口網站將 MFA 指派給使用者。如需將授權指派給使用者的方式，請參閱後續小節。
3. **為使用者開啟 Multi-Factor Authentication** 
	- 透過 Office 365 或 Azure 入口網站為使用者啟用 Azure MFA。如需操作方式的相關資訊，請參閱後續小節。
4. **傳送電子郵件給使用者，通知他們有關 MFA 的資訊**
	- 當使用者開啟帳戶的 Multi-Factor Authentication 後，建議您傳送電子郵件給他們，以資通知。系統會提示使用者在下次登入時完成程序，因此這樣能讓他們做好心理準備。如需範例電子郵件範本，請參閱後續小節。



## 建立 Azure Multi-Factor Auth Provider
依預設，擁有 Azure Active Directory 租用戶的全域管理員可以使用 Multi-Factor Authentication。不過，如果您想要將 Multi-Factor Authentication 延伸到所有使用者，以及/或想要讓全域管理員充分利用管理入口網站、自訂問候語及報告等功能，您必須購買及設定 Multi-Factor Authentication Provider。



### 建立 Multi-Factor Auth Provider
--------------------------------------------------------------------------------

1. 以系統管理員身分登入 Azure 入口網站。
2. 在左側選取 [Active Directory]。
3. 在 [Active Directory] 頁面頂端，選取 [Multi-Factor Authentication Provider]。接著，在底部按一下 [**新增**]。
4. 在 [應用程式服務] 下方選取 [作用中的驗證提供者]，然後選取 [快速建立]。
5. 填寫下列欄位並選取 [建立]。
	1. 名稱 – 作用中驗證提供者的名稱。
	2. 使用模式 – Multi-Factor Authentication Provider 的使用模式。
		- 每次驗證 – 購買依每次驗證付費的模式。通常用於在應用程式中使用 Azure Multi-factor Authentication 的案例。
		- 每個啟用的使用者 – 購買依每個啟用使用者付費的模式。通常用於 Office 365 之類的案例。
	2. 目錄 – 與 Multi-Factor Authentication Provider 相關聯的 Azure Active Directory 租用戶。請j注意以下事項：
		- 您不需要 Azure AD 目錄即可建立 Multi-Factor Auth Provider。如果您打算只使用 Azure Multi-factor Authentication Server 或 SDK，可以保留空白。
		- 如果您想要將 Multi-Factor Authentication 延伸到所有使用者，以及/或想要讓全域管理員充分利用管理入口網站、自訂問候語及報告等功能，您需要建立 Multi-Factor Auth Provider 與 Azure AD 目錄之間的關聯。
		- 如果您要讓內部部署 Active Directory 環境與 Azure AD 目錄同步處理，DirSync 或 AAD Sync 只是一項需求。如果您只要使用不與內部部署 Active Directory 執行個體同步處理的 Azure AD 目錄，就不需要 DirSync 或 AAD Sync。
		



5. 一旦按下建立，系統便會建立 Multi-Factor Authentication Provider，而且您應該會看到一則指出「已成功建立 Multi-Factor Authentication Provider」的訊息。按一下 [確定]。

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/provider.png)</center>
## 將 Azure AD Premium 或 Enterprise Mobility 授權指派給使用者

如果您擁有 Azure AD Premium 或 Enterprise Mobility Suite，便不需要建立 Multi-Factor Auth Provider。您只需要將授權指派給使用者，接著就可以開始為使用者開啟 MFA。

### 指派 Azure AD Premium 或 Enterprise Mobilitiy Suite 授權
--------------------------------------------------------------------------------
<ol>

<li>以系統管理員身分登入 Azure 入口網站。</li>
<li>選取左邊的 [Active Directory]。</li>
<li>在 [Active Directory] 頁面上，在有您要啟用的使用者之目錄上按兩下。</li>
<li>在 [目錄] 頁面的頂端，選取 [授權]。</li>
<li>在 [授權] 頁面上，選取 [Active Directory Premium] 或 [Enterprise Mobility Suite]，然後按一下 [指派]。</li>

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/license.png)</center>

<li>在對話方塊中，選取您要對其指派授權的使用者，然後按一下核取記號圖示，以儲存變更。</li>



## 為使用者開啟 Multi-Factor Authentication

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

狀況 | 說明 |受影響的非瀏覽器應用程式| 注意事項 
:-------------: | :-------------: |:-------------: |:-------------: |
已停用 | 未註冊 Multi-Factor Authentication 之新使用者的預設狀態。|否|使用者目前未使用 Multi-Factor Authentication。
已啟用 |使用者已註冊 Multi-Factor Authentication。|無。它們會繼續運作，直到註冊程序完成為止。|使用者已啟用，但尚未完成註冊程序。系統會提示他們在下次登入時完成程序。
已強制|使用者已註冊，並且已完成使用 Multi-Factor Authentication 的註冊程序。|是。除非建立及使用應用程式密碼，否則將無法運作。 | 使用者可能已完成或可能尚未完成註冊。如果他們已完成註冊程序，表示他們正在使用 Multi-Factor Authentication。否則，系統會提示使用者在下次登入時完成程序
既然我們已具備驗證提供者，或已將授權指派給使用者，接下來我們要為目錄中使用者開啟 MFA。您可以使用下列程序為使用者啟用 MFA。

### 開啟 Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  以系統管理員身分登入 Azure 管理入口網站。
2.  在左側按一下 [Active Directory]。
3.  在 [目錄] 下方，針對要啟用的使用者按一下目錄。
4.  在頂端按一下 [使用者]。
5.  在頁面底部，按一下 [管理 Multi-Factor Auth]
6.  找出要啟用 Multi-Factor Authentication 的使用者。您可能需要在頂端變更檢視。請確認使用者的狀態為已停用，然後在其名稱旁的方塊打勾。
7.  這會在右邊顯示兩個選項，[啟用] 和 [管理使用者設定]。按一下 [啟用]。這會顯示快顯視窗，指出接下來要針對使用者採取的步驟。按一下 [啟用 Multi-Factor Auth]。
8.  啟用使用者後，建議您傳送電子郵件給使用者，通知他們如何使用非瀏覽器應用程式以避免遭到鎖定。

<center>![Cloud](./media/multi-factor-authentication-get-started-cloud/user.png)</center>

若要使用 Windows PowerShell 變更使用者的狀態，您可以使用下列程式碼。您可以將 `$st.State` 變更為前述任一狀態。

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta


## 傳送電子郵件給使用者

啟用使用者後，建議您傳送電子郵件給使用者，通知他們提供連絡資訊。以下是可使用的電子郵件範本，其中包含供使用者觀看之影片的連結。

		Subject: ACTION REQUIRED: Your password for Outlook and other apps needs updated

		Body:

		For added security, we have enabled multi-factor authentication for your account. 

		Action Required: You will need to complete the enrollment steps below to make your account secure with multi-factor authentication.  

		What to expect once MFA is enabled:

		Multi-factor authentication requires a password that you know and a phone that you have in order to sign into browser applications and to access Office 365, Azure portals.

		For Office 365 non-browser applications such as outlook, lync, a mail client on your mobile device etc, a special password called an app password is required instead of your account password to sign in. App passwords are different than your account password, and are generated during the multi-factor authentication set up process. 

		Please follow these enrollment steps to avoid interruption of your Office 365 service:

			1.  Sign in to the Office 365 Portal at http://portal.microsoftonline.com.
			2.  Follow the instructions to set up your preferred multi-factor authentication method when signing into Office 365 using a web browser. 
			3.  Create one app password for each device.
			4.  Enter the same app password in all applicable apps on that device e.g. Outlook, Mail client, Lync, Word, Powerpoint, Excel, CRM etc. 
			5.  Update your Office client applications or other mobile applications to use an app password.

		You can visit http://aka.ms/mfasetup to create app passwords or change your MFA Setting.  Please bookmark this.

		NOTE: Before entering an app password, you will need to clear the sign-in information (delete sign-in info), restart the application,   and sign-in with the username and app password. Follow the steps documented : http://technet.microsoft.com/library/dn270518.aspx#apppassword.


		Watch a video showing these steps at http://g.microsoftonline.com/1AX00en/175.

		Best Regards,
		Your Administrator

## 後續步驟
既然您已在雲端設定 Multi-Factor Authentication，接下來可以前往[設定 Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) 來繼續進行後續步驟。 您將會瞭解有關報告、詐騙警示、自訂語音訊息，以及 Azure Multi-Factor Authentication 提供之所有功能的相關資訊。

<!---HONumber=July15_HO2-->