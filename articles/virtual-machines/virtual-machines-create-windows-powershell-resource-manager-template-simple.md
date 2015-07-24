<properties
	pageTitle="利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器"
	description="利用 Resource Manager 範本和 Azure PowerShell 建立新的 Windows 虛擬機器。"
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
	ms.date="04/29/2015"
	ms.author="josephd"/>

# 利用資源管理員範本和 Azure PowerShell 建立 Windows 虛擬機器

您可以使用資源管理員範本搭配 Azure PowerShell，輕鬆建立 Windows 架構的新 Azure 虛擬機器。這個範本建立的單一虛擬機器會採用 Windows ，而且是在新資源群組的單一子網路的新虛擬網路上執行。

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

開始之前，請遵循這些指示，以確定您已設定 Azure、Windows PowerShell 和 Azure PowerShell，而且準備就緒：

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## 建立 Windows 虛擬機器

依照下列步驟，搭配 Azure PowerShell 並使用 Github 範本儲存機制中的資源管理員範本來建立 Windows 虛擬機器。

針對下列每個範例執行命令，並提供您的 Azure 部署名稱、資源群組名稱，以及 Azure 資料中心位置。請注意，執行 **New-AzureResourceGroupDeployment** 命令時，系統會提示您提供 JSON 檔案 "parameters" 區段中的參數值。指定所有的參數值後，這個命令會建立資源群組和虛擬機器。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

您會看到類似下列畫面：

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

在新的資源群組中，您現在擁有新的 Windows 虛擬機器，名稱是 MyWindowsVM。

## 其他資源

[Azure 資源管理員提供的 Azure 計算、網路和儲存體提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure 資源管理員概觀](../resource-group-overview.md)

[利用 Azure 資源管理員和 Azure PowerShell 建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-resource-manager.md)

[利用 Azure PowerShell 和 Azure 服務管理員建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-service-manager.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

<!---HONumber=July15_HO2-->