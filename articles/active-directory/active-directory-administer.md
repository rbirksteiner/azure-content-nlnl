<properties 
	pageTitle="管理 Azure AD 目錄" 
	description="本主題說明 Azure AD 租用戶是什麼，以及如何管理 Azure AD 目錄。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# 管理 Azure AD 目錄

## 什麼是 Azure AD 租用戶？

在實際工作地點，「租用戶」這個字可以定義為進駐建築物的群組或公司。例如，您的組織可能擁有建築物中的辦公區域。這棟建築物可能位在有其他數個組織的街道上。您的組織將會視為該建築物的租用戶。這棟建築物是您組織的資產，以及提供安全性並確保您可以安全地執行業務。它也會與您所在街道上的其他企業分開。這確保您的組織和資產與其他組織隔離。

在啟用雲端的工作地點，「租用戶」可以定義為擁有和管理該雲端服務之特定執行個體的用戶端或組織。運用 Microsoft Azure 所提供的身分識別平台，租用戶只是您組織在註冊 Microsoft 雲端服務 (例如 Azure 或 Office 365) 時所收到和擁有的專屬 Azure Active Directory (Azure AD) 執行個體。

每個 Azure AD 目錄都不同，並與其他 Azure AD 目錄分開。就像公司辦公大樓是您組織特有的安全資產，Azure AD 目錄也是設計成僅供您組織使用的安全資產。Azure AD 架構會隔離客戶資料與身分識別資訊，避免兩者混淆。這表示某個 Azure AD 目錄的使用者和系統管理員無法意外或惡意存取另一個目錄中的資料。

![][1]

## 如何取得 Azure AD 目錄？

Azure AD 提供大部分 Microsoft 雲端服務的核心目錄和身分識別管理功能，包括：

- Azure
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

註冊上述任何 Microsoft 雲端服務時，會收到 Azure AD 目錄。您可以視需要建立額外的目錄。例如，您可能會將第一個目錄維護為生產目錄，然後建立另一個目錄來進行測試或預備。

> [AZURE.NOTE]註冊第一個服務之後，建議您在註冊其他 Microsoft 雲端服務時使用與您組織相關聯的相同系統管理員帳戶。

第一次註冊 Microsoft 雲端服務時，系統會提示您提供有關組織和組織網際網路網域名稱註冊機構的詳細資料。這項資訊之後會用來建立您組織的新 Azure AD 目錄執行個體。訂閱多個 Microsoft 雲端服務時，會使用該相同的目錄來驗證登入嘗試。

額外的服務會完整運用您所設定的任何現有使用者帳戶、原則、設定或內部部署目錄整合，協助提升您組織的內部部署身分識別基礎結構與 Azure AD 之間的效率。

例如，如果您第一次註冊 Microsoft Intune 訂用帳戶，並完成透過部署目錄同步處理和 (或) 單一登入伺服器進一步整合內部部署 Active Directory 與 Azure AD 目錄所需的步驟，則可以註冊另一個 Microsoft 雲端服務 (例如 Office 365)，而此服務也可以利用您現在與 Microsoft Intune 搭配使用的相同目錄整合優勢。

如需整合內部部署目錄與 Azure AD 的詳細資訊，請參閱[目錄整合](active-directory-aadconnect.md)。

### 關聯 Azure AD 目錄與新的 Azure 訂用帳戶

您可以關聯新的 Azure 訂用帳戶與驗證現有 Office 365 或 Microsoft Intune 訂用帳戶登入的相同目錄。使用您的工作或學校帳戶，登入 Azure 管理入口網站。管理入口網站會傳回一則訊息，指出找不到該帳戶的任何訂用帳戶。選取 [**註冊 Azure**]，而且您的目錄將可在入口網站內進行系統管理。如需詳細資訊，請參閱[在 Azure 中管理 Office 365 訂用帳戶的目錄](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure)。

如需 Azure AD 常見使用問題的影片，請參閱 [Azure Active Directory - 常見註冊、登入和使用問題](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions)。

### 以組織身分註冊 Microsoft 雲端服務來建立 Azure AD 目錄

如果您還沒有 Microsoft 雲端服務的訂用帳戶，請使用下面的其中一個連結進行註冊。註冊第一個服務的動作將會自動建立 Azure AD 目錄。

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### 管理 Azure 所佈建的預設目錄

