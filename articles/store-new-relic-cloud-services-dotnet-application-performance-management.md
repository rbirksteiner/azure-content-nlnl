<properties 
	pageTitle="將 New Relic 與 Azure 搭配使用 - Azure 功能指南" 
	description="了解如何使用 New Relic 服務來管理與監控 Azure 應用程式。" 
	services="" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>



#Azure 上的 New Relic 應用程式效能管理

本指南說明如何將 New Relic 的世界級效能監視加入至 Azure 託管的應用程式。我們將討論以快速又簡單的程序將 New Relic 加入至應用程式，並介紹一些 New Relic 功能。如需有關使用 New Relic 的詳細資訊，請參閱＜[使用 New Relic](#using-new-relic)＞。

什麼是 New Relic？--

New Relic 是一種開發人員專用工具，可監視生產應用程式，並深入探索其效能和可靠性。主要是讓您在識別和診斷效能問題時可節省時間，並隨手取得解決這些問題所需的資訊。

New Relic 會追蹤 Web 交易的載入時間和輸送量，包括來自伺服器和使用者瀏覽器。它會顯示您在資料庫中花費多少時間、分析較慢的查詢和 Web 要求、提供運作時間監視和警示、追蹤應用程式例外狀況，還有許多其他功能。

透過 Azure 市集享受 New Relic 優惠價格 --

New Relic Standard 供 Azure 使用者免費使用。New Relic Pro 依 Azure 雲端服務的執行個體大小來報價

如需定價資訊，請參閱＜[Azure 市集的 New Relic 頁面](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic)＞。

> [AZURE.NOTE]最多只列出 10 個運算執行個體的定價。若超過 10 個，請連絡 New Relic (sales@newrelic.com) 詢問批量價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱 2 週。

使用 Azure 市集註冊 New Relic --

New Relic 與 Azure Web 角色和背景工作角色緊密整合。

若要直接從 Azure 市集註冊 New Relic，請依照下列三個簡單的步驟。

### 步驟 1.透過 Azure 市集註冊

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在管理入口網站的下方窗格中，按一下 [新增]。
3. 按一下 [市集]。
4. 在 [選擇附加元件] 對話方塊中，選取 [New Relic]，然後按 [下一步]。
5. 在 [個人化附加元件] 對話方塊中，選取您要的 New Relic 方案。
6. 輸入促銷代碼 (若有的話)。
7. 輸入要在 Azure 設定中顯示的 New Relic 服務名稱，或接受預設值 **NewRelic**。此名稱在已訂閱的 Azure 市集項目清單中必須是唯一的。
8. 選擇地區值，例如 [美國西部]。
9. 按 [下一步]。
10. 在 [檢閱購買項目] 對話方塊中，檢閱方案和價格資訊，並檢閱法律條款。如果您同意接受這些條款，請按一下 [購買]。
11. 按一下 [購買] 之後，New Relic 帳戶的建立程序就會開始。您可以在 Azure 管理入口網站中監視狀態。
12. 若要擷取 New Relic 授權金鑰，請按一下 [輸出值]。 
13. 複製出現的授權金鑰。安裝 New Relic Nuget 套件時需要輸入此金鑰。

### 步驟 2.安裝 Nuget 封裝

1. 開啟 Visual Studio 方案，或選取 [檔案] > [新增] > [專案] 來建立新專案。

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. 如果方案尚無 Azure 雲端服務專案，請在 [方案總管] 中以滑鼠右鍵按一下應用程式，然後選取 [加入 Azure 雲端服務專案]。

	![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. 選取 [工具] > [程式庫套件管理員] > [套件管理器主控台] 來開啟 [套件管理器主控台]。在 [套件管理器主控台] 視窗頂端，將您的專案設為預設專案。

	![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. 在封裝管理員命令提示字元中，輸入 `Install-Package
   NewRelicWindowsAzure`，然後按 **Enter**。

	![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. 在授權金鑰提示上，輸入您從 Azure 市集取得的授權金鑰。

	![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. 選擇性：在應用程式名稱提示上，輸入要在 New Relic 儀表板中顯示的應用程式名稱。或者，使用方案名稱做為預設值。

	![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. 從 [方案總管] 中，以滑鼠右鍵按一下 Azure 雲端服務專案並選取 [發行]。

	![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**注意：**如果這是您第一次將此應用程式部署至 Azure，系統會提示您輸入 Azure 認證。如需詳細資訊，請參閱<a href="/develop/net/tutorials/get-started/">將 ASP.NET Web 應用程式部署至 Azure 網站</a>。

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### 步驟 3.在 New Relic 中檢查應用程式的效能。

檢視 New Relic 儀表板：

1. 從 Azure 入口網站，按一下 [管理] 按鈕。
2. 以您的 New Relic 帳戶電子郵件和密碼登入。
3. 從 New Relic 功能表列，選取 [Applications] > (應用程式的名稱)。

	自動會顯示 [Monitoring] > [Overview] 儀表板。

	![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	從 [Applications] 功能表的清單中選取應用程式之後，[Overview] 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。

### <a id="using-new-relic"></a>使用 New Relic

從 [Applications] 功能表的清單中選取應用程式之後，[Overview] 儀表板會顯示目前的應用程式伺服器和瀏覽器資訊。若要在這兩個檢視之間切換，請按一下 [App server] 或 [Browser] 按鈕。

除了<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準 New Relic UI</a>和<a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">儀表板向下鑽研</a>功能，[Applications Overview] 儀表板還有其他功能。

<table border="1">
  <thead>
    <tr>
      <th><b>如果您想要...</b></th>
      <th><b>執行此動作...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>顯示所選取應用程式的伺服器或瀏覽器的儀表板資訊</td>
       <td>按一下 [App Server]<b></b> 或 [Browser]<b></b> 按鈕。</td>
    </tr>
     <tr>
       <td>檢視應用程式的 <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> 分數的臨界值層級</td>
       <td>指向 [Apdex score <b>?<b>] 圖示。</b></b></td>
    </tr>
    <tr>
       <td>檢視全球 Apdex 詳細資料</td>
       <td>從 [Overview] 的 [Browser]<b></b> 檢視中，指向 Global Apdex 地圖的任意處。<br /><b>秘訣：</b>若要直接移至選取的應用程式的 <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">Geography</a> 儀表板，請按一下 [Global Apdex]<b></b> 標題，或按一下 Global Apdex 地圖的任意處。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a> 儀表板</td>
       <td>按一下 [Applications Overview] 儀表板的 [Web Transactions] 資料表。或者，若要檢視特定 Web 交易 (包括 <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) 的詳細資料，請按一下其名稱。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a> 儀表板</td>
       <td>按一下 [Applications Overview] 儀表板的 [Error rate] 圖表標題。<br /><b>祕訣：</b>您也可以從 [Applications]<b></b> > (您的應用程式) > [Events] > [Errors] 來檢視 [Errors] 儀表板。</td>
    </tr>
    <tr>
       <td>檢視應用程式的伺服器詳細資料</td>
       <td><p>執行下列任何動作：<p>
        <ul>
          <li>在主機的資料表檢視或每個主機的分組度量詳細資料之間切換。</li>
          <li>按一下個別伺服器的名稱。</li>
          <li>指向個別伺服器的 Apdex 分數。</li>
          <li>按一下個別伺服器的 CPU 使用率或記憶體。</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

以下是選取 [Browser] 檢視時的 [Applications Overview] 儀表板範例。

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## 後續步驟

如需詳細資訊，請參閱下列其他資源：

 * [在 Azure 上安裝 .NET 代理程式](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure) (英文)：New Relic .NET 代理程式安裝程序 
 * [New Relic 使用者介面](https://newrelic.com/docs/site/the-new-relic-ui) (英文)：New Relic UI、設定使用者權限和設定檔，以及使用標準功能和儀表板向下鑽研詳細資料的概觀
 * [應用程式概觀](https://newrelic.com/docs/site/applications-overview) (英文)：使用 New Relic 的 [Applications Overview] 儀表板時可用的功能
 * [Apdex](https://newrelic.com/docs/site/apdex) (英文)：Apdex 如何衡量應用程式的使用者滿意度概觀
 * [實際使用者監控](https://newrelic.com/docs/features/real-user-monitoring) (英文)：RUM 如何詳述使用者瀏覽器載入網頁所需的時間、使用者來自何方及使用什麼瀏覽器的概觀
 * [尋找說明](https://newrelic.com/docs/site/finding-help) (英文)：New Relic 線上說明中心提供的資源

<!---HONumber=July15_HO1-->