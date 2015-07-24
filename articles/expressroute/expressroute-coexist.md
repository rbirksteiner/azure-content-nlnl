<properties
   pageTitle="設定並存的 ExpressRoute 和站對站 VPN 連線"
   description="本教學課程會引導您設定要並存的 ExpressRoute 和站對站 VPN 連線。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="cherylmc"/>

# 設定並存的 ExpressRoute 和站對站 VPN 連線

您現在可以將 ExpressRoute 和站對站 VPN 連線到相同的虛擬網路。有兩種不同案例和兩個不同設定程序可供您選擇。

### 案例

有兩種不同的案例。下圖顯示了這兩個案例。

- **案例 1** - 如果您有一個區域網路，ExpressRoute 就會變成作用中連結，而站對站 VPN 會變成備份。如果 ExpressRoute 連線失敗，站對站 VPN 連線就會變成作用中。這種案例最適合用於高可用性。

- **案例 2** - 如果您有兩個區域網路，可以透過 ExpressRoute 將一個連線到 Azure，而另一個則透過站對站 VPN 連線到 Azure。在此情況下，這兩個連線就能同時作用。


![並存](media/expressroute-coexist/coexist1.jpg)


### 設定程序

有兩個個別設定程序可供您選擇。無論您已經有想要連線的現有虛擬網路，或者想要建立新的虛擬網路，都將決定您所選取的設定程序。


