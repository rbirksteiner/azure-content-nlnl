<properties 
   pageTitle="建立具有多個 NIC 的 VM"
   description="深入了解如何建立與設定具有多個 NIC 的 VM"
   services="virtual-network, virtual-machines"
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
   ms.date="07/02/2015"
   ms.author="telmos" />

# 建立具有多個 NIC 的 VM

多個 NIC 功能可讓您建立和管理 Azure 虛擬機器 (VM) 上的多個虛擬網路介面卡 (NIC)。多個 NIC 是許多網路虛擬應用裝置的需求，例如，應用程式傳遞和 WAN 最佳化解決方案。多個 NIC 也會提供更多網路流量管理功能，包括前端 NIC 與後端 NIC 之間的流量隔離，或者從管理平面流量中將資料平面流量分隔開來。

![適用於 VM 的多個 NIC](./media/virtual-networks-multiple-nics/IC757773.png)

上圖顯示具有三個 NIC 的 VM，每個都連接到不同的子網路。

## 需求和限制

多個 NIC 目前具有下列需求和限制：

- 多個 NIC 的 VM 必須建立於 Azure 虛擬網路 (VNet) 中。不支援非 VNet 的 VM。 
- 在單一雲端服務中，僅允許下列設定： 
	- 該雲端服務中的所有 VM 必須已啟用多個 NIC 功能，或者 
	- 該雲端服務中的每一個 VM 都必須具有單一 NIC 

>[AZURE.IMPORTANT]如果您嘗試將多個 NIC 的 VM 加入已經包含單一 NIC 的 VM (反之亦然) 的部署 (雲端服務)，您將會收到下列錯誤：同一個部署中不支援同時具有次要網路介面的虛擬機器和沒有次要網路介面的虛擬機器，而且也無法更新沒有任何次要網路介面的虛擬機器，使其具有次要網路介面，反之亦然。
 
- 只有「預設」VIP 上才支援網際網路對向的 VIP。只有一個 VIP 可以連接到預設 NIC 的 IP。 
- 執行個體層級的公用 IP 位址目前不支援多個 NIC 的 VM。 
- VM 內部的 NIC 順序是隨機的，而且也可透過 Azure 基礎結構更新來變更。不過，IP 位址及對應的乙太網路 MAC 位址會維持不變。例如，假設 **Eth1** 具有 IP 位址 10.1.0.100 和 MAC 位址 00-0D-3A-B0-39-0D；在 Azure 基礎結構更新並重新開機之後，就無法變更為 Eth2，但是 IP 和 MAC 配對會保持相同。當客戶起始重新啟動時，NIC 順序會保持相同。 
- 每個 VM 上的每個 NIC 位址都必須位於子網路中，單一 VM 上的多個 NIC 每個都可以指派位於相同子網路的位址。 
- VM 大小會決定您可以為 VM 建立的 NIC 數目。下表列出與 VM 大小相對應的 NIC 數目： 

|VM 大小 (標準 SKU)|NIC 數目 (每個 VM 允許的最大值)|
|---|---|
|所有基本大小|1|
|A0\\超小型|1|
|A1\\小型|1|
|A2\\中型|1|
|A3\\大型|2|
|A4\\超大型|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|所有的其他大小|1|

## 網路安全性群組
VM 上的任何 NIC 可能會關聯至網路安全性群組 (NSG)，包括已啟用多個 NIC 功能的 VM 上的任何 NIC。如果已為 NIC 指派子網路 (該子網路會關聯至 NSG) 內的位址，則子網路 NSG 中的規則也適用於該 NIC。除了將子網路關聯至 NSG，您也可以將 NIC 關聯至 NSG。

如果將子網路關聯至 NSG，而且該子網路內的 NIC 會個別關聯至 NSG，則相關聯的 NSG 規則會根據傳入或傳出 NIC 的流量方向套用「流程順序」****：

