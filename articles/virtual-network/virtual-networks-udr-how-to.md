<properties 
   pageTitle="如何在 Azure 中建立路由並啟用 IP 轉送"
   description="了解如何管理 UDR 與 IP 轉送"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# 如何在 Azure 中建立路由並啟用 IP 轉送
您可以使用 Azure 中的虛擬應用裝置處理 Azure 虛擬網路中的流量。不過，您需要建立可讓虛擬網路中的 VM 和雲端服務將封包傳送至虛擬應用裝置的路由，而不是傳送至封包所需的目的地的路由。您也需要在虛擬應用裝置 VM 上啟用 IP 轉送，讓它可以接收和轉送未定址到實際虛擬應用裝置 VM 的封包。

##如何管理路由
您可以在 Azure 中使用 PowerShell 新增、移除及變更路由。在您可以建立路由之前，必須先建立一個託管路由的路由表。

### 如何建立路由表
若要建立名為 *FrontEndSubnetRouteTable* 的路由表，執行下列 PowerShell 命令：

```powershell
New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location uscentral `
	-Label "Route table for frontend subnet"
```

上述命令的輸出看起來應該像下面這樣：

	Error          :
	HttpStatusCode : OK
	Id             : 085ac8bf-26c3-9c4c-b3ae-ebe880108c70
	Status         : Succeeded
	StatusCode     : OK
	RequestId      : a8cc03ca42d39f27adeaa9c1986c14f7

### 如何將路由新增到路由表
若要在以上建立的路由表中新增將 *10.1.1.10* 設定為 *10.2.0.0/16* 子網路的下一個躍點的路由，執行下列 PowerShell 命令：

```powershell
Get-AzureRouteTable FrontEndSubnetRouteTable `
	|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10
```

上述命令的輸出看起來應該像下面這樣：

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### 如何將路由與子網路建立關聯
路由表必須與一個或多個子網路建立關聯，才能使用。若要在虛擬網路 *ProductionVnet* 中，將 *FrontEndSubnetRouteTable* 路由表與名稱為 *FrontEndSubnet* 的子網路建立關聯，執行下列 PowerShell 命令：

```powershell
Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable
```

### 如何在 VM 中查看套用的路由
您可以查詢 Azure 以查看針對特定 VM 或角色執行個體套用的實際路由。顯示的路由包括 Azure 提供的預設路由以及 VPN 閘道通告的路由。顯示的路由限制為 800。

若要在名稱為 *FWAppliance1* 的 VM 上查看與主要 NIC 相關聯的路由，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable
```

上述命令的輸出看起來應該像下面這樣：

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

若要在名稱為 *FWAppliance1* 的 VM 上查看與名稱為 *backendnic* 的次要 NIC 相關聯的路由，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
```

若要在名稱為 *myRole* 的角色執行個體 (屬於名稱為 *ProductionVMs* 的雲端服務的一部分) 上查看與主要 NIC 相關聯的路由，請執行下列 PowerShell 命令：

```powershell
Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
	-RoleInstanceName myRole
```

## 如何管理 IP 轉送
如先前所提及，您需要在當做虛擬應用裝置的任何 VM 或角色執行個體上啟用 IP 轉送。

### 如何啟用 IP 轉送
若要在名稱為 *FWAppliance1* 的 VM 中啟用 IP 轉送，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

若要在名稱為 *DMZService* 的雲端服務中，名稱為 *FWAppliance* 的角色執行個體內啟用 IP 轉送，請執行下列 PowerShell 命令：

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### 如何停用 IP 轉送
若要在名稱為 *FWAppliance1* 的 VM 中停用 IP 轉送，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Disable
```

若要在名稱為 *DMZService* 的雲端服務中，名稱為 *FWAppliance* 的角色執行個體內停用 IP 轉送，請執行下列 PowerShell 命令：

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### 如何檢視 IP 轉送的狀態
若要在名稱為 *FWAppliance1* 的 VM 中檢視 IP 轉送的狀態，請執行下列 PowerShell 命令：

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
```

## 另請參閱

[使用者定義的路由和 IP 轉送概觀](../virtual-networks-udr-overview)

<!---HONumber=62-->