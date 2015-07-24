<properties
  pageTitle="在 Ubuntu 上使用資源管理員範本建立 MongoDB 叢集"
  description="在 Ubuntu 上透過 PowerShell 或 Azure CLI 使用資源管理員範本建立 MongoDB 叢集"
  services="virtual-machines"
  documentationCenter=""
  authors="karthmut"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="karthmut"/>

# 在 Ubuntu 上使用資源管理員範本建立 MongoDB 叢集

MongoDB 是開放原始碼的文件資料庫，可提供高效能、高可用性，以及自動調整範圍。MongoDB 可獨立安裝或安裝於叢集中，以利用內建的複寫功能。在某些情況下，您可以使用複寫來提高讀取產能。用戶端能夠將讀取和寫入操作傳送到其他伺服器。您也可以在不同的資料中心保留複本，以針對分散式應用程式增加資料的位置和可用性。使用 MongoDB，複寫也可提供備援並提高資料可用性。在不同的資料庫伺服器上使用多個資料複本，複寫可以保護資料庫免於遺失單一伺服器。複寫也能讓您從硬體故障和服務中斷復原。使用其他資料複本，您可以將其中一個專門用於災害復原、報告或備份。

除了提供 Azure Marketplace 已有的各種功能之外，現在您也可以使用透過 [Azure PowerShell](../powershell-install-configure.md) 或 [Azure CLI](../xplat-cli.md) 部署的資源管理員範本，在 Ubuntu VM 上輕鬆部署新的 MongoDB 叢集。

根據這個範本部署的新叢集會採用下圖中所述的拓撲，不過，您可以自訂本文中所述的範本，輕鬆實現其他拓撲：

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

