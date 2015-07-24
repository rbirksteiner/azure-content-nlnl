<properties 
	pageTitle="Azure 訂閱如何與 Azure AD 產生關聯" 
	description="有關登入 Microsoft Azure 及相關問題的主題，例如 Azure 訂用帳戶與 Azure AD 之間的關係。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
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

# Azure 訂閱如何與 Azure AD 產生關聯

本主題涵蓋有關登入 Microsoft Azure 及相關問題的資訊，例如 Azure 訂用帳戶與 Azure Active Directory (AD) 之間的關係。

## 您可以用來登入的帳戶
讓我們先著手處理可以用來登入的帳戶。共有兩種類型：Microsoft 帳戶 (先前稱為 Microsoft Live ID) 和工作或學校帳戶，也就是儲存在 Azure AD 的帳戶。

 Microsoft 帳戶 | Azure AD 帳戶
	------------- | -------------
由 Microsoft 執行的消費者識別系統 | 由 Microsoft 執行的商務識別系統
消費者導向 (例如 Hotmail 和 MSN) 的服務驗證 | 商務導向 (例如 Office 365) 的服務驗證
消費者會建立他們自己的 Microsoft 帳戶，例如當他們註冊電子郵件時 | 公司和組織會建立與管理他們自己的工作或學校帳戶
系統會建立識別並將它們儲存在 Microsoft 帳戶系統 | 系統會使用 Azure 或其他服務 (例如 Office 365) 來建立識別，並將它們儲存在指派給組織的 Azure AD 執行個體

雖然 Azure 原先只允許 Microsoft 帳戶使用者進行存取，但它現在可讓*這兩個*系統的使用者進行存取。此乃透過讓所有 Azure 屬性信任 Azure AD 進行驗證、讓 Azure AD 驗證組織使用者，以及透過建立同盟關係，讓 Azure AD 信任 Microsoft 帳戶消費者識別系統進行消費者驗證等方式達成。如此一來，Azure AD 便能驗證「來賓」Microsoft 帳戶，以及「原生」Azure AD 帳戶。

比方說，一位具有 Microsoft 帳戶的使用者登入 Azure 管理入口網站。

> [AZURE.NOTE]若要登入 Azure 管理入口網站，msmith@hotmail.com 必須具有 Azure 訂用帳戶。帳戶必須是服務管理員或訂用帳戶的共同管理員。

![][1]

因為這個 Hotmail 地址是消費者帳戶，而登入是透過 Microsoft 帳戶消費者識別系統進行驗證。Azure AD 識別系統信任 Microsoft 帳戶系統所執行的驗證，並將發行可存取 Azure 服務的權杖。

## Azure 訂用帳戶如何與 Azure AD 有關

