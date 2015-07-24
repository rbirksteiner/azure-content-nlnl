<properties 
	pageTitle="使用資源管理員範本和適用於 Mac、Linux 和 Windows 的 Microsoft Azure CLI 來部署和管理虛擬機器" 
	description="使用 Azure 資源管理員和 Azure CLI 輕鬆部署以及管理 Azure 虛擬機器最常用的設定。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="rasquill"/>

# 使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器

本文會為您示範如何使用 Azure 資源管理員範本和 Azure CLI，進行下列部署和管理 Azure 虛擬機器的常見工作。如需您可以使用的其他範本，請參閱 [Azure 快速入門範本](http://azure.microsoft.com/documentation/templates/)和[應用程式架構](virtual-machines-app-frameworks.md)。

- [在 Azure 中快速建立虛擬機器](#quick-create-a-vm-in-azure)
- [在 Azure 中利用範本部署虛擬機器](#deploy-a-vm-in-azure-from-a-template)
- [從自訂映像建立虛擬機器](#create-a-custom-vm-image) 
- [使用虛擬網路和負載平衡器部署 VM](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [移除資源群組](#remove-a-resource-group)
- [顯示資源群組部署記錄檔](#show-the-log-for-a-resource-group-deployment)
- [顯示虛擬機器的相關資訊](#display-information-about-a-virtual-machine)
- [連線至 Linux 型虛擬機器](#log-on-to-a-linux-based-virtual-machine)
- [停止虛擬機器](#stop-a-virtual-machine)
- [啟動虛擬機器](#start-a-virtual-machine)
- [連接資料磁碟](#attach-a-data-disk)

## 準備就緒

在您能搭配 Azure 資源群組使用 Azure CLI 前，請您準備好正確的 Azure CLI 版本以及公司或學校識別碼 (也稱為組織識別碼」)。

### 將 Azure CLI 版本更新為 0.9.0 或更新版本

輸入 `azure --version`，即可以查看您是否已經安裝 0.9.0 版或更新版本

	azure --version
    0.9.0 (node: 0.10.25)

如果您的版本不是 0.9.0 或更新版本，則必須[安裝 Azure CLI](../xplat-cli-install.md) 或使用其中一個原生安裝程式或輸入 `npm update -g azure-cli` 透過 **npm**，進行版本的更新。

您也可以使用下列 [Docker 映像](https://registry.hub.docker.com/u/microsoft/azure-cli/)，以 Docker 容器執行 Azure CLI。從 Docker 主機中，執行下列命令：

	docker run -it microsoft/azure-cli

### 設定 Azure 帳戶和訂用帳戶

如果您還沒有 Azure 訂閱帳戶，但是有 MSDN 訂閱帳戶，請啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。或者申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

您必須要有公司或學校帳戶，才能使用 Azure 資源管理範本。如果有的話，請輸入 `azure login`，然後輸入使用者名稱和密碼，最後應該可以成功登入。

> [AZURE.NOTE]如果您沒有帳戶，您會看到錯誤訊息，指出您需要不同類型的帳戶。若要從目前的 Azure 帳戶建立一個帳戶，請參閱[在 Azure Active Directory 中建立工作或學校身分識別](resource-group-create-work-id-from-personal.md)。

您的帳戶可能會有一個以上的訂閱帳戶。您可以輸入 `azure account list`，即可列出訂閱帳戶，如以下所示：

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenandt Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true   
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    
若要設定目前的 Azure 訂閱帳戶，請輸入

	azure account set <subscription name or ID> true

並加上訂閱帳戶名稱或識別碼 (有您想管理的資源)。

### 切換至 Azure CLI 資源群組模式

根據預設，Azure CLI 會在服務管理模式下啟動 (** asm ** 模式)。類型

	azure config mode arm

切換至資源群組模式。

> [AZURE.NOTE]要切換回預設的命令，可以輸入 `azure config mode asm`。

## 了解 Azure 資源範本和資源群組

大部分的應用程式在建立時會使用不同資源類型的組合 (例如一或多個 VM 和儲存體帳戶、SQL 資料庫、虛擬網路、內容傳遞網路或 *CDN*)。預設 Azure 服務管理 API 與 Azure 傳統入口網站分別代表使用 service-by-service 方法的項目，如此一來您需要部署和管理每一個服務 (或尋找其他具備相同功能的工具)，而不是當做單一邏輯部署單元。

您可以利用 *Azure 資源管理員*，將這些不同的資源宣告為一個邏輯部署單元，然後就可以進行部署和管理。請不要以命令方式告訴 Azure 逐一部署命令，您應該在 JSON 檔案描述整個部署過程 -- 所有資源和相關設定以及部署參數 -- 然後告訴 Azure 將這些資源視為一個群組加以部署。

然後您可以使用 Azure CLI 資源管理命令執行以下動作，即可管理群組的資源整體使用週期：

- 一次性停止、啟動或刪除群組內的所有資源。 
- 將角色型存取控制 (RBAC) 規則套用至鎖定它們的安全權限。 
- 稽核作業。 
- 利用其他中繼資料標記資源，方便追蹤。 

如需深入了解 Azure 資源群組以及它們的功能，請參閱[這裡](../resource-group-overview.md)。如果您想了解如何設計範本，請參閱[設計 Azure Resource Manager 範本](../resource-group-authoring-templates.md)。

## <a id="quick-create-a-vm-in-azure"></a>工作：在 Azure 中快速建立 VM

有時候您知道需要何種映像，而且您現在需要該映像的 VM，並且不太在意基礎結構 -- 或許您必須在全新的 VM 上進行某些測試。當您想要使用 `azure vm quick-create` 命令，然後傳遞必要引數來建立 VM 和基礎結構的時候。

首先，建立資源群組。

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick                                          
    + Creating resource group coreos-quick                                         
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

第二，您將需要映像。想利用 Azure CLI 尋找映像時，請參閱[利用 PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](resource-groups-vm-searching.md)。不過在這個快速入門中，以下是常用映像的簡要清單。我們會使用 CoreOS 的 Stable 映像，縮短整個建立流程。

> [AZURE.NOTE]對於 ComputeImageVersion，您也可以只提供 'latest' 做為範本語言和 Azure CLI 中的參數。這可讓您永遠使用最新且經過修補的映像版本，而不必修改您的指令碼或範本。如下所示。

| PublisherName | 提供項目 | SKU | 版本 |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7.0.201503 |
| OpenLogic | CentOS | 7.1 | 7.1.201504 |
| CoreOS | CoreOS | Beta | 647.0.0 |
| CoreOS | CoreOS | Stable | 633.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | 標準 | 1.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise | 1.0.0 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW | 12.0.2430 |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP | 12.0.2430 |
| Canonical | UbuntuServer | 12.04.5-LTS | 12.04.201504230 |
| Canonical | UbuntuServer | 14.04.2-LTS | 14.04.201503090 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 3.0.201503 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 4.0.201503 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 5.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4.3.4665 |

只要輸入 `azure vm quick-create command`，然後根據系統提示執行，就可以建立 VM。您應該會看到類似下面的畫面：

    azure vm quick-create 
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"                                                   
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts                                                  
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"              
    + Looking up the storage account cli9fd3fce49e9a9b3d14302                      
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    + Creating NIC "coreo-westu-1430261891570-nic"                                 
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Creating VM "coreos"                                                         
    + Looking up the VM "coreos"                                                   
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:    
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK
    
無論身在何處，新的 VM 就在您身邊。

## <a id="deploy-a-vm-in-azure-from-a-template"></a>工作：在 Azure 中利用範本部署 VM

請按照以下各節描述的操作方法，使用 Azure CLI 搭配範本來部署新的 Azure VM。這個範本會在只有單一子網路的新虛擬網路中建立單一虛擬機器，而不同於 `azure vm quick-create`，它可以讓您精確描述想要的內容，而且重複使用時也不會發生任何錯誤。以下是這個範本建立的內容：

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/new-vm.png)
 
### 步驟 1：檢查 JSON 檔案的範本參數。

以下是範本的 JSON 檔案內容。(這個範本也位於 GitHub [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/101-simple-linux-vm/azuredeploy.json))。

範本可彈性運用，所以這位設計人員可能已經決定提供很多的參數給您，或者她決定建立一個更固定的範本，而只提供幾個參數給您。為了收集資訊，請您將這個範本以參數的形式傳遞，然後開啟範本檔案 (這個主題內嵌一個範本)，接下來檢查 [參數] 值。

在這個案例中，系統會要求您提供下列範本：

- 唯一的儲存體帳戶名稱
- VM 的系統管理員使用者名稱
- 密碼
- 讓外界使用的網域名稱
- 而且會接受 Ubuntu Server 版本號碼 -- 但只能有一個清單。 

決定這些值之後，就可以開始建立群組，然後將這個範本部署到 Azure 訂閱帳戶。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "newStorageAccountName": {
                "type": "string",
                "metadata": {
                    "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                   "Description": "User name for the Virtual Machine."
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                    "Description": "Password for the Virtual Machine."
                }
            },
            "dnsNameForPublicIP": {
                "type": "string",
                "metadata": {
                      "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
                }
            },
            "ubuntuOSVersion": {
                "type": "string",
                "defaultValue": "14.10",
                "allowedValues": [
                    "12.04.2-LTS",
                    "12.04.3-LTS",
                    "12.04.4-LTS",
                    "12.04.5-LTS",
    				"12.10",
                    "14.04.2-LTS",
                    "14.10",
                    "15.04"
                ],
                "metadata": {
                    "Description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.2-LTS, 12.04.3-LTS, 12.04.4-LTS, 12.04.5-LTS, 12.10, 14.04.2-LTS, 14.10, 15.04."
                }
            }
        },
        "variables": {
            "location": "West US",
            "imagePublisher": "Canonical", 
            "imageOffer": "UbuntuServer", 
            "OSDiskName": "osdiskforlinuxsimple",
            "nicName": "myVMNic",
            "addressPrefix": "10.0.0.0/16", 
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyUbuntuVM",
            "vmSize": "Standard_D1",
            "virtualNetworkName": "MyVNET",        
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
        },    
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('newStorageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[variables('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[variables('publicIPAddressName')]",
                "location": "[variables('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[variables('virtualNetworkName')]",
                "location": "[variables('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[variables('nicName')]",
                "location": "[variables('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                                },
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
                "name": "[variables('vmName')]",
                "location": "[variables('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[variables('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[variables('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[variables('imagePublisher')]",
                            "offer": "[variables('imageOffer')]",
                            "sku" : "[parameters('ubuntuOSVersion')]",
                            "version":"latest"
                        },
                       "osDisk" : {
                            "name": "osdisk",
                            "vhd": {
                                "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                            },
                            "caching": "ReadWrite",
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                            }
                        ]
                    }
                }
            }
        ]
    } 
  
### 步驟 2：使用範本建立虛擬機器

準備好參數值之後，您必須建立一個部署範本時會用到的資源群組，然後再部署範本。

若要建立資源群組，請輸入 `azure group create <group name> <location>` 以及您想用之群組的名稱以及要部署到哪一個資料中心位置。進行速度十分快：

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup                                       
    + Creating resource group myResourceGroup                                      
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

現在要建立部署，請呼叫 `azure group deployment create` 並傳遞：

- 範本檔案 (如果您會將上述 JSON 範本儲存到本機檔案) 
- 範本 URI (如果您想指向 Github 中的檔案或其他網址)
- 部署的目標資源群組
- 以及選用部署名稱。 

系統會提示您輸入 JSON 檔案的 **"parameters"** 區段中的參數值。指定好所有的參數值後，就會開始部署。

下列是一個範例：

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname
    
您會收到下列類型的資訊：

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "firstDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value        
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops          
    data:    adminPassword          SecureString  undefined    
    data:    dnsNameForPublicIP     String        newdomainname   
    data:    ubuntuOSVersion        String        14.10        
    info:    group deployment create command OK
    


## <a id="create-a-custom-vm-image"></a>工作：建立自訂的 VM 映像

您已基本了解上述範本的用法，那麼現在我們可以使用類似的操作方法，使用 Azure 的特定 .vhd 檔案搭配範本和 Azure CLI 建立自訌的範本。其中的差別就是這個範本會從指定的虛擬硬碟 (VHD) 建立單一虛擬機器。

### 步驟 1：檢查範本的 JSON 檔案

以下是本章節舉例說明時，範本的 JSON 檔案內容，不過隨時可以到[這裡](https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json)找到這個範本。

再說一次，參數如果沒有預設值，就必須找出您想輸入的值。當您執行 `azure group deployment create` 命令時，Azure CLI 會提示您輸入這些值。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
          }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### 步驟 2：取得 VHD

很明顯，您需要 .vhd。您可以使用 Azure 現有的 .vhd 或者可以上傳一個 .vhd。

若是 Windows 型虛擬機器，請參閱[建立 Windows Server VHD 並上傳至 Azure](virtual-machines-create-upload-vhd-windows-server.md)。

若是 Linux 型虛擬機器，請參閱[建立 Linux VHD 並上傳至 Azure](virtual-machines-linux-create-upload-vhd.md)。

### 步驟 3：使用範本建立虛擬機器

現在您已準備利用 .vhd 建立新的虛擬機器。使用 `azure group create <location>`，建立一個部署時會用到的群組：

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser                                            
    + Creating resource group myResourceGroupUser                                           
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    
然後使用 `--template-uri` 選項直接呼叫範本 (或者使用 `--template-file` 選項，使用自己儲存在本機中的檔案)，開始建立部署。請注意，因為範本已指定預設值，所以只會提示您只輸入幾項資料。如果將範本部署到幾個不同的地方，可能會發現某些名稱與預設值衝突 (特別是您建立的 DNS 名稱)。

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        
您會看到類似下列輸出：

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "customVhdDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value                               
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName         
    data:    userImageStorageContainerName  String        userImageStorageContainerName       
    data:    userImageVhdName               String        userImageVhdName                    
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP            
    data:    adminUserName                  String        ops                                 
    data:    adminPassword                  SecureString  undefined                           
    data:    osType                         String        linux                               
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US                             
    data:    vmSize                         String        Standard_A2                         
    data:    publicIPAddressName            String        myPublicIP                          
    data:    vmName                         String        myVM                                
    data:    virtualNetworkName             String        myVNET                              
    data:    nicName                        String        myNIC                               
    info:    group deployment create command OK
    

## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>工作：部署一個多重 VM 應用程式，它會使用虛擬網路和外部負載平衡器

您可以利用這個範本，在一個負載平衡器上建立兩個虛擬機器，然後在連接埠 80 設定負載平衡規則。這個範本也會部署儲存體帳戶、虛擬網路、公用 IP 位址、可用性設定組以及網路介面。

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/multivmextlb.png)
 
按照下列步驟部署一個多重 VM 應用程式，它會利用 Azure PowerShell 命令使用 Github 範本儲存機制中的 Resource Manager 範本，然後就可以使用虛擬網路和負載平衡器。

### 步驟 1：檢查範本的 JSON 檔案。

以下是範本的 JSON 檔案內容。如果您需要最新的版本，可以在[這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json)找到。這個主題使用 `--template-uri` 參數來呼叫範本，不過您也可以使用 `--template-file` 參數來傳遞本機版本。


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin username"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for Load Balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Backend port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load Balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network Interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP Name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "VNET name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
            }
        },
        "variables": {
            "storageAccountType": "Standard_LRS",
            "vmStorageAccountContainerName": "vhds",
            "availabilitySetName": "myAvSet",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet-1",
            "subnetPrefix": "10.0.0.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
            "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
            "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
            "numberOfInstances": 2,
            "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
            "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
            "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
            "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
            "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
            "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
            "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
            "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
        },
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('storageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
                "copy": {
                    "name": "nicLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]
    
    
                        }
                    ]
    
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "nicLoop",
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LBFE",
                            "properties": {
                                "publicIPAddress": {
                                    "id": "[variables('publicIPAddressID')]"
                                }
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LBBE"
    
                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50001,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        },
                        {
                            "name": "RDP-VM2",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50002,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        }
                    ],
                    "loadBalancingRules": [
                        {
                            "name": "LBRule",
                            "properties": {
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID')]"
                                },
                                "protocol": "tcp",
                                "frontendPort": 80,
                                "backendPort": 80,
                                "enableFloatingIP": false,
                                "idleTimeoutInMinutes": 5,
                                "probe": {
                                    "id": "[variables('lbProbeID')]"
                                }
                            }
                        }
                    ],
                    "probes": [
                        {
                            "name": "tcpProbe",
                            "properties": {
                                "protocol": "tcp",
                                "port": 80,
                                "intervalInSeconds": "5",
                                "numberOfProbes": "2"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
                ],
                "properties": {
                    "availabilitySet": {
                        "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                    },
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "sourceImage": {
                            "id": "[variables('sourceImageName')]"
                        },
                        "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                            }
                        ]
                    }
                }
            }
        ]
    }

