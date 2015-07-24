<properties
	pageTitle="我使用行動服務，應用程式服務對我有何幫助？"
	description="了解 App Service 為您現有的行動服務專案帶來哪些優勢。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>我使用行動服務，App Service 有何幫助？

##概觀
您現有的行動服務很安全並將繼續提供支援。不過，*Azure App Service* 平台為您的行動應用程式提供了行動服務目前所沒有的數個優點：

- 針對包含 Web 和行動用戶端的應用程式，提供更簡單、更容易且更符合成本效益的功能
- 新的主機功能包括 Web 工作、自訂 CName、更完善的監視
- 與 Office 365、Dynamics CRM、Salesforce 和其他重要 SaaS API 的周全整合。
- 除了 Node.js 和.NET 以外，還支援 Java 和 PHP 後端程式碼
- 與流量管理員的周全整合
- 除了混合式連線以外，使用 VNet 連線至內部部署資源和 VPN
- 使用 NewRelic 或 AppInsights 以及警示，監視和疑難排解您的應用程式
- 更廣泛的基礎計算資源，例如 VM 大小
- 內建自動調整、負載平衡，以及效能監視。
- 內建預備、備份、回復和實際環境測試功能

## 新的裝載功能
在 *AzureApp Service* 中，*行動應用程式* 後端程式碼會在與 Web 應用程式和 API 應用程式相同的容器中執行。因此，您可以利用此容器中的所有功能，包括行動服務中目前不存在的功能：

- 透過 Web 工作加入連續執行的後端邏輯
- 確保您的後端程式碼持續不斷執行
- 使用自訂 CName，為您的行動裝置後端端點提供友善且穩定的名稱
- 使用流量管理員，異地擴充您的應用程式


##將*行動應用程式* 連接至 SaaS API
*Azure App Service* 可讓您輕鬆地將行動應用程式連接至 SaaS API，包括 Office 365、Dynamics、Salesforce、SAP 等等。*Azure App Service* 會代表使用者提供周全的驗證，藉由將個別 SaaS API 的權杖與主要身分識別關聯，讓您跨越您使用所有的 SaaS API 執行真正的單一登入。

##使用 VNet 存取內部部署資料
現今有了行動服務，您已可使用混合式連線來存取內部部署資源。不過，有些情況還是慣用 VPN 解決方案。透過 *Azure App Service*，您可以將 Azure VNet 用於您的行動應用程式後端程式碼。

##使用您最愛的後端語言
*Azure App Service* 為開發人員平台提供更廣泛、更豐富的支援，而除了行動服務中支援的 .NET 和 Node.js 以外，這些平台還包括 Java、PHP 和 Python。

##設定自動調整
有了行動服務，後端程式碼的所有執行個體都已在小型 VM 上執行。*Azure App Service* 可讓您從更豐富的選項中選取 VM 的大小。您可以根據各種效能度量，快速相應增加或相應放大，以處理任何傳入的客戶負載。

##處於「知情」狀態
利用監視及警示即時對問題做出反應，以自動通知您和您的團隊。整合 New Relic 和 AppInsights 的進階應用程式分析和監視功能，更深入了解如何執行您的行動應用程式。利用 *Azure App Service*，您現在可以根據各種效能度量，以程式設計方式及透過入口網站設定警示

##讓您的資產安全無虞
自動備份您的後端和資料庫。您的程式碼和資料可免於遭受災害並可輕鬆還原，讓您有信心地執行您的業務。

##準備上場 ！
利用 *Azure App Service*，您現在可以為行動應用程式建立多個私人測試和預備環境。您可以在部署之前，使用這些環境來執行測試。不需停機即可切換至生產環境。Web 應用程式已預先載入，可確保最佳的客戶體驗。



當 *Azure App Service 行動應用程式* 功能公開推出的時候，我們將提供從現有行動服務移轉至 App Service 的順暢體驗 (如果您選擇移轉的話)。在此同時，您即可開始探索 *App Service*，並遵循此 [教學課程](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md)，將 *App Service* 運用於您現有的行動服務。
 

<!---HONumber=62-->