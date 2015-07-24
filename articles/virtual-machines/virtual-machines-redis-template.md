<properties
	pageTitle="Redis 叢集資源管理員範本 "
	description="了解如何使用 Azure PowerShell 或 Azure CLI 搭配 Azure Resource Manager 範本，在 Ubuntu VM 上輕鬆部署新的 Redis 叢集"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# 使用資源管理員範本的 Redis 叢集

Redis 是開放原始碼的索引鍵值快取和存放區，其中的索引鍵可以包含類似字串、雜湊、清單、集合和排序的集合等資料結構。Redis 針對這些資料類型支援一組不可部分完成的操作。隨著 Redis 版本 3.0 的發行，Redis 叢集現在可以在穩定的最新 Redis 版本中使用。Redis 叢集是 Redis 的分散式實作，其中的資料會自動分區到多個 Redis 節點，能夠在節點子集發生故障時繼續運作。

Microsoft Azure Redis 快取是專用的 Redis 快取服務，由 Microsoft 所管理，但並非所有的 Microsoft Azure 客戶都想要使用 Azure Redis 快取。有些客戶想要在他們自己的 Azure 部署內將 Redis 快取保留於子網路後面，其他客戶則是更滿意在 Linux 虛擬機器上裝載他們自己的 Redis 伺服器，以便充分利用所有 Redis 功能。

這個教學課程將逐步引導您使用 Azure 資源管理員範本範例，在 Microsoft Azure 的資源群組子網路中，於 Ubuntu VM 上部署 Redis 叢集。除了 Redis 3.0 叢集，這個範本還支援具備 Redis Sentinel 的 Redis 2.8。請注意，這個教學課程將著重在 Redis 3.0 叢集實作。

Redis 叢集是建立在子網路後面，因此，不會有可存取 Redis 叢集的公開 IP 位址。您可以在部署期間，選擇部署「跳躍箱」。這個「跳躍箱」也是部署於子網路中的 Ubuntu VM，但是它*會*使用您可以 SSH 的公開 SSH 連接埠來公開公用 IP 位址。接著，從「跳躍箱」，您可以 SSH 到子網路中的所有 Redis VM。

這個範本會利用「T 恤尺寸」概念，來說明「小型」、「中型」或「大型」Redis 叢集設定。當 Azure 資源管理員範本語言支援多個動態範本大小設定時，這可以變更來指定 Redis 叢集主要節點的數目、從屬節點的數目、VM 大小等項目。從現在開始，您可以查看 azuredeploy.json 檔案中，以變數 `tshirtSizeSmall`、`tshirtSizeMedium` 以及 `tshirtSizeLarge` 定義的 VM 大小與主要和從屬節點的數目。

適用於「中型」T 恤尺寸的 Redis 叢集範本會建立下列設定：

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

在深入了解與 Azure 資源管理員和將針對此次部署使用之範本的詳細資訊之前，請確定您已正確設定 Azure PowerShell 或 Azure CLI。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 使用資源管理員範本部署 Redis 叢集

請依照下列步驟，使用 Github 範本儲存機制中的資源管理員範本來建立 Redis 叢集。每個步驟都將包含適用於 Azure PowerShell 和 Azure CLI 的指引。

### 步驟 1-a：使用 Azure PowerShell 下載範本檔案

為 JSON 範本和其他相關聯的檔案建立本機資料夾 (例如，C:\\Azure\\Templates\\RedisCluster)。

使用您本機資料夾的資料夾名稱來替代，並執行下列命令：

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### 步驟 1-b：使用 Azure CLI 下載範本檔案

使用您選擇的 Git 用戶端，來複製整個範本儲存機制，例如：

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

複製完成時，尋找 C:\\Azure\\Templates 目錄中的 **redis-high-availability** 資料夾。

### 步驟 2：(選用) 了解範本參數

當您使用範本建立 Redis 叢集時，請務必指定一組設定參數。在執行命令以建立 Redis 叢集之前，若要查看您需要為本機 JSON 檔案中的範本指定哪些參數，請利用自選的工具或文字編輯器開啟 JSON 檔案。

