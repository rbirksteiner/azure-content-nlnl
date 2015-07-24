身分識別管理在公用雲端與內部部署中同等重要。Azure 支援數種不同的雲端身分識別技術，協助進行身分識別管理。這些選項包括：

- 您可以使用以 Azure 虛擬機器建立的虛擬機器，在雲端中執行 Windows Server Active Directory (通常簡稱為 AD)。當您使用 Azure 將內部部署資料中心擴充到雲端時，這是合理的方法。

- 您可以使用 Azure Active Directory，為使用者提供軟體即服務 (SaaS) 應用程式的單一登入。例如，Microsoft Office 365 會使用這項技術，而且在 Azure 或其他雲端平台中執行的應用程式也可以使用此技術。

- 在雲端或內部部署中執行的應用程式可以使用 Azure Active Directory 存取控制，讓使用者使用來自 Facebook、Google、Microsoft 和其他身分識別提供者的身分識別進行登入。


本文詳細說明下列三個選項。

## 目錄

- [在虛擬機器中執行 Windows Server Active Directory](#adinvm)

- [使用 Azure Active Directory](#ad)

- [使用 Azure Active Directory 存取控制](#ac)


## <a name="adinvm"></a>在虛擬機器中執行 Windows Server Active Directory

在 Azure 虛擬機器中執行 Windows Server AD，與在內部部署中執行 Windows Server AD 非常類似。[圖 1](#fig1) 顯示其外觀的典型範例。

![虛擬機器中的 Azure Active Directory](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>圖 1：Windows Server Active Directory 可以在連接到使用 Azure 虛擬網路的組織內部部署資料中心的 Azure 虛擬機器中執行。

在此處顯示的範例中，Windows Server AD 會在使用 Azure 虛擬機器 (即平台的 IaaS 技術) 建立的 VM 中執行。這些 VM 和其他幾部 VM 會群組成連接至使用 Azure 虛擬網路的內部部署資料中心的虛擬網路。虛擬網路會分割出一組透過虛擬私人網路 (VPN) 連接與內部部署網路進行互動的雲端虛擬機器。如此一來，這些 Azure 虛擬機器就如同內部部署資料中心的另一個子網路。如圖所示，其中兩個 VM 正在執行 Windows Server AD 網域控制站。虛擬網路中的其他虛擬機器可能正在執行應用程式 (例如 SharePoint)，或作其他用途使用 (例如開發和測試)。內部部署資料中心也會執行兩個 Windows Server AD 網域控制站。

有數個選項可用來連接雲端中的網域控制站與在內部部署執行的網域控制站：

- 讓這些網域控制站全都屬於單一 Active Directory 網域。

- 在屬於相同樹系的內部部署和雲端建立個別的 AD 網域。

- 在雲端和內部部署中建立獨立的 AD 樹系，然後使用跨樹系信任或也可以在 Azure 上的虛擬機器中執行 Windows Server Active Directory 同盟服務 (AD FS) 連接這些樹系。

不論選擇哪個選項，系統管理員都必須確定只有在必要時，內部部署使用者所提出的驗證要求才會移至雲端網域控制站，因為雲端的連結可能比內部部署網路的連結還要慢。考慮連接雲端和內部部署網域控制站的另一個因素就是複寫所產生的流量。雲端的網域控制站通常位於自己的 AD 站台中，以便系統管理員安排複寫的頻率。Azure 會列計從 Azure 資料中心送出的流量 (雖然不會列計送入的位元組)，這可能會影響系統管理員的複寫選擇。此外，值得一提的是，雖然 Azure 會提供自己的網域名稱系統 (DNS) 支援，但此服務是 Active Directory 所需的遺漏功能 (例如 Dynamic DNS 和 SRV 記錄支援)。因此，在 Azure 上執行 Windows Server AD 時，需要在雲端設定自己的 DNS 伺服器。

在幾個不同的情況下，於 Azure VM 中執行 Windows Server AD 是合理的。這裡有一些範例：

- 如果您使用 Azure 虛擬機器作為自有資料中心的延伸模組，則可能在需要本機網域控制站的雲端執行應用程式，以處理 Windows 整合式驗證要求或 LDAP 查詢等作業。例如，SharePoint 會與 Active Directory 頻繁互動，雖然可能在 Azure 上執行使用內部部署目錄的 SharePoint 伺服器陣列，但在雲端設定網域控制站將會大幅改善效能。(不過請務必了解，未必需要這麼做；許多應用程式可以在僅使用內部部署網域控制站的雲端中順利執行)。

- 假設遠處分公司缺乏執行自己的網域控制站的資源。現在，其使用者必須在世界的另一端進行網域控制站驗證 - 登入速度很慢。在較近的 Microsoft 資料中心的 Azure 上執行 Active Directory，分公司不需要有更多的伺服器，即可加速處理。

- 使用 Azure 進行災害復原的組織可能會在雲端維護一小組作用中 VM (包括網域控制站)。然後即可視需要準備擴充此站台，以接管其他位置失敗的站台。

此外，還有其他可能性。例如，您不需要將雲端的 Windows Server AD 連線到內部部署資料中心。如果您要執行為特定一組使用者 (例如，他們全都以雲端型身分識別單獨登入) 提供服務的 SharePoint 伺服器陣列，您可以在 Azure 上建立一個獨立樹系。您使用此技術的方式取決於您的目的。(如需使用 Windows Server AD 搭配 Azure 的詳細指引，[請參閱這裡](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx))。

## <a name="ad"></a>使用 Azure Active Directory

隨著 SaaS 應用程式日益普及，因而引發一個明顯的問題：這些雲端型應用程式應使用哪種目錄服務？ Azure Active Directory 是 Microsoft 對於該問題的解決之道。

在雲端使用此目錄服務的主要選項有兩個：

- 僅使用 SaaS 應用程式的個人和組織可以依賴 Azure Active Directory 作為其唯一目錄服務。

- 執行 Windows Server Active Directory 的組織可以將其內部部署目錄連線到 Azure Active Directory，然後用它來將 SaaS 應用程式的單一登入提供給其使用者。


[圖 2](#fig2) 說明僅需要 Azure Active Directory 的第一個選項。

![虛擬機器中的 Azure Active Directory](./media/identity/identity_02_AD.png)

<a name="fig2"></a>圖 2：Azure Active Directory 會將 SaaS 應用程式 (包括 Office 365) 的單一登入提供給組織的使用者。

如圖所示，Azure AD 是一項多租用戶服務。這表示它可以同時支援許多不同的組織，以及儲存各組織的使用者相關目錄資訊。在此範例中，組織 A 的使用者正在嘗試存取 SaaS 應用程式。此應用程式可能屬於 Office 365 的一部分 (如 SharePoint Online)，或屬於其他應用程式 - 非 Microsoft 應用程式也可以使用此技術。因為 Azure AD 支援 SAML 2.0 通訊協定，所以應用程式只需具備使用此業界標準進行互動的能力。(事實上，使用 Azure AD 的應用程式可以在任何資料中心執行，而不限於 Azure 資料中心)。

此程序會在使用者存取 SaaS 應用程式時開始 (步驟 1)。若要使用此應用程式，使用者必須展現 Azure AD 所簽發的權杖。

此權杖包含使用者的身分識別資訊，並由 Azure AD 進行數位簽章。為了取得此權杖，使用者提供使用者名稱和密碼，以向 Azure AD 驗明正身 (步驟 2)。Azure AD 接著會傳回使用者所需的權杖 (步驟 3)。

然後，此權杖會被傳送至 SaaS 應用程式 (步驟 4)，以便驗證權杖的簽章並使用其內容 (步驟 5)。此應用程式通常會使用權杖內含的身分識別資訊，決定使用者能夠存取的資訊，或以其他方式決定。

相較於權杖內含的資訊，如果應用程式需要更多有關使用者的資訊，可以使用 Azure AD 圖形 API 直接向 Azure AD 要求此資訊 (步驟 6)。在 Azure AD 的初版中，目錄架構相當簡單：其中僅包含使用者與群組及彼此之間的關係。應用程式可以使用此資訊來了解使用者之間的連線。例如，假設應用程式必須知道此使用者的經理是誰，才能決定該使用者是否能夠存取某些資料。透過圖形 API 查詢 Azure AD，即可得知此資訊。

圖形 API 採用一般 REST 型通訊協定，使其能夠從大多數用戶端 (包括行動裝置) 直接使用。此 API 也支援 OData 所定義的延伸模組，而新增查詢語言等項目，可讓用戶端以更實用的方式存取資料。(如需 OData 的詳細資訊，請參閱 [OData 簡介](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf))。 因為圖形 API 可用於了解使用者之間的關係，所以讓應用程式能了解特定組織的 Azure AD 架構中內嵌的社交圖形 (這就是稱為圖形 API 的原因)。為了要針對圖形 API 要求向 Azure AD 驗證本身，應用程式會使用 OAuth 2.0。

如果組織未使用 Windows Server Active Directory (該組織沒有內部部署伺服器或網域)，且唯獨依賴採用 Azure AD 的雲端應用程式，則只需要使用此雲端目錄，就可以將所有雲端應用程式的單一登入提供給公司的使用者。雖然此種情況日益普遍，但大多數組織仍使用以 Windows Server Active Directory 建立的內部部署網域。Azure AD 在此也扮演了有用的角色，如[圖 3](#fig3) 所示。

![虛擬機器中的 Azure Active Directory](./media/identity/identity_03_AD.png) <a id="fig3"></a>圖 3：組織可以聯合 Windows Server Active Directory 與 Azure Active Directory，將 SaaS 應用程式的單一登入提供給其使用者。

在此案例中，組織 B 的使用者希望存取 SaaS 應用程式。在使用者這麼做之前，組織的目錄管理員必須先使用 AD FS 來與 Azure AD 建立同盟關係 (如圖所示)。這些管理員也必須設定組織的內部部署 Windows Server AD 與 Azure AD 之間的資料同步處理。資料同步處理會自動將使用者和群組資訊從內部部署目錄複製到 Azure AD。請注意，這麼一來：組織會實際將其內部部署目錄擴充到雲端。以此方式聯合 Windows Server AD 與 Azure AD，組織即可擁有一項可當作單一實體管理的目錄服務，但仍可支配內部部署和雲端。

為了使用 Azure AD，使用者會先如往常般登入自己的內部部署 Active Directory 網域 (步驟 1)。當他嘗試存取 SaaS 應用程式時 (步驟 2)，同盟程序會導致 Azure AD 對他簽發此應用程式的權杖 (步驟 3)。(如需同盟運作方式的詳細資訊，請參閱 [Windows 的宣告式身分識別：技術和案例](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx)。) 如先前所述，此權杖包含使用者的身分識別資訊，並由 Azure AD 進行數位簽章。然後，此權杖會被傳送至 SaaS 應用程式 (步驟 4)，以便驗證權杖的簽章並使用其內容 (步驟 5)。在先前的案例中，SaaS 應用程式可以視需要使用圖形 API 來深入了解此使用者 (步驟 6)。

Azure AD 目前並未完全取代內部部署 Windows Server AD。如先前所述，雲端目錄的架構簡單許多，但也有所欠缺，例如群組原則、儲存電腦相關資訊的功能，以及對 LDAP 的支援。(事實上，無法將 Windows 電腦設定成讓使用者僅使用 Azure AD 進行登入 - 不支援此案例)。 Azure AD 的最初目標包括讓企業使用者不需維護個別的登入，並且讓內部部署目錄管理員不需手動同步處理其內部部署目錄與其組織所用的每個 SaaS 應用程式，即可存取雲端的應用程式。但是，過一段時間後，便期望此雲端目錄服務能處理更廣泛的案例。

## <a name="ac"></a>使用 Azure Active Directory 存取控制

雲端型身分識別技術可用於解決各種問題。舉例來說，Azure Active Directory 可將多個 SaaS 應用程式的單一登入提供給組織的使用者。但雲端的身分識別技術也可用於其他層面。

假設有應用程式希望讓使用者使用多個「身分識別提供者 (IdP)」所簽發的權杖進行登入。現今有為數眾多的身分識別提供者，包括 Facebook、Google、Microsoft 和其他提供者，而應用程式經常讓使用者使用上述其中一個身分識別進行登入。既然可以依賴已經存在的身分識別，應用程式為何還要麻煩地維護自己的使用者和密碼清單？ 對於要記住不只一個使用者名稱和密碼的使用者，以及建立應用程式且不必再維護自己的使用者名稱和密碼清單的人而言，接受現有的身分識別比較方便。

但是當每一個身分識別提供者簽發某種權杖時，這些權杖並不標準 - 每個 IdP 都有自己的格式。此外，這些權杖中的資訊也不標準。希望接受 Facebook、Google 和 Microsoft 等提供者簽發之權杖的應用程式，面臨到撰寫唯一程式碼來處理各種不同格式的挑戰。

為何要這麼做？ 何不建立一個媒介，以共通的身分識別資訊表示法產生單一權杖格式？ 這種方法對於建立應用程式的開發人員而言比較方便，因為他們現在只需要處理一種權杖。Azure Active Directory 存取控制就是這麼做，在雲端提供一個媒介以便處理各種權杖。[圖 4](#fig4) 顯示其運作方式

![虛擬機器中的 Azure Active Directory](./media/identity/identity_04_IdentityProviders.png) <a id="fig4"></a>圖 4：Azure Active Directory 存取控制讓應用程式能輕鬆地接受由不同身分識別提供者所簽發的身分識別權杖。

此程序會在使用者嘗試透過瀏覽器存取應用程式時開始。應用程式將使用者重新導向至其所選的 IdP (而且也是應用程式所信任的 IdP)。使用者可以向此 IdP 驗明正身，例如輸入使用者名稱和密碼 (步驟 1)，而 IdP 會傳回包含其相關資訊的權杖 (步驟 2)。

如圖所示，存取控制支援各種不同的雲端型 IdP (包括 Google、Yahoo、Facebook、Microsoft (先前稱為 Windows Live ID) 及任何 OpenID 提供者所建立的帳戶)。此外，還支援使用 Azure Active Directory 和 (透過與 AD FS 同盟) Windows Server Active Directory 建立的身分識別。目的在於涵蓋目前最常使用的身分識別 (不論是由雲端或內部部署的 IdP 所簽發)。

一旦使用者的瀏覽器擁有來自其所選 IdP 的 IdP 權杖，瀏覽器便會將此權杖傳送至存取控制 (步驟 3)。存取控制會驗證此權杖，確定該權杖真的是由此 IdP 所簽發，然後根據對此應用程式定義的規則建立新的權杖。如同 Azure Active Directory，存取控制是一項多租用戶服務，但這些租用戶是應用程式而非客戶組織。每個應用程式都可取得自己的命名空間 (如圖所示)，而且可以定義有關授權等的各種規則。

每個應用程式的管理員可利用這些規則來定義應如何將各種 IdP 提供的權杖轉換為存取控制權杖。例如，如果不同 IdP 以各種類型表示使用者名稱，則存取控制規則可以將這些類型轉換成一般使用者名稱類型。接著，存取控制會將這個新權杖送回瀏覽器 (步驟 4)，而瀏覽器會將其提交至應用程式 (步驟 5)。一旦擁有存取控制權杖，應用程式就會驗證此權杖是否真的由存取控制所簽發，然後使用其內含的資訊 (步驟 6)。

雖然此程序似乎有點複雜，但實際上讓應用程式的建立者輕鬆許多。應用程式可以接受由多個身分識別提供者所簽發的身分識別，同時仍可只接收一個具有類似資訊的權杖，而不用處理內含不同資訊的各種權杖。此外，不需要將每個應用程式都設定成信任各種 IdP，存取控制 (一個值得信任的應用程式) 會維護這些信任關係。

值得一提的是，存取控制與 Windows 毫無關連 - 它也會由僅接受 Google 和 Facebook 身分識別的 Linux 應用程式所使用。即使存取控制屬於 Azure Active Directory 系列的一部分，您仍可將它視為與上一節中描述的服務全然不同。雖然兩種技術都可處理身分識別，但他們所處理的問題極為不同 (縱使 Microsoft 已表示它期望在某個時間點整合這兩者)。

對於幾乎所有應用程式而言，處理身分識別是相當重要的。存取控制的目的在於讓開發人員能夠更輕鬆地建立可接受各種身分識別提供者所提供之身分識別的應用程式。Microsoft 將此服務放在雲端，以供在任何平台上執行的任何應用程式使用。

##關於作者

David Chappell 是 Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) (位於美國加州舊金山) 的主席。

<!---HONumber=July15_HO2-->