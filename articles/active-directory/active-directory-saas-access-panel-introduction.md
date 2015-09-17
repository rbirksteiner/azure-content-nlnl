<properties
	pageTitle="存取面板簡介"
	description="了解如何使用各種類別的存取面板 (網頁瀏覽器、Android App、iPhone 和 iPad App) 存取已指派給您的 SaaS App。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# 存取面板簡介


「存取面板」是一種 Web 入口網站，可讓 Azure Active Directory 中擁有組織帳戶的使用者檢視和啟動 Azure AD 系統管理員已授與他們存取權的雲端應用程式。如果您是使用 Azure Active Directory 版本的使用者，您也可以透過存取面板使用自助式群組管理功能。<br>存取面板與 Azure 管理入口網站不同，使用者不需要具備 Azure 訂用帳戶。


![存取面板][1]


存取面板可讓使用者編輯某些設定檔設定，包括以下功能：

- 變更與您的組織帳戶相關聯的密碼

- 編輯密碼重設設定

- 編輯與 Multi-Factor Authentication 相關的連絡人和喜好設定的設定 (對於系統管理員必須具備才能使用的這些帳戶)

- 檢視帳戶詳細資料，例如，您的使用者識別碼、替代電子郵件、行動電話和辦公室電話號碼

- 檢視和啟動 Azure AD 系統管理員已授與您存取權的雲端應用程式。如需有關從使用者的觀點來看存取面板的詳細資訊，請參閱[使用存取面板](https://msdn.microsoft.com/library/azure/dn756411.aspx)。

- 自我管理群組。更具體地說，您可以在 Azure AD 中建立及管理安全性群組，並要求安全性群組成員資格。如需詳細資訊，請參閱 [Azure AD 中使用者的自助群組管理](active-directory-accessmanagement-self-service-group-management.md)與[管理群組](active-directory-manage-groups.md)。




## 存取存取面板


使用者可以使用網頁瀏覽器瀏覽以下 URL 以存取「存取面板」：<br> ****http://myapps.microsoft.com**

如果您已經為您的登入頁面設定自訂商標，在預設情況下，您可以將您組織的網域附加到 URL 結尾以載入此商標：<br> ****http://myapps.microsoft.com/contosobuild.com**

在此情況下，可能會使用已經在 Azure 管理入口網站中您目錄的 [網域] 索引標籤底下設定的任何作用中網域名稱或已驗證的網域名稱，如以下螢幕擷取畫面所示。


![Wingtip Toys][2]


此 URL 必須散佈給要登入與 Azure AD 整合的應用程式的所有使用者。
 




## 驗證

若要前往存取面板，必須使用 Azure AD 中的組織帳戶驗證使用者。<br>Azure AD 可以直接驗證使用者。<br>或者，如果組織已經使用 ADFS 或其他技術設定同盟，使用者可以由 Windows Server Active Directory 進行驗證。

如果使用者具備 Azure 或 Office 365 的訂用帳戶，而且已經在使用 Azure 管理入口網站或 Office 365 應用程式，則他們將會看到應用程式清單，而不需要再次登入。系統將會提示未經過驗證的使用者在 Azure AD 中使用其帳戶的使用者名稱和密碼登入。如果組織已設定同盟，則輸入使用者名稱已經足夠。

驗證之後，使用者將能夠與系統管理員已經整合到目錄中的應用程式互動。若要了解如何整合應用程式與 Azure AD，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。
 




## 網頁瀏覽器需求

存取面板至少需要可啟用 JavaScript 和 CSS 支援的瀏覽器。若要讓使用者使用密碼 SSO 登入應用程式，必須在使用者的瀏覽器中安裝存取面板延伸模組。當使用者選取針對密碼 SSO 設定的應用程式時，會自動下載此存取面板延伸模組。

存取面板延伸模組目前適用於 Internet Explorer 8 和更新版本、Chrome 及 Firefox 瀏覽器。





## 行動應用程式支援

為了能夠在 iOS 和 Android 裝置上登入密碼 SSO 應用程式，使用者應該安裝 Azure Active Directory 小組所發行的 My Apps 行動應用程式。





### My Apps for Android


執行 Android 4.1 版和更新版本的任何 Android 裝置都支援 My Apps for Android，而且現在已在 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.myapps)中提供。


![My Apps][3]






### My Apps for iPhone 和 My Apps for iPad


執行 iOS 7 版和更新版本的任何 iPhone 或 iPad 都支援 My Apps for iOS，而且現在在 Apple App Store 中有提供。


![應用程式設定檔][4]




