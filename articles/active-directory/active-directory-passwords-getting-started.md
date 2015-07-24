<properties 
	pageTitle="開始使用：Azure AD 密碼管理 |Microsoft Azure" 
	description="讓使用者重設自己的密碼、探索密碼重設的必要條件，以及啟用密碼回寫以管理 Active Directory 中的內部部署密碼。" 
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

# 開始使用密碼管理
讓您的使用者管理他們自己的雲端 Azure Active Directory 或內部部署 Active Directory 密碼，只要採用幾個簡單的步驟。確定您符合幾個簡單的必要條件之後，您會在您知悉之前，對整個組織啟用密碼變更和重設。本文章將會逐步引導您完成下列概念：

* [**如何讓使用者重設其雲端 Azure Active Directory 密碼**](#enable-users-to-reset-their-azure-ad-passwords)
 - [自助式密碼重設必要條件](#prerequisites)
 - [步驟 1：設定密碼重設原則](#step-1-configure-password-reset-policy)
 - [步驟 2：為您的測試使用者加入連絡人資料](#step-2-add-contact-data-for-your-test-user)
 - [步驟 3：以使用者的身分重設您的密碼](#step-3-reset-your-azure-ad-password-as-a-user)
* [**如何讓使用者重設或變更其內部部署 Active Directory 密碼**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [密碼回寫必要條件](#writeback-prerequisites)
 - [步驟 1：下載最新版本的 Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [步驟 3：設定您的防火牆](#step-3-configure-your-firewall)
 - [步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [步驟 5：以使用者的身分重設您的 AD 密碼並且確認](#step-5-reset-your-ad-password-as-a-user)

## 讓使用者重設其 Azure AD 密碼
本節會逐步引導您對 AAD 雲端目錄啟用自助式密碼重設、註冊使用者進行自助式密碼重設，最後以使用者的身分執行自助式密碼重設測試。

- [自助式密碼重設必要條件](#prerequisites)
- [步驟 1：設定密碼重設原則](#step-1-configure-password-reset-policy)
- [步驟 2：為您的測試使用者加入連絡人資料](#step-2-add-contact-data-for-your-test-user)
- [步驟 3：以使用者的身分重設您的密碼](#step-3-reset-your-azure-ad-password-as-a-user)


###  必要條件
在您可以啟用及使用自助式密碼重設之前，您必須完成下列必要條件：

- 建立 AAD 租用戶。如需詳細資訊，請參閱[開始使用 Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
- 取得 Azure 訂用帳戶。如需詳細資訊，請參閱 [Azure AD 租用戶是什麼？](active-directory-administer.md#what-is-an-azure-ad-tenant)。
- 將 AAD 租用戶與 Azure 訂用帳戶產生關聯。如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure AD 產生關聯](https://msdn.microsoft.com/library/azure/dn629581.aspx)。
- 升級至 Azure AD Premium 或 Basic。如需詳細資訊，請參閱 [Azure Active Directory 版本](http://azure.microsoft.com/pricing/details/active-directory/)。

  >[AZURE.NOTE]若要啟用自助式密碼重設，您必須升級至 Azure AD Premium 或 Azure AD Basic。如需詳細資訊，請參閱「Azure Active Directory 版本」。這項資訊包括如何註冊 Azure AD Premium 或 Basic、如何啟動您的授權方案並啟動您的 Azure AD 存取權，以及如何將存取權指派給系統管理員和使用者帳戶的詳細指示。
  
- 在 AAD 目錄中建立至少一個系統管理員帳戶和一個使用者帳戶。
- 將 AAD Premium 或 Basic 授權指派給您所建立的系統管理員和使用者帳戶。

### 步驟 1：設定密碼重設原則
若要設定使用者密碼重設原則，請完成下列步驟：
 
1.	開啟您選擇的瀏覽器並瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)。
2.	在 [Azure 管理入口網站](https://manage.windowsazure.com)中，於左側導覽列上找到 [**Active Directory 延伸模組**]。

    ![][001]

3. 在 [**目錄**] 索引標籤底下，按一下您要設定使用者密碼重設原則的目錄，例如，Wingtip Toys。

    ![][002]

4.	按一下 [**設定**] 索引標籤。

    ![][003]

5.	在 [**設定**] 索引標籤底下，向下捲動至 [**使用者密碼重設原則**] 區段。這是您設定指定目錄的使用者密碼重設原則各個層面的位置。

    >[AZURE.NOTE]此**原則只適用於您的組織中的使用者，不適用於系統管理員**。基於安全性理由，Microsoft 會為系統管理員控制密碼重設原則。如果看不到此區段，請確定您已註冊 Azure Active Directory Premium 或 Basic，並且**指派授權**給設定此功能的系統管理員帳戶。

    ![][004]

6.	若要設定使用者密碼重設原則，滑動 [**使用者啟用密碼重設**] 以切換至 [**是**] 設定。這會顯示更多控制項，讓您設定這項功能如何在您的目錄中運作。放心地自訂您覺得適合的密碼重設。如果您想要深入了解關於每一項密碼重設原則控制項所執行的項目，請參閱[自訂：Azure AD 密碼管理](active-directory-passwords-customize)。

    ![][005]

7.	視需要為您的租用戶設定使用者密碼重設原則之後，請按一下畫面底部的 [**儲存**] 按鈕。

  >[AZURE.NOTE]建議使用兩個挑戰使用者密碼重設原則，這樣您就能看到在最複雜的情況下，功能如何運作。

  ![][006]

### 步驟 2：為您的測試使用者加入連絡人資料
您有幾個選項可用於指定要用於密碼重設的組織中使用者資料。

-	在 [Azure 管理入口網站](https://manage.windowsazure.com)或 [Office 365 系統管理入口網站](https://portal.microsoftonline.com)編輯使用者
-	使用 AAD Connect 從內部部署 Active Directory 網域將使用者屬性同步處理至 Azure AD
-	使用 Windows PowerShell 以編輯使用者屬性
-	引導使用者至位於 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) 的註冊入口網站，讓他們自行註冊資料
-	當使用者登入位於 [http://myapps.microsoft.com](http://myapps.microsoft.com) 的存取面板時，要求使用者註冊密碼重設，方法是將 [**要求使用者在登入存取面板時註冊**] SSPR 組態選項設定為 [**是**]。

如果您想要深入了解密碼重設使用哪些資料，以及此資料的任何格式需求，請參閱[密碼重設使用哪些資料？](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)。

#### 透過使用者註冊入口網站加入使用者連絡人資料
1.	若要使用密碼重設註冊入口網站，您必須提供組織中的使用者這個頁面的連結 ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup))，或開啟選項要求使用者自動註冊。一旦使用者按一下此連結，系統會要求他們使用其組織帳戶登入。完成後，他們會看到下列頁面：

    ![][007]

2.	在這裡，使用者可以提供並確認其行動電話、備用電子郵件地址或安全性問題。這是驗證行動電話的外觀。

    ![][008]

3.	使用者指定這項資訊之後，頁面會更新以指出該資訊有效 (下方已模糊處理)。按一下 [**完成**] 或 [**取消**] 按鈕，使用者會被帶到存取面板。

    ![][009]

4.	一旦使用者驗證這兩項資訊，他或她的設定檔將會以他或她所提供的資料更新。在此範例中，[**辦公室電話**] 數目已手動指定，因此使用者也可以使用做為連絡方法，以重設其密碼。

    ![][010]

### 步驟 3：以使用者的身分重設您的 Azure AD 密碼
您已經設定使用者重設原則，並指定使用者的連絡人詳細資料，這位使用者可以執行自助式密碼重設。

#### 執行自助式密碼重設
1.	如果您移至像是 [**portal.microsoftonline.com**](http://portal.microsoftonline.com) 的網站，您會看到如下的登入畫面。按一下 [**無法存取您的帳戶？**] 連結，以測試密碼重設 UI。

    ![][011]

2.	按一下 [**無法存取您的帳戶？**] 之後，您會被帶往新頁面，詢問您要重設密碼的 [**使用者識別碼**]。在這裡輸入您的測試 [**使用者識別碼**]，通過 captcha，然後按 [**下一步**]。

    ![][012]

3.	因為在此案例中，使用者已指定 [**辦公室電話**]、[**行動電話**] 和 [**備用電子郵件**]，所以您會看到系統給予他或她所有項目做為選項，以通過第一項挑戰。

    ![][013]

4.	在此情況下，首先選擇 [**呼叫**] [**辦公室電話**]。請注意，當選取以電話為基礎的方法時，系統會要求使用者 [**確認電話號碼**]，然後他們才可以重設其密碼。這是為了防止惡意的個人濫發垃圾郵件給組織中使用者的電話號碼。

    ![][014]

5.	一旦使用者確認其電話號碼，按一下 [通話] 會顯示撥號畫面，且使用者的電話響鈴。使用者接聽電話之後，會播放訊息，指出 [**使用者應該按 "#"**] 以確認他或她的帳戶。按下此按鍵會自動確認使用者完成第一項挑戰，並前進到第二個驗證步驟的 UI。

    ![][015]

6.	一旦您通過第一項挑戰，UI 會自動更新，從使用者擁有的選擇清單中移除。在此情況下，因為您先使用您的 [**辦公室電話**]，所以還剩下 [**行動電話**] 和 [**備用電子郵件**] 是有效的選項，可以做為第二個驗證步驟的挑戰。按一下 [**寄電子郵件到我的備用電子郵件地址**] 電子郵件選項。完成這個動作之後，按下電子郵件將會以電子郵件將檔案寄送到備用電子郵件。

    ![][016]

7.	以下是使用者會看到的電子郵件範例 – 請注意租用戶商標：

    ![][017]

8.	一旦電子郵件送達時，頁面會更新，而且您可以在如下所示的輸入方塊中輸入電子郵件中找到的驗證。輸入適當的代碼之後，[下一步] 按鈕會亮起，您可以通過第二個驗證步驟。

    ![][018]

9.	一旦您符合組織原則的需求，您就可以選擇新密碼。密碼是根據其符合 AAD「強式」密碼需求而經過驗證的 (請參閱 [Azure AD 中的密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx))，強度驗證器會出現，向使用者表示輸入的密碼是否符合該原則。

    ![][019]

10.	一旦您提供符合組織原則的相符密碼，您的密碼就會重設，您可以立即使用您的新密碼登入。

    ![][020]


## 讓使用者重設或變更其 AD 密碼

本章節會引導您透過設定密碼重設，將密碼回寫至內部部署 Active Directory。

- [密碼回寫必要條件](#writeback-prerequisites)
- [步驟 1：下載最新版本的 Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)
- [步驟 3：設定您的防火牆](#step-3-configure-your-firewall)
- [步驟 4：設定適當的權限](#step-4-set-up-the-appropriate-active-directory-permissions)
- [步驟 5：以使用者的身分重設您的 AD 密碼並且確認](#step-5-reset-your-ad-password-as-a-user)


### 回寫必要條件
在您可以啟用及使用密碼回寫之前，您必須確定完成下列必要條件：

- 您有 Azure AD 租用戶且啟用 Azure AD Premium。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
- 已經在您的租用戶中設定和啟用密碼重設。如需詳細資訊，請參閱[讓使用者重設其 Azure AD 密碼](#enable-users-to-reset-their-azure-ad-passwords)
- 您必須擁有至少一個系統管理員帳戶和一個測試使用者帳戶，且具有 Azure AD Premium 授權，可讓您用來測試這項功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

  >[AZURE.NOTE]請確定您用來啟用密碼回寫的系統管理員帳戶是雲端系統管理員帳戶 (在 Azure AD 中建立)，不是同盟帳戶 (在內部部署 AD 中建立並同步處理至 Azure AD)。
  
- 您擁有單一或多樹系 AD 內部部署執行 Windows Server 2008、Windows Server 2008 R2、Windows Server 2012 或 Windows Server 2012 R2，且安裝最新的 Service Pack。

  >[AZURE.NOTE]如果您執行舊版 Windows Server 2008 或 2008 R2，您仍然可以使用此功能，但是需要先[下載及安裝 KB 2386717](https://support.microsoft.com/kb/2386717)，才能在雲端中強制執行您的本機 AD 密碼原則。
  
- 您已安裝 Azure AD Connect 工具，且已備妥 AD 環境進行同步處理至雲端。如需詳細資訊，請參閱[在雲端中使用內部部署身分識別基礎結構](active-directory-aadconnect.md)
- 如果您使用 DirSync，您必須確定您組織的防火牆設定為封鎖輸出連線和解除封鎖 **TCP 連接埠 828 或 818**，才能啟用及使用密碼回寫。如果您使用 Azure AD Sync 或 Azure AD Connect，此步驟並非必要，因為只有 **TCP 443** 輸出 (在某些情況下 **TCP 9350-9354**) 必須開啟。

  >[AZURE.NOTE]我們強烈建議使用 Azure AD Sync 或 DirSync 工具的任何人升級至最新版的 Azure AD Connect，以確保擁有最佳使用經驗和發行的新功能。
  

### 步驟 1：下載最新版本的 Azure AD Connect
密碼回寫可以在 Azure AD Connect 版本或具有版本號碼 **1.0.0419.0911** 或更高版本的 Azure AD Sync 工具中使用。具有自動帳戶解除鎖定的密碼回寫可以在 Azure AD Connect 版本或具有版本號碼 **1.0.0485.0222** 或更高版本的 Azure AD Sync 工具中使用。如果您執行較舊的版本，請至少升級至此版本，再繼續作業。[按一下這裡以下載最新版本的 Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect)。

#### 檢查 Azure AD Sync 的版本
1.	瀏覽至 **%ProgramFiles%\Azure Active Directory Sync**。2.	尋找 **ConfigWizard.exe** 可執行檔。
3.	以滑鼠右鍵按一下可執行檔，然後從內容功能表選取 [**屬性**] 選項。
4.	按一下 [**詳細資料**] 索引標籤。
5.	尋找 [**檔案版本**] 欄位。

    ![][021]

如果此版本號碼大於或等於 **1.0.0419.0911**，或您安裝 Azure AD Connect，您可以跳到[步驟 2：在 Azure AD Connect 中透過 UI 或 powershell 啟用密碼回寫並且確認](#step-2-enable-password-writeback-in-azure-ad-connect)。

 >[AZURE.NOTE]如果這是您第一次安裝 Azure AD Connect 工具，建議您依照一些最佳作法以準備環境進行目錄同步處理。安裝 Azure AD Connect 工具之前，您必須在 [Office 365 系統管理入口網站](https://portal.microsoftonline.com)或 [Azure 管理入口網站](https://manage.windowsazure.com)中啟動目錄同步作業。如需詳細資訊，請參閱[管理 Azure AD Connect](active-directory-aadconnect-whats-next.md)。


### 步驟 2：在 Azure AD Connect 中啟用密碼回寫
您已下載 Azure AD Connect 工具，已準備就緒可以啟用密碼回寫。您可以使用下列兩種方式之一來執行這個動作。您可以在 Azure AD Connect 安裝精靈的選用功能畫面中啟用密碼回寫，或者您可以透過 Windows PowerShell 啟用它。

#### 在組態精靈中啟用密碼回寫
1.	在您的 [**目錄同步處理電腦**] 中，開啟 [**Azure AD Connect**] 組態精靈。
2.	按一下各個步驟，直到您到達 [**選用功能**] 組態畫面。
3.	勾選 [**密碼回寫**] 選項。

    ![][022]

4.	完成精靈，最後一頁將會摘要說明變更，並且會包含密碼回寫組態變更。

> [AZURE.NOTE]您可以隨時停用密碼回寫，方法是重新執行此精靈，並且取消選取此功能，或是將 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤的 [**使用者密碼重設原則**] 中的 [**將密碼回寫至內部部署目錄**] 設定設為 [**否**]。如需有關自訂您的密碼重設體驗的詳細資訊，請參閱[自訂：Azure AD 密碼管理](active-directory-passwords-customize.md)。

#### 使用 Windows PowerShell 啟用密碼回寫
1.	在您的 [**目錄同步處理電腦**] 中，開啟新的 [**提升權限的 Windows PowerShell 視窗**]。
2.	如果尚未載入模組，輸入 `Import-Module ADSync` 命令以將 Azure AD Connect Cmdlet 載入到您目前的工作階段。
3.	取得您的系統中的 AAD 連接器清單，方法是執行 `Get-ADSyncConnector` Cmdlet，並將結果儲存在 `$aadConnectorName`
4.	若要取得目前連接器的回寫的目前狀態，請執行下列 Cmdlet：`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	執行下列 Cmdlet，以啟用密碼回寫：`Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]如果系統提示輸入認證，請確定您為 AzureADCredential 指定的系統管理員帳戶是**雲端系統管理員帳戶 (在 Azure AD 中建立)**，不是同盟帳戶 (在內部部署 AD 中建立並且同步處理至 Azure AD。[AZURE.NOTE]您可以透過 PowerShell 停用密碼回寫，方法是重複上述的相同指示，但是在步驟中傳遞 `$false`，或者將 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤的 [**使用者密碼重設原則**] 區段的 [**將密碼回寫至內部部署目錄**] 設為 [**否**]。

#### 確認組態是否成功
一旦組態成功，您會在 Windows PowerShell 視窗中看到密碼重設回寫已啟用的訊息，或者在組態 UI 中看到成功訊息。

您也可以藉由開啟 [事件檢視器]、瀏覽至應用程式事件記錄檔，並且從來源 **PasswordResetService** 尋找事件 **31005 - OnboardingEventSuccess**，以確認服務是否正確安裝。

  ![][023]

### 步驟 3：設定您的防火牆
您已在 Azure AD Connect 工具中啟用密碼回寫之後，您必須確定服務可以連線至雲端。

1.	一旦安裝完成之後，如果您在環境中封鎖不明的輸出連線，您也必須將下列規則加入至您的防火牆。請確定重新啟動 AAD Connect 電腦，再進行這些變更：
   - 允許透過連接埠 443 TCP 的輸出連線
   - 允許對 https://ssprsbprodncu-sb.accesscontrol.windows.net/的輸出連線 
   - 當使用 proxy 或有一般連線問題時，允許透過連接埠 9350-9534 TCP 的輸出連線

### 步驟 4：設定適當的 Active Directory 權限
對於包含使用者 (其密碼將會重設) 的每個樹系，如果 X 為組態精靈 (初始組態期間) 中針對該樹系指定的帳戶，則必須為 X 指定**重設密碼**、**變更密碼**、`lockoutTime` 的 **寫入權限** 和 `pwdLastSet` 的 **寫入權限**、該樹系中每個網域之根物件的擴充權限。權限應該標示為由所有使用者物件繼承。

設定這些權限將會允許每個樹系的 MA 服務帳戶代表該樹系內的使用者帳戶管理密碼。如果您沒有指定這些權限，則即使回寫看起來設定正確，使用者在嘗試從雲端管理其內部部署密碼時還是會遇到錯誤。以下是您如何使用 [**Active Directory 使用者和電腦**] 管理嵌入式管理單元以進行作業的詳細步驟：

>[AZURE.NOTE]最多可能需要一小時讓這些權限複寫至您的目錄中的所有物件。

#### 設定正確權限以執行回寫

1.	以具有適當網域管理權限的帳戶開啟 [**Active Directory 使用者和電腦**]。
2.	在 [**檢視功能表**] 選項中，確定 [**進階功能**] 已開啟。
3.	在左面板中，以滑鼠右鍵按一下代表網域根目錄的物件。
4.	按一下 [**安全性**] 索引標籤。
5.	然後按一下 [**進階**]。

    ![][024]

6.	按一下 [**權限**] 索引標籤上的 [**新增**]。

    ![][025]

7.	選取您想要授與權限的帳戶 (這是與設定該樹系同步處理時所指定的相同帳戶)。
8.	在最上層的下拉式清單中選取 [**下階使用者物件**]。
9.	在顯示的 [**權限項目**] 對話方塊中，勾選 [**重設密碼**]、[**變更密碼**]、[`lockoutTime` 的**寫入權限**] 和 [`pwdLastSet` 的**寫入權限**] 的方塊。

    ![][026] ![][027] ![][028]

10.	然後在所有開啟的對話方塊中按一下 [**套用/確定**]。

### 步驟 5：以使用者的身分重設您的 AD 密碼
現在已啟用密碼回寫，您可以測試它是否正常運作，方法是對已同步處理至您的雲端租用戶的使用者帳戶重設密碼。
 
#### 確認密碼回寫是否正常運作
1.	瀏覽至 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)，或前往任何組織識別碼登入畫面，然後按一下 [**無法存取您的帳戶？**] 連結。

    ![][029]

2.	您現在應該會看到新的頁面，向您要求提供您想要重設密碼的使用者識別碼。輸入您的測試使用者識別碼，然後繼續完成密碼重設流程。
3.	重設密碼之後，您會看到如下的畫面。這表示您已成功在您的內部部署和/或雲端目錄中重設您的密碼。

    ![][030]

4.	若要確認作業是否成功或診斷任何錯誤，請移至您的 [**目錄同步處理電腦**]、開啟 [**事件檢視器**]、瀏覽至 [**應用程式事件記錄檔**]，並針對您的測試使用者從來源 **PasswordResetService** 尋找事件 **31002 - PasswordResetSuccess**。

    ![][031]


<br/> <br/> <br/>

**其他資源**


* [密碼管理是什麼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"

 

<!---HONumber=62-->