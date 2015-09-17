<properties
   pageTitle="將應用程式加入至 Azure Active Directory 的方式"
   description="本文將說明如何將應用程式加入至 Azure Active Directory 的執行個體。"
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="07/30/2015"
      ms.author="shoatman"/>

# 將應用程式加入至 Azure AD 的方式和原因

在 Azure Active Directory 的執行個體中檢視應用程式清單時，最初令人感到困惑的其中一件事情是了解應用程式的來源以及其存在目的。本文將提供應用程式如何在目錄中呈現的高階概觀，並提供內容來協助您了解應用程式如何出現在您的目錄中。

## Azure AD 對應用程式提供哪些服務？

將應用程式加入至 Azure AD，即可利用一或多個其所提供的服務。這些服務包括：

* 應用程式驗證與授權
* 使用者驗證與授權
* 使用同盟或密碼的單一登入 (SSO)
* 使用者佈建與同步處理
* 角色型存取控制。使用目錄來定義應用程式角色，才能在應用程式中執行角色型授權檢查。
* oAuth 授權服務 (Office 365 和其他 Microsoft 應用程式會用它來授與對 API/資源的存取權限)。
* 應用程式發佈與 Proxy。將應用程式從私人網路發佈到網際網路

## 目錄中呈現應用程式的方式？

在 Azure AD 中，應用程式會透過 2 種物件呈現：應用程式物件和服務主體物件。在「家用」/「擁有者」或「發佈」目錄下註冊的一個應用程式物件，和在其運作的每個目錄中代表應用程式的一個或多個服務主體物件。

應用程式物件說明該應用程式與 Azure AD (多租用戶服務) 之間的關係，而且可能包含下列任何項目：(*注意*：這不是完整清單。)

* 名稱、標誌和發行者
* 密碼 (用來驗證應用程式的對稱和/或非對稱金鑰)
* API 相依性 (oAuth)
* API/資源/發佈範圍 (oAuth)
* 應用程式角色 (RBAC)
* SSO 中繼資料和組態 (SSO)
* 使用者佈建中繼資料和組態
* Proxy 中繼資料和組態

在每個應用程式運作的目錄中 (包括其主目錄)，服務主體會是應用程式的記錄。服務主體：

* 透過應用程式識別碼屬性參考回至應用程式物件
* 記錄本機使用者和群組應用程式角色指派
* 記錄本機使用者和授與應用程式的系統管理員權限
    * 例如：應用程式存取特定使用者電子郵件的權限
* 記錄本機原則，包括條件式存取原則
* 記錄應用程式的本機替代本機設定
    * 宣告轉換規則
    * 屬性對應 (使用者佈建)
    * 租用戶特定應用程式角色 (如果應用程式支援自訂角色)
    * 名稱/標誌

### 目錄間的應用程式物件和服務主體圖表

![說明應用程式物件和服務主體如何與現有 Azure AD 執行個體共存的圖表。][apps_service_principals_directory]

從以上圖表可以清楚得知。Microsoft 會在內部 (左側) 維護兩個它用來發佈應用程式的目錄。

* 一個用於 Microsoft 應用程式 (Microsoft 服務目錄)
* 一個用於預先整合的協力廠商應用程式 (應用程式庫目錄)

與 Azure AD 整合的應用程式發行者/供應商必須具有發佈目錄。(某些 SAAS 目錄)。

自行新增的應用程式包括：

* 自行開發的應用程式 (與 AAD 整合)
* 為單一登入而進行連線的應用程式
* 使用 Azure AD 應用程式 Proxy 發佈的應用程式。

### 幾個附註和例外狀況

* 並非所有的服務主體都會指回應用程式物件。是嗎？ 原先建置 Azure AD 時提供給應用程式的服務受到更多的限制，而且服務主體足以建立應用程式身分識別。原先服務主體的功能狀況接近 Windows Server Active Directory 服務帳戶。基於這個原因，您還是可以使用 Azure AD PowerShell 來建立服務主體，而無需先建立應用程式物件。Graph API 需要應用程式物件才能建立服務主體。
* 並非所有上述資訊目前都處於以程式設計方式公開的狀態。以下功能僅適用於 UI：
    * 宣告轉換規則
    * 屬性對應 (使用者佈建)
