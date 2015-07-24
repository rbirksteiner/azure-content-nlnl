<properties 
	pageTitle="對雲端服務啟用遠端桌面 (Node.js)" 
	description="了解如何對裝載 Azure Node.js 應用程式的虛擬機器啟用遠端桌面存取。" 
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
	ms.date="05/29/2015" 
	ms.author="mwasson"/>


# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取執行 Azure 之角色執行個體的桌面。您可以使用遠端桌面連接來設定虛擬機器，或對應用程式的問題進行疑難排解。

> [AZURE.NOTE]本文適用於以 Azure 雲端服務形式託管的 Node.js 應用程式。


## 必要條件

- 安裝和設定 [Azure PowerShell](../install-configure-powershell.md)。
- 將 Node.js 應用程式部署到 Azure 雲端服務。如需詳細資訊，請參閱「[建立 Node.js 應用程式並部署到 Azure 雲端服務](cloud-services-nodejs-develop-deploy-app.md)」。


## 步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您必須透過使用者名稱、密碼和憑證更新 Azure 的服務定義和組態。

使用包含您應用程式的來源檔案的電腦執行下列步驟。

1. 以系統管理員的身分執行 **Azure PowerShell**。(從 [**開始**] 功能表或**開始畫面** 中，搜尋 **Azure PowerShell**)。

2.  瀏覽至包含服務定義 (.csdef) 和服務組態 (.cscfg) 檔的目錄。

3. 輸入下列 PowerShell cmdlet：

		Enable-AzureServiceProjectRemoteDesktop

4. 出現提示時，請輸入使用者名稱和密碼。

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  輸入下列 PowerShell cmdlet，以發佈變更：

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## 步驟 2：連接到角色執行個體

發佈更新的服務定義後，您可以連接至角色執行個體。

1.  在 [Azure 管理入口網站]中，選取 [**雲端服務**]，然後選取您的服務。

	![azure management portal][cloud-services]

2.  按一下 [執行個體]，然後按一下 [生產] 或 [執行]，以檢視服務的執行個體。選取執行個體，然後按一下頁面底部的 [連接]。

    ![The instances page][3]

2.  當您按一下 [**連接**] 時，網頁瀏覽器會提示您儲存 .rdp 檔案。開啟這個檔案。(例如，如果您使用的是 Internet Explorer，請按一下 [**開啟**])。

    ![prompt to open or save the .rdp file][4]

3.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][5]

4.  按一下 [**連接**] 時會出現安全性提示，要求您輸入存取執行個體所需的認證。輸入您在 [步驟 1] 中建立的密碼 [步驟 1：使用 Azure PowerShell 設定服務以供遠端桌面存取]，然後按一下 [**確定**]。

    ![username/password prompt][6]

連接建立後，「遠端桌面連接」會在 Azure 中顯示執行個體的桌面。

![Remote desktop session][7]

## 步驟 3：將服務設定成停用遠端桌面存取 

當您不再需要透過遠端桌面連接到雲端中的角色執行個體時，請使用 [Azure PowerShell] 停用遠端桌面存取。

1.  輸入下列 PowerShell cmdlet：

    	Disable-AzureServiceProjectRemoteDesktop

2.  輸入下列 PowerShell cmdlet，以發佈變更：

    	Publish-AzureServiceProject

## 其他資源

- [從遠端存取 Azure 中的角色執行個體] 
- [搭配使用遠端桌面與 Azure 角色]


  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 管理入口網站]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [從遠端存取 Azure 中的角色執行個體]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [搭配使用遠端桌面與 Azure 角色]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!---HONumber=62-->