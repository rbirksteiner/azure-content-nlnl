<properties
   pageTitle="應用程式物件和服務主體物件"
   description="Azure Active Directory 中 Application 物件與 ServicePrincipal 物件間的關聯性討論"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/08/2015"
   ms.author="mbaldwin"/>


# 應用程式物件和服務主體物件

此圖說明在範例應用程式 **HR app** 的內容中，Application 物件與 ServicePrincipal 物件之間的關聯性。此處有三個租用戶：開發應用程式的 **Adatum** 租用戶，以及取用 **HR app** 的 **Contoso** 和 **Fabrikam** 租用戶。

![Application 物件和 ServicePrincipal 物件之間的關聯性](./media/active-directory-application-objects/application-objects-relationship.png)


當您在 Azure 管理入口網站中註冊應用程式時，系統會在您的目錄租用戶中建立兩個物件：

- **Application 物件**：此物件代表您應用程式的定義。您可以在下方的 **Application 物件**一節中找到其屬性的詳細的說明。

- **ServicePrincipal 物件**：此物件代表您目錄租用戶中應用程式的執行個體。您可以將原則套用到 ServicePrincipal 物件，包括指派權限給 ServicePrincipal，讓應用程式能夠讀取您租用戶的目錄資料。每當您變更 Application 物件時，所做的變更也會套用到您租用戶中相關聯的 ServicePrincipal 物件上。


> [AZURE.NOTE]當您的應用程式設定為供外部存取時，如果沒有先移除取用者租用戶的存取權，然後再重新授與存取權，則對 Application 物件的變更就不會反映在取用者租用戶的 ServicePrincipal 上。
 


上圖的步驟 "1" 是建立 Application 和 ServicePrincipal 物件的流程。

在步驟 2 中，當公司的系統管理員授與存取權時，系統就會在公司的 Azure AD 租用戶中建立 ServicePrincipal 物件，並對物件指派公司的系統管理員所授與的目錄存取層級。

在步驟 3 中，應用程式的取用者租用戶 (例如 Contoso 和 Fabrikam) 都有自己的 ServicePrincipal 物件，以代表它們的應用程式執行個體。在此範例中，它們都各自具有代表 HR app 的 ServicePrincipal。
 




## Application 物件屬性

下表列出 Application 物件的所有屬性，並包含適用於開發人員的重要詳細資料。這些屬性會套用到已向 Azure AD 註冊的 Web 應用程式、Web API 和原生用戶端應用程式。

 
### 一般

屬性 | 說明
| ------------- | ----------- 
| 名稱 | 應用程式的顯示名稱。**新增應用程式**精靈中的必要屬性。
| 標誌 | 代表您應用程式或公司的應用程式標誌。此標誌可讓外部使用者更容易將授與存取權要求與您的應用程式產生關聯。上傳標誌時，請遵照**上傳標誌**精靈中的規格。如果您沒有提供標誌，就會出現預設標誌。
| 外部存取 | 決定是否允許外部組織的使用者授與他人權限，以使用您的應用程式單一登入，以及存取其組織目錄中的資料。此控制項只會影響授與存取權的能力，不會變更已授與的存取權。只有公司的系統管理員才可以授與存取權。
 

### 單一登入
 
