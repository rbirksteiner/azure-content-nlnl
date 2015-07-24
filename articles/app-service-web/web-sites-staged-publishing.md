<properties
	pageTitle="針對 Azure App Service 中的 Web 應用程式設定預備環境"
	description="了解如何針對 Azure App Service 中的 Web 應用程式使用預備發行。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="cephalin"/>

# 針對 Azure App Service 中的 Web 應用程式設定預備環境
<a name="Overview"></a>

當您將 Web 應用程式部署至 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 時，如果是在 [標準] 或 [高階] App Service 方案模式中執行，就可以部署到個別的部署位置，而不是預設的生產位置。部署位置實際上是含有自己主機名稱的作用中 Web 應用程式。兩個部署位置 (包括生產位置) 之間的 Web 應用程式內容與設定項目可以互相交換。將應用程式部署至部署位置具有下列優點：

- 您可以先驗證預備部署位置中的 Web 應用程式變更，再將它與生產位置進行交換。

- 先將 Web 應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。這麼做可排除部署 Web 應用程式時的停機情況。交換作業期間所有的流量都能順暢地重新導向，而且不會捨棄任何要求封包。不需要預先交換驗證時，這整個工作流程可藉由設定[自動交換](#configure-auto-swap-for-your-web-app)來自動化。

- 交換之後，含有之前預備 Web 應用程式的位置，現已擁有之前的生產 Web 應用程式。若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以取回「上一個已知良好的網站」。

每個 App Service 方案模式所支援的部署位置個數都不一樣。若要找出 Web 應用程式模式所支援的位置個數，請參閱 [App Service 定價](/pricing/details/app-service/)。

- 當您的 Web 應用程式擁有多個位置時，就無法變更該模式。

- 非生產的位置無法使用調整規模。

- 非生產位置不支援連結的資源管理。

	> [AZURE.NOTE]只有在 [Azure 預覽入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)中，您才能藉由暫時將非生產位置移到其他 App Service 方案模式，來避免這種對生產位置的潛在影響。請注意，非生產位置必須先再次與生產位置共用相同模式，您才能交換這兩個位置。

<a name="Add"></a>
## 將部署位置新增至 Web 應用程式 ##

Web 應用程式必須在 [標準] 或 [高階] 模式中執行，您才能啟用多個部署位置。

1. 在 [Azure 預覽入口網站](https://portal.azure.com/)中，開啟 Web 應用程式的刀鋒視窗。
2. 按一下 [部署位置]。然後，在 [部署位置] 刀鋒視窗中，按一下 [加入位置]。

	![新增部署位置][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]如果 Web 應用程式尚未處於 [標準] 或 [高階] 模式，您將會收到訊息，指出支援啟用預備發行的模式。此時，您可以選取 [升級]，並瀏覽至 Web 應用程式的 [級別] 索引標籤後再繼續。

2. 在 [新增位置] 刀鋒視窗中，指定位置名稱，然後選取是否要複製其他現有部署位置的 Web 應用程式設定。按一下打勾記號繼續。

	![組態來源][ConfigurationSource1]

	第一次新增位置時，您只會有兩個選項：從生產環境的預設位置複製設定，或者完全不複製。

	建立數個位置後，就可以從生產位置以外的位置複製組態：

	![組態來源][MultipleConfigurationSources]

5. 在 [部署位置] 刀鋒視窗裝，按一下部署位置以開啟該位置的刀鋒視窗，其中含有一組度量和設定，就像任何其他 Web 應用程式一樣。<strong><i>your-web-app-name</i>-<i>deployment-slot-name</i></strong> 將出現在刀鋒視窗頂端，提醒您正在檢視該部署位置。

	![Deployment Slot Title][StagingTitle]

5. 在位置的刀鋒視窗中按一下應用程式 URL。請注意，部署位置有自己的主機名稱，同時也是作用中的應用程式。若要限制對部署位置的公用存取，請參閱 [App Service Web 應用程式 - 封鎖對非生產部署位置的 Web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)。

建立部署位置之後不會有任何內容。您可以從不同的儲存機制分支，或從整個不同的儲存機制部署至位置。您也可以變更位置的組態。更新內容時，請使用與部署位置相關聯的發行設定檔或部署認證。例如，您可以[使用 Git 發行至此位置](web-sites-publish-source-control.md)。

<a name="AboutConfiguration"></a>
## 部署位置組態 ##
當您複製其他部署位置的組態時，可以編輯複製的組態。此外，某些組態項目在交換時會遵循內容 (非位置特定)，而其他組態項目將會在交換之後保留於同一個位置中 (位置特定)。以下清單顯示當您交換位置時會變更的組態。

**交換的設定**：

- 一般設定 - 例如 Framework 版本、32/64 位元、Web 通訊端
- 應用程式設定 (可以設定為停在某一個位置)
- 連接字串 (可以設定為停在某一個位置)
- 處理常式對應
- 監視與診斷設定
- WebJobs 內容

**無法交換的設定**：

- 發行端點
- 自訂網域名稱
- SSL 憑證與繫結
- 擴充設定
- WebJobs 排程器

若要將應用程式設定或連接字串設定為停留在某一個位置 (未交換)，可存取特定位置的 [應用程式設定] 刀鋒視窗，然後針對應停留在該位置的設定項目選取 [位置設定] 方塊。請注意，將組態項目標記為位置特定的，會在將該項目建立為無法跨所有與該 Web 應用程式相關聯的部署位置進行交換時產生影響。

![位置設定][SlotSettings]

<a name="Swap"></a>
## 交換部署位置 ##

>[AZURE.IMPORTANT]在您將 Web 應用程式從部署位置交換到生產位置之前，請確定所有非位置特定的設定已完全依照您想要在交換目標中擁有它的方式明確地加以設定。

1. 若要交換部署位置，可按一下 Web 應用程式命令列或部署位置命令列中的 [交換] 按鈕。請確定交換來源和交換目標都已正確設定。交換目標通常是生產位置。  

	![Swap Button][SwapButtonBar]

3. 按一下 [確定] 來完成操作。當操作完成時，部署位置就已交換完畢。

## 設定 Web 應用程式的自動交換 ##

自動交換會簡化 DevOps 案例，在此案例中，您希望為該 Web 應用程式的客戶在不需冷啟動和不需關機的情況下連續部署您的 Web 應用程式。當部署位置已設為自動交換至生產位置時，每當您將程式碼更新推送至該位置時，App Service 就會在其已於該位置上做好準備之後，自動將該 Web 應用程式交換至生產位置。

>[AZURE.IMPORTANT]當您為某個位置啟用自動交換時，請確定位置設定會與適用於目標位置 (通常是生產位置) 的設定完全相同。

為位置設定自動交換很容易。請依照下列步驟執行：

1. 在 [部署位置] 刀鋒視窗中，選取非生產位置，按一下該位置刀鋒視窗的 [所有設定]。  

	![][Autoswap1]

2. 按一下 [應用程式設定]。針對 [自動交換] 選取 [開啟]、在 [自動交換位置] 中選取所需的目標位置，然後按一下命令列中的 [儲存]。確定此位置的組態設定完全適用於目標位置的組態設定。

	當操作完成時，[通知] 索引標籤會有綠色的「成功」字樣閃爍顯示。

	![][Autoswap2]

	>[AZURE.NOTE]若要針對您的 Web 應用程式測試自動交換，可在 [自動交換位置] 中選取非生產的目標位置，以便先熟悉這個功能。

3. 執行程式碼推送至該部署位置。自動交換不久之後就會發生，而更新將反映於目標位置的 URL 上。

<a name="Rollback"></a>
## 交換之後回復生產應用程式 ##
若交換位置後，在生產位置中識別出錯誤，可以立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Delete"></a>
## 刪除部署位置##

在部署位置的刀鋒視窗中，按一下命令列上的 [刪除]。

![刪除部署位置][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## 適用於部署位置的 Azure PowerShell Cmdlet

Azure PowerShell 模組提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure App Service 中的 Web 應用程式部署位置。

- 如需安裝與設定 Azure PowerShell，以及使用您的 Azure 訂用帳戶驗證 Azure PowerShell 的詳細資訊，請參閱[如何安裝和設定 Microsoft Azure PowerShell](../install-configure-powershell.md) (英文)。  

- 若要列出 PowerShell 中可供 Azure App Service 使用的 Cmdlet，請呼叫 `help AzureWebsite`。

----------

### Get-AzureWebsite
**Get-AzureWebsite** Cmdlet 會針對現有訂用帳戶呈現 Azure Web 應用程式相關資訊，如以下範例所示。

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
您可以使用 **New-AzureWebsite** Cmdlet 並指定 Web 應用程式和位置的名稱，來建立部署位置。此外，您還可以指定相同區域做為部署位置建立作業所需的 Web 應用程式，如以下範例所示。

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
您可以在部署內容時使用 **Publish-AzureWebsiteProject** Cmdlet，如以下範例所示。

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
當內容與組態更新都套用到新的位置後，您就可以使用 **Show-AzureWebsite** Cmdlet 瀏覽至該位置以驗證更新，如以下範例所示。

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
**Switch-AzureWebsiteSlot** Cmdlet 可執行交換操作，將更新的部署位置轉變為生產網站，如以下範例所示。生產應用程式不會發生任何停機事件，也不會進行冷啟動。

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
如果不再需要某個部署位置，可以使用 **Remove-AzureWebsite** Cmdlet 將其刪除，如以下範例所示。

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## 適用於部署位置的 Azure 命令列介面 (Azure CLI) 命令

Azure CLI 提供跨平台命令供您處理 Azure，包括支援管理 Web 應用程式部署位置。

- 如需安裝與設定 Azure CLI 的相關說明，包括如何將 Azure CLI 連線至 Azure 訂用帳戶的資訊，請參閱[安裝與設定 Azure CLI](../xplat-cli.md)。

-  若要在 Azure CLI 中列出 Azure App Service 可用的命令，請呼叫 `azure site -h`。

----------
### azure site list
如需目前訂用帳戶中 Web 應用程式的相關資訊，請呼叫 **azure site list**，如下列範例所示。

`azure site list webappslotstest`

----------
### azure site create
若要建立部署位置，請呼叫 **azure site create** 並指定現有 Web 應用程式的名稱與要建立之位置的名稱，如下列範例所示。

`azure site create webappslotstest --slot staging`

若要對新位置啟用來源控制，請使用 **--git** 選項，如以下範例所示。

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
若要將已更新的部署位置轉變成生產應用程式，請使用 **azure site swap** 命令來執行交換作業，如下列範例所示。生產應用程式不會發生任何停機事件，也不會進行冷啟動。

`azure site swap webappslotstest`

----------
### azure site delete
若要刪除不再需要的部署位置，請使用 **azure site delete** 命令，如以下範例所示。

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 後續步驟 ##
[Azure App Service Web 應用程式 - 封鎖對非生產部署位置的 Web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 免費試用](/pricing/free-trial/)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=62-->