- **連入流量**的目的地是有問題的流量中第一次流經子網路的 NIC，會在傳入 NIC 之前觸發子網路的 NSG 規則，然後觸發 NIC 的 NSG 規則。- **連出流量**的來源是有問題的流量中第一次從 NIC 流出的 NIC，會在流經子網路之前觸發 NIC 的 NSG 規則，然後觸發子網路的 NSG 規則。 

上圖顯示如何根據流量來完成 NSG 規則的應用 (從 VM 到子網路，或從子網路到 VM)。

## 如何設定多個 NIC 的 VM

下列指示將協助您建立多個 NIC 的 VM，其中包含 3 個 NIC：一個預設的 NIC 和兩個其他的 NIC。組態步驟將建立 VM，此 VM 將根據下列服務組態檔片段來設定：

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


嘗試執行此範例的 PowerShell 命令之前，您需要具備下列必要條件。

- Azure 訂閱。
- 已設定的虛擬網路。如需 VNet 的詳細資訊，請參閱[虛擬網路概觀](https://msdn.microsoft.com/library/azure/jj156007.aspx)。
- 已下載並安裝最新版的 Azure PowerShell。請參閱[如何安裝和設定 Azure PowerShell](../install-configure-powershell)。

若要建立有多個 NIC 的 VM，請依照下列步驟：

1. 從 Azure VM 映像庫中選取 VM 映像請注意，映像經常變更且可依區域取得。以下範例中指定的映像可能會變更，或者可能不在您的區域中，因此，請務必指定您需要的映像。 
	    
		$image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. 建立 VM 組態。

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. 建立預設的系統管理員登入。

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. 在 VM 組態中加入其他 NIC。

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. 指定預設 NIC 的子網路和 IP 位址。

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. 在虛擬網路中建立 VM。

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]您在此處指定的 VNet 必須已經存在 (如必要條件中所提及)。下列範例會指定名為 **MultiNIC-VNet** 的虛擬網路。

## 其他子網路的次要 NIC 存取

目前 Azure 中的模型是虛擬機器中全部的 NIC 都設定為預設閘道。這可讓 NIC 與它們子網路外部的 IP 位址通訊。使用弱式路由模型的作業系統 (例如 Linux)，如果輸入和輸出流量使用不同的 NIC，網際網路連線將會中斷。

為了修正此問題，Azure 將於 2015 年 7 月第一週，將更新推出到將從次要 NIC 移除預設閘道的平台。在現有虛擬機器重新開機之前，這不會影響現有的虛擬機器。重新開機之後，新的設定將會生效，同時次要 NIC 上的流量將會限制在相同的子網路中。如果使用者想要啟用次要 NIC 在其子網路外部通訊，他們必須在路由表中新增項目以設定閘道，如下所述。

### 設定 Windows VM

假設您有包含兩個 NIC 的 Windows VM，如下所示：

- 主要 NIC IP 位址：192.168.1.4
- 次要 NIC IP 位址：192.168.2.5

此 VM 的 IPv4 路由表看起來像這樣：

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

請注意，預設路由 (0.0.0.0) 僅提供主要 NIC 使用。您將無法存取次要 NIC 子網路外部的資源，如下所示：

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

若要在次要 NIC 上新增預設路由，請遵循下列步驟：

1. 從命令提示字元執行下列命令來識別次要 NIC 的索引編號：

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. 請注意資料表中的第二個項目，索引為 27 (此範例中)。
3. 從命令提示字元執行 **route add** 命令，如下所示：在此範例中，您指定 192.168.2.1 做為次要 NIC 的預設閘道：

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. 若要測試連線，請移至命令提示字元並嘗試 ping 不同的次要 NIC 子網路，如下列範例所示：

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. 您也可以檢查您的路由表，來檢查新加入的路由，如下所示：

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### 設定 Linux VM

若是 Linux VM，因為預設行為是使用弱式主機路由，建議您將次要 NIC 的流量限制在相同的子網路中。不過，如果某些情況要求子網路外部的連線，使用者應該根據路由啟用原則，以確保輸入和輸出流量都使用相同的 NIC。

## 後續步驟

- 深入了解[在 Azure 中使用多個 VM NIC 和 VNet 設備](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=July15_HO2-->