尋找檔案頂端的 "parameters" 區段，其中會列出設定 Redis 叢集時範本所需的參數組。以下是 azuredeploy.json 範本的 "parameters" 區段：

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "redisSubnet1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

每個參數都具有資料類型和允許值之類的詳細資料。這允許驗證在互動模式 (例如 Azure PowerShell 或 Azure CLI)，及自我探索 UI (透過剖析必要參數清單及其描述並動態建置的 UI) 的範本執行期間所傳遞的參數。

### 步驟 3-a：透過 Azure PowerShell 使用範本部署 Redis 叢集

藉由建立 JSON 檔案 (其中包含適用於所有參數的執行階段值)，來為您的部署準備參數檔案。接著將此檔案當成單一實體傳遞給部署命令。如果未包含參數檔案，Azure PowerShell 將使用範本中指定的任何預設值，然後提示您填寫剩餘的值。

以下是您可以在 azuredeploy-parameters.json 檔案中找到的範例：請注意，您需要為參數 `storageAccountName`、`adminUsername` 及 `adminPassword` 提供有效值，以及為其他參數提供任何自訂：

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
	},
	"subnetName": {
		"value": "Subnet1"
	},
	"addressPrefix": {
		"value": "10.0.0.0/16"
	},
	"subnetPrefix": {
		"value": "10.0.0.0/24"
	},
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]參數 `storageAccountName` 必須是全新的唯一儲存體帳戶名稱，並滿足 Microsoft Azure 儲存體帳戶的命名需求 (僅限小寫字母和數字)。這個儲存體帳戶將建立來做為部署程序的一部分。

