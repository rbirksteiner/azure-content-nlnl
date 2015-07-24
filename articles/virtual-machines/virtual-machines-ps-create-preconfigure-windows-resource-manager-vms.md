<properties
	pageTitle="利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器"
	description="了解如何使用 Azure PowerShell 在 Azure 中建立和預先設定 Windows 和以資源管理員為基礎的虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="josephd"/>

# 利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器

下列步驟將顯示如何以 Azure PowerShell 的資源管理員模式建構命令集，以建立及預先設定以 Windows 為基礎的 Azure 虛擬機器。您可以使用此建置組塊程序，對於以 Windows 為基礎的新虛擬機器快速建立命令集，並擴充現有部署。您也可以用它來建立多個命令集以快速建置自訂開發/測試或 IT 專業人員環境。

這些步驟遵循建立 Azure PowerShell 命令集合的填空方法。如果您剛使用 PowerShell 或只想知道可指定哪些值來成功設定組態，這個方法相當實用。如果您是進階 PowerShell 的使用者，您可以使用命令並取代本身的變數值 (以 "$" 開頭的行)。

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [使用 Azure PowerShell 建立和預先設定 Windows 型虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)

## 步驟 1：安裝 Azure PowerShell

您也必須有 Azure PowerShell 0.9.0 版或更新版本。如果您尚未安裝和設定 Azure PowerShell，請按一下[這裡](../powershell-install-configure.md)以取得指示。

您可以在 Azure PowerShell 提示字元下使用這個命令來檢查已安裝的 Azure PowerShell 版本。

	Get-Module azure | format-table version

範例如下。

	Version
	-------
	0.9.0

如果您沒有 0.9.0 版或更新版本，則必須使用 [控制台] 中的 [程式和功能] 移除 Azure PowerShell，然後安裝最新版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 步驟 2：設定您的訂用帳戶

首先，請執行 Azure PowerShell 提示字元。

接下來，在 Azure PowerShell 提示字元執行這些命令，以設定您的 Azure 訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

您可以從此命令的顯示取得正確的訂用帳戶名稱。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

接下來，將 Azure PowerShell 切換至資源管理員模式。

	Switch-AzureMode AzureResourceManager 

## 步驟 3：建立所需的資源

以資源管理員為基礎的虛擬機器需要資源群組。如果需要，請使用這些命令建立新虛擬機器的新資源群組。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

若要判斷唯一的資源群組名稱，請使用此命令列出現有的資源群組。

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

若要列出您可以在其中建立以資源管理員為基礎之虛擬機器的 Azure 位置，請使用這些命令。

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

以資源管理員為基礎的虛擬機器需要以資源管理員為基礎的儲存體帳戶。如果需要，請使用這些命令建立新虛擬機器的新儲存體帳戶。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

您必須為儲存體帳戶挑選只包含小寫字母和數字的全域唯一名稱。您可以使用此命令列出現有的儲存體帳戶。

	Get-AzureStorageAccount | Sort Name | Select Name

若要測試選擇的儲存體帳戶名稱是否為全域唯一的，您必須以 PowerShell 的 Azure 服務管理模式執行 **Test-AzureName** 命令。執行這些命令。

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

如果 Test-AzureName 命令顯示 "False"，表示您提出的名稱是唯一的。當您決定好唯一的名稱時，請利用此命令將 Azure PowerShell 切換回資源管理員模式。

	Switch-AzureMode AzureResourceManager 

以資源管理員為基礎的虛擬機器可以使用公用網域名稱標籤，其中只能包含字母、數字和連字號。欄位中的第一個和最後一個字元，必須是字母或數字。

若要測試選擇的網域名稱標籤是否為全域唯一的，請使用這些命令。

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Get-AzureCheckDnsAvailability -DomainQualifiedName $domName -Location $loc 
	
如果 DNSNameAvailability 為 "True"，表是您提出的名稱是全域唯一的。

以資源管理員為基礎的虛擬機器可置於以資源管理員為基礎的可用性集。如果需要，請使用這些命令建立新虛擬機器的新可用性集。

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

使用此命令列出現有的可用性集。

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

以資源管理員為基礎的虛擬機器需要以資源管理員為基礎的虛擬網路。如果需要，請使用新虛擬機器的至少一個子網路建立以資源管理員為基礎的新虛擬網路。以下是具有兩個名為 frontendSubnet 和 backendSubnet 之子網路的新虛擬網路範例。

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

使用這些命令列出現有的虛擬網路。

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## 步驟 4：建置命令集

