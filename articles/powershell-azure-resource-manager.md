<properties 
	pageTitle="搭配使用 Azure PowerShell 與 Azure 資源管理員" 
	description="使用 Azure PowerShell 將多個資源做為資源群組部署至 Azure。" 
	services="" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>

# 搭配使用 Azure PowerShell 與 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure 資源管理員介紹一種看待 Azure 資源的嶄新方式。與其建立並管理個別資源，您首先想像一個複雜的服務，例如，部落格、相片庫、SharePoint 入口網站或 Wiki。使用範本 (服務的資源模型) 建立包含支援此服務所需資源的資源群組。然後，以邏輯單元的方式來管理與部署該資源群組。

在本教學課程中，您將了解如何搭配使用 Azure PowerShell 與 Microsoft Azure 的 Azure 資源管理員。它會逐步引導您為搭配 SQL 資料庫的 Azure 託管 web 應用程式，完成建立與部署資源群組的程序，並提供支援此群組所需的所有資源。

## 必要條件

若要完成此教學課程，您必須具備 Azure PowerShell 0.8.0 版或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

本教學課程是專為 PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) (英文)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

	Get-Help <cmdlet-name> -Detailed

例如，如需取得 Add-AzureAccount Cmdlet 的說明，請輸入：

	Get-Help Add-AzureAccount -Detailed

## 關於 Azure Powershell 模組
自 0.8.0 版開始，Azure PowerShell 安裝包括多個 PowerShell 模組。您必須明確決定是否要使用 Azure 模組或 Azure 資源管理員模組中可用的命令。為了要在兩者間輕易切換，我們已將新的 Cmdlet (**Switch-AzureMode**) 新增至 Azure 設定檔模組。

當您使用 Azure PowerShell 時，系統預設會匯入 Azure 模組中的 Cmdlet。若要切換至 Azure 資源管理員模組，請使用 Switch-AzureMode Cmdlet。它會從您的工作階段中移除 Azure 模組，並匯入 Azure 資源管理員和 Azure 設定檔模組。

若要切換至 AzureResoureManager 模組，請輸入：

    PS C:\> Switch-AzureMode -Name AzureResourceManager

若要切回至 Azure 模組，請輸入：

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

依預設，Switch-AzureMode 只會影響目前的工作階段。若要讓切換在所有的 PowerShell 工作階段中生效，請使用 Switch-AzureMode 的 **Global** 參數。

如需 Switch-AzureMode Cmdlet 的說明，請輸入：`Get-Help Switch-AzureMode` 或參閱 [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398)。
  
若要取得 AzureResourceManager 模組中的 Cmdlet 清單及說明概要，請輸入：

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

輸出類似如下摘錄：

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

To get full help for a cmdlet, type a command with the format:

	Get-Help <cmdlet-name> -Full

例如，

	Get-Help Get-AzureLocation -Full

若需完整的 Azure 資源管理員命令集合，請參閱 [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765)。
  
## 建立資源群組

本教學課程的這個部分會逐步引導您為搭配 SQL 資料庫的 web 應用程式，建立與部署資源群組的程序。

您不需要是 Azure、SQL、web 應用程式或資源管理等方面的專家就可以進行此工作。範本提供了資源群組的模型，其中包括您可能需要的所有資源。並因為使用 Windows PowerShell 來自動化工作，您可以使用這些程序作為撰寫大規模工作指令碼的模型。

### 步驟 1：切換至 Azure 資源管理員 
1. 啟動 PowerShell。您可以任意使用任何主機程式，例如，Azure PowerShell 主控台或 Windows PowerShell ISE。

2. 使用 **Switch-AzureMode** Cmdlet 來匯入 AzureResourceManager 和 AzureProfile 模組中的 Cmdlet。

        PS C:\> Switch-AzureMode AzureResourceManager

3. 若要將您的 Azure 帳戶新增至 Windows PowerShell 工作階段，請使用 **Add-AzureAccount** Cmdlet。

        PS C:\> Add-AzureAccount

cmdlet 會提示您 Azure 帳戶的登入認證。登入之後，它會下載您的帳戶設定以供 Windows PowerShell 使用。

帳戶設定已過期，因此您必須偶爾重新整理這些設定。若要重新整理帳戶設定，請再次執行 **Add-AzureAccount**。

>[AZURE.NOTE]AzureResourceManager 模組需要 Add-AzureAccount。發佈設定檔案不符合需求。

### 步驟 2：選取資源庫範本

建立資源群組及其資源的方式有幾種，但最簡單的方式是使用資源群組範本。*resource group template* 是可定義資源群組中資源的 JSON 字串。此字串包含使用者定義值的預留位置 (稱為 "parameters")，例如名稱和大小。

Azure 主控一個資源群組範本的資源庫，您也可以從頭開始或透過編輯資源庫範本以建立自己的範本。在本教學課程中，我們將使用資源庫範本。

