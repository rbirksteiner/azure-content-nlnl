<properties
	pageTitle="Ubuntu 資源管理員範本上的 Spark"
	description="了解如何使用 Azure PowerShell 或 Azure CLI 搭配 Azure 資源管理員範本，在 Ubuntu VM 上輕鬆部署新的 Spark 叢集"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# 在 Ubuntu 上使用資源管理員範本的 Spark

Apache Spark 是進行大規模資料處理的快速引擎。Spark 有支援循環資料流程和記憶體內部計算的進階 DAG 執行引擎，而且它可以存取不同的資料來源，包括 HDFS、Spark、HBase 和 S3。

除了在 Mesos 或 YARN 叢集管理員上執行，Spark 也提供簡單的獨立部署模式。這個教學課程將逐步引導您使用範例 Azure 資源管理員 (ARM) 範本，以透過 [Azure PowerShell](../powershell-install-configure.md) 或 [Azure CLI](../xplat-cli.md) 在 Ubuntu VM 上部署 Spark 叢集。

這個範本會在 Ubuntu 虛擬機器上部署 Spark 叢集。這個範本也會佈建安裝所需的儲存體帳戶、虛擬網路、可用性集、公用 IP 位址和網路介面。Spark 叢集是建立在子網路後面，因此，不會有公用 IP 可存取叢集。您可以在部署期間，選擇部署「跳躍箱」。這個「跳躍箱」也是部署於子網路中的 Ubuntu VM，但是它*會*使用您可以連節的公開 SSH 連接埠來公開公用 IP 位址。接著，從「跳躍箱」，您可以 SSH 到子網路中的所有 Spark VM。

這個範本會利用「T 恤尺寸」概念，來指定「小型」、「中型」或「大型」Spark 叢集設定。當範本語言支援多個動態範本大小設定時，這可以變更來指定 Spark 叢集主要節點和從屬節點的數目、VM 大小等項目。從現在開始，您可以查看檔案 azuredeploy.json 中，以變數 tshirtSizeS、tshirtSizeM 和 tshirtSizeL 定義的 VM 大小與主要和從屬的數目：

- S：1 主要，1 從屬
- M：1 主要，4 從屬
- L：1 主要，6 從屬

根據這個範本部署的新叢集會採用下圖中所述的拓撲，不過，您可以自訂本文中所述的範本，輕鬆實現其他拓撲：

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

如上圖所示，部署拓樸由下列項目組成：

-	新的儲存體帳戶，裝載新建立虛擬機器的作業系統磁碟。
-	具有子網路的虛擬網路。由此範本所建立的所有虛擬機器都佈建在此虛擬網路內。
-	主要和從屬節點的可用性集。
-	新建立可用性集中的主要節點。
-	四個從屬節點，在與主要節點相同的虛擬子網路和可用性集中執行。
-	位於相同虛擬網路和子網路的跳躍箱 VM，可以用來存取叢集。

Spark 3.0.0 版為預設版本，而且可以變更為 Spark 儲存機制上可用的任何預先建置之二進位檔。另外還有指令碼中的佈建，可從來源取消註解組建。靜態 IP 位址會指派給每個 Spark 主要節點 10.0.0.10 靜態 IP 位址會指派給每個 Spark 從屬節點，以解決目前無法以動態方式從範本內部撰寫一份 IP 位址清單的限制 (根據預設，第一個節點將會指派私用 IP 10.0.0.30，第二個節點指派 - 10.0.0.31，依此類推)。若要檢查部署錯誤，請移至新的 Azure 入口網站，然後查看 **資源群組** > **最後部署** > **檢查作業詳細資料**。

在深入了解與 Azure 資源管理員和將針對此次部署使用之範本的詳細資訊之前，請確定您已正確設定 Azure PowerShell 或 Azure CLI。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 使用資源管理員範本建立 Spark 叢集

