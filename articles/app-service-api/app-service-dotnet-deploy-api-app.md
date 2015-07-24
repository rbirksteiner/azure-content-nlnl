<properties 
	pageTitle="在 Azure App Service 中部署 API 應用程式" 
	description="了解如何將 API 應用程式專案部署到您的 Azure 訂用帳戶。" 
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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# 在 Azure App Service 中部署 API 應用程式 

## 概觀

在本教學課程中，您會將[前一個教學課程](app-service-dotnet-create-api-app.md)中建立的 Web API 專案部署到新的 [API 應用程式](app-service-api-apps-why-best-platform.md)。您將使用 Visual Studio，在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中建立 API 應用程式資源，並將您的 Web API 程式碼部署到 Azure API 應用程式。

### 其他部署選項

有許多部署 API 應用程式的其他方法。API 應用程式是一種 [Web 應用程式](../app-service-web/app-service-web-overview.md)，具有額外的功能來裝載 Web 服務，而且[可供 Web 應用程式使用的部署方法](../app-service-web/web-sites-deploy.md)也可與 API 應用程式搭配使用。裝載 API 應用程式的 Web 應用程式稱為 Azure 預覽入口網站中的 API 應用程式主機，而且您可以使用 API 應用程式主機入口網站刀鋒視窗來設定部署。如需 API 應用程式主機刀鋒視窗的相關資訊，請參閱[管理 API 應用程式](app-service-api-manage-in-portal.md)。

API 應用程式是以 Web 應用程式為基礎的事實，也表示您可以將針對 ASP.NET 以外平台撰寫的程式碼部署到 API 應用程式。如需使用 Git 將 Node.js 程式碼部署到 API 應用程式的範例。請參閱[在 Azure App Service 中建立 Node.js API 應用程式](app-service-api-nodejs-api-app.md)。
 
## <a id="provision"></a>在 Azure 中建立 API 應用程式 

在本節中，您會使用 Visual Studio **發行 Web** 精靈在 Azure 中建立新的 API 應用程式。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 [**發佈...**]。 

	![專案發佈功能表選項](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. 按一下 [**設定檔**] 索引標籤，然後按一下 [**Microsoft Azure API 應用程式 (預覽)**]。

	![發佈 Web 對話方塊](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. 按一下 [**新增**] 以在 Azure 訂閱中佈建新的 API 應用程式。

	![選取現有的 API 服務對話方塊](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. 在 [**建立 API 應用程式**] 對話方塊中，輸入下列項目：

	- 對於 [**API 應用程式名稱**]，輸入 ContactsList。 
	- 如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶。
	- 對於 [**App Service 方案**]，從現有的 App Service 方案進行選取，或選取 [**建立新的 App Service 方案**]，並輸入新的方案名稱。 
	- 對於 [**資源群組**]，從現有的資源群組進行選取，或選取 [**建立新的資源群組**] 並輸入名稱。此名稱必須是唯一的。請考慮使用應用程式名稱做為前置詞並附加一些個人資訊，例如 Microsoft ID (不含 @ 符號)。  
	- 對於 [**存取層級**]，選取 [**可供任何人使用**]。此選項可讓您的 API 完全公開，這在本教學課程沒有問題。稍後您可以透過 Azure 預覽入口網站限制存取。
	- 在 [**區域**] 中，選取接近您的區域。  

	![設定 Microsoft Azure Web 應用程式對話方塊](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. 按一下 [**確定**] 以在您的訂用帳戶中建立 API 應用程式。因為此程序可能花費數分鐘，所以 Visual Studio 會顯示一個確認對話方塊。

	![已開始建立 API 服務確認訊息](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. 按一下 [確認] 對話方塊上的 [**確定**]。佈建程序會在您的 Azure 訂用帳戶中建立資源群組和 API 應用程式。Visual Studio 會在 [**Azure App Service 活動**] 視窗中顯示進度。

	![透過 Azure App Service 活動視窗的狀態通知](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

## <a id="deploy"></a>將您的程式碼部署到新的 API 應用程式

在本節中，您會使用相同的**發行 Web** 精靈來部署您的程式碼。

7. 以滑鼠右鍵按一下 [**方案總管**] 中的專案，並選取 [**發佈**] 來重新開啟 [發佈] 對話方塊。在上一個步驟中建立的發佈設定檔應會預先選取。按一下 [**發佈**] 開始部署程序。 

	![部署 API 應用程式](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

	[**Azure App Service 活動**] 視窗會顯示部署進度。

	![Azure App Service 活動視窗的狀態通知](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

	在此部署程序期間，Visual Studio 會自動重新啟動*閘道器*。閘道器是處理資源群組中所有 API 應用程式之管理功能的 Web 應用程式，而且必須重新啟動它才能辨識 API 應用程式中的 API 變更。如果您使用另一種方法來部署 API 應用程式，而且您的部署變更 API，則可能必須手動重新啟動閘道器。如需如何在入口網站中重新啟動閘道器的相關資訊，請參閱[管理 API 應用程式](app-service-api-manage-in-portal.md)。

## 在 Azure 預覽入口網站中檢視應用程式

在本節中，您將導覽至入口網站來檢視 API 應用程式可用的基本設定，並且對您的 API 應用程式進行反覆變更。每次部署時，入口網站都會反映您對 API 應用程式所做的變更。

1. 在瀏覽器中導覽至 [Azure 預覽入口網站](https://portal.azure.com)。 

2. 按一下側邊列上的 [**瀏覽**] 按鈕，然後選取 [**API 應用程式**]。

	![瀏覽 Azure 入口網站上的選項](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. 從您的訂用帳戶中的 API 應用程式清單中選取您所建立的 API。

	![API 應用程式清單](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. 按一下 [**API 定義**]。應用程式的 [**API 定義**] 分頁會顯示您建立應用程式時定義的 API 作業清單。

	![API 定義](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. 現在，請回到 Visual Studio 中的專案，並將下列程式碼新增至 **ContactsController.cs** 檔案。此程式碼會新增 **Post** 方法，可以用來將新的 `Contact` 執行個體張貼至 API。

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![新增 Post 方法至控制器](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

6. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後選取 [**發佈**]。

	![專案發佈內容功能表](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. 按一下 [設定] 索引標籤。

8. 從 [**設定**] 下拉清單中，選取 [**偵錯**]。

	![發佈 Web 設定](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. 按一下 [**預覽**] 索引標籤

10. 按一下 [**開始預覽**]，以檢視將進行的變更。

	![發佈 Web 對話方塊](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. 按一下 [發佈]。

12. 在發佈程序完成後，回到入口網站，然後關閉並重新開啟 [**API 定義**] 刀鋒視窗。您會看到您剛建立並直接部署到 Azure 訂閱中的新 API 端點。

	![API 定義](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 後續步驟

您已了解 Visual Studio 中的直接部署功能如何讓您輕鬆逐一查看和快速部署您的 API，以及測試其是否運作正常。在[下一個教學課程](../app-service-dotnet-remotely-debug-api-app.md)中，您會了解如何在 API 應用程式於 Azure 中執行時進行偵錯。
 

<!---HONumber=62-->