透過參數，您可以定義將部署於新 MongoDB 叢集中的節點數目，而且根據其他參數，含有公用 IP 位址的 VM 執行個體 (Jumpbox) 可能也會部署於相同的 VNET 中，但前提假設您能夠從公用網際網路連線到叢集，並執行與該叢集相關的各種管理工作。另一個可用來做為參數的選項是能將 Arbiter 節點新增到複本集，當您具有偶數成員時，建議使用此選項。如需 MongoDB 複寫拓撲和細節的詳細資訊，您應該參閱官方的 [MongoDB 文件](http://docs.mongodb.org/manual/core/replication-introduction/)。

部署完成之後，您可以使用 SSH 連接埠 22 上設定的 DNS 位址來存取 Jumpbox。

在深入了解與 Azure 資源管理員和將針對此次部署使用之範本的詳細資訊之前，請確定您已正確設定 Azure PowerShell 或 Azure CLI。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 使用資源管理員範本建立 MongoDB 叢集

請依照下列步驟，使用 Github 範本儲存機制中的資源管理員範本來建立 MongoDB 叢集。每個步驟都將包含適用於 Azure PowerShell 和 Azure CLI 的指引。

### 步驟 1-a：使用 PowerShell 下載範本檔案

為 JSON 範本和其他相關聯的檔案建立本機資料夾 (例如，C:\\Azure\\Templates\\MongoDB)。

使用您本機資料夾的資料夾名稱來替代，並執行下列命令：

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### 步驟 1-b：使用 Azure CLI 下載範本檔案

使用您選擇的 git 用戶端，來複製整個範本儲存機制，例如：

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

完成時，尋找 C:\\Azure\\Templates 目錄中的 **mongodb-high-availability** 資料夾。

### 步驟 2：(選用) 了解範本參數

部署非簡單式解決方案時 (例如 MongoDB 叢集)，您必須指定一組設定參數來處理一些必要設定。您可以在範本定義中宣告這些參數，這樣一來，就能在透過外部檔案或在命令列中進行部署期間指定值。

在 **azuredeploy.json** 檔案頂端的 "parameters" 區段中，您將會發現範本需要用來設定 MongoDB 叢集的參數組。以下範例來自這個範本的 azuredeploy.json 檔案的 parameters 區段：

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
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
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

每個參數都具有資料類型和允許值之類的詳細資料。這允許使用互動模式來驗證在範本執行期間傳遞的參數 (例如 PowerShell 或 Azure CLI)，以及自我探索 UI，這個 UI 是透過剖析必要參數清單及其說明動態建置的。

### 步驟 3-a：使用 PowerShell，利用範本來部署 MongoDB 叢集

藉由建立 JSON 檔案 (其中包含適用於所有參數的執行階段值)，來為您的部署準備參數檔案。接著將此檔案當成單一實體傳遞給部署命令。如果未包含參數檔案，PowerShell 將使用範本中指定的任何預設值，然後提示您填寫剩餘的值。

以下是來自 **azuredeploy-parameters.json** 檔案的參數範例組：

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
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
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

填寫 Azure 部署名稱、資源群組名稱、Azure 位置，以及儲存 JSON 部署檔案的資料夾。然後執行以下命令：

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

執行 **New-AzureResourceGroupDeployment** 命令時，這將會從 JSON 參數檔案中擷取參數值，然後據以開始執行範本。定義以及使用不同環境 (例如測試、正式作業等) 所需的參數檔案，有利範本的重複使用以及簡化複雜的多重環境解決方案。

部署時，請記得需要建立新的 Azure 儲存體帳戶，因此，您提供來做為儲存體帳戶參數的名稱就必須是唯一且符合 Azure 儲存體帳戶的所有需求 (僅限小寫字母和數字)。

部署期間以及部署結束之後，您可以檢查所有佈建期間所進行的要求，包括任何發生的錯誤。

若要這樣做，請移至 [Azure 入口網站](https://portal.azure.com)，然後執行下列動作：

- 按一下左側導覽列上的 [瀏覽]，向下捲動，然後按一下 [資源群組]。
- 按一下剛建立的資源群組之後，系統就會顯示 [資源群組] 刀鋒視窗。
- 在 [資源群組] 刀鋒視窗的 [監視] 部分中，按一下 [事件] 長條圖，就能看見部署的事件：
- 按一下個別事件之後，系統會詳細列出不是由範本親自進行的各項操作。

測試之後，如果需要移除這個資源群組及其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用這個單一命令：

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 步驟 3-a：使用 Azure CLI，利用範本來部署 MongoDB 叢集

若要透過 Azure CLI 部署 MongoDB 叢集，請先指定名稱和位置來建立資源群組：

    azure group create mdbc "West US"

將此資源群組名稱、JSON 範本檔案的位置，以及參數檔案的位置 (如需詳細資訊，請參閱上方的 PowerShell 章節) 傳遞給下列命令：

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

您可以使用下列命令來檢查個別資源部署的狀態：

    azure group deployment list mdbc

## MongoDB 範本結構和檔案組織的導覽

為了讓資源管理員範本的設計更加完善且可重複使用，您必須在部署 MongoDB 之類的複雜解決方案期間，考慮清楚如何安排一連串複雜但又彼此相關的工作。除了透過相關延伸模組執行指令碼之外，還可以利用 ARM **範本連結**和**資源迴圈**，這樣就能實作模組化方法，而實際上所有以複雜範本為基礎的部署都能重複使用此方法。

下圖說明在此部署中從 GitHub 下載的所有檔案彼此間的關係：

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

本節將帶領您逐步了解 MongoDB 叢集的 **azuredeploy.json** 檔案結構。

### "parameters" 區段

**azuredeploy.json** 的 "parameters" 區段會指定此範本中所使用的可修改參數。本文先前所述的 **azuredeploy-parameters.json** 檔案是在範本執行期間，用來將值傳遞到 azuredeploy.json 的 "parameters" 區段。

### "variables" 區段

"variables" 區段會指定一些變數，這些參數會全程用在這個範本中。這包含數個欄位 (JSON 資料類型或片段)，而它們將在執行階段設定為常數或計算值。以下是此 MongoDB 範本的 "variables" 區段：

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

深入研究這個範例之後，就會看到兩種不同的方法。在第一個片段中，"osFamilyUbuntu" 變數將設定為 JSON 元素，其中包含 6 個機碼值組：

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

在第二個片段中，會將 "vmScripts" 變數指派給 JSON 陣列，在執行階段會使用範本語言函式 (concat) 以及另一個變數的值再加上字串常數，計算這個陣列中的單一元素：

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

這個範本中的一個重要概念是針對 MongoDB 叢集定義不同「T 恤尺寸」的方式。查看這些 “tshirtSizeXXXX” 變數的其中一個，您會注意到它說明了如何部署叢集的重要特性。讓我們以中等尺寸為例：

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

「中等」MongoDB 叢集將使用 D2 做為裝載資料之 3 個 MongoDB 叢集的 VM 大小，再加上第 4 個 A1 VM，這個 VM 將基於複寫目的，用來做為 Arbiter。叫用來部署資料節點的對應子範本會是 **member-resources-D2.json**，而資料檔案 (每一個 250 GB) 將儲存於 2 個儲存體帳戶中。這個變數將用於 “resources” 區段中，以協調節點部署和其他工作。

### "resources" 區段

絕大多數的動作就是在 **"resources"** 區段進行的。仔細看這個區段，您會立即找出兩個不同的案例：第一個是被定義為 `Microsoft.Resources/deployments` 類型的元素，基本上表示叫用第一個主要檔案裡面的巢狀部署。透過 "templateLink" 元素 (和相關的版本屬性)，就能指定連結的範本檔案，並在叫用此檔案時傳遞一組參數當做輸入，如同您在此片段中所見：

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

在第一個範例中，我們很清楚地知道此案例中的 **azuredeploy.json** 是用來做為一種協調流程機制，負責叫用一些其他範本檔案，而這其中每一個檔案都會負責部分的必要部署活動。

特別是，下列連結的範本將用於此部署：

-	**shared-resource.json**：包含所有要在整個部署中共用的資源定義。例如，用來儲存 VM 的作業系統磁碟和虛擬網路的儲存體帳戶。
-	**jumpbox-resources.json**：啟用時，負責部署所有與 Jumpbox VM 相關的資源，其中一個具有公用 IP 位址，可用來從公用網路存取 MongoDB 叢集。
-	**arbiter-resources.json**：啟用時，這個範本會在 MongoDB 叢集中部署仲裁程式成員。仲裁程式不會包含資料，但可在複本集包含偶數節點以管理主要選取時使用。
-	**member-resources-Dx.json**：這些資源範本可有效地部署 MongoDB 節點。您可以根據選取的 T 恤尺寸定義來使用特定檔案，每個檔案間只有每個節點的連結磁碟數的差異。
-	**mongodb-ubuntu-install.sh**：叢集中每個節點上的 CustomScriptForLinux 延伸模組所叫用的 Bash 指令碼檔案。負責掛接和格式化資料磁碟，以及在節點上安裝 MongoDB 位元。

若要部署 MongoDB 叢集，需要使用特定邏輯，才能正確設定複本集。以下是您必須在部署期間使用的特定順序：

部署資料成員 (平行進行) => 部署最後一個資料成員 => (選用) 部署仲裁程式

在此順序中，部署多個資料節點將會平行發生，但最後一個節點例外。這是將形成叢集和部署新複本集的地方，因此，所有先前的節點都必須在該時刻之前啟動並執行。最後一個步驟是部署選用的仲裁程式節點 (僅適用於此為必要項目的 T 恤尺寸)。

再次查看主要範本，讓我們從所有資料成員開始來查看這個邏輯的實作方式：

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
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
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

要強調的一個重要概念是如何能夠部署單一資源類型的多個複本，而且每一個執行個體都能為必要設定指定唯一值。這個概念稱為**資源迴圈**。

在上一個片段中，將使用參數 (要在叢集中部署的節點數) 來設定變數 (“numberOfMembers”)，然後將它傳遞給 **“copy”** 元素，以觸發子部署的成員 (迴圈)，每一個都會針對叢集中的每個成員，將範本具現化。為了能夠設定執行個體間需要唯一值的所有設定，可使用 **copyindex()** 函式來取得數值，以指出目前用來建立這個特定資源迴圈的索引。

建立資源時還有一個重要的概念，那就是指定資源之間的相依性和優先順序，如同您在 **dependsOn** JSON 陣列中看到的一樣。在這個特殊的範本中，部署每一個節點都取決於先前成功部署的**共用資源**。

當 **mongodb-ubuntu-install.sh** 指令碼檔案執行時會觸發節點準備活動，其中之一就是格式化連結的磁碟。事實上，您可以在該檔案中找到這個呼叫的執行個體：

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

**vm-disk-utils-0.1.sh** 是 **shared_scripts\\ubuntu** 資料夾的一部分 (位於 azure-quickstart-tempates github 儲存機制內)，其中包含適用於磁碟掛接、格式設定及等量的非常實用的功能，每當您在建立範本期間需要執行類似工作時就能重複使用。

我們繼續探索另一個與 CustomScriptForLinux VM 擴充功能有關而且很有趣的程式碼片段。這些都能安裝為獨立的資源類型，而且在每一個叢集節點部署範本上都具有相依性，請參閱這個位於每個 **member-resources-Dx.json** 檔案結尾處的片段：

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

讓您自己熟悉此部署內所含的其他檔案，就能了解如何利用 Azure 資源管理員範本，根據任何技術來組織和協調適用於多節點解決方案的複雜部署策略所需的所有詳細資料和最佳做法。在此建議一種範本檔案建構方法，請自行決定是否採用，如下圖重點標示的部分：

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

基本上，這種方法會建議：

-	將您的核心範本檔案定義成所有特定部署活動的協調中心，利用範本連結功能來叫用子範本的執行。
-	建立特定的範本檔案，用來部署其他特定部署作業會共用的一切資源 (例如儲存體帳戶、vnet 設定)。如果不同的部署之間，其一般基礎結構的需求皆類似，就可以高度重複使用。
-	包含選用資源範本，用於特定資源的場地需求
-	至於資源群組中的成員相同時 (例如，叢集中的節點)，可以建立一些會利用資源迴圈功能的範本，以便部署多個屬性皆不同的執行個體。
-	所有的張貼部署工作 (例如產品安裝、設定等) 都會利用指令碼部署擴充功能以及建立每一種技術獨有的指令碼

如需詳細資訊，請參閱 [Azure Resource Manager 範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。
 

<!---HONumber=July15_HO2-->