- [使用並存連線建立新的 VNet](#create-a-new-vnet-with-coexisting-connections)：如果您還沒有虛擬網路，這個程序將會引導您建立新的虛擬網路，並建立新的 ExpressRoute 和站對站 VPN 連線。  


- [設定並存連線的現有 VNet](#configure-your-existing-vnet-for-coexisting-connections)：您可能已經有虛擬網路，而且使用現有的站對站 VPN 連線或 ExpressRoute 連線。建立並存連線將需要您刪除閘道，然後設定能夠並存的新閘道。這表示當您刪除和重新建立閘道與連線時，將會有跨設備連線的停機時間，但您不需要將任何 VM 或服務移轉至新的虛擬網路。您的 VM 和服務仍能透過負載平衡器對外通訊，而您能夠在這兩者設定為這樣做時進行閘道設定。

	此程序將會引導您刪除閘道，然後建立新的 ExpressRoute 和站對站 VPN 連線。請注意，建立新的連線時，您必須以非常特定的順序來完成這些步驟。請勿使用其他文章中的指示建立閘道和連線。

### 附註和限制

- ExpressRoute 閘道和站對站 VPN 閘道必須是 Standard 或 HighPerformance 閘道 SKU。如需關於閘道 SKU 的資訊，請參閱[閘道 SKU](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
- 如果您的區域網路連線到 ExpressRoute 和站對站 VPN (案例 1)，您應該在區域網路中設定靜態路由，以將站對站 VPN 連線路由傳送到公用網際網路。 
- 您必須先建立 ExpressRoute 閘道，才能新增站對站 VPN 閘道。
- 您將無法在透過站對站 VPN 連線的區域網路，以及透過 ExpressRoute 連線的區域網路之間，進行路由傳送 (透過 Azure)。
- 這兩個程序均會假設您已設定 ExpressRoute 電路。如果您並未設定，請參閱下列文章： 

	- [透過網路服務提供者 (NSP) 設定 ExpressRoute 連線](expressroute-configuring-nsps.md) 
	- [透過 Exchange 提供者 (EXP) 設定 ExpressRoute 連線](expressroute-configuring-exps.md)


## 建立使用並存連線的新 VNet

1. 請確認您擁有最新版本的 PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 PowerShell 區段下載並安裝最新版本的 PowerShell Cmdlet。
2. 建立虛擬網路的結構描述。如需關於使用網路組態檔的詳細資訊，請參閱[使用網路組態檔設定虛擬網路](../virtual-network/virtual-networks-using-network-configuration-file.md)。如需關於組態結構描述的詳細資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

	當您建立結構描述時，請務必使用下列值：

	- 虛擬網路的閘道子網路必須是 /27 (或更短的前置詞)。
	- 閘道連線類型為「專用」。

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. 建立和設定 XML 結構描述檔案後，請上傳該檔案。這樣會建立您的虛擬網路。

	使用下列 Cmdlet 來上傳檔案，將該值替換為您自己的值。

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. 建立 ExpressRoute 閘道。請務必將 GatewaySKU 指定為 *Standard* 或 *HighPerformance*，並將 GatewayType 指定為 *DynamicRouting*。 

	使用下方範例，將該值替換為您自己的值。

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. 將 ExpressRoute 閘道連結到 ExpressRoute 電路。完成這個步驟之後，內部部署網路和 Azure 之間的連線 (透過 ExpressRoute ) 便會建立。

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. 接下來，建立站對站 VPN 閘道。GatewaySKU 必須是 *Standard* 或 *HighPerformance*，且 GatewayType 必須是 *DynamicRouting*。

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	若要擷取虛擬網路閘道設定 (包括閘道識別碼和公用 IP)，請使用 `Get-AzureVirtualNetworkGateway` Cmdlet。

		Get-AzureVirtualNetworkGateway
		
		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. 建立本機的站台 VPN 閘道實體。此命令不會設定內部部署 VPN 閘道。相反地，它可讓您提供本機閘道設定 (例如公用 IP 與內部位址空間)，使 Azure VPN 閘道能夠與其連線。

	**重要事項：**並未在 Netcfg 中定義站對站 VPN 的本機站台。您必須改為使用此 Cmdlet 來指定本機站台參數。您無法使用管理入口網站或 Netcfg 檔來定義參數。

	使用下列範例，將該值替換為您自己的值。

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	若要擷取虛擬網路閘道設定 (包括閘道識別碼和公用 IP)，請使用 `Get-AzureVirtualNetworkGateway` Cmdlet。請參閱下面的範例：

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. 設定本機 VPN 裝置以連線到新的閘道。當您設定 VPN 裝置時，請使用於步驟 6 所擷取的資訊。如需關於 VPN 裝置組態的詳細資訊，請參閱 [VPN 裝置組態](vpn-gateway-configure-vpn-gateway-mp.md/#gather-information-for-your-vpn-device-configuration)。
	

9. 將 Azure 上的站對站 VPN 閘道連結至本機閘道。

	在此範例中，connectedEntityId 是本機的閘道識別碼，您可以透過執行 `Get-AzureLocalNetworkGateway` 找到此識別碼。您可以使用 `Get-AzureVirtualNetworkGateway` Cmdlet 尋找 virtualNetworkGatewayId。完成這個步驟之後，區域網路和 Azure 之間的連線 (透過站對站 VPN 連線) 便會建立。


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## 設定並存連線的現有 VNet

如果您現有的虛擬網路已透過 ExpressRoute 或站對站 VPN 連線進行連線，則為了啟用這兩個連線以連接到現有虛擬網路，您必須先刪除現有閘道。這表示當您進行此設定時，本機設備將會與使用該閘道的虛擬網路中斷連線。

**開始設定之前：**請確認您的虛擬網路中有足夠的 IP 位址，才能夠增加閘道子網路的大小。


1. 請確認您擁有最新版本的 PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 PowerShell 區段下載並安裝最新版本的 PowerShell Cmdlet。
 
2. 刪除現有的站對站 VPN 閘道。使用下列 Cmdlet，將該值替換為您自己的值。

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. 匯出虛擬網路的結構描述。使用下列 PowerShell Cmdlet，將該值替換為您自己的值。

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. 編輯網路組態檔結構描述，讓閘道子網路是 /27 (或更短的前置詞)。請參閱下方的範例。如需關於使用網路組態檔的詳細資訊，請參閱[使用網路組態檔設定虛擬網路](../virtual-network/virtual-networks-using-network-configuration-file.md)。如需關於組態結構描述的詳細資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. 如果您先前的閘道是站對站 VPN，則必須將連線類型變更為**專用**。

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. 此時，您必須使用沒有閘道的 VNet。您可以繼續進行[使用並存連線建立新的 VNet](#create-a-new-vnet-with-coexisting-connections) 文章中的**步驟 3**，以建立新的閘道並完成連線。



## 後續步驟
	
深入了解 ExpressRoute。請參閱 [ExpressRoute 概觀](expressroute-introduction.md)。

深入了解 VPN 閘道。請參閱[關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

<!---HONumber=July15_HO2-->