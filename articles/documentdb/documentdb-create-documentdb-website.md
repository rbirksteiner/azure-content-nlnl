<properties 
	pageTitle="使用 Azure 資源管理員範本部署 DocumentDB 和 Azure 網站 | Azure" 
	description="了解如何使用 Azure 資源管理員範本的部署 DocumentDB 帳戶、Azure 網站，以及範例 Web 應用程式。" 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

# 使用 Azure 資源管理員範本部署 DocumentDB 和 Azure 網站 #

本教學課程示範如何使用 Azure 資源管理員範本來部署和整合 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)、[Azure 網站](http://azure.microsoft.com/services/websites/)和範例 Web 應用程式。

完成本教學課程後, 您將能夠回答下列問題：  

-	如何使用 Azure 資源管理員範本來部署和整合 DocumentDB 帳戶和 Azure 網站？
-	如何使用 Azure 資源管理員範本來部署和整合 DocumentDB 帳戶、Azure 網站和 Webdeploy 應用程式？

##<a id="Prerequisites"></a>必要條件 ##
> [AZURE.TIP] 雖然本教學課程不會假設先前有使用 Azure 資源管理員範本、JSON 或 Azure PowerShell 的經驗，但是，如果您想要修改參考的範本或部署選項，則需要每個領域的知識。

在依照本教學課程中的指示進行之前，請先確定您已備妥下列項目：

- Azure 訂閱。Azure 是訂用帳戶型平台。如需有關如何取得訂閱的詳細資訊，請參閱[購買選項](http://azure.microsoft.com/pricing/purchase-options/)、[會員優惠](http://azure.microsoft.com/pricing/member-offers/)或[免費試用](http://azure.microsoft.com/pricing/free-trial/)。
- 一個 Azure 儲存體帳戶。如需相關指示，請參閱[關於 Azure 儲存體帳戶](../storage-whatis-account.md)。
- 具有 Azure PowerShell 的工作站。如需相關指示，請參閱[安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

##<a id="CreateDB"></a>步驟 1：下載並解壓縮範例檔案 ##
讓我們從下載範例檔案開始，我們將在本教學課程中使用。

1. 將[建立 DocumentDB 帳戶、網站和部署示範應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)下載至本機資料夾 (例如 C:\DocumentDBTemplates) 並解壓縮檔案。此範例會將部署 DocumentDB 帳戶、Azure 網站和 Web 應用程式。它還會自動設定 Web 應用程式，以連線到 DocumentDB 帳戶。

2. 將[建立 DocumentDB 帳戶和網站範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)下載至本機資料夾 (例如 C:\DocumentDBTemplates) 並解壓縮檔案。此範例會將部署 DocumentDB 帳戶和 Azure 網站，並將修改網站的組態來輕鬆地呈現 DocumentDB 連接資訊，但不包含 Web 應用程式。  

> [AZURE.TIP] 請注意，根據您電腦的安全性設定，您可能需要解除封鎖所擷取的檔案，方法是在按一下滑鼠右鍵，並按一下 [**內容**]，然後按一下 [**解除封鎖**]。

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>步驟 2：部署文件帳戶、網站與示範應用程式範例 ##

現在讓我們來部署第一個範本。

> [AZURE.TIP] 範本不會驗證下面輸入的網站名稱和 DocumentDB 帳戶名稱 a) 是否有效且 b) 可用。強烈建議您在執行 PowerShell 部署指令碼之前，先確認您打算提供之名稱的可用性。

1. 開啟 Microsoft Azure PowerShell，然後瀏覽至您下載並解壓縮[建立 DocumentDB 帳戶、網站和部署示範應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)的資料夾 (例如 C:\DocumentDBTemplates\CreateDocDBWebsiteTodo)。


2. 我們即將執行 CreateDocDBWebsiteTodo.ps1 PowerShell 指令碼。指令碼採用下列必要參數：
	- WebsiteName：指定網站名稱，並用來建構您將用來存取網站的 URL (例如：如果您指定 "mydemodocdbwebsite"，則將存取網站的 URL 會是 mydemodocdbwebsite.azurewebsites.net)。

	- ResourceGroupName：指定要部署的 Azure 資源群組名稱。如果指定的資源群組不存在，就會建立。

	- docDBAccountName：指定要建立之 DocumentDB 帳戶的名稱。

	- location：指定要在其中建立 DocumentDB 和網站資源的 Azure 位置。有效的值為：東亞、東南亞、美國東部、美國西部、北歐、西歐 (請注意，提供的位置值區分大小寫)。


3. 以下是執行指令碼的範例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] 請注意，系統會提示您輸入您的 Azure 帳戶使用者名稱和密碼做為執行指令碼的一部分。完整部署需要 10 到 15 分鐘才能完成。  	