> [AZURE.NOTE]支援與 Azure AD 同盟的應用程式 (包括 Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 和其他 70 種以上的應用程式) 可以在任何裝置的幾乎任何網頁瀏覽器上登入，而不需要外掛程式或行動應用程式。[https://myapps.microsoft.com](https://myapps.microsoft.com/) 上的其餘存取面板經驗也不需要 My Apps 行動應用程式，就可以在行動裝置上使用。
 


 

## 測試使用者體驗的秘訣

如果您是 Azure 系統管理員，而且您已使用目錄中的帳戶登入 Azure 管理入口網站，則您將以目前的系統管理員帳戶自動登入存取面板。在此情況下，您可以看到已指派給此帳戶的所有應用程式。

**以不同的使用者帳戶測試：**

1. 按一下 Azure 入口網站或存取面板右上角的使用者功能表，並選取 [**登出**]。這可將您登出 Azure AD。

2. 前往位於 ****http://myapps.microsoft.com** 的存取面板。

3. 在登入頁面中，輸入您目錄中想要測試的帳戶的使用者名稱和密碼。
 
## 啟動應用程式

可能出現在存取面板上的應用程式有數種。
 
### Office 365 應用程式

如果組織打算使用 Office 365 應用程式，且使用者有授權，則 Office 365 應用程式將會出現在使用者的存取面板上。

當使用者按一下 Office 365 應用程式的應用程式磚時，會將他們重新導向至該應用程式，並自動登入。

### 以同盟 SSO 設定的 Microsoft 及協力廠商應用程式

這些是系統管理員在單一登入模式設定為 [*Azure AD 單一登入*] 的情況下，新增到 Azure 管理入口網站的 Active Directory 區段中的應用程式。只有在系統管理員明確地為使用者授與應用程式的存取權時，他們才會看到這些應用程式。

當使用者按一下這些其中一個應用程式的應用程式磚時，會將他們重新導向至該應用程式，並自動登入。

### 不含身分識別佈建的密碼 SSO

這些是系統管理員在單一登入模式設定為 [*密碼單一登入*] 的情況下，新增到 Azure 管理入口網站的 Active Directory 區段中的應用程式。<br>目錄中的所有使用者都會看到已在此模式下設定的所有應用程式。

當使用者第一次按一下這些其中一個應用程式的應用程式磚時，系統會提示他們安裝適用於 Internet Explorer 或 Chrome 的密碼 SSO 外掛程式，安裝後可能需要重新啟動網頁瀏覽器。當使用者返回存取面板並再按一下應用程式磚時，系統將會提示他們輸入應用程式的使用者名稱和密碼。一旦輸入使用者名稱和密碼之後，這些認證將會安全地儲存在 Azure AD 中，並連結至他們在 Azure AD 中的帳戶，且存取面板將會使用這些認證，自動將使用者登入應用程式。

下一次使用者按一下應用程式磚時，他們將會自動登入應用程式而不需要再次輸入認證，也不需要再次安裝密碼 SSO 外掛程式。

如果使用者的認證在目標協力廠商應用程式中已變更，則使用者也必須更新他們儲存在 Azure AD 中的認證。若要更新認證，使用者必須選取應用程式磚右下方的圖示，然後選取 [更新認證] 以重新輸入該應用程式的使用者名稱與密碼。

### 含身分識別佈建的密碼 SSO

這些是系統管理員在單一登入模式設定為 [*密碼單一登入*] 以及身分識別佈建的情況下，新增到 Azure 管理入口網站的 Active Directory 區段中的應用程式。

當使用者第一次按一下這些其中一個應用程式的應用程式磚時，系統會提示他們安裝適用於 Internet Explorer 或 Chrome 的密碼 SSO 外掛程式，安裝後可能需要重新啟動網頁瀏覽器。當使用者返回存取面板並再按一下應用程式磚時，他們將會自動登入應用程式。

有些應用程式可能會要求使用者在第一次登入時變更密碼。如果使用者的認證在目標協力廠商應用程式中已變更，則使用者也必須更新他們儲存在 Azure AD 中的認證。若要更新認證，使用者必須選取應用程式磚右下方的圖示，然後選取 [更新認證] 以重新輸入該應用程式的使用者名稱與密碼。

### 含現有 SSO 解決方案的應用程式

設定應用程式的單一登入時，Azure 管理入口網站提供「現有單一登入」的第三個選項。這個選項只允許系統管理員建立應用程式的連結，並將連結放在選定使用者的存取面板上。例如，如果有一個應用程式設定為使用 Active Directory Federation Services 2.0 來驗證使用者，系統管理員可以使用 [現有單一登入] 選項在存取面板上建立應用程式的連結。當使用者存取此連結時，會使用 Active Directory Federation Services 2.0，或由應用程式提供的任何現有單一登入解決方案來驗證使用者。


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

<!---HONumber=August15_HO6-->