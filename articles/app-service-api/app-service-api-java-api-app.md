<properties
	pageTitle="在 Azure App Service 中建置和部署 Java API 應用程式"
	description="了解如何建立 Java 應用程式封裝並將其部署至 Azure App Service。"
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# 在 Azure App Service 中建置和部署 Java API 應用程式

本教學課程說明如何建立 Java 應用程式，並使用 [Git](http://git-scm.com) 將其部署至 Azure App Service API 應用程式。本教學課程中的指示可運用在任何足以執行 Java 應用程式的作業系統上。本教學課程也使用 [Gradle](https://gradle.org) 來啟用 Java 應用程式的組建自動化和封裝相依性解析。最後，[RESTEasy](http://resteasy.jboss.org/) 用來建立符合 REST 限制的服務，並完全落實 [JaxRS](https://jax-rs-spec.java.net/) 規格。

完成之應用程式的螢幕擷取畫面如下：

![][sample-api-app-page]

## 在 Azure 入口網站中建立 API 應用程式

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/)或是[申請免費試用](/pricing/free-trial/)。您也可以免費試用 [App Service 應用程式範例](http://tryappservice.azure.com)。

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)。

2. 按一下入口網站左下方的 [**新增**]。

3. 按一下 **[Web + 行動] > [API 應用程式]**。

	![][portal-quick-create]

4. 輸入 [**名稱**] 的值，例如 JavaAPIApp。

5. 選取 App Service 方案或新建一個。如果您建立新方案，請選取定價層、位置和其他選項。

	![][portal-create-api]

6. 按一下 [建立]。

	![][api-app-blade]

	如果您已選取 [**新增到開始面板**] 核取方塊，則入口網站會在 API 應用程式建立後自動開啟其刀鋒視窗。如果您已清除此核取方塊，請按一下入口網站首頁上的 [**通知**]，以查看 API 應用程式建立狀態，然後按一下通知可移至新 API 應用程式的刀鋒視窗。

7. 按一下 **[設定] > [應用程式設定]**。

9. 將存取權層級設定為 [**公用 (匿名)**]。

11. 按一下 [儲存]。

	![][set-api-app-access-level]

## 啟用新 API 應用程式的 Git 發佈

