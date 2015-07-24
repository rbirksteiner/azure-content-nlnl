<properties 
	pageTitle="設定雲端服務 (Node.js) 的 SSL" 
	description="在 Azure 中設定 Node.js 雲端服務背景工作角色的 SSL" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mwasson"/>





# 在 Azure 背景工作角色中設定 Node.js 應用程式的 SSL

安全通訊端層 (SSL) 加密是最常用來保護在網際網路上傳送之資料的方法。此常見工作討論如何為背景工作角色中裝載成 Azure 雲端服務的 Node.js 應用程式指定 HTTPS 端點。

> [AZURE.NOTE]本文中的步驟僅適用於背景工作角色中裝載成 Azure 雲端服務的節點應用程式。

此工作包含下列步驟：

-   [步驟 1：建立 Node.js 服務並將服務發行至雲端]
-   [步驟 2：取得 SSL 憑證]
-   [步驟 3：修改應用程式來使用 SSL 憑證]
-   [步驟 4：修改服務定義檔]
-   [步驟 5：使用 HTTPS 來連線至角色執行個體]

## <a name="step1"> </a>步驟 1：建立 Node.js 服務並將服務發行至雲端

您可以使用下列步驟在 Azure PowerShell 中建立簡單的 Node.js 'hello world' 服務：

1. 從 [開始] 功能表 或 [開始畫面] 中，搜尋 **Azure PowerShell**。最後，以滑鼠右鍵按一下 [Azure PowerShell]，然後選取 [以系統管理員身分執行]。

	![Azure PowerShell icon][powershell-menu]

	

2.  使用 **New-AzureServiceProject** Cmdlet 建立新的服務。

	![][1]

3.  使用 **Add-AzureNodeWorkerRole** Cmdlet 將背景工作角色加入至您的服務：

    ![][2]

4.  使用 **Publish-AzureServiceProject** Cmdlet 將服務發行至雲端：

    ![][3]

	> [AZURE.NOTE]如果您先前未匯入 Azure 訂閱的發行設定，則嘗試發行時會發生錯誤。如需有關下載和匯入訂閱之發行設定的詳細資訊，請參閱＜[如何使用 Azure PowerShell 來處理 Node.js](https://www.windowsazure.com/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)＞(英文)

**Publish-AzureServiceProject** Cmdlet 傳回的 [建立的網站 URL] 值包含受託管應用程式的完整網域名稱。您需要取得此特定完整網域名稱的 SSL 憑證並部署至 Azure。

## <a name="step2"> </a>步驟 2：取得 SSL 憑證

若要設定應用程式的 SSL，首先您需要取得已由憑證授權單位 (CA) (專門核發憑證的受信任第三方) 簽署的 SSL 憑證。如果您還沒有此類憑證，則必須向銷售 SSL 憑證的公司取得。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：

-   憑證必須包含私密金鑰。
-   憑證必須是基於金鑰交換而建立 (**.pfx** 檔案)。
-   憑證的主體名稱必須符合用來存取雲端服務的網域。您無法取得 cloudapp.net 網域的 SSL 憑證，因此，憑證的主體名稱必須符合用來存取應用程式的自訂網域名稱。例如，__mysecuresite.cloudapp.net__。
-   憑證至少必須以 2048 位元加密。

在下列步驟中，含憑證的 **.pfx** 檔案將加入至服務專案並部署至 Azure。

## <a name="step3"> </a>步驟 3：修改應用程式來使用 SSL 憑證

當 Node.js 應用程式部署至背景工作角色時，是由 Node.exe 管理伺服器憑證和 SSL 連線。為了處理 SSL 流量，您必須使用 'https' 模組，而不是 'http'。請執行下列步驟將 SSL 憑證加入至專案，然後修改應用程式來使用憑證。

1.   將憑證授權單位 (CA) 提供的 **.pfx** 檔案儲存至您的應用程式所在的目錄。例如，**c:\node\securesite\workerrole1** 是本文中使用的應用程式所在的目錄。

2.   使用 Notepad.exe 開啟 **c:\node\securesite\workerrole1\server.js** 檔案，將檔案內容改成下列內容：

		var https = require('https');
		var fs = require('fs');

		var options = {
			pfx: fs.readFileSync('certificate.pfx'),
			passphrase: "password"
		};
		var port = process.env.PORT || 8000;
		https.createServer(options, function (req, res) {
 		    res.writeHead(200, { 'Content-Type': 'text/plain' });
		    res.end('Hello World\n');
		}).listen(port);

	> [AZURE.IMPORTANT]您必須使用憑證檔案名稱來取代「certificate.pfx」，並使用憑證檔案密碼 (如果有的話) 來取代「password」。

3.   儲存 **server.js** 檔案。

修改 **server.js** 檔案之後，當應用程式部署至 Azure 時，就會在連接埠 443 (SSL 通訊的標準連接埠) 接聽通訊。**.pfx** 檔案將用來實作此傳輸上的 SSL 通訊。

## <a name="step4"> </a>步驟 4：修改服務定義檔

因為應用程式現在開始接聽連接埠 443，您也必須修改定義來允許透過此連接埠進行通訊。

1.  在服務目錄中，開啟服務定義檔 (**ServiceDefinition.csdef**)、更新 **Endpoints** 區段內的 http **InputEndpoint** 元素，以允許透過連接埠 443 進行通訊：

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

	完成此變更之後，儲存 **ServiceDefinition.csdef** 檔案。

4.  重新發行服務，在雲端中重新整理您已更新的組態。在 Azure PowerShell 提示字元中，從服務目錄輸入 **Publish-AzureServiceProject**。

## <a name="step5"> </a>步驟 5：使用 HTTPS 來連線至角色執行個體

您的部署已在 Azure 啟動並執行，現在您可以使用 HTTPS 來與其連線。

1.  在 Azure 管理入口網站中，選取您的雲端服務，然後按一下 [儀表板]。

2. 向下捲動並按一下顯示為 [網站 URL] 的連結：

    ![the site url][site-url]

	> [AZURE.IMPORTANT]如果入口網站中顯示的 [網站 URL] 未指定 HTTPS，您必須在瀏覽器中使用 HTTPS (而不是 HTTP) 手動輸入 URL。

3.  將會開啟新的瀏覽器並顯示您的網站。

    瀏覽器會顯示掛鎖圖示，表示正在使用 HTTPS 連線。這也表示已正確設定應用程式的 SSL。

    ![][8]

## 其他資源

[如何使憑證與服務產生關聯]

[在 Azure Web 角色中設定 Node.js 應用程式的 SSL]

[如何在 HTTPS 端點上設定 SSL 憑證]

  [步驟 1：建立 Node.js 服務並將服務發行至雲端]: #step1
  [步驟 2：取得 SSL 憑證]: #step2
  [步驟 3：修改應用程式來使用 SSL 憑證]: #step3
  [步驟 4：修改服務定義檔]: #step4
  [步驟 5：使用 HTTPS 來連線至角色執行個體]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  [如何使憑證與服務產生關聯]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [如何在 HTTPS 端點上設定 SSL 憑證]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  
  
  [在 Azure Web 角色中設定 Node.js 應用程式的 SSL]: /develop/nodejs/common-tasks/enable-ssl/
  
 

<!---HONumber=62-->