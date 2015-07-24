<properties
	pageTitle="Azure Active Directory 版本"
	description="說明 Azure Active Directory 免費和付費版本選擇的主題。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="TerryLan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2015"
	ms.author="curtand"/>

# Azure Active Directory 版本

Azure Active Directory 是一項可在雲端中提供完整身分識別與存取管理功能的服務。它結合了目錄服務、進階身分識別控管、應用程式存取管理以及豐富的標準架構平台，以供開發人員使用。如需詳細資訊，請參閱[這段影片](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/)。

以 Microsoft Azure Active Directory 中一組龐大的免費功能為基礎所建置，Azure Active Directory Premium 與 Basic 版本提供一組更進階的功能，透過更嚴苛的身分識別和存取管理需求來進一步提升企業的能力。這兩種版本的詳細定價請參閱 [Azure Active Directory 定價](http://azure.microsoft.com/pricing/details/active-directory/)。訂閱 Azure 時，您可以選擇下列免費和付費版本的 Azure Active Directory：

- **免費** - 免費版本的 Azure Active Directory 是每個 Azure 訂用帳戶的一部分。不需要任何授權和安裝。您可以用它管理使用者帳戶、與內部部署目錄同步處理、取得 Azure、Office 365 和數千個熱門 SaaS 應用程式 (像是 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等) 之間的單一登入。
- **基本** - Azure Active Directory Basic Edition 針對包含雲端優先需求的背景工作，提供應用程式存取和自助式身分識別管理需求。有了 Azure Active Directory 的基本版本，您即可獲得 Azure Active Directory 免費版所提供的所有功能，再加上以群組為基礎的存取管理、雲端應用程式的自助式密碼重設、Azure Active Directory 應用程式 Proxy (若要使用 Azure Active Directory 發佈內部部署 Web 應用程式)、適用於啟動企業和消費者雲端應用程式的可自訂環境，以及 99.9% 運作時間的企業級 SLA。有 Azure Active Directory Basic 版本授權的系統管理員也可以啟動 Azure Active Directory Premium 試用版。
- **進階** - 有了 Azure Active Directory 的進階版本，您即可獲得 Azure Active Directory 免費與基本版本所提供的所有功能，再加上其他功能豐富的企業層級身分識別管理功能，如下所述。

若要註冊並立即開始使用 Active Directory Premium，請參閱[開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。

> [AZURE.NOTE]目前在中國不支援 Azure Active Directory Premium 和 Azure Active Directory Basic。請透過 [Azure Active Directory 論壇](http://feedback.azure.com/forums/169401-azure-active-directory)與我們連絡以取得詳細資訊。

## Azure Active Directory Basic 的功能

Active Directory Basic Edition 是一個 Azure Active Directory 的付費產品，且包含下列功能：

- **公司商標** - 若要讓使用者體驗更美好，您可以在組織的 [登入] 和 [存取面板] 頁面加上公司標誌和色彩配置。在加上標誌之後，您也可以選擇是否加入標誌的當地語系化版本，以取得不同的語言和地區設定。如需詳細資訊，請參閱[在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)。
- **以群組為基礎的應用程式存取** - 使用群組來佈建使用者，並將使用者存取大量指派給數千個 SaaS 應用程式。您可以只在雲端中建立這些群組，或者您可以利用內部部署 Active Directory 中已同步處理的現有群組來建立這些群組。如需詳細資訊，請參閱[在 Azure AD 中將群組存取權指派給 SaaS 應用程式](https://msdn.microsoft.com/library/azure/dn621141.aspx)。
- **自助式密碼重設** - Azure 一律允許目錄系統管理員重設密碼。有了 Azure Active Directory Basic，您現在可以減少在您的使用者忘了密碼功能時撥打服務台電話的次數，方法是使用 Office 365 中的相同登入體驗，對您目錄下的所有使用者提供重設其密碼的功能。如需詳細資訊，請參閱 [Azure AD 中的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)。
- **99.9% 的企業 SLA** - 我們保證至少 99.9%的 Azure Active Directory Basic 服務可用性。
- [**Azure Active Directory 應用程式 Proxy**](https://msdn.microsoft.com/library/azure/dn768214.aspx) - 提供員工使用 Azure Active Directory，從雲端安全存取內部部署應用程式 (如 SharePoint 和 Exchange/OWA) 的權限。

## Azure Active Directory Premium 的功能

Active Directory Premium 版本是一個 Azure Active Directory 的付費產品，且包含免費和 Basic 版本的所有功能再加上下列功能：

- **自助式群組管理** - Azure Active Directory Premium 透過允許使用者建立群組、要求存取其他群組、委派群組擁有權，以便其他人可以核准要求並維護其群組的成員資格等功能來簡化日常的管理工作。

    如需詳細資訊，請參閱[Azure AD 中使用者的自助式群組管理](https://msdn.microsoft.com/library/azure/dn641267.aspx)。

- **進階安全性報告和警示** – 透過檢視詳細記錄來監控並保護雲端應用程式的存取，記錄中會顯示更進階的異常和不一致的存取模式報告。進階的報告屬於 Machine Learning 型，可協助您獲得全新的見解，以改善存取安全性和對潛在威脅的回應。

    如需詳細資訊，請參閱[檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)。

- **Multi-Factor Authentication** - Premium 現在包含 Multi-Factor Authentication，可協助您保護存取內部部署應用程式 (VPN、RADIUS 等)、Azure、Microsoft 線上服務 (如 Office 365 和 Dynamics CRM Online)，以及數千個與 Azure Active Directory 預先整合的非 MS 雲端服務。只要啟用 Azure Active Directory 身分識別的 Multi-Factor Authentication，下次使用者登入時，系統便會提示使用者設定其他驗證。

    如需詳細資訊，請參閱 [在 Azure Active Directory 中新增 Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249466.aspx)。

- **Microsoft 身分識別管理員 (MIM)** - Premium 包含有權選擇是否授予在內部網路中使用 MIM 伺服器 (和 CAL)，以支援任何混合式身分識別解決方案組合的權限。如果您有要與 Azure Active Directory 直接進行同步處理的不同內部部署目錄和資料庫，則這會是個不錯的選擇。可以使用的 FIM 伺服器數目沒有限制，不過，系統會根據 Azure Active Directory Premium 使用者授權的配置授與 MIM CAL。

    如需詳細資訊，請參閱[部署 MIM 2010 R2](https://www.microsoft.com/server-cloud/products/forefront-identity-manager/features.aspx)。

- **99.9% 的企業 SLA** - 我們保證至少 99.9%的 Azure Active Directory Premium 服務可用性。

    如需詳細資訊，請參閱 [Active Directory Premium SLA](http://azure.microsoft.com/support/legal/sla/)。

- **具有寫回功能的密碼重設** - 自助式密碼重設可以寫回至內部部署目錄。




## 版本比較：所有版本通用的功能

- 目錄即服務。免費版本有 50 萬個物件數目限制。但是 50 萬個物件數目限制並不適用於採用 Azure Active Directory 目錄服務的 Office 365、Office Microsoft Intune 或任何其他 Microsoft 線上服務。基本和進階版本沒有物件數目限制。
- 使用 UI 或 Windows PowerShell Cmdlet 的使用者和群組管理
- 裝置註冊
- 適合單一登入 (SSO) 型使用者存取 SaaS 和自訂應用程式使用的存取面板入口網站。在 Azure Active Directory Free 和 Azure Active Directory Basic 中，有權存取每個 SaaS 應用程式的使用者在他們的存取面板中最多可以看到 10 個應用程式，並取得他們的單一登入存取權限 (假設系統管理員已使用 SSO 對它們進行設定)。在免費版本中，系統管理員可以設定 SSO，並將使用者存取權指派給任意數目的 SaaS 應用程式，但使用者在存取面板中一次只會看到 10 個應用程式。Azure Active Directory Premium 沒有任何應用程式數目限制。
- 以使用者為基礎的應用程式存取管理和佈建
- 雲端使用者的自助式密碼變更
- 目錄同步處理工具：適用於內部部署 Active Directory 與 Azure Active Directory 之間的同步處理
- 標準安全性報告

## 比較版本：Premium 和 Basic 的功能

- 高可用性 SLA 運作時間 (99.9%)
- 以群組為基礎的應用程式存取管理和佈建
- 在 [登入] 和 [存取面板] 頁面上自訂公司標誌和色彩
- 雲端使用者的自助式密碼重設
- 應用程式 Proxy：內部部署 Web 應用程式的安全遠端存取和單一登入

## 版本比較：僅限 Premium 的功能

- 雲端使用者的自助式群組管理
- 包含內部部署寫回功能的自助式密碼重設
- Microsoft Identity Manager (MIM) 伺服器授權 – 適用於內部部署資料庫及/或目錄與 Azure Active Directory 之間的同步處理
- 進階異常安全性報告 (Machine Learning 型)
- [雲端應用程式探索](http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery)
- 進階應用程式使用情況報告
- 適用於雲端使用者的 Multi-Factor Authentication 服務
- 適用於內部部署使用者的 Multi-Factor Authentication 服務

## 目前處於公開預覽狀態的功能

下列功能目前處於公開預覽狀態，且將於近日新增：

- [系統管理單位](https://msdn.microsoft.com/library/azure/dn832057.aspx)：新的 Azure Active Directory 資源容器，可用來將系統管理權限委派給使用者的子集，並將原則套用到使用者的子集。
- [將自己的 SaaS 應用程式新增](https://msdn.microsoft.com/library/azure/dn893637.aspx)至 Azure Active Directory。
- [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)：監控內部部署 Active Directory 基礎結構的健康情況，並取得使用情況分析。
- Facebook、Twitter 和 LinkedIn 的密碼變換。如需詳細資訊，請閱讀[本篇文章](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)。
- 動態群組成員資格。如需詳細資訊，請參閱[本篇文章](https://msdn.microsoft.com/library/azure/dn913807.aspx)。
- [條件式存取](https://msdn.microsoft.com/library/azure/dn906877.aspx)：每個應用程式的多因素驗證。
- HR 應用程式整合：Workday
- 特殊權限身分識別管理：特殊權限身分識別管理提供改進的監督，有助於符合服務等級的合約和法規遵循需求。
- 自助服務應用程式的要求：系統管理員可以提供一份 SaaS 應用程式清單給使用者，讓使用者選擇他們想要使用的項目，應用程式將立即或於核准之後可供使用。
- Azure 報告 API：其他的監控或 SIEM 將可取得所有 Azure Active Directory 的安全性報告。


## 接下來

- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)
 

<!---HONumber=62-->