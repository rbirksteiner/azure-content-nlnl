<properties 
	pageTitle="將 Web API 專案設定為 API 應用程式" 
	description="了解如何使用 Visual Studio 2013 將 Web API 專案設定為 API 應用程式" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="bradyg;tarcher"/>

# 將 Web API 專案設定為 API 應用程式

## 概觀

本教學課程顯示如何採取現有的 Web API 專案，並為了部署而將它設定為 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)。系列中的後續教學課程顯示如何[部署](app-service-dotnet-deploy-api-app.md)和[偵錯](../app-service-dotnet-remotely-debug-api-app.md)您已在本教學課程中建立的 API 應用程式專案。

如需 API 應用程式的相關資訊，請參閱[什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md)。

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

本教學課程需要 2.5.1 版或更新版本的 Azure SDK for.NET。

## 設定 Web API 專案 

本節顯示如何將現有的 Web API 專案設定為 API 應用程式。一開始您將使用 Web API 專案範本來建立 Web API 專案，接著將它設定為 API 應用程式。

1. 開啟 Visual Studio 2013。

2. 選取 [檔案] > [新增專案]。

3. 選取 [**ASP.NET Web 應用程式**] 範本。

4. 請確定已經清除 [**將 Application Insights 加入專案**] 核取方塊。

4. 將專案命名為 *ContactsList*

	![](./media/app-service-dotnet-create-api-app-visual-studio/01-filenew-v3.png)

5. 按一下 [確定]。

6. 在 [**新建 ASP.NET 專案**] 對話方塊中，選取 [**空白**] 專案範本。

7. 按一下 [**Web API**] 核取方塊。

8. 清除 [**雲端中的主機**] 選項。

	![](./media/app-service-dotnet-create-api-app-visual-studio/webapinewproj.png)

9. 按一下 [**確定**] 以產生專案。

	![](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

10. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後選取 [**新增 > Azure API 應用程式 SDK**]。

	![](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

11. 在 [**選擇 API 應用程式中繼資料來源**] 對話方塊中，按一下 [**自動產生中繼資料**]。

	![](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	此選項會啟用動態 Swagger UI，您稍後可在教學課程中看到。如果您選擇上傳 Swagger 中繼資料檔案，則它會依照下節中的說明以檔案名稱 *apiDefinition.swagger.json* 加以儲存。

12. 按一下 [確定]。
 
	此時，Visual Studio 會安裝 API 應用程式 NuGet 套件，並將 API 應用程式中繼資料新增至 Web API 專案。

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

現在已準備好部署您的 API 應用程式，而且您可以依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程進行部署。
 

<!---HONumber=62-->