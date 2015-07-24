<properties 
	pageTitle="什麼是 API 應用程式？" 
	description="了解 Azure App Service 為何是開發、發佈及裝載 RESTful API 的最佳平台。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# 什麼是 API 應用程式？

API 應用程式提供豐富的平台和生態系統，可用來在雲端和內部部署中建置、主控、使用和分送 API。將您的 API 部署為 API 應用程式，即可受益於企業級安全性、簡單的存取控制、混合式與 SaaS 連線、自動 SDK 產生，並與 [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md) 完美整合。

API 應用程式是 [Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)套件的其中一部分，此套件的其他部分包括 Web 應用程式、行動應用程式和邏輯應用程式。

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## 為何採用 API 應用程式？

API 應用程式可用來開發、部署、發行、使用和管理 RESTful Web API。應用程式服務提供公開預覽的下列立即可用功能：

- **輕鬆使用** - 使用整合的 [Swagger](http://swagger.io/) 支援，讓各種用戶端都能輕鬆使用您的 API。API Apps SDK 可以各種語言 (包括 C#、Java 和 Javascript) 為您的 API 產生用戶端程式碼。

- **簡易存取控制** - 內建的驗證服務可支援 Azure Active Directory 或協力廠商服務 (例如 Facebook 和 Twitter)。您可以保護 API 應用程式，使其免於未經驗證的存取，且不必變更程式碼。如果您熟悉 [Azure 行動服務](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication)提供的驗證服務，則 API 應用程式可建置於該架構上，並將其延伸至 API 應用程式主控的 API。App Service SDK 也可讓您使用簡化的授權碼語法。如需詳細資訊，請參閱[保護 API 應用程式](app-service-api-dotnet-add-authentication.md)。

- **輕鬆連接到 SaaS 平台** - Azure Marketplace 中的[連接器 API 應用程式](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)是由 Microsoft 和協力廠商提供，以簡化您為了與 SalesForce、Office 365、Twitter、Facebook、Dropbox 和許多其他應用程式互動而撰寫的程式碼。

- **與邏輯應用程式整合** - 您建立的 API 應用程式可供 [App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) 使用。

- **Visual Studio 整合** - Visual Studio 中的專用工具可簡化[建立](app-service-dotnet-create-api-app.md)、[部署](app-service-dotnet-deploy-api-app.md)、[偵錯](app-service-dotnet-remotely-debug-api-app)和管理 API 應用程式的工作。

您可以繼續使用現有的 API：您不必變更現有 API 的任何程式碼，就能利用 API 應用程式功能，只要將程式碼部署至 API 應用程式即可。您可以在 API 中使用 ASP.NET、Java、PHP、Node.js 或 Python。

API 應用程式另外包含 [App Service Web Apps 的功能](../app-service-web/app-service-web-overview.md)。

### API Apps 功能即將推出

在不久將來，API 應用程式平台也將建立 API 的豐富生態系統，方法為使得共用您的程式碼變得輕而易舉：

- **公用和私用市場** - [Azure Marketplace](http://azure.microsoft.com/marketplace/) 將可讓您輕鬆地找到 Microsoft 和協力廠商開發的預先套裝 API 應用程式，並將其部署至您的 Azure 訂閱。因此，您將能夠套裝並發佈您開發的專屬 API 應用程式，以便其他開發人員可將它們部署至其 Azure 訂閱。將您的 API 發佈至 Azure Marketplace 時，您將能夠只讓貴組織的其他成員看到它們。 

- **自動相依性部署** - 每當您將 Marketplace 中的 API 應用程式部署至您的 Azure 訂閱時，Azure 將自動部署相依 API 應用程式，並建立必要資源。API 應用程式套件將指定其依賴的 API 應用程式，以及其需要的 Azure 資源。

- **自動更新** - 當更新您已共用的其中一個 API 應用程式套件的程式碼時，您將能夠推送更新至已安裝並正在執行您的 API 應用程式的每一個人。這將適用於非重大變更，以及已選擇接收更新的使用者。

其中許多功能 (例如公用市場和自動更新) 已可供 Microsoft 所提供的 API 應用程式使用。

>[AZURE.NOTE] [Azure API Management](/services/api-management/) 屬於個別服務，可提供端點整合及節流等功能。您可以搭配 API Apps 使用 API 管理。
>
>API 應用程式目前處於公開預覽狀態。它是建置在[應用程式服務 Web 應用程式](../app-service-web/app-service-web-overview.md)之上，而此 Web 應用程式是正式推出 (GA) 的服務，其設計旨在組建和裝載全球規模的安全任務關鍵型應用程式。如果您目前正在尋找 GA 服務來組建 API，則 Web 應用程式正是最佳選項。當 API Apps 正式推出時，我們將提供一條採用現有 Web 應用程式並運用 API Apps 功能的途徑。

## API 應用程式概念 ##

- **閘道** - 一 種 Web 應用程式，可處理 API 管理功能，並驗證資源群組中的所有 API 應用程式。 
- **Swagger** - 一種以互動方式說明和探索 RESTful API 的架構，依預設用於 API 應用程式中。如需詳細資訊，請參閱 [http://swagger.io/](http://swagger.io/)。
- **連接器** - 一種可讓您輕鬆地連接到 SaaS 平台 (例如 Salesforce 和 Office 365) 的 API 應用程式 。如需詳細資訊，請參閱[什麼是連接器和 BizTalk API 應用程式](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)。
- **觸發程序** - 一種 REST API，[邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)可在符合特定條件時，呼叫它以初始化工作流程。例如，API 應用程式可以提供邏輯應用程式定期呼叫的方法，以在 Twitter 摘要中尋找特定詞組。如需詳細資訊，請參閱 [API 應用程式觸發程序](app-service-api-dotnet-triggers.md)。
- **動作** - 一種 REST API，[邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)可在工作流程已由觸發程序啟動之後，呼叫它以處理資料.例如，API 應用程式可以提供一種方法，供邏輯應用程式呼叫，以回應 Twitter 觸發程序找到的推文。動作是由 Swagger API 定義公開的 API 方法。

## 開始使用

若要開始使用 API 應用程式，請遵循[建立 API 應用程式教學課程](app-service-dotnet-create-api-app.md)。

若要查看 API 應用程式已知問題的清單，請參閱[這篇 MSDN 論壇文章](https://social.msdn.microsoft.com/Forums/zh-tw/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)。

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

 

<!---HONumber=62-->