[Git](http://git-scm.com) 是一個您可用來部署 Azure 網站的分散式版本控制系統。您將會在本機 Git 儲存機制中儲存您為 API 應用程式撰寫的程式碼，並藉由發送至遠端儲存機制，將您的程式碼部署至 Azure。此部署方法是您可以在 API 應用程式中使用的 App Service Web 應用程式功能，因為 API 應用程式是以 Web 應用程式為基礎：Azure App Service 中的 API 應用程式是具備其他用於裝載 Web 服務之功能 Web 應用程式。

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

## 在新的 API 應用程式上啟用 Java 執行階段

為了讓 API 應用程式成功裝載 Java 應用程式，我們必須啟用 Java 執行階段並選擇應用程式伺服器。入口網站可讓您輕鬆執行此作業。我們即將啟用 Java 7 和 Jetty 來裝載應用程式。

1. 在 [API 應用程式] 刀鋒視窗中，按一下 [**API 應用程式主機**]。

	![][api-app-host]

2. 按一下 **[設定] > [應用程式設定]**。在那裡，啟用 Java 並選取 Jetty 作為應用程式伺服器。按一下 [儲存]

	![][api-app-enable-java]

這會在您的 API 應用程式上**啟用 Java 執行階段**，並在您網站的根目錄中建立 **webapps/** 資料夾。這個資料夾將包含應用程式的所有 .war 檔案。

## 下載並檢查 Java API 應用程式的程式碼

在本節中，您將下載並查看作為 JavaAPIApp 範例的一部分提供的程式碼。

1. 在[此 GitHub 儲存機制](http://go.microsoft.com/fwlink/?LinkId=571009)中下載程式碼。您可以複製儲存機制，或按一下 [**下載 Zip**] 以下載為 .zip 檔案。如果您下載.zip 檔案，請在本機磁碟中將它解壓縮。

2. 瀏覽至您將範例解壓縮的資料夾並瀏覽至 `build\libs` 資料夾。

	![][api-app-folder-browse]

3. 在文字編輯器中開啟 **apiapp.json** 檔案並檢查內容。

	![][apiapp-json]

	若要將 Java 應用程式辨識為 API 應用程式，Azure App Service 需具備兩個先決條件：

	+ 名為 *apiapp.json* 的檔案必須存在於應用程式的根目錄中。
	+ 應用程式必須公開 Swagger 2.0 中繼資料端點。此端點的 URL 已在 *apiapp.json* 檔案中指定。

	請注意 **apiDefinition** 屬性。此 URL 的路徑相對於您的 API 的 URL，而且會指向 Swagger 2.0 端點。Azure App Service 會使用這個屬性來探索 API 的定義，並啟用許多 App Service API 應用程式功能。

4. 瀏覽至 `src\main\java\com\microsoft\trysamples\javaapiapp`，開啟 **App.java** 檔案並檢查其程式碼。

	![][app-java]

	程式碼會使用適用於 JaxRS 的 Swagger 封裝來建立 Swagger 2.0 端點。

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	`setVersion` 方法會在 Swagger 所提供的中繼資料中設定 API 版本。

	`setBasePath` 方法會設定 Swagger 用來產生中繼資料的基底路徑。此 URL 相對於 API 應用程式的基底路徑。

	`setHost` 方法會設定 API 正在接聽的主機。在此情況下，我們會使用之前已指派數行的 `websitehostname` 變數，以動態方式設定為 `localhost` (在本機執行時) 或 Azure 應用程式主機名稱 (當應用程式在 Azure App Service 中執行時)。

	`setResourcePackage` 方法會設定 Swagger 將要掃描並包含在 Swagger.json 檔案中的封裝，包含 API 中繼資料。

	`setSchemes` 方法可定義支援的配置。

	`setScan` 方法可讓 Swagger 產生應用程式文件。

	使用 RESTEasy 時，有更多的方法可自訂 Swagger 的輸出，在 Swagger 的 [Wiki 網頁](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig)上這找到這些方法。

	> [AZURE.NOTE]在 `/JavaAPIApp/api/swagger.json` 可以存取 Swagger 中繼資料檔案。

## 在本機執行 API 應用程式程式碼

在這一節中，您會在本機執行應用程式，以在部署前確認是否能運作。

1. 瀏覽至您下載範例的資料夾。

2. 開啟命令列提示字元並輸入下列命令：

		gradlew.bat

3. 當命令完成時，請輸入下列命令：

		gradlew.bat jettyRunWar

	命令列視窗輸出會顯示：

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. 在瀏覽器中瀏覽至 `http://localhost:8080/JavaAPIApp/`

	您會看到下列頁面

	![][sample-api-app-page]

6. 若要檢視 Swagger.json 檔案，請瀏覽至 `http://localhost:8080/JavaAPIApp/api/Swagger.json`。

## 將 API 應用程式程式碼發佈至 Azure App Service

在本節中，您會建立本機 Git 儲存機制並從該儲存機制發送至 Azure，以便將範例應用程式部署至在 Azure App Service 中執行的 API 應用程式。

1. 如果未安裝 Git，請從 [Git 下載頁面](http://git-scm.com/download)進行安裝。

1. 從命令列中，將目錄變更至範例應用程式目錄，然後 `build\libs` 並輸入下列命令以初始化本機 Git 儲存機制。

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

## 在 Azure 入口網站中檢視 API 定義

現已將 API 部署到您的 API 應用程式，您可在 Azure 入口網站中查看 API 定義。一開始要先重新啟動*閘道*，這可讓 Azure 認可 API 應用程式的 API 定義已經變更。閘道是一個 Web 應用程式，可處理資源群組中 API 應用程式的 API 管理和授權。

6. 在 Azure 入口網站中，移至您稍早建立之 API 應用程式的 [**API 應用程式**] 刀鋒視窗，然後按一下 [**閘道**] 連結。

	![][click-gateway]

7. 在 [**閘道**] 刀鋒視窗中，按一下 [**重新啟動**]。您現在可以關閉此刀鋒視窗。

	![][restart-gateway]

8. 在 [**API 應用程式**] 刀鋒視窗中，按一下 [**API 定義**]。

	![][api-definition-click]

	[**API 定義**] 刀鋒視窗會顯示一個 Get 方法。

	![][api-definition-blade]

## 在 Azure 中執行範例應用程式

在 Azure 入口網站中，移至 API 應用程式的 [**API 應用程式主機**] 刀鋒視窗，然後按一下 [**瀏覽** ]。

![][browse-api-app-page]

瀏覽器會顯示您先前在本機執行範例應用程式時看到的首頁。

## 後續步驟

您已將使用 API 應用程式後端的 Java Web 應用程式部署至 Azure。如需有關在 Azure 中使用 Java 的詳細資訊，請參閱 [ 開發人員中心](/develop/java/)。

您可以在[嘗試 App Service](http://tryappservice.azure.com) 嘗試此範例 API 應用程式

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=62-->