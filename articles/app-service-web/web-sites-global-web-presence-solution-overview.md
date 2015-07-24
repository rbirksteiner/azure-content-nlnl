<properties 
	pageTitle="在 Azure App Service Web Apps 上建立全域網站空間" 
	description="本指南提供技術概觀，說明如何在 Azure App Service Web Apps 上託管您的組織的 (.COM) 網站。這包括部署、自訂網域、SSL 和監視。" 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="cephalin"/>


# 在 Azure App Service Web Apps 上建立全域網站空間

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps 提供建立 .COM 網站全域網站空間所需的全部功能。無論組織的規模大小，您都需要強固、安全且可擴充的平台，以促進您的業務、品牌認知以及客戶通訊。App Service Web Apps 藉由 Microsoft 支援的營運持續性，協助維持企業的品牌與識別。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

以下是在 App Service Web Apps 上執行的.COM 網站範例。此範例示範不需要使用複雜的技術，只要組合 Web Apps 和其他服務便能完成的工作。**按一下拓樸中的項目以讀取詳細資訊**。

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]本指南說明在 Azure App Service Web Apps 中執行公用端 .COM 網站最常見的區域和工作。不過，還有其他可在 Azure App Service Web Apps 中實作的常見解決方案。若要檢閱這些解決方案，請同時參閱[數位行銷活動](web-sites-digital-marketing-application-solution-overview.md)與[商務應用程式](web-sites-business-application-solution-overview.md)方面的其他指南。

## 從頭開始建立或沿用現有資產

從組件庫中受歡迎的 CMS 快速建立新網站，或在 App Service Web 應用程式中沿用現有各種不同語言與架構的 Web 資產。

Azure Marketplace 提供受歡迎網站內容管理系統 (CMS) 的範本，例如 [Orchard]、[Umbraco]、[Drupal] 以及 [WordPress]。您可以使用喜愛的 CMS 風格來建立 Web 應用程式。您可以根據自己的需求從各種不同的資料庫後端中選擇，包括 [Azure SQL Database] 和 [MySQL]。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 App Service Web Apps 上執行。您可以使用熟悉的 [FTP] 工具或原始檔控制管理系統，將它們移至 Web Apps。Web Apps 支援從 [Visual Studio]、[Visual Studio Online] 以及 [Git] (本機、GitHub、BitBucket、DropBox、Mercurial 等) 等受歡迎的原始檔控制選項直接發佈。

## 可靠地發行

從現有的原始檔控制系統並即時測試內容，持續地直接發行，可靠地發行網站。

在網站的計劃、原型設計及初期開發期間，您可以在網站上線之前，在 App Service Web Apps 上先將您的網站[部署到預備位置]，以查看網站的實際運作版本。透過整合原始檔控制與 Web Apps ，您可以[持續發佈] (英文) 到預備位置，並在您準備好時在沒有停機時間的情況下交換至生產環境。如果生產網站發生問題，您也可以立即換回舊版的網站。

此外，針對作用中的網站規劃變更時，您很容易就能使用在生產環境測試的功能，針對提議的更新[ (英文) 執行 A/B 測試]，並分析實際的使用者行為，幫助您做出明智的網站設計決策。

## 品牌與安全

免費使用 App Service Web Apps 網域或對應至註冊的網域名稱，然後利用 CA 簽署的 SSL 憑證保護品牌安全。

當您在 Web Apps 執行網站時，可免費使用 ***.azurewebsites.net** 網域。或者，您可以將網站對應至從任何 DNS 登錄取得 (例如 GoDaddy) 的[自訂網域] (例如 contoso.com)。

如果您收集任何使用者資訊、執行電子商務或管理任何其他機密資料，您可以使用 [HTTPS] 來保護品牌信譽與客戶。**.azurewebsites.net** 網域名稱已經隨附 SSL 憑證，而如果您使用自訂網域，可以在 Web Apps 使用該網域的 SSL 憑證。每個月都會收取與各個 SSL 憑證相關聯的費用 (每小時依比例)。如需詳細資訊，請參閱 [App Service 定價詳細資料]。

## 邁向全球

使用 Azure 流量管理員來為遍佈全球的地區網站提供服務，以及使用 Azure CDN 快如閃電地提供內容。

若要向位於全球各地的客戶提供服務，請使用 [Azure 流量管理員]將網站訪客路由到可提供最佳效能的地區網站。或者，您可以將網站負載平均分配給多個地區的多個網站複本。

[整合 Web 應用程式與 Azure CDN]，就能快如閃電地將靜態內容提供給全球的使用者。Azure CDN 會將靜態內容快取在最接近使用者的 [CDN 節點]，讓延遲的情況和與網站連線的需求降至最低。

## 最佳化

透過使用自動調整功能自動地進行調整、使用 Azure Redis 快取功能進行快取、使用 WebJobs 執行背景工作，還有使用 Azure 流量管理員維持高可用性，最佳化 .COM 網站。

App Service Web Apps 具有[相應增加和放大]的功能，不論您的工作負載多寡，都能符合您 .COM 網站的需求。透過 [Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)以手動方式、透過[服務管理 API] 或 [PowerShell 指令碼處理]以程式設計方式，或是使用「自動調整」功能自動地將您的網站相應放大。在「標準」主控方案中，「自動調整」可讓您依據 CPU 使用量相應放大網站。如需最佳做法，請參閱 [Troy Hunt] 的[我所學到關於使用 Azure 快速調整 Web 應用程式的 10 件事]。

使用 [Azure Redis 快取]加速您網站的回應速度。使用此功能，可從後端資料庫以及 [ASP.NET 工作階段狀態]與[輸出快取]等其他項目快取資料。

使用 [Azure 流量管理員]維持網站的高可用性。使用「容錯移轉」方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

## 監視和分析

使用 Azure 或協力廠商工具掌握最新的網站效能。收到重大網站事件的警示。使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。

在 [Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)的 Web 應用程式刀鋒視窗中，[快速瀏覽]網站目前效能標準與資源配額。如需應用程式的可用性、效能和使用方式的全方位檢視，請使用 [Azure Application Insights] 以取得快速和強大的疑難排解、診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 的協力廠商工具，提供您網站的進階監視資料。

在「標準」主控方案中，可監視網站回應能力，還可在網站毫無回應時，收到電子郵件通知。如需詳細資訊，請參閱[作法：在 Azure 中接收警示通知及管理警示規則]。

## 使用多媒體並觸達所有裝置

透過多媒體提升 .COM 網站吸睛度，例如：

-  使用 [Azure 媒體服務]在全球上傳和串流處理視訊
-  使用 [Azure Marketplace 中的 SendGrid 服務]傳送電子郵件給使用者

## 其他資源

- [App Service Web Apps 文件](/services/app-service/web/)
- [Azure App Service Web Apps 的學習規劃](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[部署到預備位置]: web-sites-staged-publishing.md
[持續發佈]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[ (英文) 執行 A/B 測試]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[自訂網域]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service 定價詳細資料]: /pricing/details/app-service/#ssl-connections

[Azure 流量管理員]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[整合 Web 應用程式與 Azure CDN]: cdn-websites-with-cdn.md
[CDN 節點]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[相應增加和放大]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[服務管理 API]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell 指令碼處理]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[我所學到關於使用 Azure 快速調整 Web 應用程式的 10 件事]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis 快取]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 工作階段狀態]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[輸出快取]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[快速瀏覽]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[作法：在 Azure 中接收警示通知及管理警示規則]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure 媒體服務]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace 中的 SendGrid 服務]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=62-->