若要查看 Azure 資源群組範本資源庫中的所有範本，請使用 **Get-AzureResourceGroupGalleryTemplate** cmdlet；不過，這個命令會傳回大量範本。若要查看更容易管理的範本數目，請指定發佈者參數。

出現 PowerShell 提示時，請輸入：
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

此 Cmdlet 會傳回包含 Microsoft 的資源庫範本清單做為發佈者。您可以使用 **Identity** 屬性來識別命令中的範本。

Microsoft.WebSiteSQLDatabase.0.2.6-preview 範本看起來很有意思。當您執行命令時，範本的版本可能稍有不同，因為已發行新的版本。使用最新版本的範本。若要取得資源庫範本的詳細資訊，請使用 **Identity** 參數。Identity 參數的值是範本的身分識別。

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

此 Cmdlet 會傳回包含此範本更多詳細資訊 (包括摘要和說明) 的物件。

此範本看起來可以滿足我們的需求。我們可以先將它儲存至磁碟，並仔細檢視。

### 步驟 3：檢查範本

我們可以將範本儲存至磁碟上的 JSON 檔案。這不是必要步驟，但它可讓檢視範本變得更加容易。若要儲存範本，請使用 **Save-AzureResourceGroupGalleryTemplate** Cmdlet。使用其 **Identity** 參數來指定範本，並使用 **Path** 參數來指定磁碟上的路徑。

Save-AzureResourceGroupGalleryTemplate 會儲存範本，並傳回 JSON 範本檔案的檔案名稱路徑。

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


您可以在文字編輯器 (例如記事本) 中檢視範本檔案。每個範本包含**參數**區段和**資源**區段。

範本的 [參數] 區段是在所有資源中定義的參數集合。它包含您可以在設定資源群組時提供的屬性值。

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

部分參數已有預設值。當您使用範本時，您無需提供這些參數的值。如果您沒有指定值，系統便會使用預設值。

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

當參數具有列舉值時，有效值會與參數一起列出。例如，**sku** 參數可接受 [免費]、[共用]、[基本] 和 [標準] 等值。如果您沒有為 **sku** 參數指定值，則它會使用預設值 ([免費])。

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


請注意，**administratorLoginPassword** 參數會使用安全字串，而非純文字。當您提供安全字串的值時，此值便會被遮住。

	"administratorLoginPassword": {
      "type": "securestring"
    },

範本的 **resources** 區段會列出該範本建立的資源。此範本會建立 SQL 資料庫伺服器和 SQL 資料庫、伺服器陣列和網站，以及數個管理設定。
  
每個資源定義會包括其屬性 (例如，名稱、類型和位置)，以及使用者定義值的參數。例如，此範本區段定義 SQL 資料庫。它包括資料庫名稱 ([parameters('databaseName')])、資料庫伺服器位置 [parameters('serverLocation')] 和定序屬性 [parameters('collation')] 等參數。

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


我們大致上準備好開始使用範本，但在使用範本之前，我們必須找到各個資源的位置。

### 步驟 4：取得資源類型位置

大多數的範本會要求您指定資源群組中各個資源的位置。每個資源都位於 Azure 資料中心內，但不是每個 Azure 資料中心都支援每種資源類型。

選取支援資源類型的任何位置。您不需要在相同位置的資源群組中建立所有資源；不過，您會想要在想同的位置盡可能建立資源以最佳化效能。尤其，您會想要確定您的資料庫與存取資料庫的應用程式位於相同的位置。

若要取得支援各種資源類型的位置，請使用 **Get-AzureLocation** Cmdlet。若要將您的輸出限制為特定類型的資源，例如 ResourceGroup，請使用：

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

輸出將類似：

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

現在我們已經有了建立資源群組所需的資訊。

### 步驟 5：建立資源群組
 
在此步驟中，我們將使用群組範本建立資源群組。如需參考，請開啟磁碟上的 New_WebSite_And_Database.json 檔案，並依照指示進行。範本檔案可以非常有助於決定要傳遞的參數值，例如資源的正確 ApiVersion。

若要建立資源群組，請使用 **New-AzureResourceGroup** Cmdlet。

此命令會使用 **Name** 參數來指定資源群組的名稱，並使用 **Location** 參數來指定其位置。使用 **Get-AzureLocation** 的輸出來選取資源群組的位置。它會使用 **GalleryTemplateIdentity** 參數來指定資源庫範本。

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

在您輸入範本名稱之後，New-AzureResourceGroup 便會立即擷取範本、剖析範本，並將範本參數動態新增至命令。這會讓指定範本參數值變得再容易不過了。另外，如果您忘記必要參數值，則 Windows PowerShell 會出現此值的提示。

**動態範本參數**

若要取得參數，請輸入減號 (-) 以顯示參數名稱，然後按 TAB 鍵。或者，輸入參數名稱的前幾個字母，例如 siteName，然後按 TAB 鍵。

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell 會完成參數名稱。若要循環顯示參數名稱，請重複按 TAB 鍵。

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

