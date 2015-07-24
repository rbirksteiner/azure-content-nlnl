<properties 
   pageTitle="網路資源提供者"
   description="網路資源提供者"
   services="azure-portal"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# 網路資源提供者
現今企業成功的基礎是可以使用靈活、具彈性、安全且可重複的方式來建置和管理大規模網路感知應用程式。Azure 資源管理員 (ARM) 可讓您建立這類應用程式，做為資源群組中的單一資源集合。這類資源是透過 ARM 下的各種資源提供者進行管理。

Azure 資源管理員 (ARM) 可讓您建立這類應用程式和相關聯的網路資源集合，做為資源群組中的單一資源集合。應用程式和網路資源是以 ARM 資源群組中的單一單元形式執行。

您可以使用下列任何管理介面來管理網路資源：

- REST 型 API
- PowerShell
- .NET SDK
- Node.JS SDK
- Java SDK
- Azure CLI
- Azure 入口網站
- ARM 範本語言

隨著網路資源提供者的引進，您可以利用下列優點：

- **中繼資料** - 您可以使用標記新增資源的資訊。這些標記可以用來追蹤資源群組和訂用帳戶中的資源使用。
- **更進一步控制網路** - 網路資源是進行鬆散偶合，而您可以更精密地控制它們。這表示可以更彈性地管理網路資源。
- **設定更快速** - 因為網路資源是進行鬆散偶合，所以您可以平行建立並協調網路資源。這已經大幅減少設定時間。
- **角色型存取控制** - 除了允許建立自訂角色進行安全管理之外，RBAC 還提供具有特定安全性範圍的預設角色。 
- **更易於管理和部署** - 更容易部署和管理應用程式，因為您可以建立整個應用程式堆疊做為資源群組中的單一資源集合。而且可以更快速地部署，因為只要提供範本 JSON 裝載即可進行部署。
- **快速自訂** - 您可以使用宣告樣式範本進行可重複且快速自訂的部署。 
- **可重複自訂** - 您可以使用宣告樣式範本進行可重複且快速自訂的部署。

## 網路資源 
您現在可以獨立地管理網路資源，而不是透過單一計算資源 (虛擬機器) 進行管理。這確保具有較高程度的彈性和靈活性可在資源群組中撰寫複雜且大型的基礎結構。
 
下圖說明網路資源模型和其關聯的高階檢視。最上層資源會加上藍色外框。除了最上層資源之外，您還可以看到加上灰色外框的子資源。您可以個別管理每個資源。

![網路資源模型](./media/resource-group-networking/Figure1.png)

含有多介層應用程式之範例部署的概念檢視顯示如下。所有網路資源都會加上藍色外框。

![網路資源模型](./media/resource-group-networking/Figure2.png)

## REST API 
如前所述，網路資源可以透過各種介面進行管理，包括 REST API、.NET SDK、Node.JS SDK、Java SDK、PowerShell、CLI、Azure 入口網站和範本。

Rest API 符合 HTTP 1.1 通訊協定規格。API 的一般 URI 結構顯示如下：

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

而以大括弧括住的參數代表下列元素：

- **subscription-id** - Azure 訂用帳戶識別碼。
- **resource-provider-namespace** - 正在使用之提供者的命名空間。網路資源提供者的值是 *Microsoft.Network*。
- **region-name** - Azure 區域名稱

呼叫 REST API 時，支援下列 HTTP 方法：

- **PUT** - 用來建立所指定類型的資源、修改資源屬性，或變更資源之間的關聯。 
- **GET** - 用來擷取已佈建資源的資訊。
- **DELETE** - 用來刪除現有資源。

要求和回應都符合 JSON 裝載格式。如需詳細資料，請參閱 [Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)。

## ARM 範本語言
除了以命令方式 (透過 API 或 SDK) 管理資源之外，您還可以使用宣告式程式設計樣式，利用 ARM 範本語言建置和管理網路資源。

範本的範例表示法提供如下 –

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

範本主要是透過參數所插入之資源和執行個體值的 JSON 描述。下列範例可以用來建立具有 2 個子網路的虛擬網路。

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

您可以選擇在使用範本時手動提供參數值，也可以使用參數檔案。下列範例顯示一組可能的參數值以與上述範本搭配使用：

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


