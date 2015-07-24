<properties 
	pageTitle="企業的 Azure App Service Web Apps 提供項目" 
	description="顯示如何使用 Azure App Service Web Apps 為貴公司建立企業網站解決方案" 
	services="app-service\web" 
	documentationCenter="" 
	authors="apwestgarth" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="apwestgarth"/>

# 企業的 Azure App Service Web Apps 提供項目白皮書 #

在快速發展的環境中，尋求降低成本以及更快速提供 IT 解決方案的需求為開發人員、IT 專業人員和經理帶來了新的挑戰。越來越多的使用者正在尋找快速、回應靈敏且適用於任何裝置的企業營運 (LOB) Web 應用程式。同時，企業嘗試利用因與雲端和行動服務整合而不斷提升的生產力和效能，一個簡單的案例是使用 Active Directory 在不同的裝置中單一登入到 Office365 的共同作業，並使用從內部 LOB 應用程式 (依序從 Salesforce 的公司實作中提取資料) 提取的資料。[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 是一個企業級的雲端服務，可用來開發、測試，以及執行 Web 和行動應用程式、Web API 及一般網站。在資料中心 (經過延展性和可用性最佳化) 的全球網路上，Azure 網站可用來執行企業網站、業務應用程式和數位行銷活動，並針對持續整合與現代 DevOps 做法提供支援。

本白皮書中的重點會放在 [Web Apps](/services/app-service/web/) 服務的功能，尤其著重於如何執行 LOB Web 應用程式，其中包括平台上的現有 Web 應用程式移轉以及新 LOB Web 應用程式部署。

## 對象 ##

打算將目前在內部部署執行的工作負載移轉至雲端 Web 工作負載的 IT 專業人員、架構設計人員和經理。Web 工作負載可以從公司延伸到員工或是從公司延伸到夥伴 Web 應用程式。

## 簡介 ##

App Service Web Apps 是主控外部和內部 Web 應用程式和服務的理想平台，因為它可提供符合成本效益、高擴充性、受管理的解決方案，可讓您將精力放在為使用者提供商業價值，而不是花費大量時間和金錢來維護和支援不同的環境。Web Apps 提供一個可部署企業 Web 應用程式的彈性平台，透過與 Microsoft Azure Active Directory 的整合，提供繼續針對內部部署 Active Directory 進行驗證，支援利用內部連續整合與部署做法的輕鬆快速部署，並同時隨著業務需求成長自動擴充等能力 - 全部位於一個受管理的平台上，可讓您將重心放在應用程式上，而非基礎架構上。

## 問題定義 ##

IT 版圖正在快速變遷，逐漸捨棄裝載於需要長前置時間和高資本成本的傳統伺服器，改用可自動調整以處理負載的隨選服務。IT 部門面臨的挑戰包括降低基礎結構與維護的花費成本和使用量，重點在於降低 CAPEX，並同時提高靈活度。因為舊式基礎結構平台 (例如 Windows Server 2003) 的生命週期已經結束，IT 部門開始審查雲端移轉是否可作為避免新長期資本成本的潛在方法。在過去，CIO 會為其他部門做出採購決策；不過，越來越多的 CMO 及其他業務單位主管會在花費預算方式和投資報酬率方面扮演更積極的角色。越來越多的企業需要比以往更具行動力的員工，這包括員工可以在遠端工作、花更多的時間與客戶相處，以及必須能夠輕鬆存取系統。

企業需求無時無刻不在變化。企業正在尋找由協力廠商或內部提供、新功能應有盡有，且可定期更新服務的即時全球布局。許多使用者會對企業有較高的期望，他們在私生活中享受許多服務 (例如 Office365)。因此期待在工作上同樣可以存取最新且功能豐富的類似服務。為了協助企業因應此需求，IT 必須慎選協力廠商服務並加以整合，審慎遴選適合業務需求的平台，並同時降低擁有權總成本。

開發團隊希望提供可立即感受的商業利益、經常提供新功能。他們要尋找的是符合成本效益並可整合現有工具和做法的可靠平台 – 開發、測試、發行；與 IT 部門合作以自動化部署、管理和警示作業，這些全都以零停機時間作為目標。

<a href="highlevel" />
## 高階解決方案 ##

越來越多的 Web 平台和架構被用來開發、測試與裝載企業營運應用程式。在傳統的企業營運應用程式 (例如內部員工費用報銷系統) 中，通常只會包含一個 Web 應用程式和儲存連接應用程式資料的支援資料庫。

App Service Web Apps 是裝載此類應用程式的最佳選項，提供可擴充和十分可靠的基礎結構，可在幾乎零手動介入和零停機時間的情況下進行管理和修正。Microsoft Azure 平台提供許多資料儲存選項，可支援在 Web Apps 上裝載的 Web 應用程式，包括 Microsoft Azure SQL Database (一個受管理且可擴充的關聯式資料庫即服務) 和合作夥伴的熱門服務 (例如 ClearDB MySQL Database和 MongoDB)。

另一個方法是運用現有的就地投資。在本範例案例中 (員工費用報銷系統)，您會維護位於內部基礎結構中的資料存放區。這可能是為了與內部系統 (報告、薪資、帳單等) 整合或是為了滿足 IT 管理需求。Web Apps 提供兩種可讓您連線到內部部署基礎結構的方法：

- [混合式連線](../integration-hybrid-connection-overview.md) – 混合式連線是 Microsoft Azure BizTalk 服務的一個功能，可讓 Web Apps 安全連線到內部部署資源，例如 SQL Server、MySQL、Web API 和自訂 Web 服務。 
- [虛擬網路整合](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) – Web Apps 與 Azure 虛擬網路整合可讓您將 Web 應用程式連線到 Azure 虛擬網路，此虛擬網路會透過站台對站台 VPN 依序連線到您的內部部署基礎結構。 

下圖是內含內部部署資源連線選項的高階解決方案範例。

![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png)

## 商業利益 ##

App Service Web Apps 提供多種商業利益，可讓您的功能在實作業務需求時能夠更符合成本效益且更加靈活。

### PaaS 模型 ###

App Service Web Apps 採用平台即服務模型，能夠大幅節省成本又可提高效率。您不再需要花上數小時的時間來管理 VM、修正作業系統和架構。Web Apps 是一個可自動修正的環境，可讓您將重點放在管理 Web 應用程式 (而不是 VM)，進而讓團隊有更多的時間提供額外的企業價值。

強化 Web Apps 的 PaaS 模型讓執行 DevOps 方法的人員能夠實現其目標。一家企業代表了應用程式整個生命週期 (包括開發、測試、發行、監視、管理及支援) 的完整管理和整合。

開發團隊可以在 Visual Studio Online、GitHub、TeamCity、Hudson 或 BitBucket 中設定連續整合和部署工作流程，進而提供自動化建置、測試及部署，以加快發行週期並減少有關現有基礎結構中的問題。Web Apps 還支援為您的發行工作流程建立多種測試和預備環境，您不再需要保留或配置硬體即可達成上述目的，您可以建立任意數目的環境，並在發行工作流程中定義您專屬的升級。企業可以決定從原始檔控制發行至測試位置，執行一系列的測試，並在成功完成測試時升級至預備位置，最後在沒有停機時間的情況下交換至生產環境，有了這些附加的好處，系統可以預先載入在 Web Apps 上裝載的 Web 應用程式，並提供最佳的客戶經驗。

營運團隊有信心他們目前站在最有利的位置，可以回應任何有關在 Web Apps 上裝載內建監視和警示功能的 Web 應用程式問題。營運團隊已針對分析與監視方案進行投資，例如 Microsoft Visual Studio Application Insights、New Relic 和 AppDynamics。這些產品也完全支援 Web Apps，提供可監視 Web 應用程式的持續能力和類似環境。

最後，Web Apps 提供了將應用程式和裝載資料庫直接自動備份至 Azure Blob 儲存體容器的功能。提供您一個簡單的方式且極具成本效益的方法，可用來從災難中復原，降低複雜的內部部署硬體和軟體需求。

### 簡化移轉 ###

隨著硬體和作業系統的發行週期速度加快，硬體維護和輪替會是企業的重點。或許您有許多即將於 2015年結束支援的 Windows Server 2003 R2 伺服器，但是它們仍然裝載了貴企業的關鍵 Web 應用程式？ App Service Web Apps 是裝載這些 Web 應用程式，並為您合理化企業硬體資產的理想選擇。Web Apps 提供您存取各種硬體規格的權限，這些權限的管理和維護屬於服務的一部分，無須將替換和管理成本因素計入您的基礎結構預算。移轉也可以像從現有部署到 Web Apps 的複製和貼上作業那樣簡單，或像更複雜的移轉會使用 Web Apps 移轉小幫手來增加價值。移轉的 Web 應用程式享有全方位的 Azure 服務，並可將其他服務整合到 Web 應用程式。例如，您可以考慮新增 Azure Active Directory，根據使用者與安全性群組的關聯來控制應用程式的存取。另一個範例是新增可提高效能並減少延遲的快取服務，提供整體更佳的使用者經驗。

### 企業類別裝載 ###

App Service Web Apps 提供一個既穩定又可靠的平台，此平台已證實能夠處理各種不同的業務需求，從小型的內部開發和測試工作負載，到高度擴充的高流量網站都沒有問題。使用 Web Apps 後，您可以利用 Microsoft 公司在高價值 Web 工作負載中所使用的企業類別裝載平台。Web Apps 以及 Azure 平台上的所有服務是採用符合法規需求的安全性和規範建置而成，例如 ISO (ISO/IEC 27001:2005) ；SOC1 和 SOC2 SSAE 16/ISAE 3402 Attestations、HIPAA BAA、PCI 和 Fedramp 都是每個元素和功能的中心準則，如需詳細資訊，請參閱 [http://aka.ms/azurecompliance](/support/trust-center/compliance/)。

Microsoft Azure 平台允許角色型權限控管，針對 Web Apps 中的資源提供企業層級的控管。RBAC 可讓企業在 Azure 環境中實作所有資產的專屬存取管理原則，方法是將使用者指派給群組，並針對資產 (例如 Web 應用程式) 依序將必要權限指派給這些群組。如需有關 Azure 中 RBAC 的詳細資訊，請參閱 [http://aka.ms/azurerbac](../role-based-access-control-configure/)。透過使用 Web Apps，您可以確定您的 Web 應用程式會在安全的環境中進行部署，而且您會擁有部署資產所在領域的完整控制權。

此外，透過提供連線回到內部資源 (例如您的資料倉儲或 SharePoint 環境) 的能力，Web Apps 還能充分運用您的內部部署投資。如 [高階解決方案] 中所述，您可以利用混合式連線和虛擬網路連線來建立內部部署基礎結構和服務之間的連線。

### 全球規模 ###

App Service Web Apps 是一個可擴充的全域平台，可讓 Web 應用程式依照不斷成長的業務需求快速擴充與調整，並將長期規劃和成本降到最低。在傳統的內部部署基礎結構案例中，本機和各地的擴充和增加需求將會需要大量的管理、規劃和開支，才能佈建和管理額外的基礎結構。Web Apps 提供了可讓您使用 ebb 和需求流程來調整 Web 應用程式的能力。以費用報銷應用程式為例，您的使用者在每個月大部分時間並不會使用此應用程式，但每個月在接近輸入費用報銷提交的期限時，您的應用程式使用量便會增加，Web Apps 能夠為您的應用程式自動佈建更多的基礎結構，並在使用量消失時調回您所定義的基準基礎結構。

您可在全球的 17 個 (且不斷增加) 資料中心內使用 Web Apps。如需最新的區域和位置清單，請參閱 [http://aka.ms/azlocations](http://aka.ms/azlocations)。有了 Web Apps，您可以擴充企業並將它輕鬆地遍及全球。隨著公司不斷擴展到新的區域，您可將所使用並在 Web Apps 上裝載的報告應用程式部署到其他的資料中心內，並透過 Web Apps 和 Azure 流量管理員的組合來提供本機使用者更快速的服務，這些全都是可調整基礎結構的附加好處，可隨著地區辦公室的需求變更而進行縮編和擴編。
 
## 解決方案詳細資料 ##

讓我們看看應用程式移轉的案例。下列將概述有關如何結合 App Service Web Apps 功能以提供絕佳解決方案和商業價值的詳細資料。
 
在這個企業營運應用程式範例中，我們將針對可讓員工提交補助費用的費用報銷報告應用程式進行討論。本應用程式會在執行 IIS6 的 Windows Server 2003 R2 上裝載，資料庫會是 SQL Server 2005 資料庫。我們選擇舊式伺服器的原因是為了配合即將結束服務的 Windows Server 2003 R2 和 SQL Server 2005，我們準備了[工具](http://aka.ms/websitesmigration)和[指南](http://aka.ms/websitesmigrationresources)，可自動將工作負載移轉至 Azure。基於這個理由，在此範例中所使用的模式可在各種移轉案例中使用。

### 移轉現有的應用程式 ###

將企業營運應用程式移至 Web Apps 的整體解決方案中，第一個步驟是識別現有的應用程式資產和架構。這份白皮書中的範例是在單一 IIS 伺服器上裝載的 ASP.NET Web 應用程式，以及一個在不同 SQL Server 上裝載的資料庫，如下圖所示。員工可以使用使用者名稱和密碼組合登入系統，輸入每筆費用項目的詳細資料，並將每筆費用項目的掃描收據副本上傳到資料庫。
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### 要考慮的項目 ####

從內部部署環境移轉應用程式時，您可能要思考幾個 Web Apps 限制。將 Web 應用程式移轉到 Web Apps 時，以下是一些要注意的重要主題 ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources))：

-	連接埠繫結 - Web Apps 僅支援連接埠 80 (適用於 HTTP) 和連接埠 443 (適用於 HTTPS 流量)。如果您的應用程式使用其他連接埠，則移轉之後，應用程式將使用連接埠 80 (適用於 HTTP) 和連接埠 443 (適用於 HTTPS 流量)。這通常不是很大的問題，因為在內部部署部署中，為了克服使用的網域名稱問題而使用不同連接埠的情況十分常見，尤其是在開發和測試環境中。
-	驗證 - Web Apps 預設支援匿名驗證以及應用程式所指定的表單驗證。當應用程式僅與 Azure Active Directory 和 ADFS 整合時，Web Apps 可提供 Windows 驗證。有關此功能更詳細的討論請參閱[此處](http://aka.ms/azurebizapp) 
-	GAC 架構組件 – Web Apps 不允許將組件部署至全域組件快取 (GAC)。因此如果正在移轉的應用程式在內部部署使用了這項功能，請考慮將組件移至應用程式的 bin 資料夾。
-	IIS5 相容性模式 - Web Apps 不支援 IIS5 相容性模式，因此，每個 Web Apps 執行個體與上層 Web Apps 執行個體下的所有 Web 應用程式會在單一應用程式集區內的相同背景工作處理序中執行。
-	COM 元件的使用 – Web Apps 不允許在平台上註冊 COM 元件。因此，如果應用程式使用任何 COM 元件，則必須以 Managed 程式碼重新撰寫這些元件並利用應用程式進行部署。
-	ISAPI 篩選器 – Web Apps 上可支援 ISAPI 篩選器。它們必須作為應用程式的一部分進行部署，並在 Web 應用程式的 web.config 檔案中註冊。如需詳細資訊，請參閱 [http://aka.ms/azurewebsitesxdt](../web-sites-transform-extend/)。 

在思考了這些主題之後，您的 Web 應用程式應該可以開始在雲端中使用。別擔心，如果部分主題未達到要求，移轉工具仍會盡全力移轉。

移轉程序中的下一個步驟是建立 App Service Web 應用程式和 Azure SQL Database。Web Apps 執行個體有多種大小，內含不同數目的 CPU 核心和 RAM 數量可供您根據 Web 應用程式需求進行選擇。如需詳細資訊與定價，請參閱 [http://aka.ms/azurewebsitesskus](/pricing/details/websites/)。同樣地，Microsoft Azure SQL Database 能夠兼顧企業的所有需求，透過各種服務層級和效能層級來實現需求。您可以在 [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/) 找到進一步資訊。建立應用程式之後，您便可透過 FTP 或 WebDeploy 將它們上傳至 App Service Web Apps，然後移入資料庫。

在此移轉中，解決方案會使用 Azure SQL Database，但這不是 Azure 唯一支援的資料庫。公司也可以透過附加元件 (可以在 [Azure Store](/marketplace/partner-program/) 購買) 來使用 MySQL、MongoDB、Azure DocumentDB 等產品。

建立 Azure SQL Database 時，有幾個選項可用來從內部部署伺服器匯入現有資料庫，包括產生現有資料庫的指令碼，以及使用[資料層應用程式匯出和匯入](http://aka.ms/dacpac)。

建立費用報銷應用程式資料庫的方法是建立新的 Azure SQL Database，使用 SQL Server Management Studio 連線到資料庫，然後執行指令碼來建置資料庫結構描述，並填入來自內部部署資料庫的資料。

在移轉的第一個階段中，最後一個步驟需要為應用程式更新資料庫的連接字串。您可以透過 Azure 入口網站執行此作業。針對每個 Web 應用程式，您可以修改應用程式特定設定，包括應用程式用來連線到任何使用中資料庫的任何連接字串。

### 使用 Azure SQL Database 的替代方案 ###

為了將 Azure SQL Database 作為 Web 應用程式主要資料庫使用，Azure 平台提供了幾種替代方式，以啟用不同的工作負載 (例如使用 NoSQL 方案)，或提供符合業務資料需求的平台。 例如，企業可能擁有不能在異地或公用雲端環境中儲存的資料，因此將尋求維護使用內部部署資料庫。

#### 內部部署資源的連線能力 ####
App Service Web Apps 提供兩種可連線到內部部署資源 (例如資料庫) 的方式，可讓您重複使用現有的高價值基礎結構。這兩種方法是 Web Apps 虛擬網路整合和混合式連線：

- Web Apps 虛擬網路支援 Web Apps 與 Azure 虛擬網路之間的整合，可讓您存取在虛擬網路中執行的資源，如果使用站台對站台 VPN 連線到您內部部署網路，則可讓您直接連線到您的內部部署系統。
- 混合式連線是 Azure BizTalk 服務的一項功能，可提供簡單的方法來連線到個別的內部部署資源，例如 SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

#### 調整和復原 ####

隨著企業的員工逐漸增加 (透過併購或自然成長)，太多的 Web 應用程式必須進行調整以滿足新的需求。今日，常可看到散佈各地的共置團隊和遠端員工，例如公司在美國、歐洲和亞洲都有辦公室，並在更多的領域中設有行動銷售部門。Web Apps 有能力以輕鬆自動擴充的方式處理彈性變更。

App Service Web Apps 可讓您透過 Azure 入口網站，將 Web 應用程式設定為根據兩個動因 (排定時間或依 CPU 使用量) 自動調整。Web Apps 自動調整提供了一個符合成本效益且極具彈性的方式，以滿足所有企業應用程式使用方式的大量變更需求，包括 Web 應用程式 (例如費用報銷報告系統) 和行銷網站 (短暫促銷時間的高峰流量)。如需有關調整使用 Web Apps 的 Web 應用程式詳細資訊和指引，請參閱[如何調整網站](web-sites-scale.md)。

除了調整彈性 Web Apps 以外，整體平台可透過 Web 應用程式及其資產的可能分配，在多個資料中心和地理區域中提供業務持續性和復原功能。

## 摘要 ##
App Service Web Apps 針對快速發展環境中的企業動態需求，提供彈性、符合成本效益和回應靈敏的解決方案。Web Apps 利用具備現代 DevOps 功能的受管理平台並減少實際操作管理，同時提供調整、彈性、安全性及與內部部署資產整合等方面的企業功能，協助企業提高產能和效率。

## 動作的呼叫 ##
如需有關 Azure App Service Web Apps 服務的詳細資訊，請造訪 [http://aka.ms/enterprisewebsites](/sservices/websites/enterprise/)，您可在此找到更多資訊，現在立即註冊試用版，網址是 [http://aka.ms/azuretrial](/pricing/free-trial/)，即可評估服務並找到企業優勢。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=62-->