每個 Azure 訂用帳戶都會與 Azure AD 執行個體有信任關係。這表示它信任該目錄來驗證使用者、服務和裝置。多個訂用帳戶可以信任相同的目錄，但是一個訂用帳戶只能信任一個目錄。您可以在 [設定] 索引標籤下看到訂用帳戶所信任的目錄。您可以[編輯訂用帳戶設定](https://msdn.microsoft.com/library/azure/dn584083.aspx)以變更其信任的目錄。

這個訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與所有其他 Azure 資源 (網站、資料庫等) 之間的關係，後者比較像是訂用帳戶的子資源。如果訂用帳戶已過期，則也會停止存取與該訂用帳戶相關聯的其他資源。但目錄會保留在 Azure 中，而且您可以將其他訂用帳戶與該目錄產生關聯，並繼續管理目錄使用者。

同樣地，在您的訂用帳戶中看到的 Azure AD 延伸模組與在 Azure 管理入口網站中的其他延伸模組有不同的運作方式。在管理入口網站中的其他延伸模組的範圍是 Azure 訂用帳戶。您在 AD 延伸模組中看到的內容不會因為訂用帳戶而異，因為它會根據登入使用者只顯示目錄。

所有使用者都有會對他們進行驗證的單一主目錄，但是他它們也可以是其他目錄中的來賓。在 AD 延伸模組中，您會看到您的使用者帳戶為其成員的每個目錄。您的帳戶不是其成員的任何目錄將不會出現。目錄可以為 Azure AD 中的工作或學校帳戶或 Microsoft 帳戶使用者發出權杖 (因為 Azure AD 已與 Microsoft 帳戶系統建立同盟)。

下圖顯示 Michael smith 在使用 Contoso 的工作帳戶註冊之後的訂用帳戶。

![][2]

## 如何管理訂用帳戶和目錄
Azure 訂用帳戶的系統管理角色會管理繫結至 Azure 訂用帳戶的資源。這些角色和管理訂用帳戶的最佳作法均涵蓋於[管理帳戶、訂用帳戶和系統管理角色](https://msdn.microsoft.com/library/azure/hh531793.aspx)。

根據預設，當您註冊時，您會被指派服務管理員角色。如果其他人必須使用相同的訂用帳戶來登入和存取服務，您可以將他們新增為共同管理員。服務管理員和共同管理員可以是來自 Azure 訂用帳戶相關聯目錄的 Microsoft 帳戶或工作或學校帳戶。

Azure AD 有一組不同的系統管理角色，可用來管理目錄和識別相關功能。例如，目錄的全域管理員可以將使用者和群組加入至目錄，或要求使用者的多因素驗證。建立目錄的使用者會被指派全域管理員角色，而且他們可以將系統管理員角色指派給其他使用者。

和訂用帳戶管理員一樣，Azure AD 系統管理角色可以是 Microsoft 帳戶或工作或學校帳戶。其他服務 (例如 Office 365 和 Microsoft Intune) 也會使用 Azure AD 系統管理角色。如需詳細資訊，請參閱[指派系統管理員角色](https://msdn.microsoft.com/library/azure/dn468213.aspx)。

但這裡的重點是，Azure 訂用帳戶管理員和 Azure AD 目錄管理員是兩個不同的概念。Azure 訂用帳戶管理員可以管理 Azure 中的資源，而且可以在管理入口網站中檢視 Active Directory 延伸模組 (因為管理入口網站是 Azure 資源)。目錄管理員可以管理目錄中的屬性。

使用者可以同時擔任這兩個角色，但這並非必要。使用者可以被指派目錄全域管理員角色，但不可被指派為 Azure 訂用帳戶的服務管理員或共同管理員。若不是訂用帳戶的管理員，這位使用者便無法登入管理入口網站。但是使用者可以使用其他工具 (例如 Azure AD PowerShell 或 Office 365 系統管理中心) 來執行目錄管理工作。

### 為什麼我無法使用目前的使用者帳戶來管理目錄？

有時候，使用者可能會在註冊 Azure 訂用帳戶之前，嘗試使用工作或學校帳戶來登入管理入口網站。在此情況下，使用者會收到該帳戶沒有訂用帳戶的訊息。此訊息將包含可開始免費試用訂用帳戶的連結。

在註冊免費試用之後，使用者會在管理入口網站中看到組織的目錄，但因為使用者不是目錄全域管理員，所以無法進行管理 (亦即，無法新增使用者，或編輯任何現有的使用者屬性)。訂用帳戶允許使用者使用管理入口網站，並查看 Active Directory 延伸模組，但必須有全域系統管理員的其他權限才能管理目錄。

## 使用您的工作或學校帳戶來管理使用 Microsoft 帳戶建立的 Azure 訂用帳戶

最佳做法是，您應該 [以組織身分註冊 Azure](sign-up-organization.md)，並使用工作或學校帳戶來管理 Azure 中的資源。因為發佈工作或學校帳戶的組織可以集中管理這些帳戶，所以優先採用工作或學校帳戶，它們提供比 Microsoft 帳戶還要多的功能，而且它們可直接由 Azure AD 進行驗證。相同的帳戶提供供企業和組織使用之其他 Microsoft 線上服務 (例如 Office 365 或 Microsoft Intune) 的存取權限。如果您已經有用來與其他屬性搭配的帳戶，您可能會想要使用該相同帳戶來與 Azure 搭配。您也已經有支援且要 Azure 訂用帳戶信任這些屬性的 Active Directory 執行個體。

管理工作或學校帳戶的方式也比管理 Microsoft 帳戶的方式還要多。例如，系統管理員可以重設工作或學校帳戶的密碼，或要求帳戶的多因素驗證。

在某些情況下，您可能需要一位組織使用者，能夠管理消費者 Microsoft 帳戶且與 Azure 訂用帳戶相關聯的資源。如需有關如何轉換成不同帳戶管理訂用帳戶或目錄的詳細資訊，請參閱[在 Azure 中管理 Office 365 訂用帳戶的目錄](#manage-the-directory-for-your-office-365-subscription-in-azure)。


## 當您使用公司電子郵件作為 Microsoft 帳戶時登入

如果在過去的某個時間點，您使用公司電子郵件作為使用者識別碼來建立消費者 Microsoft 帳戶，則您可能會看到一個頁面，要求您選取 Microsoft Azure 帳戶系統或 Microsoft 帳戶系統。

![][3]

您會有相同名稱的使用者帳戶，一個在 Azure AD 中，另一個在消費者 Microsoft 帳戶系統中。您應該挑選您要使用且與 Azure 訂用帳戶相關聯的帳戶。如果您收到錯誤訊息指出此使用者的訂用帳戶不存在，您很有可能只是選錯選項。登出然後再試一次。如需有關可能阻止登入的錯誤詳細資訊，請參閱[疑難排解「我們找不到與您帳戶相關聯的任何訂用帳戶」錯誤](https://social.msdn.microsoft.com/Forums/zh-tw/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement)。

## 在 Azure 中管理 Office 365 訂用帳戶的目錄。

假設您在註冊 Azure 之前已先註冊 Office 365。現在，您想要在管理入口網站中管理 Office 365 訂用帳戶的目錄。若要這樣做有兩種方式，視您是否已註冊 Azure 而定。

### 我沒有 Azure 訂用帳戶

在此案例中，您只需要使用您用來登入 Office 365 的相同工作或學校帳戶來[註冊 Azure](sign-up-organization.md) 即可。Office 365 帳戶的相關資訊便會填入 Azure 註冊表單。您的帳戶將會被指派訂用帳戶的服務管理員角色。

### 我沒有使用 Microsoft 帳戶的 Azure 訂用帳戶

如果您已使用工作或學校帳戶註冊 Office 365，然後使用 Microsoft 帳戶註冊 Azure，則您會有兩個目錄：一個可供工作或學校使用，一個可供註冊 Azure 時所建立的預設目錄使用。

若要在管理入口網站中管理這兩個目錄，請完成下列步驟。

> [AZURE.NOTE]只有當使用者使用 Microsoft 帳戶登入時，才可以完成下列步驟。如果使用者使用工作或學校帳戶登入，則 [**使用現有的目錄**] 選項無法使用，因為工作或學校帳戶只能透過其主目錄 (也就是儲存工作或學校帳戶，且由工作或學校所擁有的目錄) 進行驗證。

1. 使用 Microsoft 帳戶登入管理入口網站。
2. 依序按一下 [**新增**] > [**應用程式服務**] > [**Active Directory**] > [**目錄**] > [**自訂建立**]。
3. 按一下 [**使用現有的目錄**] 並核取 [**我已經準備好立即登出**]，然後按一下核取記號以完成動作。
4. 透過使用全域管理工作或學校目錄的帳戶來登入管理入口網站。
5. 當系統出現 [**搭配 Azure 使用 Contoso 目錄？**] 提示時，請按一下 [**繼續**]。
6. 按一下 [**立即登出**]。
7. 使用您的 Microsoft 帳戶重新登入管理入口網站。這兩個目錄都會出現在 Active Directory 延伸模組中。


## 接下來
[以組織身分註冊 Azure](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

 

<!---HONumber=62-->