使用範本的主要優點如下：

- 您可以透過宣告樣式在資源群組中建置複雜的基礎結構。建立資源 (包括相依性管理) 的協調是由 ARM 所處理。 
- 只要變更參數，即可透過可重複的方式在各種區域和某個區域內建立基礎結構。 
- 宣告樣式可讓建置範本以及推出基礎結構的前置時間變短。 

如需範例範本，請參閱 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)。

如需 ARM 範本語言的詳細資訊，請參閱 [Azure 資源管理員範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。

上述範例範本使用虛擬網路和子網路資源。下列是其他您可以使用的網路資源：

## NIC
網路介面卡或 NIC 代表可以關聯到虛擬機器 (VM) 的網路介面。一個 VM 可以有一或多個 NIC。

![單一 VM 上的 NIC](./media/resource-group-networking/Figure3.png)

NIC 資源的重要屬性包括：

- IP 設定

NIC 也可以與下列網路資源相關聯：

- 網路安全性群組 (NSG) 
- 負載平衡器

## 虛擬網路和子網路
虛擬網路 (VNET) 和子網路有助於定義在 Azure 中執行之工作負載的安全性範圍。VNET 具備位址空間 (也稱為 CIDR 區塊)。

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。本質上執行各種工作負載的 VM 是在子網路界限中運作。

![單一 VM 上的 NIC](./media/resource-group-networking/Figure4.png)

VNET 資源的重要屬性包括：

- IP 位址空間 (CIDR 區塊) 
- VNET 名稱
- 子網路

VNET 也可以與下列網路資源相關聯：

- VPN 閘道

子網路的重要屬性包括：

- IP 位址首碼
- 子網路名稱

子網路也可以與下列網路資源相關聯：

- NSG

## 負載平衡器
當您想要調整應用程式時，會使用負載平衡器。典型部署案例包含在多個 VM 執行個體上執行的應用程式。VM 執行個體受到負載平衡器保護，以協助將網路流量散發到各種執行個體。

![單一 VM 上的 NIC](./media/resource-group-networking/Figure5.png)

負載平衡器包含下列子資源：

- **前端 IP 組態** - 負載平衡器可以包括一個或多個前端 IP 位址 (亦稱為虛擬 IP (VIP))。這些 IP 位址做為流量的輸入。 
- **後端位址集區** - 這些是與 VM NIC 相關聯的 IP 位址，而負載會散發到 VM NIC。
- **負載平衡規則** - 規則屬性會將指定的前端 IP 與連接埠組合對應到一組後端 IP 位址與連接埠組合。使用負載平衡器資源的單一定義，您可以定義多個負載平衡規則，而每個規則都會反映與 VM 相關聯的前端 IP 與連接埠以及後端 IP 與連接埠組合。 
- **探查** - 探查可讓您追蹤 VM 執行個體的健全狀況。如果健全狀況探查失敗，則 VM 執行個體不會自動進入輪替。
- **輸入 NAT 規則** - 定義流經前端 IP 並散發到後端 IP 之輸入流量的 NAT 規則。

## 公用 IP
公用 IP 位址資源提供保留或動態公用 IP 位址。公用 IP 位址可以指派給負載平衡器或 NAT，或是與 VM 之 NIC 上的私用 IP 位址相關聯。

公用 IP 資源的重要屬性包括：

- **IP 配置方法** - 保留或動態。 

## 網路安全性群組 (NSG)
透過實作允許和拒絕規則，NSG 資源會建立工作負載的安全性界限。這類規則可以套用於 NIC 層級 (VM 執行個體層級) 或子網路層級 (VM 群組)。

NSG 資源的重要屬性包括：

- **安全性規則** - NSG 可以定義多個安全性規則。每個規則都可以允許或拒絕不同類型的流量。

## 安全性規則
安全性規則是 NSG 的子資源。

安全性規則的重要屬性包括：

- **通訊協定** - 套用此規則的網路通訊協定。
- **來源連接埠範圍** - 來源連接埠，或 0 到 65535 的範圍。萬用字元可以用來比對所有連接埠。 
- **目的地連接埠範圍** - 目的地連接埠，或 0 到 65535 的範圍。萬用字元可以用來比對所有連接埠。
- **來源位址首碼** - 來源 IP 位址範圍。 
- **目的地位址首碼** - 目的地 IP 位址範圍。
- **存取** -「*允許*」或「*拒絕*」流量。
- **優先順序** - 100 與 4096 之間的值。安全性規則集合中每一個規則的優先順序號碼必須是唯一的。優先順序號碼愈小，規則優先順序就愈高。
- **方向** - 指定以「*輸入*」還是「*輸出*」方向，將規則套用到流量。 

## VPN 閘道 
VPN 閘道資源可讓您建立其內部部署資料中心與 Azure 之間的安全連線。您可以使用三種不同的方式設定 VPN 閘道資源：
 
- **指向網站** - 您可以從任何電腦使用 VPN 用戶端，安全地存取 VNET 中所裝載的 Azure 資源。 
- **多網站連線** - 您可以安全地從內部部署資料中心連接到 VNET 中執行的資源。 
- **VNET 對 VNET** - 您可以安全地跨相同區域內的 Azure VNET 或跨區域連接，以建置異地備援的工作負載。

VPN 閘道的重要屬性包括：
 
- **閘道類型** - 動態路由閘道或靜態路由閘道。 
- **VPN 用戶端位址集區首碼** - 要指派給用戶端的 IP 位址，而用戶端透過指向網站組態進行連接。

## 流量管理員設定檔
流量管理員和其子端點資源會將您的流量散發到 Azure 內部和 Azure 外部的端點。這類流量散發是透過原則進行控管。流量管理員也可監視端點健全狀況，並根據端點健全狀況適當地轉向流量。

流量管理員設定檔的重要屬性包括：

- **流量路由方法** - 可能的值為「*效能*」、「*加權*」和「*優先順序*」。
- **DNS 組態** - 設定檔的 FQDN。
- **通訊協定** - 監視通訊協定，可能的值為 *HTTP* 和 *HTTPS*。
- **連接埠** - 監視連接埠。 
- **路徑** - 監視路徑。
- **端點** - 端點資源的容器。

## 端點 
端點是流量管理員設定檔的子資源。它代表服務或 Web 端點，並根據流量管理員設定檔資源中所設定的原則，將使用者流量散發到此服務或 Web 端點。

端點的重要屬性包括：
 
- **類型** - 端點的類型，可能的值為「*Azure 端點*」、「*外部端點*」和「*巢狀端點*」。 
- **目標資源識別碼** - 服務或 Web 端點的公用 IP 位址。這可以是 Azure 或外部端點。
- **加權** - 用於流量管理的端點加權。 
- **優先順序** - 端點的優先順序，用來定義容錯移轉動作。 

## 使用範本

您可以使用 PowerShell 或 AzureCLI 從範本部署服務到 Azure，或執行按一下動作來從 GitHub 部署。若要在 GitHub 中從範本部署服務，請執行下列步驟：

1. 從 GitHub 開啟 template3 檔案。在此範例中，請開啟[具有兩個子網路的虛擬網路](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)。
2. 按一下 [部署至 Azure]，然後使用您的認證登入 Azure 入口網站。
3. 驗證範本，然後按一下 [儲存]。
4. 按一下 [編輯參數]並選取位置 (例如 [West US]) 做為 vnet 和子網路。
5. 如有必要，請變更 [ADDRESSPREFIX] 和 [SUBNETPREFIX] 參數，然後按一下 [確定]。
6. 按一下 [選取資源群組]，然後按一下您想要對其新增 vnet 和子網路的資源群組。或者，您可以按一下 [或建立新的] 來建立新的資源群組。
3. 按一下 [建立]。請注意顯示 [佈建範本部署] 的磚。完成部署之後，您將會看到類似下面所示的畫面。

![範例範本部署](./media/resource-group-networking/Figure6.png)

## 另請參閱

[Azure 網路 API 參考](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Azure PowerShell 網路參考](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure 資源管理員範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)

[Azure 網路 - 常用範本](https://github.com/Azure/azure-quickstart-templates)

[計算資源提供者](../virtual-machines-azurerm-versus-azuresm)

[Azure 資源管理員概觀](../resource-group-overview)

[Azure 資源管理員中的角色存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[在 Azure 資源管理員中使用標記](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[範本部署](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=62-->