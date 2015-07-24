<properties 
	pageTitle="Azure App Service 和它對現有 Azure 服務的影響" 
	description="說明新的 Azure App Service 和其功能如何影響 Azure 中的現有服務。" 
	authors="yochayk" 
	writer="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Azure App Service 和現有的 Azure 服務

本文概述現有 Azure 服務的變更做為將數個 Azure 服務一起帶入 [Azure App Service](http://azure.microsoft.com/services/app-service/) (新的整合服務) 的變更一部分。

## 概觀 

[Azure App Service](http://azure.microsoft.com/services/app-service/) 是一種新的且唯一的雲端服務，可讓開發人員建立適用於任何平台和任何裝置的 Web 和行動應用程式。App Service 是一種整合解決方案，設計目的是簡化重複的程式碼撰寫函式、整合企業和 SaaS 系統，並自動化商務程序，同時滿足您的安全性、可靠性和延展性需求。

App Service 會將下列現有 Azure 服務 - [網站](http://azure.microsoft.com/services/websites/)、[行動服務](http://azure.microsoft.com/services/mobile-services/)和 [Biztalk 服務](http://azure.microsoft.com/services/biztalk-services/)整合為單一結合服務，同時新增強大的功能。App Service 可讓您裝載下列應用程式類型：

-   Web 應用程式
-   行動應用程式
-   API 應用程式
-   邏輯應用程式

下表說明現有 Azure 服務如何對應至應用程式服務和其內可用的應用程式類型。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">現有 Azure 服務</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">變更內容</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 網站</td>
<td align="left">Web 應用程式</td>
<td align="left"><li>針對 Azure 網站，嚴格限制應用程式服務將名稱
<p><li>所有您的現有網站執行個體現在都是 App Service 中的 Web 應用程式。</p>
<p><li>您可以透過 <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure 入口網站</a>存取現有網站，在此入口網站中，您將在 [<em>Web 應用程式</em>] 下找到您的所有現有網站。</p>
<p><li><em>Web 裝載方案</em>現在為 <em>App Service 方案</em>。<em>App Service 方案</em>可以裝載 App Service 的任何應用程式類型，例如 Web、行動、邏輯或 API 應用程式。</p>
<p><li>Azure App Service Web 應用程式已正式推出。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">深入了解 Web 應用程式</a></p></td>
</tr>
<tr class="even">
<td align="left">Azure 行動服務</td>
<td align="left">行動應用程式</td>
<td align="left"><p><li>行動服務繼續可當作獨立服務，並仍提供完整支援。</p>
<p><li>行動應用程式是應用程式服務中的新應用程式類型，以整合行動服務和其他項目的所有功能。行動應用程式處於公開預覽狀態。</p>
<p><li>可以輕鬆地<a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">從行動服務移轉到行動應用程式</a>因為行動應用程式仍處於預覽狀態，所以仍不建議您執行生產應用程式。</p>
<p><li>由於隸屬於 App Service，行動應用程式可以取得行動服務以外的新功能，例如與內部部署和 SaaS 系統整合、預備位置、更好的縮放選項，以及其他。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">深入了解行動應用程式</a>。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API 應用程式</td>
<td align="left">
<p><li>API 應用程式是 App Service 中的新應用程式類型，可讓您在雲端中輕鬆地建置和使用 API。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">深入了解 API 應用程式</a>。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">邏輯應用程式</td>
<td align="left">
<p><li>邏輯應用程式是 App Service 中的新應用程式類型，可讓您輕鬆地自動化商務程序。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">深入了解邏輯應用程式</a>。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 服務</td>
<td align="left">BizTalk API 應用程式</td>
<td align="left">
<li><p>BizTalk 服務繼續可當作獨立服務，並仍提供完整支援。</p>
<li><p>BizTalk 服務的所有功能都會整合到 App Service，因為 API 應用程式可讓使用者執行與 App Service 中任何應用程式類型的企業應用程式整合和 B2B 整合案例</p>
<li><p>您現在可以使用邏輯應用程式，透過視覺化設計體驗自動化商務程序來建立工作流程</p></td>
</tr>
</tbody>
</table>

若要深入了解，請瀏覽 [App Service 文件](http://azure.microsoft.com/documentation/services/app-service/)。
 

<!---HONumber=62-->