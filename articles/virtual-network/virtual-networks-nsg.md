<properties 
   pageTitle="什麼是網路安全性群組 (NSG)"
   description="了解網路安全性群組 (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# 什麼是網路安全性群組 (NSG)？

您可以在虛擬網路中，使用 NSG 控制傳輸至一個或多個虛擬機器 (VM) 執行個體的流量。網路安全性群組是與您訂用帳戶相關聯的最上層物件，NSG 包含存取控制規則，可允許或拒絕傳輸至 VM 執行個體的流量。NSG 的規則可以隨時變更，而變更時會套用至所有相關聯的執行個體。若要使用 NSG，您必須擁有與區域 (位置) 相關聯的 VNet。

>[AZURE.WARNING]NSG 不相容於與同質群組相關聯的 VNet。如果您沒有區域 VNet，而且您想要控制傳輸至端點的流量，請參閱＜[什麼是網路存取控制清單 (ACL)？](../virtual-networks-acl)＞。

您可以將 NSG 與 VM 建立關聯，或在 VNet 中與子網路建立關聯。與 VM 建立關聯時，NSG 會套用至由 VM 執行個體傳送和接收的所有流量。套用至 VNet 中的子網路時，NSG 會套用至子網路中所有 VM 執行個體傳送和接收的所有流量。VM 或子網路可以僅與 1 個 NSG 建立關聯，且每個 NSG 可以包含最多 200 個規則。每個訂用帳戶您可以擁有 100 個 NSG。

