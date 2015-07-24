<properties 
	pageTitle="使用標記來組織您的 Azure 資源" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="tomfitz"/>


# 使用標記來組織您的 Azure 資源

Azure 入口網站和基礎資源管理員是用來組織您的資源及量身打造專屬於您的體驗。

在 Azure 傳統入口網站中，訂閱是分類及群組資源的唯一方式。在 Azure 入口網站中，[我們引進資源群組](./resource-group-portal.md)，可讓您將相關的實體群組在一起。當[我們引進角色型存取](./role-based-access-control-configure.md)時，就彰顯出它的價值。現在，本著相同的觀念，您可以使用索引鍵/值配對，在資源群組之間及入口網站內的訂閱之間，進一步分類及檢視資源。

依小組、專案、甚至是環境將資源分組，把重點放在您需要查看的確切項目上。


## Azure 入口網站中的標記

在入口網站中標記資源和資源群組很簡單。使用 [瀏覽中樞] 瀏覽到您想要標記的資源或資源群組，然後按一下分頁頂端的 [概觀] 區段中的 [標記] 組件。

![Tags part on resource and resource group blades](./media/resource-group-using-tags/rgblade.png)

這樣會開啟分頁，並列出一份已套用的標記。如果這是您的第一個標籤，則清單是空的。若要加入標記，只要指定名稱和值，然後按 Enter 即可。加入幾個標記之後，您會發現「自動完成」選項會根據既有的標記名稱和值，更加確保資源之間一致的分類法，並避免常見的錯誤，像是拼字錯誤。

![使用名稱/值組標記資源](./media/resource-group-using-tags/tag-resources.png)

從這裡，您可以按一下每個個別的標記，以檢視具有相同標記的所有資源的清單。當然，如果這是您的第一個標記，該清單會很單調。現在，我們跳到 PowerShell 來快速標記所有資源。


## 使用 PowerShell 來標記

首要之務是取得最新的 [Azure PowerShell 模組](./install-configure-powershell.md)。如果這是您第一次使用 Azure PowerShell 模組，[請閱讀說明文件](./install-configure-powershell.md)，以快速上手。基於本文的目的，我們假設您已經加入帳戶，並選取含有您想要標記之資源的訂閱。

標記只適用於[資源管理員](http://msdn.microsoft.com/library/azure/dn790568.aspx)所提供的資源和資源群組，因此，接下來我們需要切換到使用資源管理員。如需詳細資訊，請參閱[將 Azure PowerShell 與 Azure 資源管理員搭配使用](powershell-azure-resource-manager.md)。

    Switch-AzureMode AzureResourceManager

標記直接存在於資源與資源群組上，若要查看已經套用哪些標記，只要分別使用 `Get-AzureResource` 或 `Get-AzureResourceGroup`，就能取得資源或資源群組。我們從資源群組開始。

![在 PowerShell 中使用 Get-AzureResourceGroup 取得標記](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

此 Cmdlet 會傳回資源群組上的幾個位元的中繼資料，包括已套用哪些標記 (若有的話)。若要標記資源群組，我們只需要使用 `Set-AzureResourceGroup`，並指定標記名稱和值。

![在 PowerShell 中使用 Set-AzureResourceGroup 設定標記](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

請記住，標記是整體更新，因此，如果您要將一個標記加入至已標記的資源，則必須使用陣列與您想要保留的所有標記。若要移除一個標記，只要儲存不含您要移除的標記的陣列即可。

對於資源，處理方式相同，差別在於使用 `Get-AzureResource` 和 `Set-AzureResource` Cmdlet。若要取得含有特定標記的資源或資源群組，請使用 `Get-AzureResource` 或 `Get-AzureResourceGroup` Cmdlet 並指定 `-Tag` 參數。

![在 PowerShell 中使用 Get-AzureResource 和 Get-AzureResourceGroup 取得標記的資源和資源群組](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## 使用資源管理員來標記

入口網站和 PowerShell 在幕後都使用[資源管理員 REST API](http://msdn.microsoft.com/library/azure/dn790568.aspx)。如果您需要將標記整合到另一個環境中，您可以在資源識別碼上利用 GET 來取得標記，並使用 PATCH 呼叫來更新一組標記。


## 管理您的分類法

稍早，我們談到自動完成功能如何協助您確保一致性和避免錯誤。自動完成是根據訂閱所設定的可用標記的分類法來填入。您加入至資源或資源群組的每個標記，都會自動加入至整體訂閱的分類法，但您也可以在分類法中，預先填入您在未來標記資源時想要使用的標記名稱和值。

若要使用 PowerShell 取得訂閱內所有標記的清單，請使用 `Get-AzureTag` Cmdlet。

![PowerShell 中的 Get-AzureTag](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


您可能會看到開頭為 "hidden-" 和 "link:" 的標記。這些是內部標記，應該略過且避免變更。

使用 `New-AzureTag` Cmdlet 將新的標記加入分類法。這些標記會加入到自動完成中，即使它們尚未套用至任何資源或資源群組也一樣。若要移除標記名稱/值，請先從任何可能用到這個標記的資源中移除標記，再使用 `Remove-AzureTag` Cmdlet 從分類法中移除它。

若要在入口網站中檢視標記的分類法，請使用 [瀏覽中樞] 來檢視 [全部內容]，然後選取 [標記]。

![透過 [瀏覽中樞] 尋找標記](./media/resource-group-using-tags/browse-tags.png)

將最重要的標記釘選到「開始面板」以快速存取，到此全部完成。請享用！

![將標記釘選到「開始面板」](./media/resource-group-using-tags/pin-tags.png)

## 標記和計費

對於支援的服務，您可以使用標記來分組您的計費資料。例如，[和 Azure 資源管理員整合的虛擬機器](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md)可讓您定義並套用標記，以組織虛擬機器的計費使用情況。如果您執行不同組織的多個 VM，您可以使用標記根據成本中心來分組使用情況。您也可以使用標記來根據執行階段環境分類成本。例如，在生產環境中執行之 VM 的計費使用情況。

您可以透過[使用情況 api](billing-usage-rate-card-overview.md) 或使用情況以逗號分隔值 (CSV) 檔案 (您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com)下載)，擷取關於標記的資訊。

當您下載服務 (支援附計費的標記) 的使用情況 CSV 時，標記會出現在 [**標記**] 資料行。若需更多詳細資料，請參閱[了解 Microsoft Azure 的計費](billing-understand-your-bill.md)。

![查看計費中的標記](./media/resource-group-using-tags/billing_csv.png)

## 後續步驟
開始使用

- [Azure 資源管理員概觀](./resource-group-overview.md)  
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](./powershell-azure-resource-manager.md)
- [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](./xplat-cli-azure-resource-manager.md)  
- [使用 Azure 入口網站管理 Azure 資源](./resource-group-portal.md)  
  
建立和部署應用程式
  
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)  
- [使用 Azure 資源管理員範本部署應用程式](./resource-group-template-deploy.md)  
- [Azure 中的資源群組部署疑難排解](./resource-group-deploy-debug.md)  
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)  
- [進階範本作業](./resource-group-advanced-template.md)  
  
管理和稽核存取權
  
- [管理和稽核資源存取權](./resource-group-rbac.md)  
- [使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)  
- [使用 Azure 傳統入口網站建立新的 Azure 服務主體](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=July15_HO1-->