填寫 Azure 部署名稱、資源群組名稱、Azure 位置，以及儲存 JSON 檔案的資料夾。然後執行以下命令：

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` 在您的訂用帳戶內必須是唯一的。

執行 **New-AzureResourceGroupDeployment** 命令時，這將會從參數 JSON 檔案 (azuredeploy-parameters.json) 中擷取參數值，然後據以開始執行範本。定義以及使用不同環境 (測試、實際執行等) 所需的參數檔案，有利範本的重複使用以及簡化複雜的多重環境解決方案。

部署時，請記得需要建立新的 Azure 儲存體帳戶，所以您提供來做為儲存體帳戶參數的名稱必須是唯一且符合 Azure 儲存體帳戶的所有需求 (僅限小寫字母和數字)。

您將會在部署期間看見如下的內容：

	PS C:> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

部署期間以及部署結束之後，您可以檢查所有佈建期間所進行的要求，包括任何發生的錯誤。

若要這樣做，請移至 [Azure 入口網站](https://portal.azure.com)，然後執行下列動作：

- 請按一下左側導覽列上的 [瀏覽]，然後向下捲動並按一下 [資源群組]。
- 選取剛建立的資源群組，會顯示 [資源群組] 刀鋒視窗。
- 在 [監視] 區段中，選取 [事件] 橫條圖。這樣將會顯示適用於您部署的事件。
- 按一下個別事件，您可以進一步查看代表範本所做的每個作業之詳細資料。

如果您需要在測試之後移除這個資源群組及其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用下列命令：

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### 步驟 3-b：透過 Azure CLI 使用範本部署 Redis 叢集

若要透過 Azure CLI 部署 Redis 叢集，請先指定名稱和位置來建立資源群組：

```powershell
azure group create TestRG "West US"
```

將此資源群組名稱、JSON 範本檔案的位置，以及參數檔案的位置 (如需詳細資訊，請參閱上方的 Azure PowerShell 章節) 傳遞給下列命令：

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

您可以使用下列命令來檢查個別資源部署的狀態：

```powershell
azure group deployment list TestRG
```

## Redis 叢集範本結構和檔案組織的導覽

為了針對資源管理員範本的設計建立更完善且可重複使用的方法，您必須在部署 Redis 叢集之類的複雜解決方案時，考慮清楚如何安排一連串複雜但又彼此相關的工作。利用資源管理員的範本連結功能、資源迴圈，以及透過相關延伸模組執行指令碼，這樣就能實作模組化方法，而實際上所有以複雜範本為基礎的部署都能重複使用此方法。

下圖描述此次部署時，從 GitHub 下載的所有檔案，其彼此之間的關係：

![redis-files](media/virtual-machines-redis-template/redis-files.png)

本節將帶領您逐步了解 Redis 叢集的 azuredeploy.json 範本結構。

如果您尚未下載範本檔案的複本，請指定本機資料夾做為該檔案的位置並建立它 (例如，C:\\Azure\\Templates\\RedisCluster)。填寫資料夾名稱，然後執行以下命令：

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

在您選擇的文字編輯器或工具中開啟 azuredeploy.json 範本。以下資訊將說明範本檔案的結構和每個區段的用途。或者，您可以從[此處](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json)，在瀏覽器中查看這個範本的內容。

### "parameters" 區段

我們已經提過 azuredeploy-parameters.json 的角色，這會在範本執行期間用來傳遞一組指定的參數值。azuredeploy.json 的 "parameters" 區段會指定用來將資料輸入此範本的參數。

以下是適用於「T 恤尺寸」的參數範例：

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]請注意，您可以指定 `defaultValue` 以及 `allowedValues`。

### "variables" 區段

"variables" 區段會指定一些變數，這些參數會全程用在這個範本中。基本上，這包含數個欄位 (JSON 資料類型或片段)，它們將會在執行階段設定為常數或計算值。以下是一些範圍從簡單到更複雜的範例：

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

`vmStorageAccountContainerName` 和 `vmStorageAccountDomain` 變數是簡單名稱/值變數的範例。`vnetID` 是在執行階段使用函式 `resourceId` 和 `parameters` 來計算的變數範例。`machineSettings` 會藉由在 `machineSettings` 變數中巢串 JSON 物件 `osImageReference`，基於這些概念更進一步建置的。`vmScripts` 包含 JSON 陣列 `scriptsToDownload`，其是在執行階段使用 `concat` 和 `variables` 函式計算而得的。

如果您想要自訂 Redis 叢集部署的大小，則可在 azuredeploy.json 範本中變更變數 `tshirtSizeSmall`、`tshirtSizeMedium` 及 `tshirtSizeLarge` 的屬性。

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

請注意：`totalMemberCountExcludingLast` 和 `totalMemberCount` 屬性是必要的，因為範本語言目前不含「數學」運算。

如需關於範本語言的詳細資訊，請參閱 MSDN 中的 [Azure 資源管理員範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。

### "resources" 區段

絕大多數的動作是在 "resources" 區段進行。請仔細查看這個區段，您會立即找出兩個不同的案例。第一個是定義為 `Microsoft.Resources/deployments` 類型的元素，基本上它會叫用主要檔案內的巢狀部署。第二個是 `templateLink` 屬性 (和相關的 `contentVersion` 屬性)，能夠指定要叫用的連結範本檔案，傳遞一組參數做為輸入。您可以下列範本片段中查看這些內容：

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

在第一個範例中，我們很清楚地知道此案例中的 azuredeploy.json 是用來做為一種協調流程機制，負責叫用一些其他範本檔案。每個檔案會負責需要部署活動的一部分。

特別是，下列連結的範本將用於此部署：

- **shared-resource.json**：包含所有要在整個部署中共用的資源定義。例如，用來儲存 VM 的 OS 磁碟、虛擬網路及可用性設定組的儲存體帳戶。
- **jumpbox-resources.json**：部署「跳躍箱」 VM 及所有相關資源，例如，網路介面、公用 IP 位址，以及用來 SSH 到環境的輸入端點。
- **node-resources.json**：部署所有 Redis 叢集節點 VM 和連接的資源 (網路介面卡、私人 IP 等)。此範本也會部署 VM 延伸模組 (適用於 Linux 的自訂指令碼)，並叫用 bash 指令碼，在每一個節點上實際安裝並設定 Redis。要叫用的指令碼會以 `commandToExecute` 屬性的 `machineSettings` 參數傳遞給這個範本。所有的 Redis 叢集節點都可平行部署和執行指令碼，但有一個節點例外。有一個節點在結束之前都需要加以儲存，因為 Redis 叢集設定只能在一個節點上執行，而它必須在所有節點都執行 Redis 伺服器之後完成：這就是為什麼要執行的指令碼會傳遞給此範本的原因；最後一個節點需要執行些微不同的指令碼，該指令碼不只會安裝 Redis 伺服器，而且還會設定 Redis 叢集。

讓我們深入了解最後一個範本 node-resources.json 的*使用方式*，因為從範本開發角度來看，這是最有趣的範本之一。要強調一個重要概念是單一範本檔案如何能夠部署單一資源類型的多個複本，而且每一個執行個體都能為必要設定指定唯一值。這個概念稱為**資源迴圈**。

從主要 azuredeploy.json 檔案叫用 node-resources.json 時，會從使用 `copy` 元素來建立排序迴圈的資源內叫用它。使用 `copy` 元素的資源會以 `copy` 元素的 `count` 參數中指定的次數來「複製」它自己。對於您需要在不同的部署資源執行個體之間指定唯一值的所有設定，可使用 **copyindex()** 函式來取得數值，以指出目前用來建立這個特定資源迴圈的索引。在下列來自 azuredeploy.json 的片段中，您可以在為 Redis 叢集節點建立的多個 VM 上看見這個概念的運用：

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

建立資源的另一個重要概念是指定資源之間的相依性和優先順序，如同您在 `dependsOn` JSON 陣列中所見。在這個特殊範本中，您可以看見 Redis 叢集節點相依於先建立的共用資源。

如先前所提及，最後一個節點需要等待佈建，直到 Redis 叢集中的所有其他節點都已使用在其上執行的 Redis 伺服器進行佈建為止。這現在可以在 azuredeploy.json 中完成，做法是從上述的範本程式碼片段中，擁有相依於 `copy` 迴圈 (名稱為 `memberNodesLoop`) 且名為 `lastnode-resources` 的資源。在 `memberNodesLoop` 的佈建完成後，可以佈建 `lastnode-resources`：

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

請注意，`lastnode-resources` 資源如何將些微不同的 `machineSettings.commandToExecute` 傳遞到連結的範本。這是因為最後一個節點，除了已安裝的 Redis 伺服器，還需要呼叫指令碼來設定 Redis 叢集 (這只有在所有 Redis 伺服器都已啟動且執行之後才能完成)。

另一個要探索的有趣片段是與 `CustomScriptForLinux` VM 延伸模組相關的片段。這些片段都是以獨立資源類型進行安裝，而且每個叢集節點上都具有相依性。在此情況下，這可用來在每個 VM 節點上安裝並設定 Redis。讓我們查看來自 node-resources.json 範本的程式碼片段，其中會使用下列程式碼：

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

您可以看見這個資源相依於已經部署的資源 VM (`Microsoft.Compute/virtualMachines/vmMember<X>`，`<X>` 是參數 `machineSettings.machineIndex`，其為 VM 的索引，並使用 **copyindex()** 函式傳遞到此指令碼)。

讓您自己熟悉此部署內所含的其他檔案，就能了解如何利用 Azure 資源管理員範本，根據任何技術來組織和協調適用於多節點解決方案的複雜部署策略所需的所有詳細資料和最佳做法。在此建議一種範本檔案建構方法，請自行決定是否採用，如下圖重點標示的部分：

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

基本上，這種方法會建議：

- 將您的核心範本檔案定義成所有特定部署活動的協調中心，利用範本連結功能來叫用子範本的執行。
- 建立特定的範本檔案，將部署可在所有其他特定部署工作上共用的所有資源 (儲存體帳戶、虛擬網路設定等)。如果不同的部署之間，其一般基礎結構的需求皆類似，就可以高度重複使用。
- 包含選用資源範本，適用於指定資源特定的場地需求。
- 針對資源群組中的成員相同時 (叢集中的節點等等)，可以建立會利用資源迴圈功能的特定範本，以便部署多個具有唯一屬性的執行個體。
- 所有的後續部署工作 (產品安裝、設定等) 都會利用指令碼部署擴充功能以及建立每一種技術獨有的指令碼。

如需詳細資訊，請參閱 [Azure Resource Manager 範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。

<!---HONumber=July15_HO2-->