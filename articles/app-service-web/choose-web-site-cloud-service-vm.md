<properties
	pageTitle="Azure App Service、雲端服務與虛擬機器之比較"
	description="了解何時使用 Azure App Service、雲端服務和虛擬機器來裝載 Web 應用程式。"
	services="app-service\web, virtual-machines, cloud-services"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/29/2015"
	ms.author="tdykstra"/>

# Azure App Service、雲端服務與虛擬機器之比較

## 概觀

Azure 提供數種裝載網站的方式：[Azure App Service][]、[雲端服務][]和[虛擬機器][]。本文協助您了解這些選項，為您的 Web 應用程式做出正確的選擇。

Azure App Service 是大多數 Web 應用程式的最佳選擇。部署和管理都已整合到平台，網站可以迅速調整規模以因應過高的流量負載，而內建的負載平衡和流量管理員提供高可用性。您可以使用[線上移轉工具](https://www.migratetoazure.net/)輕鬆地將現有網站移至 Azure App Service、使用 Web 應用程式庫中的開放原始碼應用程式，或使用您選擇的架構和工具來建立新網站。[WebJobs][] 功能可讓您輕鬆地將背景工作處理加入至 App Service Web 應用程式中。

如果您需要加強控制 Web 伺服器環境，例如想要從遠端登入伺服器或設定伺服器啟動工作，Azure 雲端服務通常是最佳選項。

如果現有應用程式需要進行大幅修改才能在 Azure App Service 或 Azure 雲端服務中執行，您可以選擇 Azure 虛擬機器來簡化移轉至雲端的工作。不過，相較於 Azure App Service 和雲端服務，正確設定、保護和維護 VM 需要投入更多時間和 IT 專業知識。如果您考慮採用 Azure 虛擬機器，請確定您已將修補、更新和管理 VM 環境所需的持續性維護工作都納入考量。

下圖針對 Azure 的每一個 Web 裝載選項，說明控制能力與簡易使用性之間的相對關係。

![ChoicesDiagram][ChoicesDiagram]

##<a name="scenarios"></a>案例和建議

關於哪個 Azure Web 裝載選項最適合，以下是一些常見的應用程式案例與建議。

- [我需要一個具有背景處理和資料庫後端的 Web 前端，以執行與內部部署資源整合的商業應用程式。](#onprem)
- [我需要可靠的方法來裝載可靈活調整且可供全球存取的公司網站。](#corp)
- [我有一個在 Windows Server 2003 上執行的 IIS6 應用程式。](#iis6)
- [我是小型企業業主，需要有便宜的方式來裝載網站，但又想顧及網站未來成長的可能性。](#smallbusiness)
- [我是網頁或平面設計師，想要幫客戶設計及建立網站。](#designer)
- [我想將含 Web 前端的多層式應用程式移轉至雲端。](#multitier)
- [我的應用程式需要在高度自訂的 Windows 或 Linux 環境中才能運作，我想要將它移轉至雲端。](#custom)
- [我的網站使用開放原始碼軟體，我想要將該軟體裝載在 Azure 上。](#oss)
- [我有個主要商務應用程式需要連線至公司網路。](#lob)
- [我想要裝載 REST API 或 Web 服務供行動用戶端使用。](#mobile)


### <a id="onprem"></a>我需要一個具有背景處理和資料庫後端的 Web 前端，以執行與內部部署資源整合的商業應用程式。

Azure App Service 是複雜商業應用程式的絕佳解決方案。您開發的應用程式將能夠在負載平衡平台上自動調整、採用 Active Directory 來保護，以及連接到內部部署資源。它可讓您透過世界級的管理入口網站和 API 來輕鬆管理應用程式，並利用應用程式洞察工具來深入了解客戶如何使用應用程式。新的 [Webjobs][] 功能可讓您在 Web 層執行背景程序和工作，而混合式連線和 [VNET 功能](../fundamentals-introduction-to-azure.md#networking/)可讓您輕鬆地連回到內部部署資源。Azure App Service 為 Web 應用程式提供三個 9 的 SLA，可讓您：

* 在自我修復、自動修補的雲端平台上可靠地執行應用程式。
* 在全球的資料中心網路上自動調整。
* 針對災害復原進行備份與還原。
* 符合 ISO、SOC2 和 PCI。
* 與 Active Directory 整合

### <a id="corp"></a>我需要可靠的方法來裝載可靈活調整且可供全球存取的公司網站。

Azure App Service 是裝載公司網站的絕佳解決方案。它可讓 Web 應用程式快速調整，輕鬆符合全球資料中心網路的需求。它提供本機存取、容錯和智慧型流量管理。一切盡在一個提供世界級管理工具的平台上，可讓您快速又輕鬆地深入了解網站健康情況和網路流量。Azure App Service 為 Web 應用程式提供三個 9 的 SLA，可讓您：

* 在自我修復、自動修補的雲端平台上可靠地執行網站。
* 在全球的資料中心網路上自動調整。
* 針對災害復原進行備份與還原。
* 使用整合式工具來管理記錄和流量。
* 符合 ISO、SOC2 和 PCI。
* 與 Active Directory 整合

### <a id="iis6"></a> 我有一個在 Windows Server 2003 上執行的 IIS6 應用程式。

Azure App Service 可讓您輕鬆地省去移轉舊版 IIS6 應用程式時相關的基礎結構成本。Microsoft 建立[簡單易用的移轉工具和詳細的移轉指引](https://www.movemetowebsites.net/)，可讓您檢查相容性和識別任何需要進行的變更。與 Visual Studio、TFS 和一般 CMS 工具整合，讓您輕鬆地將 IIS6 應用程式直接部署到雲端。部署之後，Azure 管理入口網站提供健全的管理工具，可讓您依需要而縮小規模來管理成本，或擴大規模來符合需求。移轉工具可讓您：

* 快速又輕鬆地將舊式的 Windows Server 2003 Web 應用程式移轉至雲端。
* 選擇將連接的 SQL 資料庫留在內部部署，以建立混合式應用程式。
* 自動隨著舊式應用程式一起移動 SQL 資料庫。

### <a id="smallbusiness"></a>我是小型企業業主，需要有便宜的方式來裝載網站，但又想顧及網站未來成長的可能性。

Azure App Service 是此案例的絕佳解決方案，因為您可先免費使用它，等到需要更多功能時再新增功能即可。每一個免費 Web 應用程式都隨附 Azure 提供的網域 (*your_company*.azurewebsites.net)，且平台包含整合式部署和管理工具及應用程式庫，輕鬆地就能開始使用。另外還有其他許多服務與調整選項，可讓網站隨使用者需求的增加來發展。使用 Azure App Service，您可以：

- 從免費版本開始，視需要進行向上調整。
- 使用應用程式庫迅速設定熱門的 Web 應用程式，例如 WordPress。
- 視需要新增其他 Azure 服務與功能至您的應用程式。
- 使用 HTTPS 來保護 Web 應用程式。

### <a id="designer"></a> 我是網頁或平面設計師，想要幫客戶設計及建立網站

對於 Web 開發人員和設計人員，Azure App Service 可輕鬆整合各種架構和工具、提供 Git 和 FTP 的部署支援，並與 Visual Studio 和 SQL Database 等工具和服務密切整合。使用 App Service，您可以：

- 使用命令列工具執行[自動化工作][scripting] (英文)。
- 使用熱門語言，例如 [.Net][dotnet] (英文)、[PHP][] (英文)、[Node.js][nodejs] (英文) 及 [Python][] (英文)。
- 有三種不同的調整層級可選，以向上調整至非常高的容量。
- 與其他 Azure 服務 (如 [SQL Database][sqldatabase]、[服務匯流排][servicebus]及[儲存體][])，或 [Azure 市集][azurestore]上的合作夥伴供應項目 (如 MySQL 和 MongoDB) 整合。
- 與 Visual Studio、Git、WebMatrix、WebDeploy、TFS 和 FTP 等工具整合。

### <a id="multitier"></a>我想將含 Web 前端的多層式應用程式移轉至雲端

如果您執行多層式應用程式，例如連接到資料庫的 Web 伺服器，則 Azure App Service 是適合的選項，能夠與 Azure SQL Database 密切整合。而您可以使用 WebJobs 功能來執行背景程序。

如果您需要加強控制伺服器環境，例如想要從遠端登入伺服器或設定伺服器啟動工作，請為您的一個或多個層選擇雲端服務。

如果您要使用自己的機器映像，或執行無法在雲端服務上設定的軟體或服務，請為您的一個或多個層選擇虛擬機器。

### <a id="custom"></a>我的應用程式需要在高度自訂的 Windows 或 Linux 環境中才能運作，我想要將它移轉至雲端。

如果您的應用程式需要用到安裝或設定方式複雜的軟體與作業系統，則「虛擬機器」可能是最佳解決方案。虛擬機器可讓您：

- 使用虛擬機器庫來得到初步的作業系統，例如 Windows 或 Linux，然後根據自己的應用程式需求加以自訂。
- 建立並上傳現有內部部署伺服器的自訂映像，使其執行於 Azure 中的虛擬機器上。

### <a id="oss"></a>我的網站使用開放原始碼軟體，我想要將該軟體裝載在 Azure 上。

如果 App Service 上支援您的開放原始碼架構，則會自動設定您的應用程式所需的語言和架構。App Service 可讓您：

- 使用許多熱門的開放原始碼語言，例如 [.NET][dotnet] (英文)、[PHP][] (英文)、[Node.js][nodejs] (英文) 及 [Python][] (英文)。
- 設定 WordPress、Drupal、Umbraco、DNN 及其他許多協力廠商 Web 應用程式。
- 移轉現有的應用程式，或從應用程式庫建立新的應用程式。

如果 App Service 上不支援您的開放原始碼架構，您可以在其他兩個 Azure Web 裝載選項上執行它。「雲端服務」需要您使用啟動工作，以安裝並設定任何在 Windows 上執行的必要開放原始碼軟體。「虛擬機器」則需要您在機器映像 (可以是 Windows 或 Linux 型) 上安裝並設定軟體。

### <a id="lob"></a>我有個主要商務應用程式需要連線至公司網路。

若要建立企業營運系統應用程式，您的網站可能需要直接存取公司網路上的服務或資料。在 App Service、雲端服務和虛擬機器上利用 [Azure 虛擬網路服務](/services/virtual-network/)即可達成。在 App Service 上，您可以使用 [VNET 整合功能](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)，讓您的 Azure 應用程式彷彿就像在公司網路上執行一樣。

### <a id="mobile"></a>我想要裝載 REST API 或 Web 服務供行動用戶端使用

HTTP 型 Web 服務可讓您支援各種用戶端，包括行動用戶端。ASP.NET Web API 等架構會與 Visual Studio 整合，讓您更容易建立及取用 REST 服務。這些服務是透過 Web 端點公開，因此在 Azure 上可以使用任何 Web 裝載技術來支援此案例。不過，App Service 是裝載 REST API 的絕佳選擇。使用 App Service，您可以：

- 迅速建立一個 Web 應用程式，以將 HTTP Web 服務裝載於 Azure 全球各地的其中一個資料中心內。
- 移轉現有的服務或建立新服務。
- 以單一執行個體達到可用性 SLA，或向外延展至多部專用機器。
- 使用已發佈的網站來提供 REST API 給任何 HTTP 用戶端，包括行動用戶端。

此外，Azure App Service 有 REST API 的新預覽功能：API 應用程式。如需 API 應用程式的詳細資訊，請參閱[什麼是 API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)。

##<a name="features"></a>功能比較

下表比較 App Service、「雲端服務」與「虛擬機器」的功能，以協助您做出最佳選擇。如需每個選項目前的 SLA 資訊，請參閱＜[Azure 服務等級協定](/support/legal/sla/)＞。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">功能</th>
   <th align="left" valign="middle">App Service (Web 應用程式)</th>
   <th align="left" valign="middle">雲端服務 (Web 角色)</th>
   <th align="left" valign="middle">虛擬機器</th>
   <th align="left" valign="middle">注意事項</th>
</tr>
<tr>
   <td valign="middle"><p>近乎即時的部署</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">將應用程式或應用程式更新部署到雲端服務，或建立 VM，至少需要幾分鐘的時間；將應用程式部署到 Web 應用程式只需要幾秒鐘。</td>
</tr>
<tr>
   <td valign="middle"><p>向上調整至更大的機器而不重新部署</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Web 伺服器執行個體會共用內容和組態，這表示您在調整規模時不需要重新部署或重新設定。</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>多個部署環境 (生產環境和預備環境)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>自動管理 OS 更新</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>順暢切換平台 (輕鬆地切換到 32 位元或 64 位元)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>利用 GIT、FTP 來部署程式碼</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>利用 Web Deploy 來部署程式碼</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">雲端服務支援使用 Web Deploy 將更新部署到個別角色的執行個體。不過，您無法將它用於角色的初始部署，如果使用 Web Deploy 來部署更新，則必須分別地部署到角色的每一個執行個體。需要有多個執行個體，才能符合生產環境的雲端服務 SLA。</td>
</tr>
<tr>
   <td valign="middle"><p>支援 WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>存取服務匯流排、儲存體、SQL Database 等服務。</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>裝載多層式架構的 Web 或 Web 服務層</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>裝載多層式架構的中間層</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">App Service Web 應用程式可以輕易裝載 REST API 中間層，而 <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> 功能可以裝載背景處理工作。您可以在專用網站中執行 WebJobs，以實現此層的獨立擴充性。預覽 [API 應用程式] (../app-service-api/app-service-api-apps-why-best-platform.md) 功能提供了更多裝載 REST 服務的功能。</td>
</tr>
<tr>
   <td valign="middle"><p>整合 MySQL 即服務的支援</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">雲端服務可透過 ClearDB 的供應項目來整合 MySQL 即服務，而不需要在管理入口網站工作流程中進行。</td>
</tr>
<tr>
   <td valign="middle"><p>支援 ASP.NET、傳統 ASP、Node.js、PHP、Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>向外延展至多個執行個體而不重新部署</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">「虛擬機器」可向外延展至多個執行個體，但這些機器上執行的服務必須設計成應付這個向外延展情況。您必須設定負載平衡器來將要求路由傳送到各機器，並建立同質群組，以避免在維護或硬體故障時所有執行個體同時重新啟動。</td>
</tr>
<tr>
   <td valign="middle"><p>支援 SSL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">在 App Service Web 應用程式中，只有基本和標準模式才支援自訂網域名稱的 SSL。如需 Web 應用程式使用 SSL 的相關資訊，請參閱＜<a href="../web-sites-configure-ssl-certificate/">設定 Azure 網站的 SSL 憑證</a>＞。</td>
</tr>
<tr>
   <td valign="middle"><p>整合 Visual Studio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>遠端偵錯</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>利用 TFS 來部署程式碼</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>利用 <a href="/services/virtual-network/">Azure 虛擬網路</a>進行網路隔離</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">另請參閱＜<a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Azure 網站虛擬網路整合</a>＞</td>
</tr>
<tr>
   <td valign="middle"><p>支援 <a href="/services/traffic-manager/">Azure 流量管理員</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>整合式端點監視</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>透過桌面遠端來存取伺服器</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>安裝任何自訂 MSI</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>能夠定義/執行啟動工作</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>可接聽 ETW 事件</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]如果您要在註冊帳戶前開始使用 Azure App Service，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure App Service 中立即建立短期的免費簡易版應用程式。不需要信用卡，沒有承諾。


## <a id="nextsteps"></a> 後續步驟

如需這三個 Web 裝載選項的詳細資訊，請參閱下列資源：

* [Azure 簡介](../fundamentals-introduction-to-azure.md)
* [計算 Azure 所提供的裝載選項](../fundamentals-application-models.md)

若要開始對應用程式使用您選擇的選項，請參閱下列資源：

* [Azure 網站](/documentation/services/app-service/)
* [Azure 雲端服務](/documentation/services/cloud-services/)
* [Azure 虛擬機器](/documentation/services/virtual-machines/)

  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Azure App Service]: /services/app-service/
  [雲端服務]: http://go.microsoft.com/fwlink/?LinkId=306052
  [虛擬機器]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/gallery/store/
  [scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/develop/net/
  [nodejs]: http://www.windowsazure.com/develop/nodejs/
  [PHP]: http://www.windowsazure.com/develop/php/
  [Python]: http://www.windowsazure.com/develop/python/
  [servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
  [儲存體]: http://www.windowsazure.com/documentation/services/storage/
 

<!---HONumber=62-->