請遵循下列步驟，使用來自 Github 範本儲存機制 (使用 Azure PowerShell 或 Azure CLI) 的資源管理員範本建立 Spark 叢集。

### 步驟 1-a：使用 PowerShell 下載範本檔案

為 JSON 範本和其他相關聯的檔案建立本機資料夾 (例如，C:\\Azure\\Templates\\Spark)。

使用您本機資料夾的資料夾名稱來替代，並執行下列命令：

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### 步驟 1-b：使用 Azure CLI 下載範本檔案

使用您選擇的 git 用戶端，來複製整個範本儲存機制，例如：

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

完成時，尋找 C:\\Azure\\Templates 目錄中的 **spark-on-ubuntu** 資料夾。

### 步驟 2：(選用) 了解範本參數

當您利用範本建立 Spark 時，您必須指定一組組態參數來處理一些必要設定。您可以在範本定義中宣告這些參數，這樣一來當您透過外部檔案或者在命令列進行部署的時候，就可以指定每一個值。

在 **azuredeploy.json** 檔案頂端的 "parameters" 區段中，您將會發現範本需要用來設定 Spark 叢集的參數集。以下範例來自這個範本的 azuredeploy.json 檔案的 parameters 區段：

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
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image Publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image Offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
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
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned in to"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

每個參數都具有資料類型和允許值之類的詳細資料。這允許驗證在互動模式 (例如 PowerShell 或 Azure CLI) ，及自我探索 UI (透過剖析必要參數清單及其描述並動態建置的 UI) 的範本執行期間所傳遞的參數。

### 步驟 3-a：使用 PowerShell，利用範本來部署 Spark 叢集

藉由建立 JSON 檔案 (其中包含適用於所有參數的執行階段值)，來為您的部署準備參數檔案。接著將此檔案當成單一實體傳遞給部署命令。如果未包含參數檔案，PowerShell 將使用範本中指定的任何預設值，然後提示您填寫剩餘的值。

以下是來自 **azuredeploy-parameters.json** 檔案的參數集範例。請注意，您必須為參數 storageAccountName、adminUsername 及 adminPassword 提供有效值，以及為其他參數提供任何自訂：

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
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
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
填寫 Azure 部署名稱、資源群組名稱、Azure 位置，以及儲存 JSON 部署檔案的資料夾。然後執行以下命令：

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]$RGName 在您的訂用帳戶內必須是唯一的。

執行 **New-AzureResourceGroupDeployment** 命令時，這將會從 JSON 參數檔案中擷取參數值，然後據以開始執行範本。定義以及使用不同環境 (例如測試、正式作業等) 所需的參數檔案，有利範本的重複使用以及簡化複雜的多重環境解決方案。

部署時，請記得需要建立新的 Azure 儲存體帳戶，因此，您提供來做為儲存體帳戶參數的名稱就必須是唯一且符合 Azure 儲存體帳戶的所有需求 (僅限小寫字母和數字)。

您將會在部署期間看見如下的內容：

