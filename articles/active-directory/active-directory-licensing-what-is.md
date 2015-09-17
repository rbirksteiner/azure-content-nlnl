<properties
   pageTitle="什麼是 Microsoft Azure Active Directory 授權？ | Microsoft Azure"
	description="描述 Microsoft Azure AD 授權、如何運作、如何開始著手，以及最佳作法，包括 Office 365、Microsoft Intune 和 Azure Active Directory Premium 與 Basic 版本"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="swadhwa"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/16/2015"
	ms.author="curtand"/>

# 什麼是 Microsoft Azure Active Directory 授權？

##說明
Azure Active Directory (AD) 是 Microsoft 的「身分識別即服務 (IDaaS)」解決方案與平台。Azure AD 提供許多功能與技術的版本，從可搭配任何 Microsoft 服務 (例如 Office 365、Dynamics、Microsoft Intune 和 Azure) 使用的 Azure AD 免費版 (Azure AD 在此模式中不會產生任何使用費用)，到許多 Azure AD 付費版本，例如 Enterprise Mobility Suite (EMS)、Azure AD Premium 與 Basic，以及 Azure Multi-Factor Authentication (MFA)。如同許多 Microsoft 線上服務一般，大多數 Azure AD 付費版本都是透過賦予每位使用者權利來提供，就像在 Office 365、Microsoft Intune 和 Azure AD 中一樣。在這些情況下，服務購買是由一或多個訂用帳戶來表示，而且每個訂用帳戶會在您的租用戶中包含預先購買的授權數目。每位使用者權利是透過授權指派、在使用者與產品之間建立連結、啟用使用者的服務元件，以及使用一個預付授權來達成。

