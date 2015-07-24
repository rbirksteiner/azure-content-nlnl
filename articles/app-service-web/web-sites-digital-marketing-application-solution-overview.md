<properties 
	pageTitle="在 Azure App Service Web Apps 上建立數位行銷活動" 
	description="本指南提供如何使用 Azure 應用程式服務 Web 應用程式來建立數位行銷活動的技術概觀。這包括部署、社交媒體整合、調整策略和監視。" 
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

# 在 Azure App Service Web Apps 上建立數位行銷活動
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps 是數位行銷活動的絕佳選擇。數位行銷活動通常只有短暫週期，目的是推廣短期的行銷目標。有兩個需要考量的主要案例。在第一個案例中，協力廠商行銷公司在促銷期間為其客戶建立及管理行銷活動。第二個案例則涉及行銷公司建立數位行銷活動資源擁有權，然後將該擁有權轉移給其客戶。客戶再接著自行執行和管理數位行銷活動。這是這兩種案例的絕佳解決方案。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

以下是使用 Azure 應用程式服務 Web 應用程式進行全域、多管道之數位行銷活動的範例。此範例示範不需要使用複雜的技術，只要組合應用程式服務 Web 應用程式和其他服務便能完成的工作。**按一下拓樸中的項目以讀取詳細資訊**。

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]本指南說明在 Azure 應用程式服務 Web 應用程式中執行數位行銷活動最常見的區域和工作。不過，還有其他可在應用程式服務 Web 應用程式中實作的常見解決方案。若要檢閱這些解決方案，請參閱關於[全域網站空間](web-sites-global-web-presence-solution-overview.md)和[商務應用程式](web-sites-business-application-solution-overview.md)上的其他指南。

## 從頭開始建立或沿用現有資產

從組件庫中受歡迎的 CMS 快速建立新的 Web 應用程式，或在應用程式服務 Web 應用程式中沿用現有各種不同語言與架構的 Web 資產。

Azure Marketplace 提供受歡迎網站內容管理系統 (CMS) 的範本，例如 [Orchard]、[Umbraco]、[Drupal] 以及 [WordPress]。您可以使用喜愛的 CMS 風格來建立 Web 應用程式。您可以根據自己的需求從各種不同的資料庫後端中選擇，包括 [Azure SQL Database] 和 [MySQL]。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 Web Apps 上執行。您可以使用熟悉的 [FTP] 工具將它們移至 Web Apps。如果您經常建立數位行銷活動，您可能在原始檔控制管理系統已經有 Web 資產。您可以直接從受歡迎的原始檔控制選項，例如 [Visual Studio]、[Visual Studio Online] 和 [Git] (本機、GitHub、BitBucket、DropBox 及 Mercurial 等) 部署至 Web Apps。

## 維持敏捷性

透過直接從您現有的原始檔控制持續發佈，以及在應用程式服務 Web 應用程式中執行 A/B 測試，以維持敏捷性。

在 Web 應用程式的計劃、原型設計及初期開發期間，您和您的客戶可以透過[部署到預備位置] (在 Web 應用程式中)，以在行銷活動應用程式上線之前，先查看 Web 應用程式的實際運作版本。透過整合原始控制檔與 App Service Web Apps ，您可以[持續發佈]到預備位置，而且不需停機，在準備好時即可切換到實際執行環境。

此外，針對作用中的 Web 應用程式規劃變更時，您很容易就能使用在生產環境測試的功能，針對提議的更新[執行 A/B 測試]，並分析實際的使用者行為，幫助您做出明智的應用程式設計決策。


## 社交網路化

透過向受歡迎的提供者 (例如 Facebook 與 Twitter) 驗證，應用程式服務 Web 應用程式中的數位行銷活動可以和社交媒體整合。如需在 ASP.NET 應用程式使用這個方法的範例，請參閱[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service]。

此外，每個社交媒體網站通常會提供透過 .NET 和許多其他架構來與它整合的資訊。

## 使用多媒體並觸達所有裝置

使用其他 Azure 服務豐富您的數位行銷活動，例如：

-  使用 [Azure 媒體服務]在全球上傳和串流處理視訊
-  使用 [Azure Marketplace 中的 SendGrid 服務]傳送電子郵件給使用者
-  使用[行動服務]在 Windows、iOS 及 Android 裝置上建立空間
-  使用[通知中樞]傳送推播通知到數百萬個裝置

## 邁向全球

使用 Azure 流量管理員來為遍佈全球的地區網站提供服務，以及使用 Azure CDN 快如閃電地提供內容。

若要向位於全球各地的客戶提供服務，請使用 [Azure 流量管理員]將網站訪客路由到可提供最佳效能的地區網站。或者，您可以將網站負載平均分配給多個地區的多個 Web 應用程式。

[整合 Web 應用程式與 Azure CDN]，就能快如閃電地將靜態內容提供給全球的使用者。Azure CDN 會將靜態內容快取在最接近使用者的 [CDN 節點]，讓延遲的情況和與 Web 應用程式連線的需求降至最低。

## 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的 Web 應用程式。

App Service Web Apps [相應增加和放大]的能力非常適合無法預期的工作負載，這正是數位行銷活動的情況。透過 [Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)以手動方式、透過[服務管理 API] 或 [PowerShell 指令碼處理]以程式設計方式，或是使用「自動調整」功能自動地將您的 Web 應用程式相應放大。在「標準」層中，「自動調整規模」可讓您依據 CPU 使用量自動相應放大 Web 應用程式。這項功能只會在必要時依據使用者活動向外調整 Web 應用程式，因此可協助您達到最大敏捷性，同時將成本降至最低。如需最佳做法，請參閱 [Troy Hunt] 的[我所學到關於使用 Azure 快速調整 Web 應用程式的 10 件事]。

使用 [Azure Redis Cache] 加速您 Web 應用程式的回應速度。使用此功能，可從後端資料庫以及 [ASP.NET 工作階段狀態]與[輸出快取]等其他項目快取資料。

使用 [Azure 流量管理員]來維持 Web 應用程式的高可用性。使用「容錯移轉」方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

## 監視和分析

使用 Azure 或協力廠商工具，掌握關於您 Web 應用程式效能的最新資訊。接收重要 Web 應用程式事件的警示。使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。

在 [Azure 預覽入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)的 Web 應用程式刀鋒視窗中，[快速瀏覽] Web 應用程式目前效能標準與資源配額。如需應用程式的可用性、效能和使用方式的全方位檢視，請使用 [Azure Application Insights] 以取得快速和強大的疑難排解、診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 的協力廠商工具，提供您 Web 應用程式的進階監視資料。

在**標準**層中，每當您的 Web 應用程式沒有回應時，監視 Web 應用程式回應便會收到電子郵件通知。如需詳細資訊，請參閱[作法：在 Azure 中接收警示通知及管理警示規則]。

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
[執行 A/B 測試]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure 媒體服務]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace 中的 SendGrid 服務]: sendgrid-dotnet-how-to-send-email.md
[行動服務]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[通知中樞]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure 流量管理員]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[整合 Web 應用程式與 Azure CDN]: cdn-websites-with-cdn.md
[CDN 節點]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[相應增加和放大]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[服務管理 API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell 指令碼處理]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[我所學到關於使用 Azure 快速調整 Web 應用程式的 10 件事]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 工作階段狀態]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[輸出快取]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[快速瀏覽]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[作法：在 Azure 中接收警示通知及管理警示規則]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=62-->