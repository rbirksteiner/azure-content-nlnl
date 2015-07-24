<properties 
	pageTitle="什麼是邏輯應用程式？" 
	description="深入了解應用程式服務邏輯應用程式" 
	authors="joshtwist" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#什麼是邏輯應用程式？

Azure App Service 是專為專業開發人員提供的完全受管理的 PaaS (平台即服務)，能夠輕鬆地建置 Web、行動和整合應用程式。邏輯應用程式是此套件的一部分，可讓任何技術使用者或開發人員透過容易使用的視覺化設計工具，將商務程序執行和工作流程自動化。

最棒的是，邏輯應用程式可以從 Marketplace 與[連接器][connectors]結合，協助您輕鬆地解決棘手的整合案例。

![流程應用程式設計工具](./media/app-service-logic-what-are-logic-apps/Designer.png)

如果您要自動複寫 SQL DB 中的新記錄並寄送郵件至前臺，或自動尋找負評推文並將其傳送至 Slack 通道

##為什麼要使用邏輯應用程式？

邏輯應用程式可讓開發人員設計從觸發程序啟動的工作流程，然後執行一系列的步驟，每個步驟都會叫用 App Service API 應用程式，同時安全地處理驗證和最佳作法，如檢查點和長期執行的工作流程。

如果您要自動執行任何商業程序 (例如尋找負評推文並張貼至內部 Slack 通道或從 SQL 將送達的新客戶記錄複寫道 CRM 系統中)，邏輯應用程式可讓您輕鬆整合完全不同的資料來源 (從雲端至內部部署)。查看我們的[連接器][connectors]以獲得更多靈感並立即[開始][create]看看您可以做些什麼。

此外，透過 [BizTalk API][biztalk] 應用程式，您可以利用[規則引擎][rules]、[交易夥伴管理][tpm]等的功效擴展至成熟的整合案例。

- **容易使用的設計工具** - 邏輯應用程式可在瀏覽器中端對端地設計。從觸發程序啟動 - 從簡單的排程，到每次有關於貴公司的推文出現時。然後，使用豐富的連接器組件庫協調任何數量的動作。

- **輕鬆撰寫 SaaS** -即使可輕鬆說明的撰寫工作，也很難在程式碼中實作。邏輯應用程式可讓連接不同的系統變成簡單的作業。想要在 CRM 軟體中建立以 Facebook 或 Twitter 帳戶中的活動為基礎的工作嗎？ 想要將您的雲端行銷解決方案連接到內部部署計費系統嗎？ 邏輯應用程式可用最快、最可靠方式，提供這些問題的解決方案。

- **從範本快速使用** - 為了方便您著手使用，我們提供了範本組件庫，讓您可快速建立一些常見的解決方案。從進階 BizTalk 解決方案到簡單的 SaaS 連線 (甚至只為了「好玩」) - 組件庫是了解邏輯應用程式強大功能的最快方式。

- **現成可用的擴充性** - 找不到您所需要的連接器嗎？ 邏輯應用程式是應用程式服務套件的一部分，依設計可與 API 應用程式搭配使用；您可以輕鬆地建立您自己的 API 應用程式，以做為連接器。建置您專屬的新應用程式，或在 Marketplace 中共用及銷售。

- **真正的整合能力** - 入門容易，並且可依需求擴充。邏輯應用程式可輕易地讓 Microsoft 領先業界的整合解決方案 BizTalk 發揮效益，使整合專業人員得以建置其所需的解決方案。進一步了解[透過應用程式服務提供的 BizTalk 功能][biztalk]。

## 邏輯應用程式概念

以下是構成邏輯應用程式使用性的一些重要元素。

- **工作流程** - 邏輯應用程式提供圖形化的方式，以一系列的步驟或工作流程的形式為您的商務程序建立模型。
- **[連接器][connectors]** - 邏輯應用程式需要存取資料和服務。連接器是特殊類型的 API 應用程式。它是為了讓協助您連接及使用資料而特別設計的。請參閱[使用連接器][connectors]中目前提供的連接器清單。
- **觸發程序** - 某些連接器也可以做為觸發程序。觸發程序會根據特定的事件建立新的工作流程執行個體，例如在電子郵件送達或您的 Azure 儲存體帳戶有所變更時。
-  **動作** - 工作流程中觸發程序後的每個步驟登稱為動作。每個動作通常會對應至連接器或自訂 API 應用程式上的作業。
- **BizTalk** - 在更進階的整合案例中，Azure 應用程式服務會包含來自 Biztalk 的功能。Biztalk 是 Microsoft 領先業界的整合平台。BizTalk API 應用程式可讓您輕鬆地將驗證、轉換、規則等項目納入邏輯應用程式工作流程中。進一步了解[什麼是 BizTalk API 應用程式][biztalk]。

## 開始使用

若要開始使用邏輯應用程式，請遵循[建立邏輯應用程式][create]教學課程。

如需 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][appservice]。

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-use-biztalk-connectors.md
[tpm]: app-service-logic-create-a-trading-partner-agreement
[rules]: app-service-logic-use-biztalk-rules

 

<!---HONumber=62-->