現在，如果註冊 Azure 而且您的訂用帳戶與該目錄相關聯，則會自動建立目錄。但是，如果您在 2013 年 10 月前第一次註冊 Azure，則不會自動建立目錄。在該情況下，Azure 可能已透過佈建您帳戶的預設目錄，以針對帳戶進行「回填」。您的訂用帳戶之後會與該預設目錄相關聯。

目錄的回填工作是在 2013 年 10 月於 Azure 安全性模型的整體改善期間完成。它可協助將組織身分識別功能提供給所有 Azure 客戶，並確保可在目錄中以使用者身分存取所有 Azure 資源。您必須要有目錄，才能使用 Azure。為了達到此目標，任何在 2013 年 7 月 7 日之前註冊但沒有目錄的使用者都必須建立目錄。如果您已經建立目錄，則訂用帳戶會與該目錄相關聯。

Azure AD 沒有任何使用成本。目錄是一個免費的資源。還有一個額外的 Azure Active Directory Premium 層分開授權並提供其他功能 (例如公司商標和自助式密碼重設)。

若要變更目錄的顯示名稱，請按一下入口網站中的目錄，然後按一下 [**設定**]。如本主題稍後所說明，您可以新增目錄，或刪除您不再需要的目錄。若要關聯您的訂用帳戶與不同的目錄，請按一下左導覽中的 [**設定**] 延伸模組，並按一下 [**訂用帳戶**] 頁面底部的 [**編輯目錄**]。您也可以建立使用 DNS 名稱並註冊的自訂網域，而非預設 *.onmicrosoft.com 網域，但服務 (例如 SharePoint Online) 可能偏好使用後者。

## 如何管理目錄資料

身為一或多個 Microsoft 雲端服務訂用帳戶的系統管理員，您可以使用 Azure 管理入口網站、Microsoft Intune 帳戶入口網站或 Office 365 系統管理中心來管理組織的目錄資料。您也可以下載並執行 [[適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx)] Cmdlet，協助您管理 Azure AD 中所儲存的資料。

從上述其中一個入口網站 (或 Cmdlet)，您可以：

- 建立和管理使用者和群組帳戶
- 管理您組織所訂閱的相關雲端服務
- 設定內部部署與目錄服務的整合

Azure 管理入口網站、Office 365 系統管理中心、Microsoft Intune 帳戶入口網站和 Azure AD Cmdlet 全都會讀取和寫入與您組織的目錄相關聯的單一 Azure AD 共用執行個體 (如下圖所示)。因此，入口網站 (或 Cmdlet) 可做為提取和 (或) 修改您目錄資料的前端介面。

![][2]

用來管理使用者和群組的這些帳戶入口網站和相關聯的 Azure AD PowerShell Cmdlet 建置於 Azure AD 平台上。

如果利用其中一個服務身分登入時使用任何入口網站 (或 Cmdlet) 變更組織資料，則變更也會在您下次使用該服務身分登入時顯示在其他入口網站中，因為這項資料會在您所訂閱的 Microsoft 雲端服務之間共用。例如，如果您已使用 Office 365 系統管理中心封鎖使用者登入，該動作將封鎖使用者登入您組織目前所訂閱的任何其他服務。如果您是使用 Microsoft Intune 帳戶入口網站身分提取該相同使用者的帳戶，則會封鎖該使用者。

## 如何新增和管理多個目錄？

您可以在 Azure 管理入口網站中新增 Azure AD 目錄。選取左方的 [**Active Directory**] 延伸模組，然後按一下 [**新增**]。

您可以管理每個目錄當做完全獨立的資源：每個目錄都是對等、全功能且邏輯上與您所管理的其他目錄無關；目錄之間沒有任何父子關聯性。目錄之間的這項獨立性包括資源獨立性、系統管理獨立性和同步處理獨立性。

