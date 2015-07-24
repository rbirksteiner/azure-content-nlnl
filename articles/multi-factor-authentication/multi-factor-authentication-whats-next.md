<properties 
	pageTitle="Azure Multi-Factor Authentication - 後續步驟" 
	description="這是描述 Azure Multi-factor Authentication 後續步驟的 MFA 頁面。其內容包括報告、詐騙警示、一次性略過、自訂語音訊息、快取、信任的 IP 及應用程式密碼。" 
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

# 設定 Azure Multi-Factor Authentication

既然您已啟動並執行 Azure Multi-Factor Authentication，下文將協助您進行管理。本文章涵蓋各種主題，可讓您充分發揮 Azure Multi-Factor Authentication 的功效。請注意，並非所有版本的 Azure Multi-Factor Authentication 均提供這些功能。

功能| 說明| 涵蓋的項目
:------------- | :------------- | :------------- | 
[詐騙警示](#fraud-alert)|您可以配置及設定詐騙警示，讓使用者得以針對存取其資源的詐騙嘗試提出報告。|如何設定、配置及提報詐騙活動
[一次性略過](#one-time-bypass) |一次性略過可讓使用者「略過」Multi-Factor Authentication 來通過驗證一次。|如何設定及配置一次性略過
[自訂語音訊息](#custom-voice-messages) |自訂語音訊息可讓您將自己的錄音或問候語用於 Multi-Factor Authentication。 |如何設定及配置自訂問候語和訊息
[快取](#caching)|您可以利用快取來設定一段特定的時間，讓後續的驗證嘗試自動成功。 |如何設定及配置驗證快取。
[信任的 IP](#trusted-ips)|信任的 IP 是 Multi-Factor Authentication 的功能，它賦予受管理或同盟租用戶管理員，讓從公司近端內部網路登入之使用者略過 Multi-Factor Authentication 的能力。|配置及設定要免除 Multi-Factor Authentication 的 IP 位址	
[應用程式密碼](#app-passwords)|應用程式密碼允許非 MFA 感知應用程式略過 Multi-Factor Authentication 並繼續運作。|應用程式密碼的相關資訊。
[暫停對已記住之裝置和瀏覽器進行 Multi-Factor Authentication (公開預覽)](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|可讓您在使用者使用 MFA 成功登入後的設定天數內暫停 MFA。|啟用此功能及設定的天數的相關資訊。




## 詐騙警示
您可以配置及設定詐騙警示，讓使用者得以針對存取其資源的詐騙嘗試提出報告。使用者可以透過行動應用程式或電話來報告詐騙活動。

### 設定及配置詐騙警示


1. 登入 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。
4. 如果您有一個以上的 Multi-Factor Auth Provider，請選取要啟用詐騙警示的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。
5. 在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [設定]。
6. 在 [詐騙警示] 區段下，核取 [允許使用者提交詐騙警示]。
7. 如果您想要在使用者提報詐騙活動時封鎖使用者，請核取 [提報詐騙時封鎖使用者]。
8. 於 [在開始問候期間提報詐騙的代碼] 下方，輸入可在通話驗證期間使用的數字代碼。如果使用者輸入此代碼而不是 # 符號，系統將會提報詐騙警示。
9. 在底部按一下 [儲存]。

<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### 提報詐騙警示
提報詐騙活動的方法有兩種。使用者可以透過或行動應用程式或電話來提報。

### 利用行動應用程式提報詐騙警示
<ol>
<li>當系統將驗證傳送到電話時，請按一下，Multi-Factor Authentication 應用程式隨即會啟動。</li>
<li>若要提報詐騙活動，請按一下 [取消及提報詐騙]。指出系統將會通知組織 IT 支援員工的方塊</li> 隨即會出現。按一下 [提報詐騙]。
<li>在應用程式中，按一下 [關閉]。</li></ol>

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### 利用電話提報詐騙警示
<ol>
<li>當電話響起驗證來電時，請逕行接聽。</li>
<li>若要提報詐騙活動，請透過電話輸入設定為提報詐騙的對應代碼，然後輸入 # 符號。系統會通知您詐騙警示已提交。</li>
<li>結束通話。</li></ol>

### 檢視詐騙報告

1. 登入 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。
4. 如果您有一個以上的 Multi-Factor Auth Provider，請選取要檢視詐騙警示報告的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。
5. 在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [檢視報告] 下方的 [詐騙警示]。
6. 在報告中指定要檢視的日期範圍。您也可以指定任何特定的使用者名稱、電話號碼和使用者狀態。
7. 按一下 [執行]。這會顯示與以下範例相似的報告。如果您想要匯出報告，也可以按一下 [匯出至 CSV]。

## 一次性略過

一次性略過可讓使用者「略過」Multi-Factor Authentication 來通過驗證一次。略過的效力是暫時的，因此會在指定的秒數過後到期。在行動應用程式或電話無法接收通知或來電的情況下，您可以啟用一次性略過，讓使用者能夠存取所需的資源。

### 建立一次性略過

<ol>
<li>登入 [http://azure.microsoft.com](http://azure.microsoft.com)</li>
<li>在左側選取 [Active Directory]。</li>
<li>在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。</li>
<li>如果您有一個以上的 Multi-Factor Auth Provider，請針對要建立一次性略過的使用者選取與目錄相關的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。</li>
<li>在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [使用者管理] 下方的 [設定]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create1.png)</center>

<li>在 [一次性略過] 頁面中，按一下 [新增一次性略過]。</li>
<li>輸入使用者的使用者名稱、略過的持續秒數、略過的原因，然後按一下 [略過]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create2.png)</center>

<li>此時，使用者必須在一次性略過到期之前登入。</li>

### 檢視一次性略過報告

1. 登入 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。
4. 如果您有一個以上的 Multi-Factor Auth Provider，請選取要檢視詐騙警示報告的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。
5. 在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [檢視報告] 下方的 [一次性略過]。
6. 在報告中指定要檢視的日期範圍。您也可以指定任何特定的使用者名稱、電話號碼和使用者狀態。
7. 按一下 [執行]。這會顯示與以下範例相似的報告。如果您想要匯出報告，也可以按一下 [匯出至 CSV]。

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## 自訂語音訊息

自訂語音訊息可讓您將自己的錄音或問候語用於 Multi-Factor Authentication。您可以在 Microsoft 錄音之外額外使用自訂語音訊息，也可以取代 Microsoft 的錄音。

在開始之前，請注意下列項目：

- 目前支援的檔案格式為 .wav 和 .mp3。
- 檔案大小限制為 5MB。
- 對於驗證訊息，建議您將訊息長度維持在 20 秒以內。長度大於 20 秒的訊息可能會導致驗證失敗，因為使用者可能無法在訊息結束之前回應，因此驗證會逾時。



### 在 Azure Multi-factor Authentication 中設定自訂語音訊息
<ol>
<li>使用其中一種支援的檔案格式建立自訂語音訊息。請參閱下列自訂語音訊息建議。</li>
<li>登入 [http://azure.microsoft.com](http://azure.microsoft.com)</li>
<li>在左側選取 [Active Directory]。</li>
<li>在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。</li>
<li>如果您有一個以上的 Multi-Factor Auth Provider，請選取要設定自訂語音訊息的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。</li>
<li>在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [語音訊息]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)</center>

<li>在 [語音訊息] 區段下方，按一下 [新增語音訊息]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)</center>

<li>在 [設定：新增語音訊息] 頁面上，按一下 [管理聲音檔]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)</center>

<li>在 [設定：聲音檔] 頁面上，按一下 [上傳聲音檔]。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)</center>

<li>在 [設定：上傳聲音檔] 上，按一下 [瀏覽] 以瀏覽至您的語音訊息，然後按一下 [開啟]。</li>
<li>新增描述，然後按一下 [上傳]。</li>
<li>完成之後，您會看到一則訊息，指出檔案已成功上傳。</li>
<li>在左側按一下 [語音訊息]。</li>
<li>在 [語音訊息] 區段下方，按一下 [新增語音訊息]。</li>
<li>從 [語言] 下拉式清單選取語言。</li>
<li>如果此訊息僅適用於特定應用程式，請在 [應用程式] 方塊中指定。</li>
<li>從 [訊息類型] 選取新自訂訊息要覆寫的訊息類型。</li>
<li>從 [聲音檔] 下拉式清單選取聲音檔。</li>
<li>按一下 [建立]。您會看到一則訊息，指出語音訊息已成功建立。</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>


## Azure Multi-Factor Authentication 中的快取

您可以利用快取來設定一段特定的時間，讓後續的驗證嘗試自動成功。如果使用者在這段時間內進行驗證，便不需要等候電話或簡訊。



### 在 Azure Multi-Factor Authentication 中設定快取
<ol>

1. 登入 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。
4. 如果您有一個以上的 Multi-Factor Auth Provider，請選取要啟用詐騙警示的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。
5. 在 Azure Multi-factor Authentication 管理入口網站的左側，按一下 [快取]。
6. 在 [設定快取] 頁面上，按一下 [新增快取]。
7. 選取快取類型和快取秒數。按一下 [建立]。


<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## 信任的 IP

信任的 IP 是 Multi-Factor Authentication 的功能，它賦予受管理或同盟租用戶管理員，讓從公司近端內部網路登入之使用者略過 Multi-Factor Authentication 的能力。這些功能適用於擁有 Azure AD Premium、Enterprise Mobility Suite 或 Azure Multi-Factor Authentication 授權的 Azure AD 租用戶。

 
Azure AD 租用戶類型| 可用的信任 IP 選項
:------------- | :------------- | 
受管理|特定 IP 位址範圍 – 對於從公司內部網路登入的使用者，管理員可以指定要略過 Multi-Factor Authentication 的 IP 位址範圍。
同盟|<li>所有同盟使用者 - 所有從組織內部登入的同盟使用者，都能使用 AD FS 發行的宣告略過 Multi-Factor Authentication。</li><li>特定 IP 位址範圍 – 對於從公司內部網路登入的使用者，管理員可以指定要略過 Multi-Factor Authentication 的 IP 位址範圍。

此略過機制只適用於公司內部網路。所以如果您只選取所有同盟使用者，當使用者從公司的內部網路之外登入時，即使該使用者出示 AD FS 宣告，仍必須使用 Multi-Factor Authentication 進行驗證。下表描述在啟用信任 IP 的情況下，公司網路內部和外部何時需要 Multi-Factor Authentication 和應用程式密碼。


|信任的 IP 已啟用| 信任的 IP 已提用
:------------- | :------------- | :------------- | 
公司網路內部|瀏覽器流量不需要 Multi-Factor Authentication。|瀏覽器流量需要 Multi-Factor Authentication。
|對於豐富型用戶端應用程式，如果使用者尚未建立任何應用程式密碼，使用一般密碼即可。一旦建立應用程式密碼之後，使用者就必須使用應用程式密碼。|豐富型用戶端應用程式需要應用程式密碼
公司網路外部|瀏覽器流量需要 Multi-Factor Authentication。|瀏覽器流量需要 Multi-Factor Authentication。
|豐富型用戶端應用程式需要應用程式密碼。|豐富型用戶端應用程式需要應用程式密碼。

### 啟用信任的 IP

 
<ol>
<li>登入 Azure 管理入口網站。</li>
<li>在左側按一下 [Active Directory]。</li>
<li>在 [目錄] 下方，按一下要設定信任 IP 登入的目錄。</li>
<li>在您選取的目錄上，按一下 [設定]。</li>
<li>在 Multi-Factor Authentication 區段中，按一下 [管理服務設定]。</li>
<li>在 [服務設定] 頁面之 [信任的 IP] 下方，選取以下任一項：<ul> <li>對於來自內部網路之同盟使用者所發出的要求 – 所有從公司網路登入同盟使用者，都能使用 AD FS 發行的宣告略過 Multi-Factor Authentication。

<li>對於來自特定公開 IP 範圍的要求 – 使用 CIDR 表示法在提供的方塊中輸入 IP 位址。例如：xxx.xxx.xxx.0/24 代表介於 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 之範圍內的 IP 位址；xxx.xxx.xxx.xxx/32 代表一個 IP 位址。您最多可以輸入 12 個 IP 位址範圍。</li></ul>


<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>


<li>按一下 [儲存]。</li>
<li>套用更新之後，請按一下 [關閉]。</li>
## 應用程式密碼

在 Office 2010 或更舊版本和 Apple Mail 等某些應用程式中，您無法使用 Multi-Factor Authentication。若要使用這些應用程式，您需要使用「應用程式密碼」來取代傳統的密碼。應用程式密碼可讓應用程式略過 Multi-Factor Authentication 並繼續運作。

>[AZURE.NOTE]適用於 Office 2013 用戶端的新式驗證
>
> Office 2013 用戶端 (包括 Outlook) 現在支援新的驗證通訊協定，而且可以啟用 Multi-Factor Authentication 支援。這表示一旦啟用後，應用程式密碼就不需要使用於 Office 2013 用戶端。如需詳細資訊，請參閱[發表的 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

### 應用程式密碼重要須知

以下清單是應用程式密碼的重要須知。

驗證體驗|以瀏覽器為基礎的應用程式|非以瀏覽器為基礎的應用程式
:------------- | :------------- | :------------- 
|<ul><li>驗證的第一個要素會在內部部署</li><li>第二個要素是由雲端識別執行的電話式方法。</li>|<ul><li>管理員和使用者可以使用應用程式密碼來登入。

- 使用者可以擁有多個應用程式密碼，不過這樣會增加遭竊的機率。因為應用程式密碼很難記住，導致使用者傾向於將它們書寫下來。我們不建議且不鼓勵這種做法，因為只有一個要素需要使用應用程式密碼登入。
- 在內部部署案例中會快取密碼並予以使用的應用程式可能會開始失敗，因為應用程式密碼無法在組織識別碼之外辨識。將 Exchange 電子郵件存放在內部部署設施，但是將封存郵件存放在雲端即是一例。同樣的密碼無法適用於兩者。
- 實際的密碼會自動產生，而不是由使用者提供。這是因為攻擊者比較難以猜中自動產生的密碼，因此比較安全。
- 每位使用者的密碼目前以 40 組為限。系統會提示您刪除某一個現有的應用程式密碼，才能再建立新密碼。


### 應用程式密碼的命名指引
建議您在應用程式密碼名稱中反映出要使用密碼的裝置。例如，如果膝上型電腦擁有 Outlook、Word 及 Excel 之類的非瀏覽器應用程式，您只需要建立一個名為 Laptop 的應用程式密碼，並將該應用程式密碼用於前述所有應用程式中。雖然您可以針對以上所有應用程式建立不同的密碼，不過我們不建議這種做法。建議的方法是每部裝置使用一個應用程式密碼。


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### 同盟 (SSO) 應用程式密碼
Azure AD 支援與內部部署 Windows Server Active Directory 網域服務 (AD DS) 同盟。如果您的組織與 Azure AD 同盟 (SSO)，而且您想要使用 Azure Multi-Factor Authentication，以下是在使用應用程式密碼時應注意的重要資訊。這些資訊只適用於同盟 (SSO) 客戶。

- 應用程式密碼由 Azure AD 驗證，因此會略過同盟。唯有在設定應用程式密碼時才能主動使用同盟。
- 對於同盟 (SSO) 使用者，我們不會像被動流量一項尋求識別提供者 (IdP)。密碼會儲存在組織識別碼中。如果使用者離開公司，這些資訊必須使用 DirSync 即時流向組織識別碼。帳戶停用/刪除最長可能需要 3 個小時才能完成同步處理，導致 Azure AD 中的應用程式密碼停用/刪除延遲。
- 應用程式密碼不會遵守內部部署用戶端存取控制設定
- 應用程式密碼不適用內部部署驗證記錄 / 稽核功能
- Microsoft Lync 2013 用戶端需要更多使用者教育。如需必要步驟，請參閱＜如何將您電子郵件中的密碼變更為應用程式密碼＞。
- 某些進階架構設計在使用 Multi-Factor Authentication 時，可能需要搭配使用組織使用者名稱和密碼及應用程式密碼，須視驗證的位置而定。對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。

例如，假設您的架構由以下項目組成：

- 您要讓內部部署 Active Directory 執行個體與 Azure AD 同盟
- 您正在使用 Exchange Online
- 您只在內部使用 Lync
- 您正在使用 Azure Multi-Factor Authentication


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 在這些情況下，您必須執行下列作業：

- 在登入 Lync 時，使用組織使用者名稱和密碼。
- 在嘗試透過連接 Exchange online 的 Outlook 用戶端存取通訊錄時，使用應用程式密碼。

### 允許建立應用程式密碼
根據預設，使用者無法建立應用程式密碼。這項功能必須經過啟用。若要允許使用者建立應用程式密碼，請使用下列程序。

#### 允許使用者建立應用程式密碼



1. 登入 Azure 管理入口網站。
2. 在左側按一下 [Active Directory]。
3. 在 [目錄] 下方，針對要啟用的使用者按一下目錄。
4. 在頂端按一下 [使用者]。
5. 在頁面底部，按一下 [管理 Multi-Factor Auth]這會開啟 Multi-Factor Authentication 頁面。
6. 在 Multi-Factor Authentication 頁面的頂端，按一下 [服務設定]。
7. 確認 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式] 旁的選項按鈕已選取。

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

### 建立應用程式密碼
使用者可以在一開始的註冊期間建立應用程式密碼。他們可以在註冊程序的結尾選擇建立應用程式密碼。

此外，使用者日後也可以藉由在 Azure 入口網站和 Office 365 入口網站中變更設定來建立應用程式密碼。

### 在 Office 365 入口網站中建立應用程式密碼
--------------------------------------------------------------------------------


1. 登入 Office 365 入口網站
2. 在右上角選取設定 Widget
3. 在左側選取 [其他安全性驗證]
4. 在右側，選取 [**更新用於帳戶安全性的電話號碼**]
5. 在 proofup 頁面的頂端，選取應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按 [**下一步**]
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### 在 Azure 入口網站中建立應用程式密碼
--------------------------------------------------------------------------------
1. 登入 Azure 管理入口網站
3. 在頂端，以滑鼠右鍵按一下您的使用者名稱並選取 [其他安全性驗證]。
5. 在 proofup 頁面的頂端，選取應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按 [**下一步**]
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### 在沒有 Office 365 或 Azure 訂用帳戶的情況下建立應用程式密碼
--------------------------------------------------------------------------------
1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. 在頂端，選取 [設定檔]。
3. 按一下您的使用者名稱並選取 [其他安全性驗證]。
5. 在 proofup 頁面的頂端，選取應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按 [**下一步**]
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## 暫停對已記住之裝置和瀏覽器進行 Multi-Factor Authentication (公開預覽)

暫停對已記住之裝置和瀏覽器進行 Multi-Factor Authentication，是允許您讓使用者選擇在使用 MFA 執行成功登入後，於設定天數內暫停 MFA 的功能。它是適用於所有 MFA 使用者的免費功能，也能增強實用性。不過，因為使用者可以暫停 MFA，這項功能可能會降低帳戶安全性。

為了確保使用者帳戶的安全，發生以下任一案例時您應該還原使用者裝置的 Multi-Factor Authentication：

- 當他們的公司帳戶遭到入侵
- 當已記住的裝置遺失或遭竊

> [AZURE.NOTE]這項功能的實作方式採用瀏覽器 Cookie 快取。如果未啟用瀏覽器 Cookie，它便無法運作。

### 如何啟用/停用及設定暫停對已記住之裝置進行 MFA

<ol>
<li>登入 Azure 管理入口網站。</li>
<li>在左側按一下 [Active Directory]。</li>
<li>在 Active Directory 下方，按一下要設定暫停對已記住之裝置進行 Multi-Factor Authentication 的目錄。</li>
<li>在您選取的目錄上，按一下 [設定]。</li>
<li>在 Multi-Factor Authentication 區段中，按一下 [管理服務設定]。</li>
<li>在 [服務設定] 頁面上，於管理使用者裝置設定下方選取/取消選取 [**藉由讓系統記住裝置來允許使用者暫停 Multi-Factor Authentication**]。</li>
![暫停裝置](./media/multi-factor-authentication-manage-users-and-devices/suspend.png) <li>設定要允許暫停的天數。預設值為 14 天。</li> <li>按一下 [儲存]。</li> <li>按一下 [關閉]。</li>

<!---HONumber=July15_HO2-->