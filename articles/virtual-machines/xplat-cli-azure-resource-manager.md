<properties
	pageTitle="搭配使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure CLI 與 Azure 資源管理 | Microsoft Azure"
	description="搭配使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure CLI 與 Azure 資源管理。"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# 使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)


此主題描述如何使用 Mac、Linux 和 **arm** 模式 Windows 適用的 Azure CLI 建立、管理以及刪除 Azure 資源與 VM。

>[AZURE.NOTE]若要使用命令列建立與管理 Azure 資源，您必須有 Azure 帳戶 ([免費試用版](http://azure.microsoft.com/pricing/free-trial/))。您還需要[安裝 Azure CLI](../xplat-cli-install.md)，並[登入使用您帳戶的相關 Azure 資源](../xplat-cli-connect.md)。如果上述事項都已完成，您就能開始進行了。

## Azure 資源

資源管理員可讓您將一組_資源_ (使用者管理的實體，例如虛擬機器、資料庫伺服器、資料庫或網站) 作為單一邏輯單位管理，或作為_資源群組_管理。您可以在命令列上以命令建立、管理及刪除這些資源，就如同在 **asm** 模式。

您也可以使用 **arm** 模式，以_宣告_的方式來描述可在 JSON *範本*中部署的資源群組其結構與關係，來管理 Azure 資源。範本會說明可在執行命令時內嵌的參數，或儲存於個別 JSON **azuredeploy-parameters.json** 檔案中的參數。如此一來，您只要提供不同的參數，就能使用相同的範本輕易建立新資源。例如，建立網站的範本將具有網站名稱的參數、網站將放置的區域，以及其他共同參數。

使用範本修改或建立群組時，會建立_部署_，接著將其套用至群組。如需資源管理員的詳細資訊，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

## 驗證

若要透過 Azure CLI 使用資源管理員，您必須使用公司或學校的帳戶驗證 Microsoft Azure。透過 .publishsettings 檔案安裝的憑證無法進行驗證。

如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](../xplat-cli-connect.md)。

> [AZURE.NOTE]您使用的公司或學校帳戶是由 Azure Active Directory 管理，因此您也可以使用 Azure 角色式存取控制 (RBAC) 管理 Azure 資源的存取與使用。如需詳細資訊，請參閱[管理與稽核資源存取權](../resource-group-rbac.md)。

## 設定 **arm** 模式

因為根據預設不啟用資源管理員模式，您必須使用下列命令來啟用 Azure CLI 資源管理員命令。

	azure config mode arm

>[AZURE.NOTE]Azure 資源管理員模式與 Azure 服務管理模式是互斥的。亦即，任一模式所建立的資源，將無法由另一種模式來管理。

## 尋找位置

大部分的 **arm** 命令需要有可用來建立或尋找資源的有效位置。您可以使用命令來尋找所有可用的位置

	azure location list

這會列出區域特定的位置，例如 "West US"、"East US" 等。

## 建立資源群組

資源群組是網路、儲存體和其他資源的邏輯群組。**arm** 模式中幾乎所有命令都需要資源群組。您可以使用命令建立名為 _testrg_ 的資源群組，例如使用命令

	azure group create -n "testrg" -l "West US"

在此之後，您可以開始將資源新增到此群組中，並用它來設定新的虛擬機器。

## 建立虛擬機器

有兩種方式可在 **arm** 模式中建立虛擬機器：

1. 使用個別的 Azure CLI 命令
2. 使用資源群組範本

在您開始使用任何一種方法之前，請務必先建立至少一個資源群組。

### 使用個別的 Azure CLI 命令

這是根據您的需求設定和建立虛擬機器的基本方法。在 **arm** 模式中，您必須先設定某些必要的資源，像是網路功能，才能使用 **vm create** 命令。

>[AZURE.NOTE]如果您是第一次在命令列上為您的訂用帳戶建立資源，系統可能會提示您註冊特定的資源提供者。如果發生這種情況，很容易會註冊到上述的提供者，並再次嘗試到失敗的命令。例如，
>
> `azure provider register Microsoft.Storage`
>
> 您可以透過執行下列命令來找出為您的訂用帳戶註冊的提供者清單。
>
> `azure provider list`


#### 建立公用 IP 資源

您必須建立公用 IP，才能對新的虛擬機器執行 SSH，以進行任何有意義的工作。建立公用 IP 十分簡單。命令需要資源群組、您公用 IP 資源的名稱和位置 (依此順序)。例如，

	azure network public-ip create "testrg" "testip" "westus"

#### 建立網路介面卡資源

網路介面卡或 NIC 需要先建立子網路和虛擬網路。若在特定的位置和資源群組中建立虛擬網路，請使用 **network vnet create**。

	azure network vnet create "testrg" "testvnet" "westus"

在此範例中，您可以接著使用 **network vnet subnet create** 在此虛擬網路中建立子網路：

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

您應讓能夠透過 **network nic create** 來使用這些資源建立 NIC。

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]雖然不是必要，但很重要，就是將公用 IP 名稱作為參數傳遞給 **network nic create** 命令，因為 NIC 與此 IP 間的這個連結稍後會用於對使用此 NIC 建立的虛擬機器執行 SSH。

