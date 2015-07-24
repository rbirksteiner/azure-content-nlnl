<a name="tellmeas"></a>
## 了解 App Service

Azure 虛擬機器可以處理多種不同的雲端裝載工作。但是，建立和管理 VM 基礎結構需要特殊的技能和大量人力。如果您不需要完全控制執行您的 Web 應用程式、行動應用程式後端、API 應用程式等的 VM，則有一個較簡單 (且成本較低) 的解決方案：*平台即服務* (PaaS)。使用 PaaS 時，Azure 會為執行應用程式的 VM 處理大部分的管理工作。[Azure App Service](../article/app-service/app-service-value-prop-what-is.md) 是可以完全管理的 PaaS 供應項目，可讓您在數秒內建置、部署及調整企業級應用程式。

App Service 是許多應用程式工作負載種類的最佳選擇。公司可能想要建置或移轉能夠處理一週數百萬次點擊，並且可部署到全球多個資料中心的商用網站。同一家公司可能也有從公司 Active Directory 追蹤已驗證使用者之費用報表的企業營運系統應用程式，且該應用程式可能會有行動裝置元件，並連接到內部部署資源和商務程序。費用報告可能需要以定期背景工作來計算和彙總大量的資訊。IT 顧問可能採用常用的開放原始碼應用程式，設定小型企業的內容管理系統。下圖顯示一些可以在 Azure App Service 中執行的 Web 應用程式種類。

<a name="appservice_diagram"></a> ![App Service 圖表](media/app-service-choose-me-content/diagram.png)
 
**圖：Azure App Service 支援靜態網頁、熱門 Web 應用程式，以及使用各種技術建立的自訂 Web 應用程式。您也可以執行行動後端、API 應用程式和非 Web 計算工作負載 (使用 WebJobs)。**

透過 Azure App Service，您也可以使用 [WebJobs](../article/app-service-web/websites-webjobs-resources.md) 功能執行任何種類的計算工作負載。

Azure App Service 可讓您選擇在包含多個使用者所建立之多個應用程式的共用 VM 上執行，或在只由您使用的 VM 上執行。VM 是 Azure App Service 所管理之資源集區的一部分，因此可提供高度可靠性及容錯能力。

開始使用相當簡單。透過 Azure App Service，使用者可以在一系列應用程式、架構和範本中選擇，並在數秒內建立起 Web 應用程式。使用者可以接著使用自己最愛的開發工具 (WebMatrix、Visual Studio、其他任何編輯器) 和原始檔控制選項來設定持續整合，並且進行團隊開發。以 MySQL 資料庫為基礎的應用程式可以使用 Microsoft 合作夥伴 ClearDB 對於 Azure 提供的 MySQL 服務。

開發人員可以透過 Azure App Service 建立大型、可調整的 Web 應用程式。這項技術支援使用 ASP.NET、PHP、Node.js 和 Python 建立應用程式。應用程式可以使用黏性工作階段，許多現有的 Web 應用程式也可以移到此雲端平台，而不必進行變更。在 Azure App Service 上建置的 Web 應用程式可以使用 Azure 的其他層面，例如服務匯流排、SQL Database 和 Blob 儲存體。您也可以在不同的 VM 中執行多個應用程式副本，Azure App Service 會在這些副本之間自動進行負載平衡要求。而因為新 Web 應用程式執行個體是建立在已存在的 VM 中，所以啟動新應用程式執行個體非常快速，比等候新的 VM 建立更快。

如上[圖](#appservice_diagram)所示，您可以用數種方式發佈程式碼和其他 Web 內容至 Azure App Service。您可以使用 FTP、FTPS 或 Microsoft 的 WebDeploy 技術。Azure App Service 也支援從原始檔控制系統 (包括 Git、GitHub、CodePlex、BitBucket、Dropbox、Mercurial、Team Foundation Server 和雲端型 Team Foundation Service) 發佈程式碼。

<!---HONumber=62-->