4. 同時，以下是產生輸出的範例： 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. 在查看我們的範例應用程式之前，讓我們了解完成的範本部署：

	- 建立了 Azure 網站。

	- 建立了 Azure DocumentDB 帳戶。

	- 部署了 Web 部署封裝到 Azure 網站

	- 修改了 Azure 網站組態，因此 Azure DocumentDB 端點和主要金鑰呈現為應用程式設定。

	- 建立了一系列的預設監視規則。

	
6. 為使用應用程式，只要瀏覽至網站 URL (上述範例中的 URL 會是 http://mydemodocdbwebsite.azurewebsites.net)。您會看到下列的 Web 應用程式：

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. 請繼續進行，並建立幾個工作，然後讓我們開啟 [Azure 預覽入口網站](https://portal.azure.com)。

8. 選擇瀏覽資源群組，並選取我們部署期間建立的資源群組 (以上範例的 myDemoResourceGroup)。

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  請注意 [摘要] 透鏡中的資源對應如何顯示我們所有的相關資源 (DocumentDB 帳戶、網站、監視)。

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  按一下您的 DocumentDB 帳戶，並啟動「查詢總管」中 (靠近 [帳戶] 分頁的底部)。

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. 執行預設查詢 ("SELECT * FROM c")，並檢查結果。請注意查詢已擷取您在步驟 7 所建立待辦事項的 JSON 表示法。任意嘗試查詢；例如，嘗試執行 SELECT * FROM c WHERE c.isComplete = true，傳回所有已標示為完成的待辦事項。


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. 任意瀏覽 DocumentDB 入口網站體驗，或修改範例 Todo 應用程式。當您準備好時，讓我們來部署另一個範本。
	 
##<a id="Build"></a>步驟 3：部署文件帳戶和網站範例 ##

現在讓我們來部署第二個範本。

> [AZURE.TIP] 範本不會驗證下面輸入的網站名稱和 DocumentDB 帳戶名稱 a) 是否有效且 b) 可用。強烈建議您在執行 PowerShell 部署指令碼之前，先確認您打算提供之名稱的可用性。

1. 開啟 Microsoft Azure PowerShell，然後瀏覽至您下載並解壓縮[建立 DocumentDB 帳戶和網站範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) 的資料夾 (例如 C:\DocumentDBTemplates\CreateDocDBWebsite)。


2. 我們即將執行 CreateDocDBWebsite.ps1 PowerShell 指令碼。指令碼採用與我們部署的第一個範本相同的參數，即：
	- WebsiteName：指定網站名稱，並用來建構您將用來存取網站的 URL (例如：如果您指定 "myotherdocumentdbwebsite"，則將存取網站的 URL 會是 myotherdocumentdbwebsite.azurewebsites.net)。

	- ResourceGroupName：指定要部署的 Azure 資源群組名稱。如果指定的資源群組不存在，就會建立。

	- docDBAccountName：指定要建立之 DocumentDB 帳戶的名稱。

	- 	location：指定要在其中建立 DocumentDB 和網站資源的 Azure 位置。有效的值為：東亞、東南亞、美國東部、美國西部、北歐、西歐 (請注意，提供的位置值區分大小寫)。

3. 以下是執行指令碼的範例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] 請注意，系統會提示您輸入您的 Azure 帳戶使用者名稱和密碼做為執行指令碼的一部分。完整部署需要 10 到 15 分鐘才能完成。  	

4. 部署輸出將非常類似第一個範本範例。 
5. 在我們開啟 Azure 入口網站之前，讓我們先了解完成的範本部署：

	- 建立了 Azure 網站。

	- 建立了 Azure DocumentDB 帳戶。

	- 	修改了 Azure 網站組態，因此 Azure DocumentDB 端點、主要金鑰和次要金鑰呈現為應用程式設定。

	- 	建立了一系列的預設監視規則。

6. 讓我們開啟 [Azure 預覽入口網站](https://portal.azure.com)，並選擇瀏覽資源群組，然後選取我們在部署期間建立的資源群組 (在上面的範例中，是 myOtherDemoResourceGroup)。
7. 在 [摘要] 透鏡中，按一下剛部署的 Azure 網站。

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. 在 [網站] 分頁中，按一下 [**所有設定**]，然後 [**應用程式設定**]，並注意出現的 DocumentDB 端點和每個 DocumentDB 主要金鑰的應用程式設定。

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. 任意繼續瀏覽探索 Azure 入口網站，或遵循其中一個 DocumentDB [範例](http://go.microsoft.com/fwlink/?LinkID=402386)來建立您自己的 DocumentDB 應用程式。

	
	

##<a name="NextSteps"></a>後續步驟

恭喜！您已使用 Azure 資源管理員範本部署了 DocumentDB、Azure 網站以及範例 Web 應用程式。

- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
- 若要深入了解 Azure 網站，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=325362)。
- 若要深入了解 Azure 資源管理員範本，請按一下[這裡](https://msdn.microsoft.com/library/azure/dn790549.aspx)。

<!--HONumber=49--> 