### 步驟 2：使用範本建立部署。

使用 `azure group create <location>` 為範本建立資源群組，然後使用 `azure group deployment create` 並傳遞資源群組、部署名稱，然後為範本中沒有預設值的參數，輸入相關的值，就可以在資源群組中建立一個部署。


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup                                               
    + Creating resource group lbgroup                                              
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

現在使用 `azure group deployment create` 命令和 `--template-uri` 選項來部署範本。提示您輸入的時候，開始輸入您的參數值時，如下所示。

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "newdeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value                 
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus                
    data:    newStorageAccountName  String        storagename         
    data:    adminUsername          String        ops                   
    data:    adminPassword          SecureString  undefined             
    data:    dnsNameforLBIP         String        lbdomainname         
    data:    backendPort            Int           3389                  
    data:    vmNamePrefix           String        myVM                  
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer         
    data:    imageSKU               String        2012-R2-Datacenter    
    data:    lbName                 String        myLB                  
    data:    nicNamePrefix          String        nic                   
    data:    publicIPAddressName    String        myPublicIP            
    data:    vnetName               String        myVNET                
    data:    vmSize                 String        Standard_A1           
    info:    group deployment create command OK
    
請注意，這個範本會部署 Windows Server 映像。不過，任何 Linux 映像都可以輕易取代它。想要建立一個跨多個地區的 Docker 叢集嗎？ [您做得到](http://azure.microsoft.com/documentation/templates/201-discover-private-ip-dynamically/)。

## <a id="remove-a-resource-group"></a>工作：移除資源群組

請記住，您可以重新部署至資源群組，但是如果其中一個不想使用了，可以使用 `azure group delete <group name>` 刪除它。

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup                                               
    info:    group delete command OK
    
## <a id="show-the-log-for-a-resource-group-deployment"></a>工作：顯示資源群組部署記錄檔

建立或使用範本時，此種情況很常見。您可以使用 `azure group log show <groupname>` 呼叫來顯示群組的部署日誌，它會顯示相當多的實用資訊，幫助您了解為何發生某些狀況，或者為何未發生某些狀況。(如需疑難排解部署以及其他問題的詳細資訊，請參閱[疑難排解 Azure 的資源群組部署](resource-group-deploy-debug.md))。

例如，為了查明某些異常狀況，您可以使用 **jq** 此類的工具，就可以更清楚掌握前因後果，例如您需要更正的異常狀況。下列範例會使用 **jq** 剖析 **lbgroup** 的部署記錄檔，找出各種異常狀況。

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties' 

您可以快速發現問題出在哪裡，然後予以修正，最後再試一次。在下列情況中，範本已經同時建立兩個 VM，這樣會造成 .vhd 被鎖定。(修改範本後，很快就部署成功)。

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }
    

