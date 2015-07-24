<properties
	pageTitle="搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理 | Microsoft Azure"
	description="了解如何使用適用於 Mac、Linux 和 Windows 的 Azure CLI，以便使用 Azure CLI arm 模式來管理 Azure 資源。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# 使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理

本主題描述如何使用 **arm** 模式中的 Azure 命令列介面 (Azure CLI)，在 Mac、Linux 和 Windows 電腦的命令列上建立、管理和刪除服務。您可以使用 Azure SDK 的各種程式庫、透過 PowerShell，以及使用 Azure 入口網站來執行相同的工作。

Azure 資源管理可讓您建立資源群組，包含虛擬機器、網站、資料庫等，並作為單一部署單位。然後，您可以透過單一、協調的工作來部署、更新或刪除應用程式的所有資源。您會在 JSON 範本中描述部署的群組資源，然後可以將該範本用於不同的環境，例如測試、預備和生產環境。

## 命令式和宣告式方法

如同[服務管理模式 (**asm**)](../virtual-machines-command-line-tools.md)，Azure CLI 的 **arm** 模式可讓您在命令列以命令方式建立資源。例如，若您輸入 `azure group create <groupname> <location>` 表示要求 Azure 建立資源群組，若輸入 `azure group deployment create <resourcegroup> <deploymentname>` 表示指示 Azure 建立任意數目的項目部署，並將其放在群組中。因為每一種資源類型皆具有命令式指令，您可以將他們鏈結在一起以建立相當複雜的部署。

不過，使用說明資源群組是以更強大宣告式方法所建立的資源群組_範本_時，可讓您基於 (幾乎) 任何用途自動進行 (幾乎) 任意資源數目的複雜部署。使用範本時，唯一的命令式指令是進行部署。如需範本、資源及資源群組的一般概觀，請參閱＜[Azure 資源群組概觀](resource-groups-overview)＞。

> [AZURE.NOTE]除了下述和命令列上的命令特定選項，還有三個選項可用來檢視詳細輸出，例如要求選項和狀態碼。-v 參數提供詳細資訊輸出，而 -vv 參數提供更詳細的詳細資訊輸出。--json 選項將會以原始 JSON 格式輸出結果，這對於指令碼案例相當實用。
>
> 使用 --json 參數的使用方式相當常見，同時對於取得和了解傳回資源資訊、狀態和記錄檔的 Azure CLI 工作結果，以及使用範本也是重要的部分。您可能想要安裝 JSON 剖析器工具，例如 **jq** 或 **jsawk**，或使用您偏好的語言程式庫。

##使用需求

透過 Azure CLI 使用 **arm** 模式的設定需求為：