[立即試用 Azure AD Premium。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE]Azure AD 管理入口網站是 Azure 管理入口網站的一部分。雖然使用 Azure AD 不需要購買任何 Azure，但是存取此入口網站需要有效的 Azure 訂用帳戶或 [Azure 試用版訂用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

如需 Azure AD 服務功能的一般概觀，請參閱[什麼是 Azure AD](active-directory-whatis.md)。[深入了解 Azure AD 服務等級](http://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]Azure 預付型訂用帳戶則不同：雖然也會在您的目錄中表示，但是這些訂用帳戶可以建立 Azure 資源，而且會將資源對應到您的付款方法。在這種情況下，訂用帳戶「沒有」相關聯的授權計數。使用者與訂用帳戶的關聯就是使用者管理訂用帳戶資源的存取權，是透過授與使用者權限來達成，讓他們能夠操作與訂用帳戶對應的 Azure 資源。


##Azure AD 授權如何運作？

以授權為基礎 (以權利為基礎) 的 Azure AD 服務的運作方式，是在您的 Azure AD 目錄/服務租用戶中啟用訂用帳戶。一旦訂用帳戶啟用，服務功能就可以由目錄/服務系統管理員管理，並由授權的使用者使用。

當您購買或啟用 Enterprise Mobility Suite、Azure AD Premium 或 Azure AD Basic 時，您的目錄會隨著訂用帳戶更新，包括其有效期間和預付授權。您的訂用帳戶資訊，包括狀態、下一個週期事件，以及已指派或可用的授權數目，可在 Azure AD 管理入口網站特定目錄的 [授權] 索引標籤下取得。這也是管理您的授權指派最佳的位置。

每個訂用帳戶都包含一或多個服務方案，每個方案都會對應服務類型包含的功能等級。例如，Azure AD、Azure MFA、Microsoft Intune、Exchange Online 或 SharePoint Online。Azure AD 授權管理「不」需要服務方案等級管理。這與 Office 365 不同，Office 365 是依賴此進階組態模式來管理內含服務的存取權。而 Azure AD 則是依賴服務組態來啟用功能和管理個別的權限。

一般而言，Azure AD 訂用帳戶資訊是透過 Azure 管理入口網站在特定目錄的 [授權] 索引標籤下進行管理。Azure AD 訂用帳戶 (除了 Azure AD Premium 之外)「不會」顯示在 Office 入口網站中。

> [AZURE.IMPORTANT]Azure AD Premium 和 Basic，以及 Enterprise Mobility Suite 訂用帳戶，會受限於其佈建的目錄/租用戶。訂用帳戶無法分割至不同目錄，或用來賦予權利給其他目錄中的使用者。在目錄之間可以移動訂用帳戶，但是需要提交支援票證，如果是直接購買的情況，則需要取消並重新購買。

> [AZURE.IMPORTANT]透過大量授權購買 Azure AD 或 Enterprise Mobility Suite 時，如果協議包含其他 Microsoft Online Services (例如 Office 365)，將會自動啟用訂用帳戶。

付費 Azure AD 功能可以擴展目錄的範圍。範例包括：- 以群組為基礎的應用程式指派，可在您管理的特定應用程式下啟用。- 進階和自助式群組管理功能可在目錄組態下或特定群組內使用。- Premium 安全性報告位於 [報告] 索引標籤上。- 雲端應用程式探索會出現在 Azure Preview 入口網站的 [身分識別] 下。

###指派授權
取得訂用帳戶之後，您只需要設定付費功能，但是要使用 Azure AD 的付費功能，就必須將授權分配給適當的人員。一般而言，應存取 Azure AD 付費功能或者透過 Azure AD 付費功能管理的人員，都必須被指派授權。授權指派是一種使用者與購買的服務 (例如 Azure AD Premium、Basic 或 Enterprise Mobility Suite) 之間的對應。

管理您的目錄中哪些使用者應該擁有授權很簡單。只要透過 Azure AD 管理入口網站指派給群組來建立指派規則，或者透過入口網站、PowerShell 或 API 直接將授權指派給適當的人員即可。將授權指派給群組時，則會對所有群組成員指派授權。如果群組新增或移除使用者，也會對他們指派或移除適當的授權。群組指派可以利用您可用的任何群組管理功能，而且與以群組為基礎的應用程式指派一致。使用這個方法，您可以設定規則讓您的目錄中所有使用者都會自動被指派，確保有適當職稱的每個人都獲得授權，或者甚至委派決策權給組織中的其他管理人員。

透過以群組為基礎的授權指派，缺少使用位置的任何使用者在指派期間將會繼承目錄位置。系統管理員可以隨時變更此位置。在自動指派因錯誤而失敗的情況下，在該授權類型下的使用者資訊會反映該狀態。

##開始使用 Azure AD 授權

要開始使用 Azure AD 很簡單。您註冊免費的 Azure 試用版之後，就可以建立您的目錄。[深入了解以組織身分註冊](sign-up-organization.md)。以下可協助您確定您的目錄最適合您可能會使用或打算使用的其他 Microsoft 服務，以及您取得服務的目標。

以下是幾種最佳作法：- 如果您已經在使用任何 Microsoft 的組織服務，您就已經有 Azure AD 目錄。在此情況下，您應該為其他服務繼續使用相同的目錄，以便可以跨服務使用核心身分識別管理 (包括佈建和混合式 SSO)。您的使用者將會擁有單一登入經驗，而且將從跨服務更豐富的功能受益。因此，如果您決定為員工購買 Azure AD 付費服務，我們建議您使用相同的目錄來進行。- 如果您打算為不同一組使用者 (夥伴、客戶等等) 使用 Azure AD，或如果您想要評估 Azure AD 服務，且想要隔離實際執行服務進行，或如果您想要為您的服務設定沙箱環境，我們建議您先透過 Azure 管理入口網站建立新的目錄。[深入了解在 Azure 管理入口網站中建立新的 Azure AD 目錄](active-directory-licensing-directory-independence.md)。您的帳戶將會建立新的目錄，成為具有全域系統管理員權限的外部使用者。當您使用此帳戶登入 Azure 管理入口網站中時，您將會看到這個目錄，並且能夠存取所有的目錄管理工作。我們建議您建立一個具有適當權限的本機帳戶，來管理其他 Microsoft 服務 (即無法透過 Azure 管理入口網站存取的服務)。[深入了解在 Azure AD 中建立使用者帳戶](active-directory-create-users.md)。

> [AZURE.NOTE]Azure AD 支援「外部使用者」，這是在 Azure AD 執行個體中，使用 Microsoft 帳戶 (MSA) 或另一個目錄中的 Azure AD 身分識別所建立的使用者帳戶。雖然我們忙著將此功能擴充到所有 Microsoft 的組織服務，但是目前在某些服務體驗中不支援這些帳戶。例如，Office 365 系統管理入口網站目前不支援這些使用者。因此，使用 Microsoft 帳戶的外部使用者將無法存取 Office 365 系統管理入口網站，而來自其他 Azure AD 目錄的外部使用者將會被忽略。在後者的情況中，只有使用者的本機帳戶，最初建立使用者的 Azure AD 或 Office 365 目錄，才能透過這些體驗存取。

如之前所述，Azure AD 有不同的付費版本。這些版本在其購買可用性有些微的差異：


| 產品 | EA/VL | 開啟 | 	CSP | 	MPN 使用權利 | 	直接購買 | 試用版 |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Basic | X | X | X | X | <br /> | <br /> |

###選取一或多個授權試用版
 在所有情況下，您可以藉由在您目錄中的 [授權] 索引標籤選取想要的特定試用版，啟用 Azure AD Premium 或 Enterprise Mobility Suite 試用版訂用帳戶。任何一個試用版都包含 100 個授權的 30 天訂用帳戶。

![Azure Active Directory 試用版授權方案](./media/active-directory-licensing-what-is/trial_plans.png)

![Enterprise Mobility Suite 試用版授權方案](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![啟用的試用版授權方案](./media/active-directory-licensing-what-is/active_license_trials.png)

###指派授權
一旦訂用帳戶啟用之後，您應該指派一個授權給自己，然後重新整理瀏覽器以確保您可以看到所有的功能。下一個步驟是將授權指派給需要存取或要包含在 Azure AD 付費功能中的使用者。如先前在〈指派授權〉中所述，執行這項操作最好的方式是，識別代表目標對象的群組，並且將授權指派給群組；如此一來，在群組的週期期間新增到群組或從群組移除的使用者，就會被指派授權或移除授權。

若要將授權指派給群組或個別使用者，請選取您想要指派的授權方案，然後在命令列上按一下 [指派]。

![啟用的試用版授權方案](./media/active-directory-licensing-what-is/assign_licenses.png)

在所選方案的指派對話方塊中，您可以選取使用者，並將他們新增到右邊的 [指派] 欄。您可以逐頁查看使用者清單，或是利用使用者方格右上角的放大鏡來搜尋特定的個人。若要指派群組，請從 [顯示] 功能表選取 [群組]，然後按一下右邊的核取按鈕以重新整理顯示的指派。

![指派授權給群組](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

您現在可以搜尋或逐頁查看群組，並以相同方式將它們加入 [指派] 欄。您可以使用這些方式，在單一作業中指派使用者和群組的組合。若要完成指派程序，請按一下頁面右下角的核取按鈕。

![授權指派進度訊息](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

當群組被指派時，其成員會在 30 分鐘內繼承授權，但通常會在 1-2 分鐘內。

在 Azure AD 入口網站授權指派期間，可能會發生指派錯誤，但通常很少見。可能發生的指派錯誤僅限於：- 指派衝突 - 使用者先前已被指派一個與最新授權不相容的授權。在此情況下，若要指派新的授權，必須先移除前一個授權。- 超過可用的授權數目 - 當指派群組中的使用者數目超過可用的授權數目時，使用者的指派狀態將會反映因缺少授權的指派失敗。

###檢視已指派的授權

[授權] 索引標籤上會顯示已指派授權的摘要檢視，其中包括可用的授權、已指派的授權，以及下一個訂用帳戶週期事件。

![檢視已指派的授權數目](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

瀏覽到授權方案時，會看見已指派的使用者和群組的詳細清單，其中包括指派狀態和路徑 (直接或繼承自一或多個群組)。

![詳細顯示授權方案中已指派的授權](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

移除授權就和指派授權一樣簡單。如果使用者是直接被指派，或屬於某個被指派的群組，您可以選取授權類型、選取 [移除]、將使用者或群組加入移除清單，然後確認動作來移除授權。或者，您可以開啟授權類型，選取特定的使用者或群組，然後在命令列上點選 [移除]。若要結束使用者從群組繼承的授權，只要從群組移除使用者即可。

###延長試用期

透過 Office 365 入口網站可以自助延長客戶的試用期。客戶系統管理員可以瀏覽至 [Office 入口網站](https://portal.office.com/#Billing) (存取權取決於 Office 入口網站的權限)，然後選取您的 Azure AD Premium 試用版。按一下 [延長試用期] 連結，然後依照指示執行。您必須輸入信用卡，但不會被收費。

![在 Office 入口網站中延長授權試用期](./media/active-directory-licensing-what-is/extend_license_trial.png)

客戶也可以提交支援要求來要求延長試用期。客戶系統管理員可以瀏覽至 Office 入口網站[支援頁面](http://aka.ms/extendAADtrial) (存取權取決於 Office 支援頁面的權限)。在此頁面上的 [功能] 下方選取 [訂用帳戶和試用版]，以及在 [徵兆] 下方選取 [試用版問題]。最後，視情況輸入資訊

![使用支援要求延長授權試用期](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## 後續步驟

現在您可能已經準備好設定和使用某些 Azure AD Premium 功能。

- [自助式密碼重設](active-directory-manage-passwords.md)
- [自助式群組管理](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD Connect 健康情況](active-directory-aadconnect-health.md)
- [群組指派給應用程式](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](multi-factor-authentication.md)
- [直接購買 Azure AD Premium 授權](http://aka.ms/buyaadp)

<!---HONumber=August15_HO9-->