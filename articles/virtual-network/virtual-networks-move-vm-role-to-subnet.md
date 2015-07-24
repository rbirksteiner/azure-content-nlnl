<properties 
   pageTitle="如何將 VM 或角色執行個體移至不同的子網路"
   description="了解如何將 VM 和角色執行個體移至不同的子網路"
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
   ms.date="05/29/2015"
   ms.author="telmos" />

# 如何將 VM 或角色執行個體移至不同的子網路

您可以使用 PowerShell 將 VM 從一個子網路移至相同虛擬網路 (VNet) 中的另一個子網路。您可以藉由編輯 CSCFG，而非使用 PowerShell 來移動角色執行個體。

為什麼要將 VM 移到另一個子網路？ 當較舊的子網路太小，且由於在該子網路中執行的現有 VM 而無法擴充時，子網路移轉相當實用。在此案例中，您可以建立較新且較大的子網路，並將 VM 移轉至新的子網路，然後在完成移轉之後，您可以刪除舊的空白子網路。

## 如何將 VM 移至另一個子網路

若要移動 VM，請執行 Set AzureSubnet PowerShell Cmdlet，並使用下方範例作為範本。在下列範例中，我們會將 TestVM 從其目前的子網路移到 Subnet-2。請務必編輯該範例來反映您的環境。請注意，每當您執行 Update-azurevm Cmdlet 作為程序的一部分，其會重新啟動您的 VM 作為更新程序的一部分。

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

如果您針對 VM 指定靜態 DIP，您必須清除該設定，才能將 VM 移至新的子網路。在此案例中，請使用下列方法：

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## 若要將角色執行個體移至另一個子網路

若要移動角色執行個體，請編輯 CSCFG 檔案。在下方範例中，我們會將虛擬網路 *VNETName* 中的「Role0」從其目前的子網路移至 *Subnet-2*.因為已部署角色執行個體，您僅需變更 Subnet name = Subnet-2 的部份。請務必編輯該範例來反映您的環境。

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=July15_HO2-->