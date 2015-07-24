<properties
	pageTitle="在 Azure App Service 中建置和部署 Node.js API 應用程式"
	description="了解如何建立 Node.js API 應用程式封裝並將其部署至 Azure App Service。"
	services="app-service\api"
	documentationCenter="nodejs"
	authors="pkefal"
  manager="" 
  editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# 在 Azure App Service 中建置和部署 Node.js API 應用程式

本教學課程說明如何建立 [Node.js](http://nodejs.org) 應用程式，並使用 [Git](http://git-scm.com) 將其部署至 Azure App Service API 應用程式。本教學課程中的指示可運用在任何足以執行 Node 應用程式的作業系統上。

完成之應用程式的螢幕擷取畫面如下：

![][sample-api-app-page]

> [AZURE.NOTE]您也可以使用 Visual Studio 程式碼，在 Azure App Service 中建置和部署 Node.js API 應用程式。如需 Visual Studio 程式碼和 Node.js 的詳細資訊，請參閱 [Visual Studio 程式碼](http://code.visualstudio.com/Docs/)和[開發節點應用程式](http://code.visualstudio.com//Docs/nodejs)。

## 在 Azure 預覽入口網站中建立 API 應用程式

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/)或是[申請免費試用](/pricing/free-trial/)。您也可以免費試用 [App Service 應用程式範例](http://tryappservice.azure.com)。

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)。

2. 按一下入口網站左下方的 [**新增**]。

3. 按一下 **[Web + 行動] > [API 應用程式]**。

	![][portal-quick-create]

4. 輸入 [**名稱**] 的值，例如 NodejsAPIApp。

5. 選取 App Service 方案或建立新方案。如果您建立新方案，請選取定價層、位置和其他選項。

	![][portal-create-api]

6. 按一下 [建立]。

	![][api-app-blade]

	如果您已選取 [**新增到儀表板**] 核取方塊，則入口網站會在 API 應用程式建立之後自動開啟其刀鋒視窗。如果您已清除此核取方塊，請按一下入口網站首頁上的 [**通知**]，以查看 API 應用程式建立狀態，然後按一下通知可移至新 API 應用程式的刀鋒視窗。

7. 按一下 **[設定] > [應用程式設定]**。

9. 將存取權層級設定為 [**公用 (匿名)**]。

11. 按一下 [儲存]。

	![][set-api-app-access-level]

## 啟用新 API 應用程式的 Git 發佈

[Git](http://git-scm.com/%20target="_blank) 是一個您可用來部署 Azure 網站的分散式版本控制系統。您將會在本機 Git 儲存機制中儲存您為 API 應用程式撰寫的程式碼，並藉由發送至遠端儲存機制，將您的程式碼部署至 Azure。此部署方法是您可以在 API 應用程式中使用的 App Service Web 應用程式功能，因為 API 應用程式是以 Web 應用程式為基礎：Azure App Service 中的 API 應用程式是具備其他用於裝載 Web 服務之功能 Web 應用程式。

在入口網站中，您可以在 [**API 應用程式**] 刀鋒視窗中管理 API 應用程式的特定功能，而您可以在 [**API 應用程式主機**] 刀鋒視窗中管理與 Web 應用程式共用的功能。在本節中，您會前往 [**API 應用程式主機**] 刀鋒視窗以設定 Git 部署功能。

1. 在 [API 應用程式] 刀鋒視窗中，按一下 [**API 應用程式主機**]。

	![][api-app-host]

2. 尋找 [**API 應用程式**] 刀鋒視窗的 [**部署**] 區段，然後按一下 [**設定連續部署**]。您可能需要向下捲動至刀鋒視窗的這個部分。

	![][deployment-part]

3. 按一下 **[選擇來源] > [本機 Git 儲存機制]**。

5. 按一下 [確定]。

	![][setup-git-publishing]

6. 如果您先前尚未設定部署認證以供發佈 API 應用程式或其他 App Service 應用程式，請立即設定：

	* 按一下 [**設定部署認證**]。

	* 建立使用者名稱和密碼。

	* 按一下 [儲存]。

	![][deployment-credentials]

1. 在 [**API 應用程式主機**] 刀鋒視窗中，按一下 **[設定] > [屬性]**。您將部署至的遠端 Git 儲存機制 URL 會顯示在 "GIT URL" 下方。

2. 複製此 URL 以便稍後在教學課程中使用。

	![][git-url]

## 下載並檢查 Node.js API 應用程式的程式碼

在本節中，您將下載並查看作為 NodeAPIApp 範例的一部分提供的程式碼。

1. 在[此 GitHub 儲存機制](http://go.microsoft.com/fwlink/?LinkID=534023&clcid=0x409)中下載程式碼。您可以複製儲存機制，或按一下 [**下載 Zip**] 以下載為 .zip 檔案。如果您下載.zip 檔案，請在本機磁碟中將它解壓縮。

2. 瀏覽至您將範例解壓縮的資料夾。

	![][api-app-folder-browse]

3. 在文字編輯器中開啟 **apiapp.json** 檔案並檢查內容。

	![][apiapp-json]

	若要將 Node.js 應用程式辨識為 API 應用程式，Azure App Service 需具備兩個先決條件：

	+ 名為 *apiapp.json* 的檔案必須存在於應用程式的根目錄中。
	+ 應用程式必須公開 Swagger 2.0 中繼資料端點。此端點的 URL 已在 *apiapp.json* 檔案中指定。

	請注意 **apiDefinition** 屬性。此 URL 的路徑相對於您的 API 的 URL，而且會指向 Swagger 2.0 端點。Azure App Service 會使用這個屬性來探索 API 的定義，並啟用許多 App Service API 應用程式功能。

	> [AZURE.NOTE]此端點必須是 Swagger 2.0 規格，因為平台不支援較舊的版本 (例如 1.2)。範例應用程式使用 swaggerize-express 來建立 Swagger 2.0 規格端點。

4. 開啟 **server.js** 檔案並檢查程式碼。

	![][server-js]

	程式碼會使用 swaggerize-express 模組來建立 Swagger 2.0 端點。

		app.use(swaggerize({
		    api: require('./api.json'),
		    docspath: '/swagger',
		    handlers: './handlers/'
		}));

	`api` 屬性指向的 api.json 檔案包含我們的 API 的 Swagger 2.0 規格定義。您可以在文字編輯器中手動建立此檔案，或使用 [Swagger 的線上編輯器](http://editor.swagger.io)並從該處下載 JSON 檔案。(*api.json* 檔案指定 `host` 屬性，但這個屬性的值已在執行階段動態決定和取代。)

	`docspath` 屬性會指向 Swagger 2.0 端點。此 URL 相對於您的 API 的基底路徑。基底路徑已在 api.json 檔案中宣告。在我們的範例中，基底路徑是 */api/data*，因此 swagger 端點的相對路徑是 */api/data/swagger*。

	> [AZURE.NOTE]因為基底路徑已在 *api.json* 檔案中宣告，所以嘗試存取 */swagger* 端點做為 API 應用程式 URL 的相對路徑會傳回 404。這是嘗試存取 swagger 端點時常見的錯誤。

	`handlers` 屬性會指向包含 Express.js 模組的路由處理常式的本機資料夾。

## 在本機執行 API 應用程式程式碼

在這一節中，您會在本機執行應用程式，以在部署前確認是否能運作。

1. 瀏覽至您下載範例的資料夾。

2. 開啟命令列提示字元並輸入下列命令：

		npm install

3. 當 *install* 命令完成時，請輸入下列命令：

		node server.js

	命令列視窗輸出會顯示 "Server started ..."

5. 在瀏覽器中瀏覽至 http://localhost:1337/

	您會看到下列頁面

	![][sample-api-app-page]

6. 若要檢視 Swagger.json 檔案，請瀏覽至 http://localhost:1337/api/data/swagger。

## 將 API 應用程式程式碼發佈至 Azure App Service

在本節中，您會建立本機 Git 儲存機制並從該儲存機制發送至 Azure，以便將範例應用程式部署至在 Azure App Service 中執行的 API 應用程式。

1. 如果未安裝 Git，請從 [Git 下載頁面](http://git-scm.com/download%20target="_blank")進行安裝。

1. 從命令列中，將目錄變更至範例應用程式目錄，然後輸入下列命令以初始化本機 Git 儲存機制。

		git init


2. 使用下列命令將檔案新增至儲存機制：

		git add .
		git commit -m "Initial commit of the API App"

3. 使用您稍早複製的 Git URL，建立遠端參考，以便將更新發送至之前建立的 Web 應用程式 (API 應用程式主機)：

		git remote add azure [URL for remote repository]

4. 輸入下列命令，將您的變更發送至 Azure：

		git push azure master

	系統會提示您輸入先前建立的密碼。

	此命令的輸出最後會顯示部署成功的訊息：

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## 在 Azure 預覽入口網站中檢視 API 定義

現已將 API 部署到您的 API 應用程式，您可在 Azure 預覽入口網站中查看 API 定義。一開始要先重新啟動*閘道*，這可讓 Azure 認可 API 應用程式的 API 定義已經變更。閘道是一個 Web 應用程式，可處理資源群組中 API 應用程式的 API 管理和授權。

6. 在 Azure 預覽入口網站中，移至您稍早建立之 API 應用程式的 [**API 應用程式**] 刀鋒視窗，然後按一下 [**閘道**] 連結。

	![](./media/app-service-api-nodejs-api-app/clickgateway.png)

7. 在 [**閘道**] 刀鋒視窗中，按一下 [**重新啟動**]。您現在可以關閉此刀鋒視窗。

	![](./media/app-service-api-nodejs-api-app/gatewayrestart.png)

8. 在 [**API 應用程式**] 刀鋒視窗中，按一下 [**API 定義**]。

	![](./media/app-service-api-nodejs-api-app/apidef.png)

	**API 定義**] 刀鋒視窗會顯示兩個 Get 方法。

	![](./media/app-service-api-nodejs-api-app/apidefblade.png)

## 在 Azure 中執行範例應用程式

在 Azure 預覽入口網站中，移至 API 應用程式的 [**API 應用程式主機**] 刀鋒視窗，然後按一下 [**瀏覽** ]。

![][browse-api-app-page]

瀏覽器會顯示您先前在本機執行範例應用程式時看到的首頁。

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

您已將使用 API 應用程式後端的 Node.js Web 應用程式部署至 Azure。如需有關在 Azure 中使用 Node.js 的詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

* 您可以在[嘗試 App Service](http://tryappservice.azure.com) 嘗試此範例 API 應用程式

[portal-quick-create]: ./media/app-service-api-nodejs-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-nodejs-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-nodejs-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-nodejs-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-nodejs-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-nodejs-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-nodejs-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-nodejs-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-nodejs-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-nodejs-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-nodejs-api-app/apiapp-json.png
[server-js]: ./media/app-service-api-nodejs-api-app/server-js.png
[sample-api-app-page]: ./media/app-service-api-nodejs-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-nodejs-api-app/browse-api-app-page.png
 

<!---HONumber=62-->