輸入網站名稱，並針對每個參數重複 TAB 程序。具有預設值的參數為選擇性參數。若要接受預設值，請略過來自命令的參數。

當範本參數具有列舉值時 (例如本範本中的 sku 參數)，若要循環顯示參數值，請按 TAB 鍵。

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

以下是 New-AzureResourceGroup 命令的範例，將只指定必要範本參數和 **Verbose** 命令參數。請注意，將略過 **administratorLoginPassword**。

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

當您輸入命令時，系統會提示您輸入遺漏的必要參數 **administratorLoginPassword**。並在您輸入密碼時，此安全字串值便會被遮住。如此，就不會有以純文字方式提供密碼的風險。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-azureresourcegroup** 會傳回它建立和部署的資源群組。

只需幾個步驟，即可建立與部署複雜網站所需的資源。資源庫範本提供了執行此工作所需的幾乎所有資訊。另外，還可輕易自動化此工作。

## 取得資源群組的相關資訊

建立資源群組之後，您可以使用 AzureResourceManager 模組中的 Cmdlet，來管理您的資源群組。

- 若要取得訂用帳戶中的所有資源群組，請使用 **Get-AzureResourceGroup** Cmdlet：

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- 若要取得資源群組中的資源，請使用 **Get-AzureResource** Cmdlet 及其 ResourceGroupName 參數。在沒有使用參數的情況下，Get-AzureResource 會取得 Azure 訂閱中的所有資源。

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## 新增至資源群組

- 若要將資源新增至資源群組，請使用 **New-AzureResource** Cmdlet。此命令會將新網站新增至 TestRG 資源群組。由於此命令沒有使用範本，所以稍微有點複雜。 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- 若要將新的範本型部署新增至資源群組，請使用 **New-AzureResourceGroupDeployment** 命令。

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## 移動資源

- 若要將現有資源移至資源群組，請使用 **Move-AzureResource** 命令。

		PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

## 刪除資源群組

- 若要將資源從資源群組中刪除，請使用 **Remove-AzureResource** Cmdlet。此 Cmdlet 會刪除資源，但不會刪除資源群組。

	此命令會將 TestSite2 網站從 TestRG 資源群組中移除。

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- 若要刪除資源群組，請使用 **Remove-AzureResourceGroup** Cmdlet。此 Cmdlet 會刪除資源群組及其資源。

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## 疑難排解資源群組
在嘗試使用 AzureResourceManager 模組中的 Cmdlet 後，您很有可能會遇到錯誤。請使用本節中的秘訣來解決這些錯誤。

### 防止錯誤

AzureResourceManager 模組包含可協助您防止錯誤的 Cmdlet。


- **Get-AzureLocation**：此 Cmdlet 會取得支援每種資源類型的位置。在您輸入資源的位置之前，請使用此 Cmdlet 來確認該位置是否支援此資源類型。


- **Test-AzureResourceGroupTemplate**：使用範本和範本參數之前，請先進行測試。請輸入自訂或資源庫範本，以及您打算使用的範本參數值。此 Cmdlet 會測試範本是否達到內部的一致性，以及您的參數值設定是否與範本相符。



### 修正錯誤

- **Get-AzureResourceGroupLog**：此 Cmdlet 會取得每個資源群組部署記錄中的項目。如果出現問題，便會首先檢查部署記錄。 

- **詳細資訊和偵錯**：AzureResourceManager 模組中的 Cmdlet 會呼叫實際執行工作的 REST API。若要查看 API 傳回的訊息，請將 $DebugPreference 變數設為 "Continue"，並在您的命令中使用 Verbose 命令參數。這些訊息通常會提供任何失敗原因的重要線索。

- **您的 Azure 認證未設定或已過期**：若要在 Windows PowerShell 工作階段中重新整理認證，請使用 Add-AzureAccount Cmdlet。發佈設定檔中的認證無法滿足 AzureResourceManager 模組中 Cmdlet 的需求。


## 後續步驟
開始使用

- [Azure 資源管理員概觀](./resource-group-overview.md)
- [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](./xplat-cli-azure-resource-manager.md)
- [使用 Azure 入口網站管理 Azure 資源](./resource-group-portal.md)

建立和部署應用程式

- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)
- [使用 Azure 資源管理員範本部署應用程式](./resource-group-template-deploy.md)
- [透過可預測方式在 Azure 中部署複雜應用程式](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Azure 中的資源群組部署疑難排解](./resource-group-deploy-debug.md)
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)
- [進階範本作業](./resource-group-advanced-template.md)

組織資源

- [使用標記來組織您的 Azure 資源](./resource-group-using-tags.md)

管理和稽核存取權

- [管理和稽核資源存取權](./resource-group-rbac.md)
- [使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)
- [使用 Azure 傳統入口網站建立新的 Azure 服務主體](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO1-->