* 如需服務主體與應用程式物件的詳細資訊，請參閱 Azure AD Graph REST API 參考文件。*提示*：目前可用的 Azure AD 結構描述參考中，Azure AD Graph API 文件是最有用的資源。  
    * [應用程式](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [服務主體](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## 如何將應用程式加入 Azure AD 執行個體？
有很多方式可以將應用程式加入 Azure AD：

* 從 [Azure Active Directory 應用程式庫](http://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)新增應用程式
* 註冊/登入與 Azure Active Directory 整合的協力廠商應用程式 (例如：[Smartsheet](https://app.smartsheet.com/b/home) 或 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * 註冊/登入期間，系統會要求使用者提供應用程式的存取權限，才能存取其設定檔及其他權限。做出同意動作的第一個人會造成代表應用程式的服務主體被新增至目錄。
* 註冊/登入 Microsoft 線上服務 (如 [Office 365](http://products.office.com/))
    * 訂閱或開始試用 Office 365 時，目錄中會建立一或多個服務主體，代表用來傳遞所有 Office 365 相關功能的各種服務。
    * 某些 Office 365 服務(如 SharePoint) 會持續建立服務主體，以允許元件之間的安全通訊，包括工作流程。
* 在 Azure 管理入口網站中加入您正在開發的應用程式，請參閱：https://msdn.microsoft.com/library/azure/dn132599.aspx
* 新增您使用 Visual Studio 開發的應用程式，請參閱：
    * [ASP.Net 驗證方法](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [連接的服務](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* 新增應用程式來使用 [Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* 連接應用程式進行使用 SAML 或密碼 SSO 的單一登入
* 許多其他項目，包括在 Azure 中的各種開發人員經驗和/在跨開發人員中心的 API 總管體驗

## 誰有權將應用程式加入我的 Azure AD 執行個體？

只有全域系統管理員可以：

* 從 Azure AD 應用程式庫 (預先整合的協力廠商應用程式) 新增應用程式
* 使用 Azure AD 應用程式 Proxy 來發佈應用程式

您目錄中的所有使用者都有權新增其所開發的應用程式，並自行決定要共用哪些應用程式，以及提供哪些應用程式存取其組織資料的權限。*請記住，使用者註冊/登入應用程式和授與權限可能會導致系統建立服務主體。*

剛聽到時您可能會有些疑慮，但請記住下列要點：

* 在無需在目錄中註冊/記錄應用程式的情況下，應用程式能夠利用 Windows Server Active Directory 進行使用者驗證已有數年的時間。現在組織已提高可見度，您可以知道究竟有多少個應用程式正在使用目錄以及使用目的為何。
* 不需要系統管理導向的應用程式發佈/註冊程序。有了 Active Directory Federation Services，系統管理員很有可能必須代表開發人員，將應用程式新增為信賴憑證者。現在開發人員可以自助。
* 基於商業用途，使用者使用其組織帳戶登入/註冊應用程式是件好事。如果使用者之後離開組織，他們將無法在先前所使用的應用程式中存取帳戶。
* 記錄哪些資料與哪些應用程式共用是件好事。資料比以往更容易進行傳輸，且清楚記錄哪些人員與哪些應用程式共用哪些資料會很有用。
* 使用 Azure AD for oAuth 的應用程式可確切決定使用者可以授與應用程式哪些權限，以及哪些權限需要系統管理員的同意。不言而喻，唯有系統管理員可以同意較大範圍且更重要的權限。
* 新增使用者和允許應用程式存取其資料屬於稽核事件，可讓您在 Azure 管理入口網站中檢視稽核報告，進而決定將應用程式加入目錄的方式。

**附註：***Microsoft 本身使用預設組態進行運作已有幾個月的時間了。*

說了這麼多，您可能可以防止使用者在目錄中新增應用程式，以及在對與應用程式共用哪些資訊上行使同意權，方法是修改 Azure 管理入口網站中的目錄組態。在 Azure 管理入口網站中，您可以在目錄的 [設定] 索引標籤上存取下列組態。

![進行整合式應用程式設定的 UI 螢幕擷取畫面][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

深入了解如何將應用程式加入 Azure AD，以及如何設定應用程式的服務。

* 開發人員：[了解如何整合應用程式與 AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* 開發人員：[檢閱在 Github 上與 Azure Active Directory 整合的應用程式範例程式碼](https://github.com/AzureADSamples)
* 開發人員和 IT 專業人員：[檢閱 Azure Active Directory Graph API 的 REST API 文件](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT 專業人員：[了解如何使用應用程式庫的 Azure Active Directory 預先整合應用程式](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT 專業人員：[尋找設定特定預先整合應用程式的教學課程](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT 專業人員：[了解如何使用 Azure Active Directory 應用程式 Proxy 發佈應用程式](https://msdn.microsoft.com/library/azure/dn768219.aspx)

<!--Image references-->
[apps_service_principals_directory]: media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]: media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg

<!---HONumber=August15_HO6-->