- **資源獨立性**。如果您在某個目錄中建立或刪除資源，則不會影響另一個目錄中的任何資源 (但外部使用者有部分例外狀況)，如下所述。如果您搭配使用自訂網域 'contoso.com' 與某個目錄，則它不能與任何其他目錄搭配使用。 
- **系統管理獨立性**。如果是 'Contoso' 目錄的非系統管理使用者，請建立 'Test' 測試目錄，然後： 
    - ◦目錄同步作業工具，以與單一 AD 樹系同步處理資料。 
    - ◦'Contoso' 目錄的系統管理員沒有 'Test' 目錄的直接系統管理權限，除非 'Test' 的系統管理員特別將這些權限授與他們。'Contoso' 的系統管理員憑藉著其對已建立 'Test' 之使用者帳戶的控制而可以控制 'Test' 目錄的存取權。 

    如果您變更 (新增或移除) 某個目錄中使用者的系統管理員角色，則變更不會影響另一個目錄中該使用者可能有的任何系統管理員角色。


- **同步處理獨立性**。您可以單獨設定每個 Azure AD，以取得從任一項目的單一執行個體同步處理的資料：
    - 目錄同步作業工具，以與單一 AD 樹系同步處理資料
    - Azure Active Directory Connector for Forefront Identity Manager，以與一或多個內部部署樹系和 (或) 非 AD 資料來源同步處理資料。 

也請注意，與其他 Azure 資源不同，您的目錄不是 Azure 訂用帳戶的子資源。因此，如果您取消或允許 Azure 訂用帳戶到期，則還是可以使用 Azure AD PowerShell、Azure Graph API 或其他介面 (例如 Office 365 系統管理中心) 存取目錄資料。您也可以關聯另一個訂用帳戶與目錄。

## 如何刪除 Azure AD 目錄？
全域管理員可以從入口網站刪除 Azure AD 目錄。刪除目錄時，也會一併刪除目錄中所含的所有資源；因此，您應該確定不需要該目錄之後再予以刪除。

> [AZURE.NOTE]如果使用者以工作或學校帳戶登入，則使用者不得嘗試刪除其主目錄。例如，如果使用者以 joe@contoso.onmicrosoft.com 身分登入，則該使用者無法刪除預設網域為 contoso.onmicrosoft.com 的目錄。

### 刪除 Azure AD 目錄必須符合的條件

Azure AD 需要符合特定條件才能刪除目錄。這可降低刪除目錄對使用者或應用程式造成負面影響的風險 (例如使用者登入 Office 365 或存取 Azure 中資源的能力)。例如，如果不小心刪除訂用帳戶的目錄，則使用者無法存取該訂用帳戶的 Azure 資源。

會檢查下列條件：

- 目錄中的唯一使用者是將刪除目錄的全域管理員。必須先刪除任何其他使用者，才能刪除目錄。如果使用者是從內部部署進行同步處理，則需要關閉同步處理，而且必須使用管理入口網站或適用於 Windows PowerShell 的 Azure 模組來刪除雲端目錄中的使用者。不需要刪除群組或連絡人 (例如從 Office 365 系統管理中心新增的連絡人)。
- 目錄中可能沒有任何應用程式。必須先刪除任何應用程式，才能刪除目錄。 
- 任何 Microsoft Online Service 都沒有任何訂用帳戶 (例如與目錄相關聯 Microsoft Azure、Office 365 或 Azure AD Premium)。例如，在 Azure 中建立預設目錄時，如果您的 Azure 訂用帳戶仍然依賴此目錄來進行驗證，則無法刪除此目錄。同樣地，如果另一位使用者擁有與目錄相關聯的訂用帳戶，則您無法刪除目錄。若要關聯您的訂用帳戶與不同的目錄，請登入 Azure 管理入口網站，然後按一下左導覽中的 [**設定**]。然後，按一下 [**訂用帳戶**] 頁面底部的 [**編輯目錄**]。如需 Azure 訂用帳戶的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure AD 產生關聯](active-directory-how-subscriptions-associated-directory.md)。 

    > [AZURE.NOTE]如果使用者以工作或學校帳戶登入，則使用者不得嘗試刪除其主目錄。例如，如果使用者以 joe@contoso.onmicrosoft.com 身分登入，則該使用者無法刪除預設網域為 contoso.onmicrosoft.com 的目錄。

- 任何 Multi-Factor Authentication 提供者都無法連結至目錄。


## 其他資源

- [Azure AD 論壇](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Azure Multi-Factor Authentication 論壇](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [以組織身分註冊 Azure](sign-up-organization.md)
- [使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png


 

<!---HONumber=62-->