開啟您所選的或 PowerShell 整合式指令碼環境 (ISE) 的文字編輯器之新執行個體，並複製下列行以啟動您的命令集。指定這個新的虛擬機器的資源群組名稱、Azure 位置和儲存體帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

在虛擬網路中，您必須指定以資源管理員為基礎之虛擬網路的名稱和子網路的索引編號。使用這些命令列出虛擬網路的子網路。

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets 

子網路索引是此命令所顯示的子網路編號，從左至右依序為它們編號，從 0 開始。

關於此範例：

	PS C:> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets
	
	Subnets
	-------
	{frontendSubnet, backendSubnet}

FrontendSubnet 的子網路索引為 0，而 backendSubnet 的子網路索引為 1。

將這幾行複製到您的命令集，並指定現有的虛擬網路名稱和虛擬機器的子網路索引。

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

接下來，您要建立網路介面卡 (NIC)、要求公用 IP 位址，以及選擇性地指派 DNS 網域名稱標籤給它。將下列兩個選項的其中一個複製到命令集，並填入 NIC 名稱和 DNS 網域名稱標籤。

選項 1：指定 NIC 名稱。

將這幾行複製到您的命令集，並指定 NIC 的名稱。

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

選項 2：指定 NIC 名稱和 DNS 網域名稱標籤。

將這幾行複製到您的命令集，並指定 NIC 的名稱與全域唯一的網域名稱標籤。當您以 Azure PowerShell 的服務管理模式建立虛擬機器時，Azure 會自動完成這些步驟。

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

接下來，建立本機 VM 物件，並選擇性地將它新增至可用性集。將下列兩個選項的其中一個複製到命令集，並填入名稱、大小和可用性集名稱。

選項 1：指定虛擬機器名稱和大小。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

若要判斷選項 1 之 VM 大小字串的可能值，請使用這些命令。

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

選項 2：指定虛擬機器名稱和大小，並將它新增至可用性集。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

若要判斷選項 2 之 VM 大小字串的可能值，請使用這些命令。

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]利用資源管理員，您目前只能在建立虛擬機器期間，將其新增至可用性集。

若要將其他資料磁碟新增至 VM，請將這幾行複製到您的命令集，並指定磁碟設定。

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

接下來，您必須判斷虛擬機器發行者、優惠以及映像的 SKU。以下是常用的、以 Windows 為基礎的映像資料表。

|發行者名稱 | 優惠名稱 | SKU 名稱
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

如果未列出您需要的虛擬機器映像，請使用[這裡](resource-groups-vm-searching.md#powershell)的指示來判斷發行者、優惠以及 SKU 名稱。

將這些命令複製到您的命令集，並填入發行者、優惠和 SKU 名稱。

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

最後，將這些命令複製到您的命令集，並填入 VM 作業系統磁碟的名稱識別項。

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 步驟 5：執行命令集

檢閱在步驟 4 中使用文字編輯器或 PowerShell ISE 以多個命令區塊建立的 Azure PowerShell 命令集。確定您已指定所需的所有變數，並且這些變數具有正確的值。也確定已移除所有 < and > 字元。

如果您讓命令位於文字編輯器中，請將命令集複製到剪貼簿，然後以滑鼠右鍵按一下 [開啟 Azure PowerShell 提示字元]。這將發出命令集作為一系列的 PowerShell 命令，並建立 Azure 虛擬機器。或者，從 Azure PowerShell ISE 執行命令集。

如果您將再次建立這個虛擬機器或類似的虛擬機器，您可以將這個命令集儲存為 PowerShell 指令碼檔案 (*.ps1)。

## 範例

我需要 PowerShell 命令集以建立 web 式企業營運工作負載的其他虛擬機器：

- 位於現有的 LOBServers 資源群組
- 使用 Windows Server 2012 R2 Datacenter 映像
- 名稱為 LOB07，且位於現有的 WEB_AS 可用性集
- 在現有 AZDatacenter 虛擬網路的 FrontEnd 子網路 (子網路索引 0) 中具有內附公用 IP 位址的 NIC
- 有 200 GB 的額外資料磁碟 

以下是建立這個虛擬機器的相對應 Azure PowerShell 命令集，以步驟 4 中所述的程序為基礎。

	# Switch to the Resource Manager mode	
	Switch-AzureMode AzureResourceManager
	
	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosoLOBServersSA"
	
	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Create the NIC
	$nicName="AzureInterface"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id
	
	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty
	
	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	
	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](../resource-group-overview.md)

[使用資源管理員範本和 PowerShell 部署以及管理 Azure 虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)

[利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)
 

<!---HONumber=July15_HO2-->