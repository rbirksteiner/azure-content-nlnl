#Azure 安全性指引

##摘要

開發 Azure 的應用程式時，身分識別與存取是您必須謹記於心的主要安全性考量。本主題說明雲端中與身分識別和存取相關的主要安全性考量，以及您可以如何為雲端應用程式提供最佳保護。

##概觀

應用程式的安全性是其介面的功能之一。應用程式公開越多介面，安全性疑慮也就越高。例如，從安全性角度來看，以自動批次處理方式執行之應用程式的公開介面會比公開可用的網站還要少。

在移往雲端時，您無需再擔心基礎結構和網路，因為資料中心會使用世界級安全性作法、工具、技術來管理基礎結構和網路。但另一方面，雲端在本質上會為您的應用程式公開較多的介面區域，因而有可能會遭到攻擊者所利用。這是因為許多雲端技術和服務會以端點 (而非記憶體內部元件) 的方式公開。您可透過線路上的端點來存取 Azure 儲存體、服務匯流排、SQL Database (之前稱為 SQL Azure) 和許多其他服務。

在雲端應用程式中，應用程式開發人員會擔負更多設計、開發及維護其雲端應用程式以符合高安全性標準的重責大任，來將攻擊者阻擋於千里之外。請細想下圖 (出處源自 J.D.Meier 的 [Azure 安全性注意事項 PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx))：請注意，雲端提供者 (在範例中是 Azure) 會負責解決基礎結構的部分，而更多的安全性工作會留給應用程式開發人員：

![保護應用程式的安全][01]

好消息是您所熟悉的所有安全性開發作法、原則和技術都仍然適用於雲端應用程式的開發。請考量下列必須解決的主要區域：

-   所有輸入都會經過類型、長度、範圍及字串模式的驗證以避免插入式攻擊，應用程式回應的所有資料也會受到妥善處理。
-   在靜止狀態和在線路上的敏感性資料應受到保護，來減輕資訊洩漏與資料遭到竄改等威脅。
-   稽核和記錄必須已正確實作，才能減輕非否認性威脅。
-   驗證與授權必須使用平台所提供的已經實證機制進行實作，來防止身分詐騙和提升權限等威脅。