```powershell
PS C:> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

部署期間以及部署結束之後，您可以檢查所有佈建期間所進行的要求，包括任何發生的錯誤。

若要這樣做，請移至 [Azure 入口網站](https://portal.azure.com)，然後執行下列動作：

- 按一下左側導覽列上的 [瀏覽]，向下捲動，然後按一下 [資源群組]。
- 按一下剛建立的資源群組之後，系統就會顯示 [資源群組] 刀鋒視窗。
- 在 [資源群組] 刀鋒視窗的 [監視] 部分中，按一下 [事件] 長條圖，就能看見部署的事件：
- 按一下個別事件之後，系統會詳細列出不是由範本親自進行的各項操作。

![portal-events](media/virtual-machines-spark-template/portal-events.png)

測試之後，如果需要移除這個資源群組及其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用這個單一命令：

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### 步驟 3-b：使用 Azure CLI，利用範本來部署 Spark 叢集

若要透過 Azure CLI 部署 Spark 叢集，請先指定名稱和位置來建立資源群組：

	azure group create SparkResourceGroup "West US"

將此資源群組名稱、JSON 範本檔案的位置，以及參數檔案的位置 (如需詳細資訊，請參閱上方的 PowerShell 章節) 傳遞給下列命令：

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

您可以使用下列命令來檢查個別資源部署的狀態：

	azure group deployment list SparkResourceGroup

## Spark 範本結構和檔案組織的導覽

為了讓資源管理員範本的設計更加完善且可重複使用，您必須在部署 Spark 之類的複雜解決方案期間，考慮清楚如何安排一連串複雜但又彼此相關的工作。除了透過相關延伸模組執行指令碼之外，還可以利用 ARM **範本連結**和**資源迴圈**，這樣就能實作模組化方法，而實際上所有以複雜範本為基礎的部署都能重複使用此方法。

下圖說明在此部署中從 GitHub 下載的所有檔案彼此間的關係：

![spark-files](media/virtual-machines-spark-template/spark-files.png)

本節將帶領您逐步了解 Spark 叢集的 **azuredeploy.json** 檔案結構。

如果您尚未下載範本檔案的複本，請指定本機資料夾做為該檔案的位置並建立它 (例如，C:\\Azure\\Templates\\Spark)。填寫資料夾名稱，然後執行以下命令。

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

在您選擇的文字編輯器或工具中開啟 azuredeploy.json 範本。以下將說明範本檔案的結構和每個區段的用途。或者，您可以從[此處](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json)，在瀏覽器中查看這個範本的內容。

### "parameters" 區段

**azuredeploy.json** 的 "parameters" 區段會指定此範本中所使用的可修改參數。先前提及的 **azuredeploy-parameters.json** 檔案是在範本執行期間，用來將值傳遞到 azuredeploy.json 的 "parameters" 區段。

以下是適用於「T 恤尺寸」的參數範例：

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

>。[AZURE.NOTE]請注意，您可以指定 "defaultValue" 以及 "allowedValues"。

### "variables" 區段

"variables" 區段會指定一些變數，這些參數會全程用在這個範本中。這包含數個欄位 (JSON 資料類型或片段)，而它們將在執行階段設定為常數或計算值。以下是一些範圍從簡單到更複雜的範例：

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

"**vmStorageAccountContainerName**" 是簡單名稱/值變數的範例。"**vnetID**" 是在執行階段使用函式 "**resourceId**" 和 "**parameters**" 計算的變數範例。"**numberOfMasterInstances**" 和 "**vmSize**" 變數的值會在執行階段使用 "**concat**"、"**variables**" 和 "**parameters**" 函式計算。

如果您想要自訂 Spark 叢集部署的大小，則可在 azuredeploy.json 範本中變更變數 tshirtSizeS、tshirtSizeM 和 tshirtSizeL 的屬性。

如需關於範本語言的詳細資訊，請參閱 [Azure 資源管理員範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx) 中的 MSDN。


### "resources" 區段

絕大多數的動作就是在 **"resources"** 區段進行的。仔細看這個區段，您會立即找出兩個不同的案例：第一個是被定義為 `Microsoft.Resources/deployments` 類型的元素，基本上表示叫用第一個主要檔案裡面的巢狀部署。透過 "**templateLink**" 元素 (和相關的版本屬性)，就能指定連結的範本檔案，並在叫用此檔案時傳遞一組參數當做輸入，如同您在此片段中所見：

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

在第一個範例中，我們很清楚地知道此案例中的 **azuredeploy.json** 是用來做為一種協調流程機制，負責叫用一些其他範本檔案，而這其中每一個檔案都會負責部分的必要部署活動。

特別是，下列連結的範本將用於此部署：

-	**shared-resource.json**：包含所有要在整個部署中共用的資源定義。例如，用來儲存 VM 的作業系統磁碟和虛擬網路的儲存體帳戶。
-	**jumpbox-resources-enabled.json**：部署「跳躍箱」VM 及所有相關資源，例如，網路介面、公用 IP 位址，以及用來 SSH 到環境的輸入端點。

叫用這兩個範本之後，**azuredeploy.json** 會佈建所有 Spark 叢集節點 VM 和連接的資源 (例如，網路卡、私人 IP 等)。此範本也會部署 VM 延伸模組 (適用於 Linux 的自訂指令碼)，並叫用 bash 指令碼 (**spark-cluster-install.sh**)，在每一個節點上實際安裝並設定 Spark。

讓我們深入了解最後一個範本 **azuredeploy.json** 的*使用方式*，因為從範本開發角度來看，這是最有趣的範本之一。要強調一個重要概念是單一範本檔案如何能夠部署單一資源類型的多個複本，而且每一個執行個體都能為必要設定指定唯一值。這個概念稱為**資源迴圈**。

使用「複製」元素的資源會以「複製」元素的「計數」參數中指定的次數來「複製」它自己。對於您需要在不同的部署資源執行個體之間指定唯一值的所有設定，可使用 **copyindex()** 函式來取得數值，以指出目前用來建立這個特定資源迴圈的索引。在下列來自 **azuredeploy.json** 的片段中，您可以在為 Spark 叢集建立的多個網路卡、VM 及 VM 延伸模組上看見這個概念的運用：

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

建立資源的另一個重要概念是能夠指定資源間的相依性和優先順序，如同您在 **dependsOn** JSON 陣列中所見。在這個特殊範本中，您可以看見 Spark 叢集節點相依於先建立的共用資源和網路介面資源。

另一個要探索的有趣片段是與 **CustomScriptForLinux** VM 延伸模組相關的片段。這些片段都是以獨立資源類型進行安裝，而且每個叢集節點上都具有相依性。在此情況下，這可用來在每個 VM 節點上安裝並設定 Spark。讓我們查看來自 **azuredeploy.json** 範本的程式碼片段，其中會使用下列程式碼：

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

請注意，主要和從屬節點資源的延伸模組會在佈建程序期間執行不同的命令，這些命令定義於 **commandToExecute 屬性** 中。

您可以看見這個資源相依於已經部署的資源 VM **Microsoft.Compute/virtualMachines/vmMember<X>**，其中 **<X>** 是參數 "**machineSettings.machineIndex**"，它是使用 "**copyindex()**" 函式傳遞到這個指令碼的 VM 索引。

讓您自己熟悉此部署內所含的其他檔案，就能了解如何利用 Azure 資源管理員範本，根據任何技術來組織和協調適用於多節點解決方案的複雜部署策略所需的所有詳細資料和最佳做法。在此建議一種範本檔案建構方法，請自行決定是否採用，如下圖重點標示的部分：

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

基本上，這種方法會建議：

-	將您的核心範本檔案定義成所有特定部署活動的協調中心，利用範本連結功能來叫用子範本的執行。
-	建立特定的範本檔案，用來部署其他特定部署作業會共用的一切資源 (例如儲存體帳戶、vnet 設定)。如果不同的部署之間，其一般基礎結構的需求皆類似，就可以高度重複使用。
-	包含選用資源範本，用於特定資源的場地需求
-	至於資源群組中的成員相同時 (例如，叢集中的節點)，可以建立一些會利用資源迴圈功能的範本，以便部署多個屬性皆不同的執行個體。
-	所有的張貼部署工作 (例如產品安裝、設定等) 都會利用指令碼部署擴充功能以及建立每一種技術獨有的指令碼

如需詳細資訊，請參閱 [Azure Resource Manager 範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。

## 後續步驟

閱讀[部署範本](../resource-group-template-deploy.md)上的詳細資料。

探索更多[應用程式架構](virtual-machines-app-frameworks.md)。

[疑難排解範本部署](resource-group-deploy-debug.md)。
 

<!---HONumber=July15_HO2-->