如需 **network** 命令的詳細資訊，請參閱命令列說明或[透過 Azure 資源管理使用 Azure CLI](azure-cli-arm-commands.md)。

#### 尋找作業系統映像

目前，您只可以根據影像的發佈者尋找作業系統。換句話說，您必須執行這個命令，才能在所需的位置中找到作業系統映像發佈者清單。

	azure vm image list-publishers "westus"

然後從清單中選擇發佈者，接著舉例來說，按照發佈者尋找映像清單執行，

	azure vm image list "westus" "CoreOS"

最後，從如下所示的清單中選擇作業系統映像：

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

記下您想要在虛擬機器上載入映像的 URN。

#### 建立虛擬機器

您現在已經準備好透過執行 **vm create** 命令並傳送必要資訊來建立虛擬機器。在此階段選擇性地傳遞公用 IP，因為 NIC 已經有此資訊。您的命令可能看起來如下，其中 _testvm_ 是在 _testrg_ 資源群組中建立的虛擬機器名稱。

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

您應該能夠透過下列方法啟動此虛擬機器：執行

	azure vm start "testrg" "testvm"

並使用 **ssh username@ipaddress** 命令對它執行 SSH。若要快速查閱您公用 IP 資源的 IP 位址，請使用此命令：

	azure network public-ip show "testrg" "testip"

使用 **vm** 命令能夠很輕鬆地管理此虛擬機器，如需詳細資訊，請參閱[透過 Azure 資源管理使用 Azure CLI](azure-cli-arm-commands.md)。

### **vm quick-create** 捷徑

新的 **vm quick-create** 捷徑能夠減略以命令建立 VM 的方法中大部分的步驟。當您想要嘗試建立簡單的虛擬機器，或者您不在意網路組態，此捷徑十分方便。它是互動式命令，您只需要先找出作業系統映像 URN，再執行。

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

Azure CLI 將使用預設的 VM 大小建立虛擬機器。它也會建立儲存體帳戶、NIC、虛擬網路和子網路，以及公用 IP。開機後，您可以使用公用 IP 對虛擬機器執行 SSH。

### 使用資源群組範本

#### 尋找和設定資源群組範本

1. 使用範本時，您可以建立自己的範本、使用其中一個資源庫，或使用 [github](https://github.com/azurermtemplates/azurermtemplates) 中的範本。首先，讓我們使用資源庫中名為 **CoreOS.CoreOSStable.0.2.40-preview** 的範本。若要列出範本庫的可用範本，請使用下列命令。因為有數千個範本可使用，所以請務必將結果重新編頁，或使用 **grep** 或 **findstr** (在 Windows 上)，或您最愛的 string-searching 命令找到您想用的範本。或者，您可以使用 **--json** 選項，並下載 JSON 格式的完整清單以方便搜尋。

		azure group template list

	回應將會列出發行者和範本名稱，並將出現以下的類似結果 (雖然會有更多)。

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. 若要檢視範本的詳細資料，請使用以下命令。

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	如此會傳回範本的描述資訊。我們使用的範本會建立 Linux 虛擬機器。

3. 選取範本後，可使用下列命令下載範本。

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	下載範本可讓您自訂該範本，如此能更貼近您的需求。例如，將其他資源新增至範本。

	>[AZURE.NOTE]如果您修改範本，請先使用 `azure group template validate` 命令來驗證範本，再使用它來建立或修改現有的資源群組。

4. 若要設定您要使用的資源群組範本，請在文字編輯器中開啟範本檔案。請注意，**parameters** JSON 集合靠近頂端。這包含此範本預期的參數清單，目的是建立範本所說明的資源。部分參數有預設值，而其他參數會指定值的類型或允許的值範圍。

	使用範本時，您可以將參數做為命令列參數的一部分提供，或藉由指定包含參數值的檔案提供。您也可以直接在範本的 **parameters** 區段中撰寫 **value** 欄位，雖然這會讓範本緊密地與特定的部署連結，因而無法輕鬆地重複使用。無論如何，參數都必須是 JSON 格式，並且您必須為沒有預設值的金鑰提供您自己的值。

	例如，若要建立一個檔案並包含 **CoreOS.CoreOSStable.0.2.40-preview** 範本的參數，請使用下列資料建立名為 **params.json** 的檔案。用您自己的值來取代此範例中使用的值。[**位置**] 應指定您附近的 Azure 區域，例如 [**北歐**] 或[ **美國中南部**]。(此範例使用 [**美國西部**])

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. 儲存 **params.json** 檔案後，請使用下列命令以根據範本建立新資源群組。`-e` 參數會指定前一個步驟所建立的 **params.json** 檔案。使用您想要的群組名稱取代 **testRG**，並使用您的部署名稱取代 **testDeploy**。位置應與您在 **params.json** 範本參數檔案中指定的位置相同。

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	上傳部署後，但在將部署套用至群組中資源前，此命令將傳回 [OK]。若要檢查部署的狀態，請使用下列命令。

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState** 會顯示部署的狀態。

	如果您部署成功，會看到類似下列的輸出：

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]如果您認為您的設定不正確，並需要停止長期執行的部署，請使用下列命令。
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> 若未提供部署名稱，將根據範本檔案的名稱自動建立一個名稱。`azure group create` 命令的輸出將傳回該名稱。

