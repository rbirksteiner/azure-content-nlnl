<properties pageTitle="指定 Node.js 版本" description="了解如何指定 Azure 網站和雲端服務所使用的 Node.js 版本" services="" documentationCenter="nodejs" authors="MikeWasson" manager="wpickett" editor="mollybos"/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="02/19/2015" ms.author="mwasson"/>





# 在 Azure 應用程式中指定 Node.js 版本

裝載 Node.js 應用程式時，您可能會想要確定應用程式是使用特定版本的 Node.js。有數種方式可以為 Azure 上裝載的應用程式完成這項動作。

##預設版本

Azure 提供的 Node.js 版本會持續進行更新。除非另有指定，否則會使用最新的版本。目前包含下列版本：

- 0.12.x：0.12.0
- 0.10.x：0.10.32、0.10.31、0.10.29、0.10.28、10.26、0.10.24、0.10.21、0.10.18、0.10.5
- 0.8.x：0.8.28、0.8.27、0.8.26、0.8.19、0.8.2
- 0.6.x：0.6.20、0.6.17

> [AZURE.NOTE]如果您要將應用程式裝載在 Azure 雲端服務 (Web 或背景工作角色) 中，而且這是您第一次部署應用程式，只要您安裝在部署環境中的 Node.js 版本符合 Azure 上提供的其中一個預設版本，Azure 就會嘗試使用這個版本。

##以 package.json 進行版本設定

您可以在 **package.json** 檔案中新增下列內容，以指定要使用的 Node.js 版本。

	"engines":{"node":version}

其中 *version* 是要使用的特定版本號碼。您可以針對 version 指定較複雜的條件，例如：

	"engines":{"node": "0.6.22 || 0.8.x"}

由於 0.6.22 不是主控環境中已提供的版本，因此將改用 0.8 系列可用的最高版本，也就是 0.8.4 版。

##版本控制網站與應用程式設定
如果您在網站中裝載應用程式，您可以將環境變數 **WEBSITE_NODE_DEFAULT_VERSION** 設定為所需的版本。

##以 PowerShell 進行雲端服務版本設定

如果您要將應用程式裝載在雲端服務中，而且要使用 Azure PowerShell 來部署應用程式，您可以使用 **Set-AzureServiceProjectRole** PowerShell Cmdlet 覆寫預設 Node.js 版本。例如：

	Set-AzureServiceProjectRole WebRole1 node 0.8.4

您也可以使用 **Get-AzureServiceProjectRoleRuntime** 擷取對於以雲端服務形式裝載的應用程式而言，可用的 Node.js 版本清單。

##對 Azure 網站使用自訂版本

雖然 Azure 提供數個預設 Node.js 版本，不過您可能會想要使用預設未提供的版本。如果您的應用程式是以 Azure 網站形式裝載，您可以使用 **iisnode.yml** 檔案達到該目的。下列步驟將逐步引導您對 Azure 網站使用自訂版本的 Node.Js：

1. 建立新目錄，然後在目錄中建立 **server.js** 檔案。**server.js** 檔案應該包含下列內容：

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	這將顯示當您瀏覽網站時會使用的 Node.js 版本。

2. 建立新網站並記下網站的名稱。例如，以下使用 [Azure 命令列工具]建立新的 Azure 網站 (名稱為 **mywebsite**)，然後編輯該網站的 Git 存放庫。

		azure site create mywebsite --git

3. 建立名稱為 **bin** 的新目錄作為 **server.js** 檔案所在目錄的子目錄。

4. 下載要讓應用程式使用的特定 **node.exe** 版本 (Windows 版本)。例如，以下使用 **curl** 下載 0.8.1 版。

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	將 **node.exe** 檔案儲存到先前建立的 **bin** 資料夾中。

5. 在 **server.js** 檔案所在的同一個目錄中建立 **iisnode.yml** 檔案，然後在 **iisnode.yml** 檔案中新增下列內容：

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	您將應用程式發行到 Azure 網站後，您專案中的 **node.exe** 檔案將位在這個路徑中。

6. 發行您的應用程式。例如，由於我稍早是使用 --git 參數建立新的網站，下列命令會將應用程式檔案新增到我的本機 Git 存放庫，然後將這些檔案推播到網站存放庫：

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	發行應用程式後，使用瀏覽器開啟網站。您應該會看見 "Hello from Azure running node version: v0.8.1" 訊息。

##後續步驟

您已了解如何指定應用程式使用的 Node.js 版本，現在請了解如何[使用模組]、[建置並部署 Node.js 網站]和[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]。

[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: xplat-cli.md
[Azure 命令列工具]: xplat-cli.md
[使用模組]: nodejs-use-node-modules-azure-apps.md
[建置並部署 Node.js 網站]: web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=July15_HO1-->