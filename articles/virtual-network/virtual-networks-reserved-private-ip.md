<properties 
   pageTitle="如何設定靜態內部私人 IP"
   description="了解靜態內部 IP (DIP) 以及如何管理"
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

# 如何設定靜態內部私人 IP
在大部分情況下，您不需要針對虛擬機器指定靜態內部 IP 位址。虛擬網路中的 VM 會從您指定的範圍自動接收內部 IP 位址。但在某些情況下，針對特定 VM 指定靜態 IP 位址是合理的。例如，如果您的 VM 即將執行 DNS 或將成為網域控制站。

>[AZURE.NOTE]靜態內部 IP 位址會伴隨 VM 而存在，甚至是透過停止/取消佈建狀態。

## 如何驗證特定 IP 位址是否可用
若要驗證 IP 位址 *10.0.0.7* 在稱為 *TestVnet* 的 VNet 中是否可用，請執行下列 PowerShell 命令，並驗證 *IsAvailable* 的值：

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]如果您想要在安全環境中測試上述命令，請遵循＜[建立虛擬網路](https://msdn.microsoft.com/library/azure/dn631643.aspx)＞中的指導方針，建立稱為 *TestVnet* 的 VNet，並確保其會使用 *10.0.0.0/8* 位址空間。

## 如何在建立 VM 時指定靜態內部 IP
下方 PowerShell 指令碼會建立稱為 *TestService* 的新雲端服務，接著從 Azure 中擷取映像，然後在新的雲端服務中使用擷取的映像建立稱為 *TestVM* 的 VM，接下來設定要位於稱為 *Subnet-1* 之子網路的 VM，並設定 *10.0.0.7* 作為 VM 的靜態內部 IP：

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## 如何擷取 VM 的靜態內部 IP 資訊
若要檢視使用上述指令碼建立之 VM 的 IP 資訊，請執行下列 PowerShell 命令，並觀察 *IpAddress* 的值：

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## 如何從 VM 移除靜態內部 IP
若要移除在上述指令碼中新增至 VM 的靜態內部 IP，請執行下列 PowerShell 命令：
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## 如何將靜態內部 IP 位址新增至現有的 VM
若要將靜態內部 IP 新增至使用上述指令碼建立的 VM，請執行下列命令：

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## 後續步驟

[保留的 IP](../virtual-networks-reserved-public-ip)

[執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip)

[保留的 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=July15_HO2-->