如需威脅、攻擊、弱點及對策的完整清單，請參閱模式與作法的速查表：[速查表：Web 應用程式安全性框架](http://msdn.microsoft.com/library/ff649461.aspx)和[應用程式索引的安全性指引](http://msdn.microsoft.com/library/ff650760.aspx)。

雲端的驗證和存取控制機制，與內部部署應用程式所使用的驗證和存取控制機制有極大的差異。雲端應用程式有更多的驗證和存取選項，這些驗證和存取選項有可能會引起混淆，最終讓實作出現瑕疵。在定義什麼是雲端應用程式時則會產生更多的誤解。例如，您可在雲端中部署應用程式，但可能由 Active Directory 提供其驗證機制。另一方面，您也可在內部部署中部署應用程式，但將驗證機制放在雲端 (例如，由 Azure Active Directory Access Control (先前稱為存取控制服務或 ACS) 進行驗證)。

##威脅、弱點及攻擊

威脅是您要避免的潛在不良結果，例如洩漏敏感性資訊或服務無法使用。使用縮寫字 "STRIDE" 來將威脅分類是個常見的作法：

-   **S**poofing or identity theft (詐騙或身分盜用)
-   **T**ampering with data (竄改資料)
-   **R**epudiation of actions (否認動作)
-   **I**nformation disclosure (資訊洩漏)
-   **D**enial of service (拒絕服務)
-   **E**levation of privileges (提升權限)

弱點是指開發人員引進程式碼的錯誤，使得應用程式容易受到攻擊者的利用。例如，以純文字方式傳送敏感性資料，流量探查攻擊會讓資訊洩漏威脅成為可能。

攻擊是指這些弱點遭人利用，而對應用程式造成傷害。例如，跨網站指令碼或 XSS 是指利用未淨化輸出的攻擊。另一個範例是竊聽線路，以擷取以純文字方式傳送的認證。這些攻擊都會演變成您我所熟知的身分詐騙威脅。簡而言之，請將威脅、弱點和攻擊看成是壞東西。下圖視為有害的項目 (從 J.D.部署至 Azure 之 Web 應用程式相關壞東西檢視Meier 的 [Azure 安全性注意事項 PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx))：

![威脅、弱點及攻擊][02]

作為開發人員，您可以掌控弱點。您引進的弱點越少，留給攻擊者利用的選項也就越少。

STRIDE 模式中的身分識別與存取相關弱點會讓您暴露在所有威脅下。例如，未正確實作的驗證機制可能會演變成身分詐騙，結果將造成資訊外洩、資料遭到竄改、提高權限的操作，或甚至是完全關閉服務。請思考下列問題，這些問題可能點出您的雲端應用程式在身分識別與存取實作方面的潛在弱點：

-   您是否以純文字方式透過線路將認證傳送到 Azure 服務？
-   您是否以純文字方式儲存 Azure 服務認證？
-   您的 Azure 服務認證是否符合強式密碼原則？
-   您依賴 Azure 來驗證認證，還是使用自訂的驗證機制？
-   您是否有將 Azure 服務驗證工作階段或權杖存留期限制在合理的時間範圍內？
-   您是否有驗證所發佈雲端應用程式的每個 Azure 進入點權限？
-   您的授權機制是否會在不暴露敏感性資訊或不允許無限存取的情況下安全地失敗？

##對策

面對攻擊威脅的最佳對策是使用平台所提供 (而非自己實作) 的身分識別與存取機制。請考慮下列著名的身分識別與存取技術：

**Windows Identity Foundation (WIF)。** WIF 是 .NET Framework 4.5 隨附的 .NET 執行階段程式庫 (在 .NET 3.5/4.0 中亦可當作獨立項目來下載)。WIF 擔負處理通訊協定的重責大任，例如 WS-同盟和 WS-Trust 及權杖處理 (例如安全性判斷提示標記語言 (SAML))，因此您無需在應用程式中編寫很複雜的安全性相關程式碼。下列資源提供有關 WIF 的深入資訊：

-   MSDN Code Gallery 上的 [Windows Identity Foundation 4.5 範例](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication)。
-   MSDN Code Gallery 上的[適用於 Visual Studio 11 Beta 的 Windows Identity Foundation 4.5 工具](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e)。
-   MSDN 上的 [Windows Identity Foundation 執行階段 (.Net 3.5/4.0)](http://www.microsoft.com/download/details.aspx?id=17331)。
-   MSDN 上的 [Windows Identity Foundation 3.5/4.0 範例及 Visual Studio 2008/2010 範本](http://www.microsoft.com/download/details.aspx?displaylang=en&id=4451)。

**Azure AD 存取控制 (先前稱為 ACS)**。Azure AD 存取控制是個雲端服務，可提供 Security Token Service (STS)，並允許與其他身分識別提供者 (IdP) 建立同盟，例如企業 Active Directory 或網際網路 IdP (例如 Windows Live ID / Microsoft 帳戶、Facebook、Google、Yahoo! 和 Open ID 2.0 等身分識別提供者)。下列資源提供有關 Azure AD 存取控制的深入資訊：

-   [存取控制服務 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
-   [使用 ACS 的案例與解決方案](http://msdn.microsoft.com/library/gg185920.aspx)
-   [ACS 的作法](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
-   [以宣告為基礎的身分識別與存取控制指南](http://msdn.microsoft.com/library/ff423674.aspx)
-   [身分識別開發人員訓練套件](http://www.microsoft.com/download/details.aspx?id=14347)
-   [MSDN 代管的身分識別開發人員訓練課程](http://msdn.microsoft.com/IdentityTrainingCourse)

**Active Directory Federation Services (AD FS)。**Active Directory Federation Services (AD FS) 2.0 可支援包含 Windows Server 和 Active Directory 技術的宣告感知身分識別解決方案。AD FS 2.0 支援 WS-Trust、WS-同盟和 SAML 通訊協定。下列資源提供有關 AD FS 的深入資訊：

-   [AD FS 2.0 內容對應表](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [網頁 SSO 設計][Web SSO Design]
-   [同盟網頁 SSO 設計][Federated Web SSO Design]

**Azure 共用存取簽章。** 共用存取簽章可讓您微調對 Blob 或容器資源的存取。下列資源提供有關共用存取簽章的深入資訊。

-   [管理對 Blob 和容器的存取](http://msdn.microsoft.com/library/ee393343.aspx)
-   [新的儲存功能：共用存取簽章](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [現今的共用存取簽章非常簡單](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

##案例對應表

本節將簡短概述本主題所涵蓋的重要案例。請將它當作對應表，來為您的應用程式找出適合的身分識別解決方案。

-   **使用同盟驗證的 ASP.NET Web Form 應用程式。** 在此案例中，您將使用網際網路身分識別 (例如 Live ID / Microsoft 帳戶) 或 Windows Server Active Directory 中所管理的企業身分識別，來控制對 ASP.NET Web Forms 應用程式的存取。
-   **使用同盟驗證的 WCF (SOAP) 服務。**在此案例中，您將使用由 Azure AD 存取控制所管理的服務身分識別，來控制對 WCF (SOAP) 服務的存取。
-   **使用同盟驗證 (Active Directory 中的身分識別) 的 WCF (SOAP) 服務。** 在此案例中，您將使用由企業 Windows Server Active Directory 所管理的身分識別，來控制對 WCF (SOAP) Web 服務的存取。
-   **使用同盟驗證的 WCF (REST) 服務。**在此案例中，您將使用由 Azure AD 存取控制所管理的服務身分識別，來控制對 WCF (REST) 服務的存取。
-   **使用 Live ID / Microsoft 帳戶、Facebook、Google、Yahoo!、Open ID 的 WCF (REST) 服務。** 在此案例中，您將使用網際網路身分識別 (例如 Live ID / Microsoft 帳戶)，來控制對 WCF (REST) 服務的存取。
-   **使用共用 SWT 權杖，從 ASP.NET Web 應用程式到 REST WCF 服務。** 在此案例中，您擁有使用前端 ASP.NET Web 應用程式和下游 REST 服務的分散式應用程式，而且您想要透過實體層來流動使用者的內容。
-   **在宣告感知應用程式與服務中以角色為基礎的存取控制 (RBAC) 授權。** 在此案例中，您想要在應用程式中根據角色實作授權邏輯。
-   **宣告感知應用程式與服務中的宣告型授權。** 在此案例中，您想要在應用程式中根據複雜的授權規則實作授權邏輯。
-   **Azure 儲存服務身分識別與存取案例。**在此案例中，您必須安全地共用對 Azure 儲存 Blob 和容器的存取。
-   **Azure SQL Database 身分識別與存取案例。**SQL Database 僅支援 SQL Server 驗證。不支援 Windows 驗證 (整合式安全性)。使用者必須在每次連線到 SQL Database 時提供認證 (登入和密碼)。
-   **Azure 服務匯流排身分識別與存取案例。**在此案例中，您需要安全地存取 Azure 服務匯流排佇列。
-   **記憶體內部快取身分識別與存取案例。**在此案例中，您需要安全地存取記憶體內部快取所管理的資料。
-   **Azure Marketplace 身分識別與存取案例。**在此案例中，您需要安全地存取 Azure Marketplace 資料集。

##Azure 身分識別與存取案例

本節將概述其他應用程式架構的常見身分識別與存取案例。使用此案例對應表來快速定位。

###使用同盟驗證的 ASP.NET Web Form 應用程式

在此應用程式架構案例中，您的 Web 應用程式可能會部署到 Azure 或內部部署。應用程式要求其使用者須經過企業 Active Directory 或網際網路身分識別提供者的驗證。

若要解決此案例，請使用 Azure AD 存取控制和 Windows Identity Foundation。

![Azure Active Directory 存取控制][03]

若要實作此案例，請參閱下列資源：

-   [作法：使用 ACS 建立我的第一個宣告感知 ASP.NET 應用程式](http://msdn.microsoft.com/library/gg429779.aspx)
-   [作法：在您的 ASP.NET Web 應用程式中主控登入頁面](http://msdn.microsoft.com/library/gg185926.aspx)
-   [作法：使用 WIF 與 ACS 在宣告感知 ASP.NET 應用程式中實作宣告授權](http://msdn.microsoft.com/library/gg185907.aspx)    
-   [作法：使用 WIF 與 ACS 在宣告感知 ASP.NET 應用程式中實作以角色為基礎的存取控制 (RBAC)](http://msdn.microsoft.com/library/gg185914.aspx)
-   [作法：使用 X.509 憑證設定 ACS 與 ASP.NET Web 應用程式之間的信任](http://msdn.microsoft.com/library/gg185947.aspx)
-   [程式碼範例：ASP.NET 簡易表單](http://msdn.microsoft.com/library/gg185938.aspx)

###使用服務身分識別的 WCF (SOAP) 服務

在此應用程式架構案例中，您的 WCF (SOAP) 服務可能會部署到 Azure 或內部部署。Web 應用程式或甚至另一個 Web 服務會將此服務當作下游服務來進行存取。您必須使用應用程式特定身分識別來控制對此服務的存取。把它想像成您在 IIS 中使用的應用程式集區帳戶類型，即使技術不同，但使用應用程式範圍帳戶或使用者帳戶來存取服務的方法卻很類似。

在 Azure AD 存取控制中使用服務身分識別功能。這與將應用程式部署到 Windows Server 和 IIS 時所使用的 IIS 應用程式集區帳戶十分類似。設定 Azure AD 存取控制，簽發將由 WCF (SOAP) 服務中的 WIF 進行處理的 SAML 權杖。

![WCF (SOAP) 服務][04]


若要實作此案例，請參閱下列資源：

-   [作法：使用 X.509 憑證、密碼或對稱金鑰新增服務身分識別](http://msdn.microsoft.com/library/gg185924.aspx)
-   [作法：以用戶端憑證驗證受 ACS 保護的 WCF 服務](http://msdn.microsoft.com/library/hh289316.aspx)
-   [作法：以使用者名稱與密碼驗證受 ACS 保護的 WCF 服務](http://msdn.microsoft.com/library/gg185954.aspx)
-   [程式碼範例：WCF 憑證驗證](http://msdn.microsoft.com/library/gg185952.aspx)
-   [程式碼範例：WCF 使用者名稱驗證](http://msdn.microsoft.com/library/gg185927.aspx)

###使用同盟驗證 (Active Directory 中的身分識別) 的 WCF (SOAP) 服務

在此應用程式架構案例中，您的 WCF (SOAP) 服務可能會部署到 Azure 或內部部署。您需要控制對此服務的存取，方法是使用由企業 Windows Server Active Directory (AD) 所管理的身分識別。

使用針對 Windows Server AD FS 同盟所設定的 Azure AD 存取控制。在此情況中，您不需要使用 Azure AD 存取控制來設定服務身分識別。需要存取 WCF (SOAP) 服務的代理程式會提供 AD FS 認證，並在驗證成功時收到由 AD FS 簽發的權杖。此權杖會接著提交到 Azure AD 存取控制，然後重新簽發還給該代理程式。該代理程式會使用此權杖來提交要求給 WCF (SOAP) 服務。

![使用 AD 的 WCF (SOAP) 服務][05]

若要實作此案例，請參閱下列資源：

-   [作法：使用 X.509 憑證、密碼或對稱金鑰新增服務身分識別](http://msdn.microsoft.com/library/gg185924.aspx)
-   [作法：將 AD FS 2.0 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185961.aspx)
-   [作法：使用管理服務來將 AD FS 2.0 設定為企業身分識別提供者](http://msdn.microsoft.com/library/gg185905.aspx)
-   [程式碼範例：以 AD FS 2.0 驗證 WCF 同盟](http://msdn.microsoft.com/library/hh127796.aspx)

###使用服務身分識別的 WCF (REST) 服務

在此案例中，您的 WCF (REST) 服務可能會部署到 Azure 或內部部署。Web 應用程式或另一個 Web 服務會將此服務當作下游服務來進行存取。您必須使用應用程式特定身分識別來控制對此服務的存取。把它想像成您在 IIS 中使用的應用程式集區帳戶類型，即使技術不同，但使用應用程式範圍帳戶或使用者帳戶來存取服務的方法卻很類似。

在 Azure AD 存取控制中使用服務身分識別功能。設定 Azure AD 存取控制以簽發簡式 Web 權杖 (SWT) 權杖。若要在 REST 服務端處理 SWT 權杖，您可以實作自訂權杖處理常式並將它插入 WIF 管線，或者您可以在無需使用 WIF 基礎結構的情況下「手動」剖析權杖。

請細想下圖 (WIF 是選擇性的)：

![REST 服務][06]

若要實作此案例，請參閱下列資源：

-   [作法：使用對稱金鑰設定 ACS 與 WCF 服務之間的信任](http://msdn.microsoft.com/library/gg185958.aspx)
-   [作法：使用 ACS 驗證部署到 Azure 的 REST WCF 服務](http://msdn.microsoft.com/library/hh289317.aspx)
-   [程式碼範例：ASP.NET Web 服務](http://msdn.microsoft.com/library/gg983271.aspx)
-   [程式碼範例：Windows Phone 7 應用程式](http://msdn.microsoft.com/library/gg983271.aspx)
-   [使用由 Azure 存取控制服務 (ACS) 簽發之 SWT 權杖的 REST WCF](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

###使用 Live ID / Microsoft 帳戶、Facebook、Google、Yahoo!、Open ID 的 WCF (REST) 服務

在此案例中，您的 WCF (REST) 服務可能會部署到 Azure 或內部部署。您需要使用公用網際網路身分識別 (例如 Live ID / Microsoft 帳戶或 Facebook)，來控制對此服務的存取。

使用 Azure AD 存取控制來簽發 SWT 權杖。若要在 REST 服務端處理 SWT 權杖，您可以實作自訂權杖處理常式並將它插入 WIF 管線，或者您可以在無需使用 WIF 基礎結構的情況下「手動」剖析權杖。

要注意的重點是，為了實作此案例，應用程式必須使用網頁瀏覽器控制項來收集使用者認證。如此一來，它便不適用於從 ASP.NET Web 應用程式存取 REST 服務的案例。它只適用於由使用者用戶端應用程式 (例如 Windows Phone 7 應用程式或豐富型桌面用戶端) 存取 REST 服務的案例。提出網頁瀏覽器控制項的主要原因是，網際網路身分識別並不直接支援作用中設定檔案例 (Web 服務案例)。網際網路身分識別主要支援依賴瀏覽器重新導向的被動式設定檔案例 (web 應用程式)： 這是 web 瀏覽器控制項能派上用場的地方。

請細想下圖 (WIF 是選擇性的，因此未顯示)：

![WIF 是選擇性的][07]

若要實作此案例，請參閱下列資源：

-   [作法：使用 ACS 驗證部署到 Azure 的 REST WCF 服務](http://msdn.microsoft.com/library/hh289317.aspx)
-   [作法：將 Google 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185976.aspx)
-   [作法：將 Facebook 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185919.aspx)
-   [作法：將 Yahoo! 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185977.aspx)
-  [程式碼範例：Windows Phone 7 應用程式](http://msdn.microsoft.com/library/gg983271.aspx)
-   [使用由 Azure 存取控制服務 (ACS) 簽發之 SWT 權杖的 REST WCF](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


###使用共用 SWT 權杖，從 ASP.NET Web 應用程式到 REST WCF 服務

在此案例中，您擁有使用前端 ASP.NET Web 應用程式和下游 REST 服務的分散式應用程式，而且您想要跨實體層來維護使用者的內容。在下游 REST 服務中實作授權邏輯或根據使用者的身分識別登入時，這一點有時候是必要的。

設定 Azure AD 存取控制來簽發 SWT 權杖。SWT 權杖會簽發給前端 ASP.NET Web 應用程式，然後與下游 REST 服務共用。在此情況中，在 Azure AD 存取控制中只設定一個信賴憑證者。但是，請注意以下幾點情況：

-   由於 WIF 不提供現成的 SWT 權杖處理常式，您必須實作自訂權杖處理常式才能與 ASP.NET Web 應用程式搭配使用。您應依賴 WIF 執行繁重工作以支援依賴瀏覽器重新導向的 WS-同盟通訊協定，而非自己進行實作。
-   實作 SWT 自訂權杖處理常式時，請確保會處理啟動程序權杖以確實保留該權杖。否則，您將無法將其共用並傳送至下游 REST 服務。
-   您不需要在 REST 服務上使用 WIF，反過來，您可以「手動」剖析權杖，因為此案例沒有處理重新導向的必要。

![ASP.NET Web 應用程式][08]

若要實作此案例，請參閱下列資源：

-   [作法：將 Google 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185976.aspx)
-   [作法：將 Facebook 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185919.aspx)
-   [作法：將 Yahoo! 設定為身分識別提供者](http://msdn.microsoft.com/library/gg185977.aspx)
-   [使用共用 SWT 權杖，從 ASP.NET Web 應用程式到 REST WCF 服務委派](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

###在宣告感知應用程式與服務中以角色為基礎的存取控制 (RBAC)

在這種情況下，您需要在 web 應用程式或服務根據使用者角色實作授權： 具有使用者需要角色取得存取權，且不具有必要的角色會遭到拒絕。簡言之，您的應用程式必須回答這個簡單的問題 - 使用者的角色是否為 X？

有數種方式可以解決此案例。您可以使用 Azure AD 存取控制、WIF Claims Authentication Manager、samlSecurityTokenRequirement 對應或 Customer Role Manager。

所有案例都使用 WIF。WIF 支援 IPrincipal.IsInRole("MyRole") 方法。在大多數的情況下，重點是確定使用權杖中包含 http://schemas.microsoft.com/ws/2008/06/identity/claims/role URI 的角色類型宣告，因此當呼叫 IsInRole 方法時，WIF 可順利地驗證角色成員。

**Azure AD 存取控制**。在此實作中，我們將使用 Azure AD 存取控制宣告轉換規則引擎。透過宣告轉換規則引擎規則，您可以將任何傳入宣告轉換成角色類型宣告，因此當權杖傳到應用程式或服務時，WIF 可以剖析此角色宣告以確保順利呼叫 IsInRole 方法。

![][09]

**WIF ClaimsAuthenticationManager**。在此實作中，我們將會使用 ClaimsAuthenticationManager 作為 WIF 的延伸點。透過此方法，您可以在應用程式中將任何任意傳入宣告轉換成角色宣告類型。轉換的複雜度只會受到所編寫程式碼的限制。

![][10]

**samlSecurityTokenRequriement 對應**。在此實作中，您將使用 web.config 中的 samlSecurityTokenRequirement 組態，來告訴 WIF 哪個宣告類型要表現出角色宣告類型的行為方式。例如，如果權杖夾帶了群組類型的宣告，則您可以將它對應至角色宣告類型。若使用此方法，您只能執行簡易對應。

![][11]

**自訂 RoleManager。** 在此實作中，您將實作自訂 RoleManger。實作自訂 RoleManager 介面方法 (例如 GetAllRoles()) 時，我們將使用 WIF 來檢查傳入宣告。

![][12]

若要實作此案例，請參閱下列資源：

-   [作法：使用 WIF 與 ACS 在宣告感知 ASP.NET 應用程式中實作以角色為基礎的存取控制 (RBAC)](http://msdn.microsoft.com/library/gg185914.aspx)
-   [作法：使用規則實作權杖轉換邏輯](http://msdn.microsoft.com/library/gg185955.aspx)
-   [在宣告感知 (WIF) ASP.NET Web 應用程式中使用 RoleManager 進行授權](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   程式碼範例：在 [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504) 的 IsInRole 中使用宣告。

###宣告感知應用程式與服務中的宣告型授權

在此案例中，您需要在 Web 應用程式或服務中實作複雜的授權邏輯，且 IsInRole() 方法無法滿足您的授權需求。如果您的授權方法依賴角色，那麼請考慮實作以角色為基礎的存取控制，如上一節所概述。

使用 ClaimsAuthorizationManager 作為 WIF 延伸點。ClaimsAuthorizationManager 允許外部存取檢查呼叫，因此，與在應用程式程式碼中實作存取檢查的情況比起來，您的應用程式程式碼看起來會較為簡潔且較容易維護。

![][13]

若要實作此案例，請參閱下列資源：

-   [作法：使用規則實作權杖轉換邏輯](http://msdn.microsoft.com/library/gg185955.aspx)
-   [作法：使用 WIF 與 ACS 在宣告感知 ASP.NET 應用程式中實作宣告授權](http://msdn.microsoft.com/library/gg185907.aspx)
-   程式碼範例：在 [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504) 中的宣告型授權


##Azure 儲存服務身分識別與存取案例

在此案例中，您必須安全地共用對 Azure 儲存 Blob 和容器的存取。

使用共用存取簽章。若要從自己的應用程式存取儲存服務帳戶，請使用設定並管理儲存服務帳戶時透過 Azure 入口網站取得的共用雜湊。想要提供他人對儲存服務帳戶中 Blob 和容器的存取權時，請使用共用存取簽章 URL。

請特別注意要安全地管理此資訊以免曝光，另外，還要特別注意共用存取簽章的存留期。

![][14]

若要解決此案例，請參閱下列資源

-   [管理對 Blob 和容器的存取](http://msdn.microsoft.com/library/ee393343.aspx)
-   [新的儲存功能：共用存取簽章](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [現今的共用存取簽章非常簡單](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


##Azure SQL Database 身分識別與存取案例

SQL Database 僅支援 SQL Server 驗證。不支援 Windows 驗證 (整合式安全性)。使用者必須在每次連線到 SQL Database 時提供認證 (登入和密碼)。管理使用者名稱和密碼時請特別小心，以避免資訊外洩。

![][15]

若要解決此案例，請參閱下列資源：

-   [安全性方針和限制 (SQL Database)](http://msdn.microsoft.com/library/windowsazure/ff394108.aspx#authentication)
-   [作法：使用 sqlcmd 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ee336280.aspx)
-   [作法：使用 ADO.NET 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ee336243.aspx)
-   [作法：透過 ASP.NET 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ee621781.aspx)
-   [作法：透過 WCF 資料服務連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ee621789.aspx)
-  [作法：使用 PHP 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ff394110.aspx)
-   [作法：使用 JDBC 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/gg715284.aspx)
-   [作法：使用 ADO.NET Entity Framework 連線到 SQL Database](http://msdn.microsoft.com/library/windowsazure/ff951633.aspx)

##Azure 服務匯流排身分識別與存取案例

服務匯流排和 Azure AD 存取控制有著特殊的關係，那就是每個服務匯流排服務命名空間會與相同名稱 (尾碼是 "-sb") 的相同存取控制服務命名空間配對。此種特別關係的原因在於服務匯流排和存取控制管理其相互信任關係和關聯加密編譯密碼的方法。如需詳細資訊，請參閱下面所列資源。

![][16]

若要解決此案例，請參閱下列資源：

-   [使用 ACS 保護服務匯流排的安全](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (視訊)
-   [使用 ACS 保護服務匯流排的安全](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (投影片)
-   [利用存取控制服務進行服務匯流排驗證與授權](http://msdn.microsoft.com/library/hh403962.aspx)

##記憶體內部快取身分識別與存取案例

記憶體內部快取 (先前稱為 Azure Cache) 仰賴 Azure AD 存取控制來進行驗證。它會使用透過管理入口網站取得的共用金鑰。存取快取時，請使用程式碼或組態檔中的金鑰。請務必安全地儲存金鑰，以避免資訊外洩。

![][17]


若要解決此案例，請參閱下列資源：

-   [作法：透過程式設計方式設定 Azure 快取的快取用戶端](http://msdn.microsoft.com/library/windowsazure/gg618003.aspx)
-   [作法：使用應用程式組態檔設定 Azure 快取的快取用戶端](http://msdn.microsoft.com/library/windowsazure/gg278346.aspx)
-   [Azure 服務匯流排和快取範例](http://msdn.microsoft.com/library/ee706741.aspx) (快取範例一節)

##Azure Marketplace 身分識別與存取案例

每次要存取 Azure Marketplace 資料集時，不論是免費或付費，都必須先驗證使用者才能授與存取權。建立應用程式時，您的程式碼必須包含驗證程序。請細想下列常見案例：

###我要存取我的資料集

在此案例中，您打算建立將使用 Marketplace 訂閱中之資料集的應用程式。您是應用程式的使用者。應用程式可能會部署到 Azure、內部部署或 Marketplace。

使用透過 Marketplace 訂閱取得的共用金鑰。您可以使用 Marketplace 入口網站來取得該共用金鑰。

![][18]

若要解決此案例，請參閱下列資源：

-   [在您的 Marketplace 應用程式中使用 HTTP 基本驗證](http://msdn.microsoft.com/library/gg193417.aspx)

###使用者要存取我的資料集

在此案例中，您打算建立將允許使用者存取您資料集的應用程式。應用程式可能會在 Azure、內部部署或 Marketplace 中部署。

若要解決此案例，請使用 OAuth 委派。系統會出現提示要求使用者提供其 Live ID / Microsoft 帳戶認證，然後使用者會被引導完成同意程序。

![][19]

若要解決此案例，請參閱下列資源：

-   [OAuth Web 用戶端範例](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [OAuth Rich 用戶端範例](http://go.microsoft.com/fwlink/?LinkId=219163)

###應用程式存取 Marketplace API

在此案例中，您打算建立一個存取 Marketplace API 的應用程式。Marketplace API 需要驗證才能順利完成對它的呼叫。應用程式會部署到 Azure Marketplace。

![][20]

請參考 Marketplace 發佈套件，以取得有關驗證實作的詳細資訊。

若要解決此案例，請參閱下列資源：

-   [下載應用程式發佈套件](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [提供給應用程式的 Azure Marketplace 簡介](https://datamarket.azure.com/)

##安全性旋鈕

本節將概述 Windows Identity Foundation 和 Azure AD 存取控制的安全性旋鈕。設計與部署您的應用程式時，您可以把它當成這些技術的基本安全性檢查清單。

###Windows Identity Foundation

下列是 WIF 的主要安全性旋鈕。以下資訊摘錄自 [WIF 設計考量](http://msdn.microsoft.com/library/ee517298.aspx)和[適用於 ASP.NET Web 應用程式的 Windows Identity Foundation (WIF) 安全性 - 威脅與對策](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)。

-   **IssuerNameRegistry**。指定信任的安全性權杖服務 (STS)。請確認只會列出信任的 STS。
-   **cookieHandler requireSsl="true"**。指定是否透過 SSL 通訊協定傳送工作階段 Cookie。
-   **wsFederation's requireHttps="true"**。指定是否透過 SSL 通訊協定執行與身分識別提供者的同盟通訊協定通訊。
-   **tokenReplayDetection enabled="true"**。指定是否啟用權杖重新執行偵測功能。請注意，因為此功能管理已使用權杖的本機複本，所以它會建立伺服器相似性。
-   **audienceUris**。指定權杖的適用對象。如果您的應用程式收到不適用的權杖，則 WIF 會拒絕此權杖。
-   **requestValidation** 和 **httpRuntime requestValidationType**。啟用/停用 ASP.NET 驗證功能。請參閱 [Windows Identity Foundation (WIF)：潛在危險要求。從用戶端偵測到表單值](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)中所概述的指引。

###Azure AD 存取控制

請細想在 Azure AD 存取控制部署中的下列安全性旋鈕。以下資訊是摘錄自 [ACS 安全性指導方針](http://msdn.microsoft.com/library/gg185962.aspx)和[憑證與金鑰管理指導方針](http://msdn.microsoft.com/library/hh204521.aspx)。

-   **STS 權杖到期日**。使用 Azure AD 存取控制管理入口網站來設定積極的權杖到期日。
-   **使用錯誤 URL 功能時的資料驗證**。Azure AD 存取控制錯誤 URL 功能會要求對傳送錯誤訊息的應用程式頁面進行匿名存取。將傳入此頁面的所有資料都當作未受信任的來源般危險。
-   **針對高度敏感的案例加密權杖**。若要減輕資訊在權杖中洩漏的威脅，請考慮加密權杖。
-   **部署到 Azure 時使用 RSA 加密 Cookie**。依預設，WIF 會使用 DPAPI 來加密 Cookie。當部署到 Web 伺服陣列和 Azure 環境時，它會建立伺服器相似性，並可能產生例外狀況。在 Web 伺服陣列和 Azure 案例中請改用 RSA。
-   **權杖簽署憑證**。定期更新權杖簽署憑證，以防止阻斷服務發生。Azure AD 存取控制會簽署它所簽發的所有安全性權杖。當您建立應用程式來使用由 ACS 所簽發的 SAML 權杖時，會使用 X.509 憑證進行簽署。當簽署憑證到期時，您在嘗試要求權杖時會收到錯誤訊息。
-   **權杖簽署金鑰**。定期更新權杖簽署金鑰，以防止阻斷服務發生。Azure AD 存取控制會簽署它所簽發的所有安全性權杖。當您建立應用程式來使用由 ACS 所簽發的 SWT 權杖時，會使用256 位元對稱簽署金鑰。當簽署金鑰到期時，您在嘗試要求權杖時會收到錯誤訊息。
-   **權杖加密憑證**。定期更新權杖加密憑證，以防止阻斷服務發生。如果信賴憑證者應用程式是在 WS-Trust 通訊協定上使用持有證明權杖的 Web 服務，則需要權杖加密，否則權杖加密是選擇性的。當加密憑證到期時，您在嘗試要求權杖時會收到錯誤訊息。
-   **權杖解密憑證**。定期更新權杖解密憑證，以防止阻斷服務發生。Azure AD 存取控制可以接受來自 WS-同盟身分識別提供者 (例如 AD FS 2.0) 的加密權杖。在 Azure AD 存取控制中代管的 X.509 憑證可用於解密。當解密憑證到期時，您在嘗試要求權杖時會收到錯誤訊息。
-   **服務身分識別認證**。定期更新服務身分識別認證，以防止阻斷服務發生。服務身分識別會使用針對 Azure AD 存取控制命名空間全域設定的認證，可讓應用程式或用戶端直接使用 Azure AD 存取控制進行驗證，並接收權杖。Azure AD 存取控制服務身分識別可以產生關聯的認證類型有三種：對稱金鑰、密碼及 X.509 憑證。認證到期時，您將會開始收到例外狀況。
-   **Azure AD 存取控制管理服務帳戶認證**。定期更新管理服務認證，以防止阻斷服務發生。Azure AD 存取控制管理服務是一個重要元件，可讓您以程式設計方式管理與設定 Azure AD 存取控制命名空間的設定值。管理服務帳戶可以關聯的認證類型有三種。這三種類型分別是對稱金鑰、密碼和 X.509 憑證。認證到期時，您將會開始收到例外狀況。
-   **WS-同盟身分識別提供者簽署與加密憑證**。查詢 WS-同盟身分識別提供者憑證的有效性，以防止阻斷服務發生。WS-同盟身分識別提供者憑證可透過其中繼資料取得。設定 WS-同盟身分識別提供者 (如 AD FS) 時，WS-同盟簽署憑證可透過經由 URL 或作為檔案取得的 WS-同盟中繼資料進行設定。在設定 WS-同盟身分識別提供者之後，您可以使用 Azure AD 存取控制管理服務來查詢其憑證是否有效。憑證到期時，您將會開始收到例外狀況。

##使用 Azure 網站共用主控功能

本主題中概述在 Azure 網站上託管應用程式時所有有效的案例和解決方案。

##Azure 虛擬機器

本主題中概述在 Azure 虛擬機器上代管應用程式時所有有效的案例和解決方案。

##資源

-   [身分識別開發人員訓練套件](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [MSDN 代管的身分識別開發人員訓練課程](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [以宣告為基礎的身分識別與存取控制指南](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [存取控制服務](http://msdn.microsoft.com/library/windowsazure/gg429786.aspx)
-   [ACS 的作法](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
-   [使用存取控制服務 v2.0 來保護 Azure Web 角色 ASP.NET Web 應用程式的安全](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Azure AD 存取控制服務 (ACS) 學術影片](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Microsoft 安全性開發生命週期](http://www.microsoft.com/security/sdl/default.aspx)
-   [SDL 威脅模型化工具 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [安全性與隱私權部落格](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [安全性回應中心](http://www.microsoft.com/security/msrc/default.aspx)
-   [安全性智慧型報告](http://www.microsoft.com/security/sir/)
-   [安全性開發生命週期](http://www.microsoft.com/security/sdl/default.aspx)
-   [安全性開發人員中心 (MSDN)](http://msdn.microsoft.com/security/)


[01]: ./media/SecurityRX/01_SecuringTheApplication.gif
[02]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]: ./media/SecurityRX/05_AzureADAccessControl.gif
[06]: ./media/SecurityRX/06_RESTService.gif
[07]: ./media/SecurityRX/07_WIFisOptional.gif
[08]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]: ./media/SecurityRX/09_RBAC.gif
[10]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]: ./media/SecurityRX/12_CustomRoleManager.gif
[13]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]: ./media/SecurityRX/14_WindowsAzurestorage.gif
[15]: ./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]: ./media/SecurityRX/18_IAccessMyDataset.gif
[19]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Web SSO Design]: http://technet.microsoft.com/library/dd807033(WS.10).aspx
[Federated Web SSO Design]: http://technet.microsoft.com/library/dd807050(WS.10).aspx

<!---HONumber=July15_HO2-->