屬性 | 說明
| ------------- | ----------- 
| 應用程式識別碼 URI | 應用程式的唯一邏輯識別碼。**新增應用程式**精靈中的必要屬性。<br><br>因為應用程式識別碼 URI 是邏輯識別碼，所以不需要解析為網際網路位址。傳送單一登入要求至 Azure AD 時，您的應用程式會提供此識別碼。Azure AD 會識別您的應用程式，並將登入回應 (SAML 權杖) 傳送至應用程式註冊期間所提供的回覆 URL。提出登入要求時，請使用「應用程式識別碼 URI」的值設定 Wtrealm 屬性 (用於 WS 同盟) 或 Issuer 屬性 (用於 SAML-P)。**應用程式識別碼 URI** 必須是組織 Azure AD 中的唯一值。<br><br>**注意**：為外部使用者啟用應用程式時，應用程式的應用程式識別碼 URI 的值必須是目錄的其中一個已驗證網域中的位址。因此，此值不可為 URN。這個保護措施可防止其他組織指定 (及使用) 屬於您組織的唯一屬性。在開發期間，您可以將應用程式識別碼 URI 變更為組織初始網域中的位置 (如果您還沒有驗證自訂/虛名網域)，並更新應用程式以使用這個新的值。初始網域是您在註冊時建立的 3 層級網域，例如 contoso.onmicrosoft.com。
| 應用程式 URL | 讓使用者可以登入及使用您應用程式的網頁位址。**新增應用程式**精靈中的必要屬性。<br><BR>**注意**：此屬性在 [新增應用程式]精靈 中的設定值也會設為「回覆 URL」的值。
| 回覆 URL | 應用程式的實體位址。Azure AD 會將權杖連同單一登入回應一併傳送到此位址。在**新增應用程式**精靈中進行第一次註冊時，「應用程式 URL」的設定值也會設為「回覆 URL」的值。提出登入要求時，請使用「回覆 URL」的值設定 wreply 屬性 (用於 WS 同盟) 或 **AssertionConsumerServiceURL** 屬性 (用於 SAML-P)。<br><BR>**注意**：為外部使用者啟用應用程式時，「回覆 URL」必須是 **https://** 位址。| 同盟中繼資料 URL | (選擇性)。代表應用程式之同盟中繼資料文件的實體 URL。必須有此屬性才能支援 SAML-P 登出。Azure AD 會下載位於此端點的中繼資料文件，並使用它來探索憑證的公用部分 (您將其用來驗證登出要求和應用程式登出 URL 上的簽章)。當您第一次新增應用程式時，無法設定此屬性。這只能在之後進行設定。<br><BR>****注意**：如果您需要支援 SAML-P 登出，但您的應用程式沒有同盟中繼資料端點，請連絡客戶服務以獲得其他選項。
 

### 呼叫 Graph API 或 Web API
 
屬性 | 說明
| ------------- | ----------- 
| 用戶端識別碼 | 應用程式的唯一識別碼。呼叫已向 Azure AD 註冊的 Graph API 或其他 Web API 時必須使用此識別碼。Azure AD 會在應用程式註冊期間自動產生此值，且此值無法變更。<BR><BR>若要讓您的應用程式透過 Graph API 來存取目錄 (讀取或寫入存取權)，必須要有用戶端識別碼和金鑰 (在 OAuth 2.0 中稱為用戶端密碼)。您的應用程式會使用用戶端識別碼和金鑰，從 Azure AD OAuth 2.0 權杖端點要求存取權杖(若要檢視所有的 Azure AD 端點，請在命令列中，按一下 [檢視端點])。 使用 Graph API 來取得或設定 (變更) 目錄資料時，您的應用程式會在 Graph API 要求的授權標頭中使用此存取權杖。
| 金鑰 | 如果您的應用程式會在 Azure AD 中讀取或寫入資料 (例如可透過 Graph API 取得的資料)，應用程式便需要金鑰。當您要求存取權杖以呼叫 Graph API 時，您的應用程式會提供其**用戶端識別碼**和**金鑰**。權杖端點會先使用識別碼和金鑰來驗證您的應用程式，再核發存取權杖。您可以建立多組金鑰來處理金鑰變換的情況。此外，您也可以刪除過期、已遭洩露或不再使用的金鑰。
| 管理存取權 | 在三種不同的存取層級中選擇其中一種：單一登入 (SSO)、SSO 和讀取目錄資料，或是 SSO 和讀/寫目錄資料。您也可以移除存取權。如需關於目錄存取的詳細資訊，請參閱[應用程式存取層級](https://msdn.microsoft.com/library/azure/b08d91fa-6a64-4deb-92f4-f5857add9ed8#BKMK_AccessLevels)。<br><BR>**注意**：對應用程式的目錄存取層級所做的變更僅會套用至您的目錄。此變更不會套用至已獲得應用程式存取權的客戶。
 
 
### 原生用戶端
 
屬性 | 說明
| ------------- | ----------- 
| 重新導向 URI | Azure AD 會將使用者代理程式重新導向至此 URI，以回應 OAuth 2.0 授權要求。此值不必是實體端點，但必須是有效的 URI。

##


 
 

<!---HONumber=August15_HO6-->