## <a id="display-information-about-a-virtual-machine"></a>工作：顯示虛擬機器的相關資訊

使用 `azure vm show <groupname> <vmname> command` 可以了解資源群組中特定 VM 的相關資訊。您可能需要列出群組中的 VM，首先，如果 VM 不只一個，可以使用 `azure vm list <groupname>`。

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines                                                     
    data:    Name   ProvisioningState  Location  Size       
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1
    
然後，查閱 myVM1：

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"                                                    
    + Looking up the NIC "nic1"                                                    
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:    
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:    
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK
    

> [AZURE.NOTE]如果您想要以程式設計方式儲存和操作主控台命令的輸出，可以使用 JSON 剖析工具，例如 **[jq](https://github.com/stedolan/jq)**、**[jsawk](https://github.com/micha/jsawk)** 或工作適合的語言程式庫。

## <a id="log-on-to-a-linux-based-virtual-machine"></a>工作：登入 Linux 型虛擬機器

通常 Linux 機器是透過 SSH 連接的。如需詳細資訊，請參閱[如何在 Azure 上的 Linux 使用 SSH](virtual-machines-linux-use-ssh-key.md)。

## <a id="stop-a-virtual-machine"></a>工作： 停止 VM

請執行這個命令：

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]萬一它是雲端服務的最後一個 VM，您可以使用這個參數來保留雲端服務的虛擬 IP (VIP)。<br><br> 如果使用 StayProvisioned 參數，還是需要支付 VM 的費用。

## <a id="start-a-virtual-machine"></a>工作： 啟動 VM

請執行這個命令：

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>工作： 連接資料磁碟

您也需要決定是否要附加新的磁碟或附加已經包含資料的磁碟。如果是新的磁碟，這個命令會建立 .vhd 檔案，然後將它附加在同一個命令中。

若要附加新的磁碟，請執行這個命令：

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb> 

若要附加現有的資料磁碟，請執行這個命令：

    azure vm disk attach <resource-group> <vm-name> [vhd-url]
    
然後您必須先掛接磁碟，就像在 Linux 或 Windows 掛接磁碟一樣。


## 後續步驟

如需其他有關 Azure CLI 搭配 **arm** 模式使用的範例，請參閱[搭配 Azure 資源管理使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure CLI](xplat-cli-azure-resource-manager.md)。若要深入了解 Azure 資源和概念，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

如需您可以使用的其他範本，請參閱 [Azure 快速入門範本](http://azure.microsoft.com/documentation/templates/)和[應用程式架構](virtual-machines-app-frameworks.md)。








 

<!---HONumber=July15_HO2-->