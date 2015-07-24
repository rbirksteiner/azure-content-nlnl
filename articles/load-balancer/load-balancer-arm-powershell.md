<properties
   pageTitle="使用 Azure 資源管理員建立負載平衡器 | Microsoft Azure"
   description="如何建立 Azure 資源管理員的負載平衡器規則、NAT 規則、探查。逐步示範建立負載平衡器資源的端對端流程。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# 如何使用 Azure 資源管理員建立負載平衡器

下列步驟將說明如何搭配 PowerShell 使用 Azure 資源管理員建立負載平衡器。使用 Azure 資源管理員時，會個別設定建立負載平衡器所需的項目，然後放置在一起，以建立資源。

我們會在這一頁中說明必須完成才能建立負載平衡器的個別工作順序，並詳細說明要達到建立負載平衡器的目標，需要做些什麼。


## 若要建立負載平衡器，需要哪些項目？

必須在建立負載平衡器之前先設定下列項目：

- 前端 IP 組態 

- 後端位址集區

- 負載平衡規則

- 探查

- 傳入的 NAT 規則

您可以在 [Azure 資源管理員的 Azure 負載平衡器支援](load-balancer-arm.md)中取得負載平衡器元件與 Azure 資源管理員的詳細資訊。

下列步驟將示範如何設定負載平衡器，以在 2 個虛擬機器之間進行負載平衡。


## 使用 PowerShell 逐步進行


### 建立負載平衡器的資源群組


### 步驟 1
請確定您切換 PowerShell 模式來使用 ARM Cmdlet。如需詳細資訊，請參閱[將 Windows PowerShell 與資源管理員搭配使用](powershell-azure-resource-manager.md)。


    PS C:> Switch-AzureMode -Name AzureResourceManager

### 步驟 2

登入您的 Azure 帳戶。


    PS C:> Add-AzureAccount

系統會提示使用您的認證進行驗證。


### 步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

    PS C:> Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureSubscription’ Cmdlet。


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    PS C:> New-AzureResourceGroup -Name NRP-RG -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有建立負載平衡器的命令都是使用同一個資源群組。

在上述範例中，我們已建立名為 "NRP-RG" 的資源群組，且位置為「美國西部」。

## 建立前端 IP 集區的虛擬網路和公用 IP 位址


### 步驟 1

建立虛擬網路：

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

建立虛擬網路的子網路，並指派給 $backendSubnet

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

建立虛擬網路，並將子網路 lb-subnet-be 加入至虛擬網路 NRPVNet。

### 步驟 2

建立前端 IP 集區要使用的公用 IP 位址：

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip 


## 建立前端 IP 集區和後端位址集區

設定傳入負載平衡器網路流量的前端 IP 集區以及後端位址集區，以接收負載平衡流量。

### 步驟 1 

使用公用 IP 變數 ($publicIP) 建立前端 IP 集區。

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 


### 步驟 2 

設定用來從前端 IP 集區接收傳入流量的後端位址集區：

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## 建立 LB 規則、NAT 規則、探查及負載平衡器

建立前端 IP 集區和後端位址集區之後，您必須建立將屬於負載平衡器資源的規則：

### 步驟 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


上述範例會建立下列項目：

- NAT 規則，連接埠 3441 的所有傳入流量都會移至連接埠 3389。
- 第二個 NAT 規則，連接埠 3442 的所有傳入流量都會移至連接埠 3389。
- 負載平衡器規則，將公用連接埠 80 上的所有傳入流量，負載平衡至後端位址集區中的本機連接埠 80。
- 探查規則，檢查路徑 "HealthProbe.aspx" 的健全狀態。



### 步驟 2

建立負載平衡器，並將所有物件 (NAT 規則、負載平衡器規則、探查組態) 加在一起：

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## 建立網路介面

建立負載平衡器之後，您需要定義要由哪些網路介面接收傳入的負載平衡網路流量，以及定義 NAT 規則和探查。在此情況下需個別設定網路介面，並在稍後指派給虛擬機器。


### 步驟 1 


取得資源的虛擬網路和子網路以建立網路介面：

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


在此步驟中，我們會建立屬於負載平衡器後端集區的網路介面，並針對此網路介面的 RDP 與第一個 NAT 規則建立關聯：
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 步驟 2

建立第二個網路介面，稱為 LB-Nic2-BE：

在此步驟中，我們會建立第二個網路介面，並指派給同一個負載平衡器後端集區，然後與針對 RDP 所建立的第二個 NAT 規則建立關聯：

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


最終結果會顯示如下：


PS C:> $backendnic1


	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### 步驟 3 

使用 Add-AzureVMNetworkInterface 命令將 NIC 指派給虛擬機器。

您可以在以下文件中找到建立虛擬機器並指派給的 NIC 的逐步解說：[利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)


## 另請參閱

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->