- 一個 Azure 帳戶 ([在此取得免費試用](http://azure.microsoft.com/pricing/free-trial/))
- [安裝 Azure CLI](../xplat-cli-install.md)
- [設定 Azure CLI](../xplat-cli-connect.md) 以使用 Azure Active Directory 身分識別或服務主體

一旦您擁有帳戶並已安裝 Azure CLI，您必須

- 輸入 `azure config mode arm` 以切換至 **arm** 模式。
- 輸入 `azure login` 來登入您的 Azure 帳戶，並依照提示使用您工作或學校的身分識別

現在請輸入 `azure` 以查看下列各節中所述的最上層命令清單。

## azure account：用來管理帳戶資訊及發佈設定
此工具會使用您的 Azure 訂閱資訊來連線至您的帳戶。您可以從 Azure 入口網站取得這些資訊 (在發佈設定檔中，本文將有說明)。您可以匯入發佈設定檔，作為這個工具後續作業所使用的持續性本機組態設定。您的發佈設定只需匯入一次即可。

**列出匯入的訂用帳戶**

	account list [options]

**顯示關於訂用帳戶的詳細資料**

	account show [options] [subscriptionNameOrId]

**設定目前的訂用帳戶**

	account set [options] <subscriptionNameOrId>

**移除訂用帳戶或環境，或清除所有儲存的帳戶和環境資訊**

	account clear [options]

**用來管理帳戶環境的命令**

	account env list [options]
	account env show [options] [environment]
	account env add [options] [environment]
	account env set [options] [environment]
	account env delete [options] [environment]

## azure ad：用來顯示 Active Directory 物件的命令

**用來顯示 Active Directory 應用程式的命令**

	ad app create [options]
	ad app delete [options] <object-id>

**用來顯示 Active Directory 群組的命令**

	ad group list [options]
	ad group show [options]

**用來提供 Active Directory 子群組或成員資訊的命令**

	ad group member list [options] [objectId]

**用來顯示 Active Directory 服務主體的命令**

	ad sp list [options]
	ad sp show [options]
	ad sp create [options] <application-id>
	ad sp delete [options] <object-id>

**用來顯示 Active Directory 使用者的命令**

	ad user list [options]
	ad user show [options]

## azure availset：用來管理可用性設定組的命令

**在資源群組內建立可用性設定組**

	availset create [options] <resource-group> <name> <location> [tags]

**在資源群組內列出可用性設定組**

	availset list [options] <resource-group>

**在資源群組內取得一個可用性設定組**

	availset show [options] <resource-group> <name>

**在資源群組內刪除一個可用性設定組**

	availset delete [options] <resource-group> <name>

## azure config：用來管理本機設定的命令

**列出 Azure CLI 組態設定**

	config list [options]

**刪除組態設定**

	config delete [options] <name>

**更新組態設定**

	config set <name> <value>

**設定 Azure CLI 工作模式為 `arm` 或 `asm`**

	config mode [options] <modename>


## azure feature：用來管理帳戶功能的命令

**列出您訂用帳戶可用的所有功能**

	feature list [options]

**顯示功能**

	feature show [options] <providerName> <featureName>

**註冊資源提供者的預覽功能**

	feature register [options] <providerName> <featureName>

## azure group：用來管理資源群組的命令

**建立新的資源群組**

	group create [options] <name> <location>

**為資源群組設定標記**

	group set [options] <name> <tags>

**刪除資源群組**

	group delete [options] <name>

**列出您訂用帳戶的資源群組**

	group list [options]

**顯示您訂用帳戶的資源群組**

	group show [options] <name>

**用來管理資源群組記錄檔的命令**

	group log show [options] [name]

**用來管理資源群組中部署的命令**

	group deployment create [options] [resource-group] [name]
	group deployment list [options] <resource-group> [state]
	group deployment show [options] <resource-group> [deployment-name]
	group deployment stop [options] <resource-group> [deployment-name]

**用來管理本機或資源庫資源群組範本的命令**

	group template list [options]
	group template show [options] <name>
	group template download [options] [name] [file]
	group template validate [options] <resource-group>

## azure insights：用來監視 Insights (事件、警示規則、自動調整設定和度量) 的命令

**擷取訂用帳戶、CorrelationID、資源群組、資源或資源提供者的作業記錄**

	insights logs list [options]

## azure location：用來取得所有資源類型可用位置的命令

**列出可用的位置**

	location list [options]

## azure network：用來管理網路資源的命令

**用來管理虛擬網路的命令**

	network vnet create [options] <resource-group> <name> <location>
允許建立新的虛擬網路。在下列範例中，我們會針對美國西部區域中的 myresourcegroup 資源群組，建立名為 newvnet 的虛擬網路。


	azure network vnet create myresourcegroup newvnet "west us"
	info:    Executing command network vnet create
	+ Looking up virtual network "newvnet"
	+ Creating virtual network "newvnet"
	 Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet create command OK


參數選項：

 	-h, --help                                 output usage information
 	-v, --verbose                              use verbose output
	--json                                     use json output
 	-g, --resource-group <resource-group>      the name of the resource group
 	-n, --name <name>                          the name of the virtual network
 	-l, --location <location>                  the location
 	-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

	-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
 	-t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
	 -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet set [options] <resource-group> <name>

更新資源群組內的虛擬網路組態。

	azure network vnet set myresourcegroup newvnet

	info:    Executing command network vnet set
	+ Looking up virtual network "newvnet"
	+ Updating virtual network "newvnet"
	+ Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet set command OK

參數選項：

	   -h, --help                                 output usage information
	   -v, --verbose                              use verbose output
	   --json                                     use json output
	   -g, --resource-group <resource-group>      the name of the resource group
	   -n, --name <name>                          the name of the virtual network
	   -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

	   -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

	   -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

	   --no-tags                                  remove all existing tags
	   -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet list [options] <resource-group>

此命令可讓您列出資源群組中的所有虛擬網路。


	C:\>azure network vnet list myresourcegroup

	info:    Executing command network vnet list
	+ Listing virtual networks
		data:    ID
       Name      Location  Address prefixes  DNS servers
	data:    -------------------------------------------------------------------
	------  --------  --------  ----------------  -----------
	data:    /subscriptions/###############################/resourceGroups/
	wvnet   newvnet   westus    10.0.0.0/8
	info:    network vnet list command OK

參數選項：


      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

	network vnet show [options] <resource-group> <name>
此命令會顯示資源群組中的虛擬網路屬性。

	azure network vnet show -g myresourcegroup -n newvnet

	info:    Executing command network vnet show
	+ Looking up virtual network "newvnet"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet show command OK
<BR>

	network vnet delete [options] <resource-group> <name>
此命令會移除虛擬網路。

	azure network vnet delete myresourcegroup newvnetX

	info:    Executing command network vnet delete
	+ Looking up virtual network "newvnetX"
	Delete virtual network newvnetX? [y/n] y
	+ Deleting virtual network "newvnetX"
	info:    network vnet delete command OK

參數選項：

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**用來管理虛擬網路子網路的命令**

	network vnet subnet create [options] <resource-group> <vnet-name> <name>
可讓您將其他子網路加入至現有虛擬網路的命令。

	azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

	info:    Executing command network vnet subnet create
	+ Looking up the subnet "subnet"
	+ Creating subnet "subnet"
	+ Looking up the subnet "subnet"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Name:                      subnet
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet create command OK

參數選項：

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
		 --json                                                           use json output
	 -g, --resource-group <resource-group>                            the name of the resource group
	 -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

	network vnet subnet set [options] <resource-group> <vnet-name> <name>

設定資源群組內的特定虛擬網路子網路。


	C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet list [options] <resource-group> <vnet-name>

列出資源群組內適用於特定虛擬網路的所有虛擬網路子網路。

	azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet show [options] <resource-group> <vnet-name> <name>
顯示虛擬網路子網路屬性

	azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet show
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
	.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet show command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-e, --vnet-name <vnet-name>            the name of the virtual network
	-n, --name <name>                      the name of the subnet
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
從現有的虛擬網路移除子網路。

	azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet delete
	+ Looking up the subnet "subnet1"
	Delete subnet "subnet1"? [y/n] y
	+ Deleting subnet "subnet1"
	info:    network vnet subnet delete command OK

參數選項：

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-e, --vnet-name <vnet-name>            the name of the virtual network
 	-n, --name <name>                      the subnet name
 	-s, --subscription <subscription>      the subscription identifier
 	-q, --quiet                            quiet mode, do not ask for delete confirmation

**用來管理負載平衡器的命令**

	network lb create [options] <resource-group> <name> <location>
建立負載平衡器集合。

	azure network lb create -g myresourcegroup -n mylb -l westus

	info:    Executing command network lb create
	+ Looking up the load balancer "mylb"
	+ Creating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb create command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-l, --location <location>              the location
	-t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb list [options] <resource-group>
列出資源群組中負載平衡器資源。

	azure network lb list myresourcegroup

	info:    Executing command network lb list
	+ Getting the load balancers
	data:    Name  Location
	data:    ----  --------
	data:    mylb  westus
	info:    network lb list command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb show [options] <resource-group> <name>

顯示資源群組中特定負載平衡器的負載平衡器資訊

	azure network lb show myresourcegroup mylb -v

	info:    Executing command network lb show
	verbose: Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb show command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb delete [options] <resource-group> <name>

刪除負載平衡器資源。

	azure network lb delete  myresourcegroup mylb

	info:    Executing command network lb delete
	+ Looking up the load balancer "mylb"
	Delete load balancer "mylb"? [y/n] y
	+ Deleting load balancer "mylb"
	info:    network lb delete command OK

參數選項：

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-n, --name <name>                      the name of the load balancer
 	-q, --quiet                            quiet mode, do not ask for delete confirmation
 	-s, --subscription <subscription>      the subscription identifier

**用來管理負載平衡器探查的命令**

	network lb probe create [options] <resource-group> <lb-name> <name>

建立負載平衡器中健康狀態的探查組態。請記住，若要執行此命令，您的負載平衡器需要前端 IP 資源 (查看「azure network frontend-ip」命令以將 IP 位址指派至負載平衡器)。

	azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

	info:    Executing command network lb probe create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe create command OK

參數選項：

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-p, --protocol <protocol>              the probe protocol
	-o, --port <port>                      the probe port
	-f, --path <path>                      the probe path
	-i, --interval <interval>              the probe interval in seconds
	-c, --count <count>                    the number of probes
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb probe set [options] <resource-group> <lb-name> <name>

使用適用的新值來更新現有的負載平衡器探查。

	azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

	info:    Executing command network lb probe set
		+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe set command OK

參數選項

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-e, --new-probe-name <new-probe-name>  the new name of the probe
	-p, --protocol <protocol>              the new value for probe protocol
	-o, --port <port>                      the new value for probe port
	-f, --path <path>                      the new value for probe path
	-i, --interval <interval>              the new value for probe interval in seconds
	-c, --count <count>                    the new value for number of probes
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb probe list [options] <resource-group> <lb-name>

列出負載平衡器集合的探查屬性。

	C:\>azure network lb probe list -g myresourcegroup -l mylb

	info:    Executing command network lb probe list
	+ Looking up the load balancer "mylb"
	data:    Name       Protocol  Port  Path  Interval  Count
	data:    ---------  --------  ----  ----  --------  -----
	data:    mylbprobe  Tcp       443         300       2
	info:    network lb probe list command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier


	network lb probe delete [options] <resource-group> <lb-name> <name>
移除針對負載平衡器所建立的探查。

	azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

	info:    Executing command network lb probe delete
	+ Looking up the load balancer "mylb"
	Delete a probe "mylbprobe?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb probe delete command OK

**用來管理負載平衡器前端 IP 組態的命令**

	network lb frontend-ip create [options] <resource-group> <lb-name> <name>
建立前端 IP 組態至現有的負載平衡器集合。

	azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

	info:    Executing command network lb frontend-ip create
	+ Looking up the load balancer "mylb"
	+ Looking up the subnet "subnet"
	+ Creating frontend IP configuration "myfrontendip"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:           10.0.1.4
	data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Public IP address:
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip create command OK

<BR>

	network lb frontend-ip set [options] <resource-group> <lb-name> <name>

可讓您更新前端 IP 的現有組態。下列命令會將名為 mypubip5 的公用 IP 新增至名為 myfrontendip 的現有負載平衡器前端 IP。

	azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

	info:    Executing command network lb frontend-ip set
	+ Looking up the load balancer "mylb"
	+ Looking up the public ip "mypubip5"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:
	data:    Subnet:
	data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip set command OK

參數選項：

	-h, --help                                                         output usage information
	-v, --verbose                                                      use verbose output
	--json                                                             use json output
	-g, --resource-group <resource-group>                              the name of the resource group
	-l, --lb-name <lb-name>                                            the name of the load balancer
	-n, --name <name>                                                  the name of the frontend ip configuration
	-a, --private-ip-address <private-ip-address>                      the private ip address
	-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
	-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-i, --public-ip-name <public-ip-name>                              the public ip name.
	This public ip must exist in the same resource group as the lb.
	Please use public-ip-id if that is not the case.
	-b, --subnet-id <subnet-id>                                        the subnet id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
	-e, --subnet-name <subnet-name>                                    the subnet name
	-m, --vnet-name <vnet-name>                                        the virtual network name.
	This virtual network must exist in the same resource group as the lb.
	Please use subnet-id if that is not the case.
	-s, --subscription <subscription>                                  the subscription identifier

<BR>

	network lb frontend-ip list [options] <resource-group> <lb-name>

列出針對負載平衡器所設定的前端 IP 資源。

	azure network lb frontend-ip list -g myresourcegroup -l mylb

	info:    Executing command network lb frontend-ip list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Private IP allocation method  Subnet
	data:    -----------  ------------------  ----------------------------  ------
	data:    myprivateip  Succeeded           Dynamic
	info:    network lb frontend-ip list command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
刪除與負載平衡器相關聯的前端 IP 物件

	network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
	info:    Executing command network lb frontend-ip delete
	+ Looking up the load balancer "mylb"
	Delete frontend ip configuration "myfrontendip"? [y/n] y
	+ Updating load balancer "mylb"

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the frontend ip configuration
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**用來管理負載平衡器後端位址集區的命令**

	network lb address-pool create [options] <resource-group> <lb-name> <name>

建立負載平衡器的後端位址集區。

	azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

	info:    Executing command network lb address-pool create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
	data:    Name:                      myaddresspool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool create command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool add [options] <resource-group> <lb-name> <name>

負載平衡器可透過後端位址集區範圍，以便判斷哪些資源使用 Azure 資源管理員以路由傳送來自其端點的傳入網路流量。建立並命名後端位址集區範圍之後 (請參閱 azure network lb address-pool create」命令)，您需要根據現在已由名為「網路介面」的資源所定義的方式來新增端點。

若要設定的後端位址範圍，您需要至少一個「網路介面」(請參閱「azure network lb nic」命令列以取得更多詳細資訊)。

在下列範例中，其使用先前建立的「nic1」網路介面來建立後端位址集區範圍。

	azure network lb address-pool add -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool add
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:     id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/networkInterfaces/nic1/ipConfigurations/NIC-config
	data:    Load balancing rules:
	data:
	info:    network lb address-pool add command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool remove [options] <resource-group> <lb-name> <name>

移除來自後端 IP 位址集區範圍的網路介面。

	azure network lb address-pool remove -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool remove
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool remove command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb address-pool list [options] <resource-group> <lb-name>

列出特定資源群組的後端 IP 位址集區範圍

	azure network lb address-pool list -g myresourcegroup -l mylb

	info:    Executing command network lb address-pool list
	+ Looking up the load balancer "mylb"
	data:    Name           Provisioning state
	data:    -------------  ------------------
	data:    mybackendpool  Succeeded
	info:    network lb address-pool list command OK

參數選項：

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-l, --lb-name <lb-name>                the name of the load balancer
 	-s, --subscription <subscription>      the subscription identifier

<BR> network lb address-pool delete [options] <resource-group> <lb-name> <name>

移除來自負載平衡器的後端 IP 集區範圍資源。

	azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

	info:    Executing command network lb address-pool delete
	+ Looking up the load balancer "mylb"
	Delete backend address pool "mybackendpool"? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb address-pool delete command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**用來管理負載平衡器規則的命令**

	network lb rule create [options] <resource-group> <lb-name> <name>
建立負載平衡器規則。

您可以建立負載平衡器規則，以便設定負載平衡器的前端端點，以及將會接收傳入網路流量的後端位址集區範圍。設定也包含前端 IP 端點的連接埠以及後端位址集區範圍的連接埠。

下列範例顯示如何建立負載平衡器規則、接聽連接埠 80 TCP 的前端端點，以及針對後端位址集區範圍傳送至連接埠 8080 的負載平衡網路流量。

	azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


	info:    Executing command network lb rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
	data:    Name:                      mylbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule create command OK

<BR>

	network lb rule set [options] <resource-group> <lb-name> <name>

更新特定資源群組中的現有負載平衡器規則設定。在下列範例中，我們會將規則名稱從 mylbrule 變更為 mynewlbrule。

	azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

	info:    Executing command network lb rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
	data:    Name:                      mynewlbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule set command OK

參數選項：

	-h, --help                                         output usage information
	-v, --verbose                                      use verbose output
	--json                                             use json output
	-g, --resource-group <resource-group>              the name of the resource group
	-l, --lb-name <lb-name>                            the name of the load balancer
	-n, --name <name>                                  the name of the rule
	-r, --new-rule-name <new-rule-name>                new rule name
	-p, --protocol <protocol>                          the rule protocol
	-f, --frontend-port <frontend-port>                the frontend port
	-b, --backend-port <backend-port>                  the backend port
	-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
	-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
	-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
	-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
	-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
	-s, --subscription <subscription>                  the subscription identifier


	network lb rule list [options] <resource-group> <lb-name>

列出特定資源群組中針對負載平衡器所設定的所有負載平衡器規則。

	azure network lb rule list -g myresourcegroup -l mylb

	info:    Executing command network lb rule list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

	data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	info:    network lb rule list command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

	network lb rule delete [options] <resource-group> <lb-name> <name>

刪除負載平衡器規則。

	azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

	info:    Executing command network lb rule delete
	+ Looking up the load balancer "mylb"
	Delete load balancing rule mynewlbrule? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb rule delete command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**用來管理負載平衡器輸入 NAT 規則的命令**

	network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
建立負載平衡器的輸入 NAT 規則。

在以下範例中，我們會從前端 IP (這個部分先前已定義。請參閱「azure network frontend-ip」命令以取得更多詳細資料) 使用輸入接聽連接埠和輸出連接埠來建立 NAT 規則，其中負載平衡器將會傳送網路流量。


	azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              80
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule create command OK

參數選項：

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id <vm-id>                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
	Please use vm-id if that is not the case.
	this parameter will be ignored if --vm-id is specified
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
更新現有的輸入 NAT 規則。在下列範例中，我們會將輸入接聽連接埠從 80 變更為 81。

	azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              81
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule set command OK

參數選項：

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id [vm-id]                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.
	This virtual machine must exist in the same resource group as the lb.
	Please use vm-id if that is not the case
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule list [options] <resource-group> <lb-name>

列出負載平衡器的所有輸入 NAT 規則。

	azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

	info:    Executing command network lb inbound-nat-rule list
	+ Looking up the load balancer "mylb"
	data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
	data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
	---------------------
	data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

	info:    network lb inbound-nat-rule list command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

刪除特定資源群組中負載平衡器的 NAT 規則。

	azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

	info:    Executing command network lb inbound-nat-rule delete
	+ Looking up the load balancer "mylb"
	Delete inbound NAT rule "myinboundnat?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb inbound-nat-rule delete command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the inbound NAT rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**用來管理公用 IP 位址的命令**

	network public-ip create [options] <resource-group> <name> <location>
建立公用 IP 資源。您將建立公用 IP 資源，並與網域名稱建立關聯。

	azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
	info:    Executing command network public-ip create
	+ Looking up the public ip "mytestpublicip1"
	+ Creating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Dynamic
	data:    Idle timeout:         4
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip create command OK


參數選項：-h, --help 輸出使用量資訊 -v, --verbose 使用詳細資訊輸出 --json 使用 JSON 輸出 -g, --resource-group <resource-group> 資源群組的名稱 -n, --name <name> 公用 IP 的名稱 -l, --location <location> 位置 -d, --domain-name-label <domain-name-label> 網域名稱標籤。這會將 DNS 設定為 <domain-name-label>.<location>.cloudapp.azure.com -a, --allocation-method <allocation-method> 配置方法 [Static][Dynamic] -i, --idletimeout <idletimeout> 以分鐘為單位的閒置逾時 -f, --reverse-fqdn <reverse-fqdn> 反向 FQDN -t, --tags <tags> 標記的清單。可以使用多個選項。格式為「名稱 = 值」。名稱為必填，而值為選擇性項目。例如，-t tag1=value1;tag2 -s, --subscription <subscription> 訂用帳戶識別碼 <br>

	network public-ip set [options] <resource-group> <name>
更新現有公用 IP 資源的屬性。在下列範例中，我們會將公用 IP 位址從動態變更為靜態。

	azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
	info:    Executing command network public-ip set
	+ Looking up the public ip "mytestpublicip1"
	+ Updating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip set command OK

參數選項：

	-h, --help                                   output usage information
	-v, --verbose                                use verbose output
	--json                                       use json output
	-g, --resource-group <resource-group>        the name of the resource group
	-n, --name <name>                            the name of the public ip
	-d, --domain-name-label [domain-name-label]  the domain name label.
	This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
	-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
	-i, --idletimeout <idletimeout>              the idle timeout in minutes
	-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
	-t, --tags <tags>                            the list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	--no-tags                                    remove all existing tags
	-s, --subscription <subscription>            the subscription identifier

<br> network public-ip list [options] <resource-group> 列出資源群組中的所有公用 IP 資源。

	azure network public-ip list -g myresourcegroup

	info:    Executing command network public-ip list
	+ Getting the public ip addresses
	data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
	data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
	data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
	data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR> network public-ip show [options] <resource-group> <name> 顯示資源群組中公用 IP 資源的公用 IP 屬性。

	azure network public-ip show -g myresourcegroup -n mytestpublicip

	info:    Executing command network public-ip show
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
	data:    Name:                 mytestpublicip
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip show command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-s, --subscription <subscription>      the subscription identifier


	network public-ip delete [options] <resource-group> <name>

刪除公用 IP 資源。

	azure network public-ip delete -g group-1 -n mypublicipname
	info:    Executing command network public-ip delete
	+ Looking up the public ip "mypublicipname"
	Delete public ip address "mypublicipname"? [y/n] y
	+ Deleting public ip address "mypublicipname"
	info:    network public-ip delete command OK

參數選項：

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier


**用來管理網路介面的命令**

	network nic create [options] <resource-group> <name> <location>
建立稱為網路介面 (NIC) 的資源，可用於負載平衡器或與虛擬機器建立關聯。

	azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

	info:    Executing command network nic create
	+ Looking up the network interface "testnic1"
	+ Looking up the subnet "subnet-1"
	+ Creating network interface "testnic1"
	+ Looking up the network interface "testnic1"
	data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
	data:    Name:                   testnic1
	data:    Type:                   Microsoft.Network/networkInterfaces
	data:    Location:               eastus
	data:    Provisioning state:     Succeeded
	data:    IP configurations:
	data:       Name:                         NIC-config
	data:       Provisioning state:           Succeeded
	data:       Private IP address:           10.0.0.5
	data:       Private IP Allocation Method: Dynamic
	data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

參數選項：

	-h, --help                                                       output usage information
	-v, --verbose                                                    use verbose output
	--json                                                           use json output
	-g, --resource-group <resource-group>                            the name of the resource group
	-n, --name <name>                                                the name of the network interface
	-l, --location <location>                                        the location
	-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
	-o, --network-security-group-name <network-security-group-name>  the network security group name.
	This network security group must exist in the same resource group as the nic.
	Please use network-security-group-id if that is not the case.
	-i, --public-ip-id <public-ip-id>                                the public IP identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-p, --public-ip-name <public-ip-name>                            the public IP name.
	This public ip must exist in the same resource group as the nic.
	Please use public-ip-id if that is not the case.
	-a, --private-ip-address <private-ip-address>                    the private IP address
	-u, --subnet-id <subnet-id>                                      the subnet identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
	--subnet-name <subnet-name>                                  the subnet name
	-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
	-t, --tags <tags>                                                the comma seperated list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	-s, --subscription <subscription>                                the subscription identifier
	data:
	info:    network nic create command OK

<BR>

	network nic set [options] <resource-group> <name>

	network nic list [options] <resource-group>
	network nic show [options] <resource-group> <name>
	network nic delete [options] <resource-group> <name>

**用來管理網路安全性群組的命令**

	network nsg create [options] <resource-group> <name> <location>
	network nsg set [options] <resource-group> <name>
	network nsg list [options] <resource-group>
	network nsg show [options] <resource-group> <name>
	network nsg delete [options] <resource-group> <name>

**用來管理網路安全性群組規則的命令**

	network nsg rule create [options] <resource-group> <nsg-name> <name>
	network nsg rule set [options] <resource-group> <nsg-name> <name>
	network nsg rule list [options] <resource-group> <nsg-name>
	network nsg rule show [options] <resource-group> <nsg-name> <name>
	network nsg rule delete [options] <resource-group> <nsg-name> <name>

**用來管理流量管理員設定檔的命令**

	network traffic-manager profile create [options] <resource-group> <name>
	network traffic-manager profile set [options] <resource-group> <name>
	network traffic-manager profile list [options] <resource-group>
	network traffic-manager profile show [options] <resource-group> <name>
	network traffic-manager profile delete [options] <resource-group> <name>
	network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**用來管理流量管理員端點的命令**

	network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
	network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
	network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**用來管理虛擬網路閘道的命令**

	network gateway list [options] <resource-group>

## azure provider：用來管理資源提供者註冊的命令

**列出 ARM 中目前已註冊的提供者**

	provider list [options]

**顯示要求提供者命名空間的相關詳細資料**

	provider show [options] <namespace>

**註冊訂用帳戶的提供者**

	provider register [options] <namespace>

**取消註冊訂用帳戶的提供者**

	provider unregister [options] <namespace>

## azure resource：用來管理資源的命令

**建立資源群組中的資源**

	resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**更新資源群組中的資源而不使用任何範本或參數**

	resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**列出資源**

	resource list [options] [resource-group]

**取得資源群組或訂用帳戶中的一項資源**

	resource show [options] <resource-group> <name> <resource-type> <api-version>

**刪除資源群組中的資源**

	resource delete [options] <resource-group> <name> <resource-type> <api-version>

## azure role：用來管理 Azure 角色的命令

**取得所有可用的角色定義**

	role list [options]

**取得一項可用的角色定義**

	role show [options] [name]

**用來管理角色指派的命令**

	role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## azure storage：用來管理儲存體物件的命令

**用來管理儲存體帳戶的命令**

	storage account list [options]
	storage account show [options] <name>
	storage account create [options] <name>
	storage account set [options] <name>
	storage account delete [options] <name>

**用來管理儲存體帳戶金鑰的命令**

	storage account keys list [options] <name>
	storage account keys renew [options] <name>

**用來顯示儲存體連接字串的命令**

	storage account connectionstring show [options] <name>

**用來管理儲存體容器的命令**

	storage container list [options] [prefix]
	storage container show [options] [container]
	storage container create [options] [container]
	storage container delete [options] [container]
	storage container set [options] [container]

**用來管理儲存體容器共用存取簽章的命令**

	storage container sas create [options] [container] [permissions] [expiry]

**用來管理儲存體容器已儲存存取原則的命令**

	storage container policy create [options] [container] [name]
	storage container policy show [options] [container] [name]
	storage container policy list [options] [container]
	storage container policy set [options] [container] [name]
	storage container policy delete [options] [container] [name]

**用來管理儲存體 blob 的命令**

	storage blob list [options] [container] [prefix]
	storage blob show [options] [container] [blob]
	storage blob delete [options] [container] [blob]
	storage blob upload [options] [file] [container] [blob]
	storage blob download [options] [container] [blob] [destination]

**用來管理 blob 複製作業的命令**

	storage blob copy start [options] [sourceUri] [destContainer]
	storage blob copy show [options] [container] [blob]
	storage blob copy stop [options] [container] [blob] [copyid]

**用來管理儲存體 blob 共用存取簽章的命令**

	storage blob sas create [options] [container] [blob] [permissions] [expiry]

**用來管理儲存體檔案共用的命令**

	storage share create [options] [share]
	storage share show [options] [share]
	storage share delete [options] [share]
	storage share list [options] [prefix]

**用來管理儲存體檔案的命令**

	storage file list [options] [share] [path]
	storage file delete [options] [share] [path]
	storage file upload [options] [source] [share] [path]
	storage file download [options] [share] [path] [destination]

**用來管理儲存體檔案目錄的命令**

	storage directory create [options] [share] [path]
	storage directory delete [options] [share] [path]

**用來管理儲存體佇列的命令**

	storage queue create [options] [queue]
	storage queue list [options] [prefix]
	storage queue show [options] [queue]
	storage queue delete [options] [queue]

**用來管理儲存體佇列共用存取簽章的命令**

	storage queue sas create [options] [queue] [permissions] [expiry]

**用來管理儲存體佇列已儲存存取原則的命令**

	storage queue policy create [options] [queue] [name]
	storage queue policy show [options] [queue] [name]
	storage queue policy list [options] [queue]
	storage queue policy set [options] [queue] [name]
	storage queue policy delete [options] [queue] [name]

**用來管理儲存體記錄屬性的命令**

	storage logging show [options]
	storage logging set [options]

**用來管理儲存體計量屬性的命令**

	storage metrics show [options]
	storage metrics set [options]

**用來管理儲存體資料表的命令**

	storage table create [options] [table]
	storage table list [options] [prefix]
	storage table show [options] [table]
	storage table delete [options] [table]

**用來管理儲存體資料表共用存取簽章的命令**

	storage table sas create [options] [table] [permissions] [expiry]

**用來管理儲存體資料表已儲存存取原則的命令**

	storage table policy create [options] [table] [name]
	storage table policy show [options] [table] [name]
	storage table policy list [options] [table]
	storage table policy set [options] [table] [name]
	storage table policy delete [options] [table] [name]

## azure tag：用來管理資源管理員標記的命令

**新增標記**

	tag create [options] <name> <value>

**移除整個標記或標記值**

	tag delete [options] <name> <value>

**列出標記資訊**

	tag list [options]

**取得標記**

	tag show [options] [name]

## azure vm：用來管理 Azure 虛擬機器的命令

**建立 VM**

	vm create [options] <resource-group> <name> <location> <os-type>

**建立使用預設資源的 VM**

	vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>

**列出資源群組中的虛擬機器**

	vm list [options] <resource-group>

**取得資源群組中的一個虛擬機器**

	vm show [options] <resource-group> <name>

**刪除資源群組中的一個虛擬機器**

	vm delete [options] <resource-group> <name>

**關閉資源群組中的一個虛擬機器**

	vm stop [options] <resource-group> <name>

**重新啟動資源群組中的一個虛擬機器**

	vm restart [options] <resource-group> <name>

**啟動資源群組中的一個虛擬機器**

	vm start [options] <resource-group> <name>

**關閉資源群組中的一個虛擬機器，並釋放計算資源**

	vm deallocate [options] <resource-group> <name>

**列出可用的虛擬機器大小**

	vm sizes [options]

**擷取 VM 作為 OS 映像或 VM 映像**

	vm capture [options] <resource-group> <name> <vhd-name-prefix>

**將 VM 的狀態設定為一般化**

	vm generalize [options] <resource-group> <name>

**取得 VM 的執行個體檢視**

	vm get-instance-view [options] <resource-group> <name>

**可讓您重設虛擬機器上的遠端桌面存取或 SSH 設定，以及針對具有系統管理員或 sudo 授權單位的帳戶重設密碼**

	vm reset-access [options] <resource-group> <name>

**使用新資料更新 VM**

	vm set [options] <resource-group> <name>

**用來管理虛擬機器資料磁碟的命令**

	vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
	vm disk detach [options] <resource-group> <vm-name> <lun>
	vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**用來管理 VM 資源延伸的命令**

	vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
	vm extension get [options] <resource-group> <vm-name>

**用來管理 Docker 虛擬機器的命令**

	vm docker create [options] <resource-group> <name> <location> <os-type>

**用來管理 VM 映像的命令**

	vm image list-publishers [options] <location>
	vm image list-offers [options] <location> <publisher>
	vm image list-skus [options] <location> <publisher> <offer>
	vm image list [options] <location> <publisher> [offer] [sku]
 

<!---HONumber=62-->