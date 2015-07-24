<properties
	pageTitle="在 Azure App Service 中建立 Node.js Web 應用程式"
	description="了解如何在 Azure 中建置和部署 Node.js Web 應用程式。"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# 在 Azure App Service 中建置和部署 Node.js Web 應用程式

本教學課程說明如何建立 [Node][nodejs.org] 應用程式，並使用 [Git] 將其部署至 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。本教學課程中的指示可運用在任何足以執行 Node 應用程式的作業系統上。

完成之應用程式的螢幕擷取畫面如下：

![顯示「Hello World」訊息的瀏覽器。][helloworld-completed]

##建立 Web 應用程式並啟用 Git 發佈

請遵循下列步驟來建立 Web 應用程式，並啟用 Git 發佈。

> [AZURE.NOTE]若要完成本教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或是[申請免費試用](/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下入口網站左下方的 [+新增] 圖示

3. 依序按一下 [Web + 行動] 和 [Web 應用程式]。

    ![][portal-quick-create]

4. 輸入 [URL] 的值。

5. 選取 App Service 方案或建立新方案。如果您建立新方案，請選取定價層、位置和其他選項。

    ![][portal-quick-create2]

6. 按一下 [建立]。

7. 一旦狀態變更為 [執行中] 之後，入口網站就會自動開啟 Web 應用程式的分頁。您也可以按一下 [瀏覽] 開啟該分頁。

	![][go-to-dashboard]

8. 按一下 [部署]。您可能需要捲動，才能看到分頁的這個組件。

	![][deployment-part]

9. 依序按一下 [選擇來源] 和 [本機 Git 儲存機制]。按一下 [確定]。

	![][setup-git-publishing]


10. 按一下 [部署認證] 組件 (下列紅色外框)。建立使用者名稱和密碼。按一下 [儲存]。如果您先前已經啟用 Web 應用程式的發行，就不需要執行此步驟。

	![][deployment-credentials]


11. 若要發佈，就需要推送至 Git 遠端儲存機制。尋找儲存機制的 URL、按一下 [所有設定]，然後按一下 [內容]。該 URL 會列於 [GIT URL] 下方。

	![][git-url]

##在本機建置與測試您的應用程式

在本節中，您將建立 **server.js** 檔案，其中內含來自 [nodejs.org] (英文) 的 'hello world' 範例。此範例已從原始範例中加以修改，將 process.env.PORT 新增為在 Azure Web 應用程式中執行時要接聽的連接埠。

1. 使用文字編輯器，在 **helloworld** 目錄中建立名為 **server.js** 的新檔案。如果 **helloworld** 目錄不存在，請建立一個。

2. 新增下列作為 **server.js** 檔案的內容，然後加以儲存：

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. 開啟命令列，然後使用下列命令在本機啟動 Web 應用程式：

        node server.js

4. 開啟您的 Web 瀏覽器並瀏覽至 http://localhost:1337。如以下螢幕擷取畫面所示，隨即會出現顯示 "Hello World" 的網頁瀏覽器：

    ![顯示「Hello World」訊息的瀏覽器。][helloworld-localhost]

##發行您的應用程式

1. 從命令列中，將目錄變更至 **helloworld** 目錄，然後輸入以下命令以初始化本機 Git 儲存機制。

		git init

	> [AZURE.NOTE]**Git 命令無法使用？** [Git](http://git-scm.com/%20target="_blank) 是一個您可用來部署 Azure 網站的分散式版本控制系統。如需您的平台適用的安裝指示，請參閱 [Git 下載頁面](http://git-scm.com/download%20target="_blank")。

2. 使用下列命令將檔案新增至儲存機制：

		git add .
		git commit -m "initial commit"

3. 使用下列命令來加入 Git remote，以將更新推送至您先前建立的 Web 應用程式：

		git remote add azure [URL for remote repository]


4. 使用下列命令將您的變更發送至 Azure：

		git push azure master

	系統會提示您輸入先前建立的密碼。輸出應該類似如下範例：

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. 若要檢視您的應用程式，請按一下管理入口網站內 [Web 應用程式] 組件上的 [瀏覽] 按鈕。

##將變更發行至您的應用程式

1. 在文字編輯器中開啟 **server.js** 檔案，然後將「Hello World\n」變更為「Hello Azure\n」。儲存檔案。
2. 從命令列中，將目錄位置變更至 **helloworld** 目錄，然後執行下列命令：

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	系統會提示您輸入先前建立的密碼。

3. 按一下 [瀏覽] 以瀏覽至您的應用程式，並注意已經套用更新。

	![顯示「Hello Azure」的網頁][helloworld-completed]

4. 您可以在 [部署] 中選取先前的部署，以進行還原。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

##後續步驟

雖然本文內的步驟使用 Azure 入口網站來建立 Web 應用程式，但是您也可以使用 [Azure 命令列介面](../xplat-cli.md)來執行相同的操作。

Node.js 提供您豐富的模組生態系統，可供您的應用程式使用。若要了解 Web Apps 如何與模組搭配使用，請參閱[使用 Node.js 模組來搭配 Azure 應用程式](../nodejs-use-node-modules-azure-apps.md)。

若要深入了解 Azure 隨附的 Node.js 版本，以及如何指定要與您的應用程式搭配使用的版本，請參閱[在 Azure 應用程式中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md) (英文)。

如果您在將應用程式部署到 Azure 後遇到問題，請參閱[如何在 Azure 網站中對 Node.js 應用程式進行偵錯](web-sites-nodejs-debug.md) (英文) 以獲得診斷問題的詳細資訊。


##其他資源

* [Azure PowerShell](../install-configure-powershell.md)
* [Azure 命令列介面](../xplat-cli.md)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=62-->