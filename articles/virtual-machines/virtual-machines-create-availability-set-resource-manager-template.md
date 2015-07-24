<properties
	pageTitle="使用 Azure 資源管理員範本建立可用性設定組"
	description="描述如何利用可用性設定組範本以及包含範本語法"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# 使用 Azure 資源管理員範本建立可用性設定組

您可以使用 Azure PowerShell 或 Azure 命令列 (CLI) 再加上一個資源管理員範本，即可輕鬆為虛擬機器建立一套可用性設定組。這個範本會建立可用性設定組。

開始之前，請確定您已設定 Azure PowerShell 和 Azure CLI，並且準備就緒。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## 使用 Resource Manager 範本建立可用性設定組

請依照下列步驟，搭配 Azure PowerShell 並使用 Github 範本儲存機制中的資源管理員範本，建立虛擬機器的可用性設定組。

### 步驟 1：下載 JSON 檔案

指派一個本機資料夾，當做 JSON 範本檔案的存放位置，然後建立這個資料夾 (例如，C:\\Azure\\Templates\\availability)。

取代資料夾名稱，然後複製以及執行以下命令。

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### 步驟 2：收集所需參數的詳細資料

當您使用範本時，將需提供詳細資料，例如位置和設定組名稱。若要了解範本所需的參數，請執行下列其中一項：

- 在[這裡](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/)檢閱參數的清單。
- 使用您選擇的工具或文字編輯器開啟 JSON 檔案。尋找檔案中的 "parameters" 區段，這裡會列出當使用者設定虛擬機器時，範本所需的每一個參數。

收集所需的資訊，如此可在輸入前備妥這些資訊。當您執行命令來部署範本時，會提示您輸入資訊。

### 步驟 3：建立可用性設定組。

下列各節為您示範如何使用 Azure PowerShell 或 Azure CLI 來執行這項操作。

### 使用 Azure PowerShell

填寫 Azure 部署名稱、資源群組名稱、Azure 位置、儲存的 JSON 檔案放在哪一個資料夾，然後執行以下命令。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

執行 **New-AzureResourceGroupDeployment** 命令時，會提示您在 JSON 檔案 **"parameters"** 區段中提供參數的值。完成此作業之後，這個命令會建立資源群組和可用性設定組。

以下是這個範本的 PowerShell 命令集範例。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

您應該看到類似這樣的結果。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

要移除這個資源群組和其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用這個命令。

	Remove-AzureResourceGroup –Name "<resource group name>"


## 使用 Azure CLI

請依照下列步驟，搭配 Azure CLI 命令並使用 Github 範本儲存機制中的資源管理員範本建立可用性設定組。

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=July15_HO2-->