6. 若要檢視群組，請使用下列命令。

		azure group show "testRG"

	此命令會傳回群組中資源的相關資訊。如果您有多個群組，可使用 `azure group list` 命令來擷取群組名稱清單，然後使用 `azure group show` 來檢視特定群組的詳細資料。

7. 您也可以直接使用 github 中最新的範本，無須從資源庫下載。開啟 [Github.com](http://www.github.com) 並搜尋 AzureRmTemplates。選取 AzureRmTemplates 儲存機制，然後尋找任何您想用的範本，例如 _101-simple-vm-from-image_。如果您按一下範本，會看到它包含其他檔案都有的 **azuredeploy.json**。這就是您要使用 **--template-url** 選項在命令中使用的範本。以 _raw_ 模式開啟範本，然後複製瀏覽器網址列中的 URL。接著，您可以使用如下的命令直接使用此 URL 建立部署，不用從資源庫下載

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]請務必以 _raw_ 模式開啟 json 範本。瀏覽器網址列中出現的 URL，與一般模式中出現的不同。在 github 檢視檔案時，若要以 _raw_ 模式開啟檔案，請按一下右上角的 [_Raw_] 按鈕。

#### 使用資源

雖然範本可讓您宣告設定中整個群組的變更，但有時您只需要使用特定的資源。您可以使用 `azure resource` 命令執行此工作。

> [AZURE.NOTE]使用 `list` 命令以外的 `azure resource` 命令時，必須指定您正透過 `-o` 參數使用之資源的 API 版本。如果不確定要使用的 API 版本，請咨詢範本檔案並尋找資源的 **apiVersion** 欄位。

1. 若要列出群組中的所有資源，請使用下列命令。

		azure resource list "testRG"

2. 若要檢視個別資源，請在群組中使用下列命令。

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	請注意 **Microsoft.ClassicCompute/virtualMachines** 參數。這表示您正在要求哪類資源的資訊。如果您查看稍早下載的範本檔案，您會發現此相同的值可用來定義範本中說明的虛擬機器資源類型。

	此命令會傳回虛擬機器的相關資訊。

3. 檢視資源的詳細資料時，使用 `--json` 參數通常很有實用，因為此參數可製作更具可讀性的輸出，其中部分值是巢狀結構或集合。下列示範會將 show 命令的結果作為 JSON 文件傳回。

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]您可以使用 &gt; 字元將輸出導向檔案，藉此將 JSON 資料儲存至檔案。例如：
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. 若要刪除現有的資源，請使用下列命令。

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## 記錄

若要檢視群組上執行之作業的記錄資訊，請使用 `azure group log show` 命令。依預設，這會列出群組上執行的最後一個作業。若要檢視所有作業，請使用選用的 `--all` 參數。針對前次部署，使用 `--last-deployment`。針對特定部署，使用 `--deployment` 並指定部署名稱。下列範例傳回針對群組 'MyGroup' 執行的所有作業記錄。

	azure group log show mygroup --all

## 後續步驟

* 如需使用 Azure 命令列介面 (Azure CLI) 的詳細資訊，請參閱[安裝和設定 Azure CLI][clisetup]。
* 如需使用 Azure PowerShell 來使用資源管理員的詳細資訊，請參閱 [Azure PowerShell 搭配 Azure 資源管理員使用](../powershell-azure-resource-manager.md)
* 如果從 Azure 入口網站使用資源管理員的相關資訊，請參閱[使用資源群組管理 Azure 資源][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760
 

<!---HONumber=62-->