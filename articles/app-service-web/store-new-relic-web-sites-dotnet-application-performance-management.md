<properties 
	pageTitle="在 Azure App Service 中的 .NET Web 應用程式使用 New Relic 應用程式效能管理" 
	description="了解如何在 Azure App Service 上執行的 ASP.NET 應用程式使用 New Relic 的效能監視。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="stepsic"/>



# 在 Azure App Service 中的 .NET Web 應用程式使用 New Relic 應用程式效能管理

本指南說明如何將 New Relic 的世界級效能監視加入至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中的 Web 應用程式。我們將討論以快速又簡單的程序將 New Relic 加入至應用程式，並介紹一些 New Relic 功能。如需有關使用 New Relic 的詳細資訊，請參閱＜[使用 New Relic](#using-new-relic)＞。

## 什麼是 New Relic？

New Relic 是一種開發人員專用工具，可監視生產應用程式，並深入探索其效能和可靠性。主要是讓您在識別和診斷效能問題時可節省時間，並隨手取得解決這些問題所需的資訊。

New Relic 會追蹤 Web 交易的載入時間和輸送量，包括來自伺服器和使用者瀏覽器。它會顯示您在資料庫中花費多少時間、分析較慢的查詢和 Web 要求、提供運作時間監視和警示、追蹤應用程式例外狀況，還有許多其他功能。

## 透過 Azure 市集享受 New Relic 優惠價格

New Relic Standard 供 Azure 使用者免費使用。根據您所使用的網站模式及執行個體大小 (如果使用保留模式)，我們提供了多種 New Relic Pro 套件。

如需定價資訊，請參閱[Azure Marketplace 的 New Relic 頁面](/marketplace/partners/newrelic/newrelic)。

> [AZURE.NOTE]最多只列出 10 個運算執行個體的定價。若超過 10 個，請連絡 New Relic (sales@newrelic.com) 詢問批量價格。

Azure 客戶部署 New Relic 代理程式時享有 New Relic Pro 試用訂閱 2 週。

使用 Azure Marketplace 註冊 New Relic --

New Relic 與 Azure Web 角色、背景工作角色和 Azure App Service 緊密整合。

若要直接從 Azure Marketplace 市集註冊 New Relic，請依照下列四個簡單的步驟。

## 步驟 1.建立 New Relic 帳戶

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)，然後按一下左下角的 [新增]。
3. 按一下 [開發人員服務] > [New Relic APM]。
4. 指定下列各項來設定 New Relic 帳戶，然後按一下 [建立]。
	- **Name**
	- **定價層**
	- **資源群組**
	- **訂用帳戶**
	- **位置**
	- **法律條款**

11. 按一下 [建立] 之後，New Relic 帳戶的建立程序就會開始。您可以按一下 [通知] 按鈕來監視狀態。建立之後，隨即會顯示 New Relic 帳戶的刀鋒視窗。

12. 若要擷取 New Relic 授權金鑰，請查看刀鋒視窗頂端的 [Essentials] 面板。當您將 Web 應用程式與 New Relic 帳戶整合時，您的 Web 應用程式執行個體將在其應用程式設定中自動註冊此授權金鑰。

## 步驟 2：設定 Web 應用程式的 New Relic 整合

2. 在 [Azure 預覽入口網站](https://portal.azure.com)中，開啟 Web 應用程式的刀鋒視窗。
3. 按一下 [應用程式監視] > [New Relic]。選取您在前一個步驟中建立的檔案，然後按一下 [確定]。 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	完成儲存操作之後，在 Web 應用程式刀鋒視窗中，按一下 [所有設定]，然後按一下 [應用程式設定]。您應會看見 **NEWRELIC_LICENSEKEY** 設定已新增至刀鋒視窗的 [應用程式設定] 區段，以支援 New Relic：

	>[AZURE.NOTE]新的應用程式設定最多可能需要 30 秒的時間才能生效。若要強制設定立即生效，請重新啟動 Web 應用程式。

## 步驟 3：發佈 ASP.NET Web 應用程式

使用 Visual Studio 或 WebMatrix 來發佈 Web 應用程式。如果您先前已發佈 Web 應用程式，再次發佈以便 Web 應用程式執行個體新增必要的 New Relic NuGet 封裝以啟用 New Relic 監視。

## 步驟 4.在 New Relic 中檢查應用程式的效能。

檢視 New Relic 儀表板：

2. 在 [Azure 預覽入口網站](https://portal.azure.com)中，開啟 Web 應用程式的刀鋒視窗。
3. 按一下 [應用程式監視] > [應用程式名稱] > [在 New Relic 檢視]。

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. 如果這是您首次使用帳戶，請設定您的帳戶資訊。
3. 從 New Relic 功能表列，選取 [Applications] > (應用程式的名稱)。

	自動會顯示 [Monitoring] > [Overview] 儀表板。

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

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
       <td>從 [Overview] 的 [Browser]<b></b> 檢視中，指向 Global Apdex 地圖的任意處。<br /><b>秘訣：</b>若要直接移至選取的應用程式的 <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> 儀表板，請按一下 [Global Apdex]<b></b> 標題，或按一下 Global Apdex 地圖的任意處。</td>
    </tr>
    <tr>
       <td>檢視 <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a> 儀表板</td>
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

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## 後續步驟

如需詳細資訊，請參閱下列其他資源：

 * [為 Azure 網站安裝 .NET 代理程式](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual) (英文)：New Relic .NET 代理程式安裝程序 
 * [New Relic 使用者介面](https://newrelic.com/docs/site/the-new-relic-ui) (英文)：New Relic UI、設定使用者權限和設定檔，以及使用標準功能和儀表板向下鑽研詳細資料的概觀
 * [應用程式概觀](https://newrelic.com/docs/site/applications-overview) (英文)：使用 New Relic 的 [Applications Overview] 儀表板時可用的功能
 * [Apdex](https://newrelic.com/docs/site/apdex) (英文)：Apdex 如何衡量應用程式的使用者滿意度概觀
 * [實際使用者監控](https://newrelic.com/docs/features/real-user-monitoring) (英文)：RUM 如何詳述使用者瀏覽器載入網頁所需的時間、使用者來自何方及使用什麼瀏覽器的概觀
 * [尋找說明](https://newrelic.com/docs/site/finding-help) (英文)：New Relic 線上說明中心提供的資源

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需 Azure 入口網站變更為 Azure 預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


[webmatrixwebsite]: web-sites-dotnet-using-webmatrix.md
[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=62-->