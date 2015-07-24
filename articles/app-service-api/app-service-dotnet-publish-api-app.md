<properties 
	pageTitle="部署 Azure App Service API 應用程式" 
	description="了解如何將 API 應用程式專案部署到您的 Azure 訂閱。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
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

# 部署 Azure App Service API 應用程式

## 概觀

如果您正使用 Visual Studio 積極開發自己的 API 應用程式，而且需要在雲端測試您的 API，您可以在您的 Azure 訂閱中建立新的 API 應用程式，並使用 Visual Studio 方便的 App Service 部署功能來部署您的程式碼。

這是三個教學課程組成一系列中的第二個：

1. 在[建立 API 應用程式](app-service-dotnet-create-api-app.md)中，您已建立 API 應用程式專案。 
* 在本教學課程中，您會將 API 應用程式部署到您的 Azure 訂閱。
* 在[偵錯 API 應用程式](../app-service-dotnet-remotely-debug-api-app.md)中，您將使用 Visual Studio，在程式碼於 Azure 中執行時進行遠端偵錯。

## 部署 API 應用程式 

在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 [**發佈...**]。

![專案發佈功能表選項](./media/app-service-dotnet-publish-api-app/20-publish-gesture-v3.png)

按一下 [**設定檔**] 索引標籤，然後按一下 [**Microsoft Azure API 應用程式 (預覽)**]。

![發佈 Web 對話方塊](./media/app-service-dotnet-publish-api-app/21-select-api-apps-for-deployment-v2.png)

按一下 [**新增**] 以在 Azure 訂閱中佈建新的 API 應用程式。

![選取現有的 API 服務對話方塊](./media/app-service-dotnet-publish-api-app/23-publish-to-apiapps-v3.png)

在 [**建立 API 應用程式**] 對話方塊中，輸入下列項目：

- 在 [**API 應用程式名稱**] 之下，輸入應用程式的名稱。 
- 如果您有多個 Azure 訂閱，請選取您要使用的 Azure 訂閱 -在 [App Service 方案] 之下，從現有的 App Service 方案進行選取，或選取 [**建立新的 App Service 方案**] 並輸入新的方案名稱。 
- 在 [**資源群組**] 之下，從現有的資源群組進行選取，或選取 [**建立新的資源群組**] 並輸入名稱。此名稱必須是唯一的。請考慮使用應用程式名稱做為前置詞並附加一些個人資訊，例如 Microsoft ID (不含 @ 符號)。  
- 在 [**存取層級**] 之下，選取 [**可供任何人使用**]。此選項可讓您的 API 完全公開，這在本教學課程沒有問題。稍後您可以透過 Azure 入口網站限制存取。
- 選取區域。  

![設定 Microsoft Azure Web 應用程式對話方塊](./media/app-service-dotnet-publish-api-app/24-new-api-app-dialog-v3.png)

按一下 [**確定**] 以在您的訂閱中建立 API 應用程式。此程序可能會花費幾分鐘，因此 Visual Studio 會顯示一個對話方塊，通知您此程序已起始。

![已開始建立 API 服務確認訊息](./media/app-service-dotnet-publish-api-app/25-api-provisioning-started-v3.png)

佈建程序會在您的 Azure 訂閱中建立資源群組和 API 應用程式。Visual Studio 會在 [**Azure App Service 活動**] 視窗中顯示進度。

![透過 Azure App Service 活動視窗的狀態通知](./media/app-service-dotnet-publish-api-app/26-provisioning-success-v3.png)

佈建 API 應用程式後，以滑鼠右鍵按一下 [方案總管] 中的專案，並選取 [**發佈**] 來重新開啟 [發佈] 對話方塊。在上一個步驟中建立的發佈設定檔應會預先選取。按一下 [**發佈**] 開始部署程序。

![部署 API 應用程式](./media/app-service-dotnet-publish-api-app/26-5-deployment-success-v3.png)

[**Azure App Service 活動**] 視窗會顯示部署進度。

![Azure App Service 活動視窗的狀態通知](./media/app-service-dotnet-publish-api-app/26-5-deployment-success-v4.png)

## 在 Azure 入口網站中檢視應用程式

在本節中，您將導覽至入口網站來檢視 API 應用程式可用的基本設定，並且對您的 API 應用程式進行反覆變更。每次部署時，入口網站都會反映您對 API 應用程式所做的變更。

在瀏覽器中導覽至 [Azure 入口網站](https://portal.azure.com)。

按一下側邊列上的 [**瀏覽**] 按鈕，然後選取 [**API 應用程式**]。

![瀏覽 Azure 入口網站上的選項](./media/app-service-dotnet-publish-api-app/27-browse-in-portal-v3.png)

從您的訂閱中的 API 應用程式清單中選取您所建立的 API。

![API 應用程式清單](./media/app-service-dotnet-publish-api-app/28-view-api-list-v3.png)

按一下 [**API 定義**]。應用程式的 [**API 定義**] 分頁會顯示您建立應用程式時定義的 API 作業清單。

![API 定義](./media/app-service-dotnet-publish-api-app/29-api-definition-v3.png)

現在回到 Visual Studio 中的專案。將下列程式碼新增到 **ContactsController.cs** 檔案。

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

此程式碼會新增 **Post** 方法，可以用來將新的 `Contact` 執行個體張貼至 API。

![新增 Post 方法至控制器](./media/app-service-dotnet-publish-api-app/30-post-method-added-v3.png)

在 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後選取 [**發佈**]。

![專案發佈內容功能表](./media/app-service-dotnet-publish-api-app/31-publish-gesture-v3.png)

從 [**設定**] 下拉式清單中選取 [**偵錯**] 設定，然後按一下 [**發佈**] 以重新部署 API 應用程式。

![發佈 Web 設定](./media/app-service-dotnet-publish-api-app/36.5-select-debug-option-v3.png)

在 [**發佈 Web**] 精靈的 [**預覽**] 索引標籤中，按一下 [**發佈**]。

![發佈 Web 對話方塊](./media/app-service-dotnet-publish-api-app/39-re-publish-preview-step-v2.png)

在發佈程序完成後，回到入口網站，然後關閉並重新開啟 [**API 定義**] 分頁。您會看到您剛建立並直接部署到 Azure 訂閱中的新 API 端點。

![API 定義](./media/app-service-dotnet-publish-api-app/38-portal-with-post-method-v4.png)

## 後續步驟

您已了解 Visual Studio 中的直接部署功能如何讓您輕鬆逐一查看和快速部署您的 API，以及測試其是否運作正常。在下一個[教學課程](../app-service-dotnet-remotely-debug-api-app.md)中，您會了解如何在 API 應用程式於 Azure 中執行時進行偵錯。


 

<!---HONumber=62-->