>[AZURE.NOTE]端點式 ACL 和網路安全性群組，不支援用於相同的 VM 執行個體。如果您想要使用 NSG 且已經擁有就地端點 ACL，請先移除端點 ACL。如需有關執行這項作業的資訊，請參閱＜[使用 PowerShell 管理端點的存取控制清單 (ACL)](https://msdn.microsoft.com/library/azure/dn376543.aspx)＞。

## 網路安全性群組如何運作？

網路安全性群組不同於端點式 ACL。端點 ACL 僅在透過輸入端點公開的公用連接埠上運作。NSG 會在一個或多個 VM 執行個體上運作，並控制 VM 上的所有輸入和輸出流量。

網路安全性群組具有與*區域*相關聯的*名稱*，以及具有描述性的標籤。其包含兩種類型的規則，即**輸入**和**輸出**。輸出規則會套用至傳輸到 VM 的傳入封包，而輸入規則會套用至來自 VM 的傳出封包。規則會套用至 VM 所在的主機。傳入或傳出封包必須符合**允許**規則才能允許連線，如果不符合則會中止連線。

規則會依照優先順序進行處理。例如，具有較低優先順序編號 (例如 100) 的規則會在具有較高優先順序編號 (例如 200) 的規則之前進行處理。一旦找到相符項目，則不會處理其他更多的規則。

規則會指定下列項目：

- **名稱：**規則的唯一識別碼

- **類型：**輸入/輸出

- **優先順序：**<You can specify an integer between 100 and 4096>

- **來源 IP 位址：**來源 IP 範圍的 CIDR

- **來源連接埠範圍：**<integer or range between 0 and 65536>

- **目的地 IP 範圍：**目的地 IP 範圍的 CIDR

- **目的地連接埠範圍：**<integer or range between 0 and 65536>

- **通訊協定：**<允許使用 TCP、UDP 或 '*'>

- **存取：**允許/拒絕

### 預設規則

NSG 包含預設規則。預設規則無法刪除，但因為其會指派為最低優先權，因此可以由您所建立的規則覆寫預設規則。預設規則會描述平台所建議的預設設定。如下方預設規則所示，VNet 中的流量起始和結束同時允許輸入和輸出方向。

雖然輸出方向允許連接到網際網路，依預設會封鎖輸入方向。預設規則會允許 Azure 的負載平衡器 (LB) 探查 VM 的健康狀態。如果 VM 或 NSG 下的 VM 集合不參與負載平衡集合，您可以覆寫此規則。

預設規則如下：

**輸入**

| 名稱 | 優先順序 | 來源 IP | 來源連接埠 | 目的地 IP | 目的地連接埠 | 通訊協定 | 存取 |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| 允許 VNet 輸入 | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | 允許 |
| 允許 Azure 負載平衡器輸入 | 65001 | AZURE_LOADBALANCER | * | * | * | * | 允許 |
| 拒絕所有輸入 | 65500 | * | * | * | * | * | 拒絕 |

**輸出**

| 名稱 | 優先順序 | 來源 IP | 來源連接埠 | 目的地 IP | 目的地連接埠 | 通訊協定 | 存取 |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| 允許 VNet 輸出 | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | 允許 |
| 允許網際網路輸出 | 65001 | * | * | 網際網路 | * | * | 允許 |
| 拒絕所有輸出 | 65500 | * | * | * | * | * | 拒絕 |

### 特殊的基礎架構規則

NSG 規則是明確的。不允許所有流量，或拒絕超出 NSG 規則中所指定的流量。不過，一律允許兩種類型的流量，無論網路安全性群組規格為何。這些佈建用於支援基礎結構。

- **節點的虛擬 IP：**基本的基礎結構服務，例如 DHCP、DNS 和健康狀態監控是透過虛擬化主機 IP 位址 168.63.129.16 所提供。這個公用 IP 位址屬於 Microsoft，且是針對此目的唯一用於所有區域的虛擬 IP。此 IP 位址對應至伺服器電腦的實體 IP 位址 (主機節點)，該伺服器用來主控虛擬機器。主機節點的作用如同 DHCP 轉送、DNS 遞迴解析程式，以及負載平衡器健康狀態探查和電腦健康狀態探查的探查來源。此 IP 位址的通訊不應視為一種攻擊。

- **授權 (金鑰管理服務)：**應該授權在虛擬機器中執行的 Windows 映像。若要這樣做，授權要求會傳送至處理此類查詢的金鑰管理服務主機伺服器。這會一律位於輸出連接埠 1688。

### 預設標籤

預設標籤是系統提供的識別項，用來解決 IP 位址的類別。客戶定義的規則中，可以指定預設標籤。預設標籤如下所示：

- **VIRTUAL_NETWORK -** 這個預設標籤代表所有網路位址空間。其包含虛擬網路位址空間 (在 Azure 中的 IP CIDR)，以及所有已連接的內部部署位址空間 (區域網路)。這也包括連接至 VNet 位址空間的 VNet。

- **AZURE_LOADBALANCER -** 這個預設標籤代表 Azure 的基礎結構的負載平衡器。這會轉譯成作為 Azure 健康狀態探查來源的 Azure 資料中心 IP。此標籤僅在與 NSG 相關聯的 VM 或 VM 集合參與負載平衡集合時才需要。

- **INTERNET -** 這個預設標籤代表虛擬網路以外且可以透過公用網際網路進行存取的 IP 位址空間。此範圍也包括 Azure 擁有的公用 IP 空間。

### 連接埠和連接埠範圍

網路安全性群組規則可以指定於單一來源/目的地連接埠上，或指定一個連接埠範圍。當您要開啟各種不同的應用程式連接埠，例如 FTP 時，則在此案例中特別實用。範圍僅能循序排列，且不能混合使用個別的連接埠規格。

若要指定的連接埠範圍，請使用 '-' 符號，如下方在 *DestinationPortRange* 參數中所示：

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### ICMP 流量

目前的 NSG 規則僅允許用於通訊協定 'TCP' 或 'UDP'。'ICMP' 沒有特定的標籤。不過，系統依預設會透過輸入 VNet 規則來允許虛擬網路內的 ICMP 流量，該規則會允許 VNet 內任何連接埠的輸入/輸出流量以及通訊協定 '*'。

## 建立 NSG 關聯

建立 NSG 至 VM 的關聯 - 當 NSG 與 VM 直接相關聯時，NSG 中的網路存取規則會直接套用至目的地為 VM 的所有流量。每當 NSG 更新規則變更時，該變更會反映在幾分鐘內處理的流量。當 NSG 與 VM 不相關聯時，狀態就會回到 NSG 之前的狀態，例如在介紹是否要使用 NSG 之前的系統預設值。

建立 NSG 至子網路的關聯 - 當 NSG 與子網路相關聯時，NSG 中的網路存取規則會套用至子網路中的所有 VM。每當 NSG 中的存取規則更新時，變更會在數分鐘內套用至子網路中的所有虛擬機器。

建立 NSG 至子網路和 VM 的關聯 - 您可能可以建立 NSG 至 VM 的關聯，並將不同的 NSG 與 VM 所在的子網路建立關聯。系統支援此作業，且在此案例中 VM 會取得兩個階層的保護。輸入流量時，封包會通過子網路中所指定的存取規則，然後再通過 VM 中的規則，而在輸出的案例中，封包會在通過子網路中所指定的規則之前，先通過 VM 中指定的規則，如下圖所示。

![NSG ACL](./media/virtual-networks-nsg/figure1.png)

當 NSG 與 VM 或子網路相關聯時，網路存取控制規則會變得非常明確。平台不會插入任何隱含規則來允許特定連接埠的流量。在此案例中，如果您在 VM 中建立端點，則您也必須建立規則以允許來自網際網路的流量。如果您不這麼做，VIP：<Port>將無法從外部存取。

例如：您建立新的 VM，同時也建立新的 NSG。您建立 NSG 至 VM 的關聯。虛擬網路中，VM 可以透過「允許 VNet 輸入規則」與其他 VM 進行通訊。VM 也可以使用「允許網際網路輸出」規則，進行網際網路的輸出連接。稍後，您可以在連接埠 80 上建立端點來接收您在 VM 中所執行網站的流量。VIP (公用虛擬 IP 位址) 上來自網際網路且目的地為連接埠 80 的封包不會到達 VM，直到您將類似下列 (下方) 的規則新增至 NSG。

| 名稱 | 優先順序 | 來源 IP | 來源連接埠 | 目的地 IP | 目的地連接埠 | 通訊協定 | 存取 |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | 網際網路 | * | * | 80 | TCP | 允許 |

## 設計考量

當設計您的 NSG 時，您必須了解 VM 與基礎結構服務以及由 Azure 託管之 PaaS 服務通訊的方式。大部分 Azure PaaS 服務 (例如 SQL 資料庫與儲存體) 只能透過公用的對外網際網路位址存取。這也適用於負載平衡探查。

Azure 中常見的案例就是根據 VM 和 PaaS 角色是否需要存取網際網路，來分隔它們。在類似案例中，您可能具備一個子網路包含需要存取 Azure Paas 服務 (例如 SQL 資料庫與儲存體) 的 VM 或角色執行個體，但不需要公用網際網路的任何輸入或輸出通訊。

針對類似案例假設下列 NSG 規則：

| 名稱 | 優先順序 | 來源 IP | 來源連接埠 | 目的地 IP | 目的地連接埠 | 通訊協定 | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|無網際網路|100| VIRTUAL_NETWORK|&\#42;|網際網路|&\#42;|TCP|拒絕| 

因為規則會拒絕所有虛擬網路到網際網路的存取，VM 將無法存取任何需要公開網際網路端點的 Azure PaaS 服務，例如 SQL 資料庫。

如果不使用拒絕規則，請考慮使用允許虛擬網路到網際網路存取，但是拒絕網際網路到虛擬網路存取的規則，如下所示：

| 名稱 | 優先順序 | 來源 IP | 來源連接埠 | 目的地 IP | 目的地連接埠 | 通訊協定 | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|到網際網路|100| VIRTUAL_NETWORK|&\#42;|網際網路|&\#42;|TCP|允許|
|從網際網路|110| 網際網路|&\#42;|VIRTUAL_NETWORK|&\#42;|TCP|拒絕| 


## 規劃 - 網路安全性群組工作流程

以下是使用網路安全性群組時的基本工作流程步驟。

### 工作流程 – 建立和關聯 NSG

1. 建立網路安全性群組 (NSG)。

1. 新增網路安全性規則，除非預設規則已足夠。

1. 建立 NSG 至 VM 的關聯。

1. 更新 VM。

1. 更新之後，NSG 規則會立即生效。

### 工作流程 – 更新現有的 NSG

1. 新增、刪除或更新現有 NSG 中的規則。

1. 與 NSG 相關聯的所有 VM 都會在數分鐘內取得更新。當 NSG 規則已與 VM 相關聯時，則不需要 VM 更新。

### 工作流程 – 變更 NSG 關聯

1. 建立新 NSG 至 VM 的關聯，其中 VM 已與另一個 NSG 建立關聯。

1. 更新 VM。

1. 來自新 NSG 的規則會在數分鐘內生效。

## 如何建立、設定和管理您的網路安全性群組

此時，您可以僅使用 PowerShell Cmdlet 和 REST API 來設定和修改 NSG。您無法使用管理入口網站來設定 NSG。下列 PowerShell Cmdlet 將協助您建立、設定和管理您的 NSG。

**建立網路安全性群組**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**新增或更新規則**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**從 NSG 刪除規則**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**建立 NSG 至 VM 的關聯**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**檢視與 VM 相關聯的 NSG**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**從 VM 移除 NSG**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**建立 NSG 至子網路的關聯**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**檢視與子網路相關聯的 NSG**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**從子網路移除 NSG**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**刪除 NSG**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**取得 NSG 以及規則的詳細資料**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**檢視與 NSG 相關聯的所有 Azure PowerShell Cmdlet**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=July15_HO2-->