<properties
	pageTitle="搭配 Azure 服務管理使用適用於 Mac、Linux 和 Windows 的 Azure CLI | Microsoft Azure"
	description="了解如何使用適用於 Mac、Linux 和 Windows 的命令列工具，以便使用 Azure CLI asm 模式管理 Azure。"
	services="virtual-machines, mobile-services, cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# 使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 服務管理

本主題說明如何在 **arm** 模式下使用 Azure CLI，在 Mac、Linux 和 Windows 電腦的命令列上建立、管理和刪除服務。此功能類似於隨 Azure SDK for .NET、Node.JS 和 PHP 安裝的 Windows PowerShell Cmdlet 所提供的功能。

> [AZURE.NOTE]使用 Azure 服務搭配 **asm** 模式在概念上類似於個別 Azure 概念和服務的思考模式，例如網站、虛擬機器、虛擬網路、儲存體等等。使用 **arm** 模式時，可以透過以邏輯方式分組的階層式資源模型，在命令列上使用更豐富的功能。若要切換到該模式，請參閱[搭配使用 Azure 命令列介面與資源管理員](xplat-cli-azure-resource-manager.md)。

如需安裝指示，請參閱[安裝與設定 Azure 命令列介面](../xplat-cli-install.md)。

選用參數會以方括號括住 (例如，[parameter])。其他所有參數皆為必要參數。

除了本文所述的命令特定選用參數，還有三個選用參數可用來顯示詳細輸出，例如要求選項和狀態碼。-v 參數提供詳細資訊輸出，而 -vv 參數提供更詳細的詳細資訊輸出。--json 選項將以原始 json 格式輸出結果。

## 設定 **arm** 模式

目前，當您首次安裝 CLI 時，預設會啟用服務管理模式。如有需要，請使用下列命令來啟用 Azure CLI 服務管理命令。

	azure config mode asm

>[AZURE.NOTE]Azure 資源管理員模式與 Azure 服務管理模式是互斥的。亦即，任一模式所建立的資源，將無法由另一種模式來管理。

## 管理帳戶資訊及發佈設定
此工具會使用您的 Azure 訂閱資訊來連線至您的帳戶。您可以從 Azure 入口網站取得這些資訊 (在發佈設定檔中，本文將有說明)。您可以匯入發佈設定檔，作為這個工具後續作業所使用的持續性本機組態設定。您的發佈設定只需匯入一次即可。

**account download [options]**

此命令會啟動瀏覽器來從 Azure 入口網站下載 .publishsettings 檔。

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [options] &lt;file>**


此命令會匯入 publishsettings 檔或憑證，以供此工具持續使用。

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE]publishsettings 檔可能包含多個訂閱的詳細資料 (也就是訂閱名稱和 ID)。當您匯入 publishsettings 檔時，第一個訂閱將作為預設訂閱。若要使用不同的訂用帳戶，請執行下列命令。<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**account clear [options]**

此命令會移除已匯入的已儲存發佈設定。如果您在此機器上使用完此工具，而想要確保此工具日後不能再使用您的帳戶，請使用此命命。

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [options]**

列出匯入的訂閱

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [options] &lt;subscription&gt;**

設定目前訂閱

###管理同質群組的命令

**account affinity-group list [options]**

此命令會列出您的 Azure 同質群組。

當有一組虛擬機器跨越多部實體機器時，可以設定同質群組。同質群組指定實體機器應該盡可能彼此接近，以減少網路延遲。

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [options] &lt;name&gt;**

This command creates a new affinity group

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [options] &lt;name&gt;**

此命令會顯示同質群組的詳細資料

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [options] &lt;name&gt;**

此命令會刪除指定的同質群組

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###管理帳戶環境的命令

**account env list [options]**

列出帳戶環境清單

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [options] [environment]**

顯示帳戶環境詳細資料

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [options] [environment]**

此命令會將環境新增至帳戶

**account env set [options] [environment]**

此命令會設定帳戶環境

**account env delete [options] [environment]**

此命令會從帳戶中刪除指定的環境

## 管理 Azure 虛擬機器的命令
下圖顯示 Azure 虛擬機器裝載於 Azure 雲端服務之生產部署環境中的情形。

![Azure Technical Diagram](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** 會在 Blob 儲存體 (也就是圖中的 e:) 建立磁碟機；**attach** 會將已建立但未連接的磁碟連接至虛擬機器。

**vm create [options] &lt;dns-name> &lt;image> &lt;userName> [password]**

此命令會建立新的 Azure 虛擬機器。依預設，每個虛擬機器 (VM) 都是建立於專屬的雲端服務中；不過，您可以透過本文所述的 -c 選項，指定應該將虛擬機器加入現有的雲端服務。

如同 Azure 入口網站，vm create 命令只會在生產部署環境中建立虛擬機器。沒有在雲端服務的預備部署環境中建立虛擬機器的選項。如果您的訂閱沒有現有的 Azure 儲存體帳戶，這個命令會建立一個帳戶。

您可以透過 --location 參數指定位置，或透過 --affinity-group 參數指定同質群組。如果都不提供，系統會提示您從有效位置清單中提供一個位置。

提供的密碼必須有 8 至 123 個字元，並符合此虛擬機器所用之作業系統本身的密碼複雜度需求。

如果您預期需要使用 SSH 管理已部署的 Linux 虛擬機器 (此乃常見情況)，您必須在建立虛擬機器時透過 -e 選項啟用 SSH。建立虛擬機器後，就無法啟用 SSH。

日後若要讓 Windows 虛擬機器啟用 RDP，可新增連接埠 3389 作為端點。

此命令可支援下列選用參數：

**-c, --connect** 於主機服務中已建立的部署內建立虛擬機器。如果使用這個選項時並未加上 -vmname，則會自動產生新虛擬機器的名稱。<br /> **-n, --vm-name** 指定虛擬機器的名稱。此參數預設使用主機服務名稱。如果未指定 -vmname，則會以 &lt;service-name>&lt;id> 格式產生新虛擬機器的名稱，其中 &lt;id> 是服務中現有虛擬機器的編號加 1。例如，如果您使用這個命令將新的虛擬機器加入至已有一個虛擬機器的主機服務 MyService，則新虛擬機器的名稱會是 MyService2。<br /> **-u, --blob-url** 指定要在其中建立虛擬機器系統磁碟的目標 Blob 儲存體 URL。<br /> **-z, --vm-size** 指定虛擬機器的大小。有效值為："ExtraSmall"、"Small"、"Medium"、"Large"、"ExtraLarge"、"A5"、"A6"、"A7"、"A8"、"A9"、"A10"、"A11"、"Basic_A0"、"Basic_A1"、"Basic_A2"、"Basic_A3"、"Basic_A4"、"Standard_D1"、"Standard_D2"、"Standard_D3"、"Standard_D4"、"Standard_D11"、"Standard_D12"、"Standard_D13"、"Standard_D14"、"Standard_DS1"、"Standard_DS2"、"Standard_DS3"、"Standard_DS4"、"Standard_DS11"、"Standard_DS12"、"Standard_DS13"、"Standard_DS14"、"Standard_G1"、"Standard_G2"、"Standard_G3"、"Standard_G4"、"Standard_G55"。預設值為 "Small"。<br /> **-r** 將 RDP 連線功能新增至 Windows 虛擬機器。<br /> **-e, --ssh** 將 SSH 連線功能新增至 Windows 虛擬機器。<br /> **-t, --ssh-cert** 指定 SSH 憑證。<br /> **-s** 訂用帳戶 <br /> **-o, --community** 所指定的影像為社群影像 <br /> **-w** 虛擬網路名稱 <br/> **-l, --location** 指定位置 (例如，「美國中北部」)。<br /> **-a, --affinity-group** 指定同質群組。<br /> **-w, --virtual-network-name** 指定要將虛擬機器新增到的虛擬網路。若要設定和管理虛擬網路，可以從 Azure 入口網站進行。<br /> **-b, --subnet-names** 指定要對虛擬機器指派的子網路名稱。

在此範例中，MSFT__Win2K8R2SP1-120514-1520-141205-01-zh-tw-30GB 是由平台所提供的映像。如需作業系統映像的詳細資訊，請參閱 vm image list。

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;dns-name> &lt;role-file>**

此命令會從 JSON 角色檔案建立新的 Azure 虛擬機器。

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [options]**

此命令會列出 Azure 虛擬機器。--json 選項會指定要以原始 JSON 格式傳回結果。

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [options]**

此命令會列出所有可用的 Azure 帳戶位置。

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [options] &lt;name>**

此命令會顯示 Azure 虛擬機器的詳細資料。--json 選項會指定要以原始 JSON 格式傳回結果。

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [options] &lt;name>**

此命令會刪除 Azure 虛擬機器。依預設，這個命令不會刪除作業系統磁碟和資料磁碟從中建立的 Azure Blob。若要刪除 Blob 和其中的虛擬機器，請指定 -b 選項。

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [options] &lt;name>**

此命令會啟動 Azure 虛擬機器。

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [options] &lt;name>**

此命令會重新啟動 Azure 虛擬機器。

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [options] &lt;name>**

此命令會關閉 Azure 虛擬機器。您可以使用 -p 選項指定關閉時不要釋放計算資源。

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;vm-name> &lt;target-image-name>**

此命令會擷取 Azure 虛擬機器映像。

只有在虛擬機器狀態為 [已停止] 時，才能擷取虛擬機器映像。關閉虛擬機器，然後再繼續。

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [options] &lt;vm-name> &lt;file-path>**

此命令會將 Azure 虛擬機器映像匯出至檔案

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##  管理 Azure 虛擬機器端點的命令
下圖顯示某個虛擬機器之多個執行個體的一般部署架構。請注意，在此範例中，每個虛擬機器上的連接埠 3389 皆開啟 (供進行 RDP 存取)，且每個虛擬機器上皆有內部 IP 位址 (例如，168.55.11.1)，供負載平衡器用來將流量路由傳送至虛擬機器。此內部 IP 位址也可用於進行虛擬機器之間的通訊。

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

給虛擬機器的外部要求會先通過負載平衡器。因此，遇到含多個虛擬機器的部署，不能在要求中指定要找特定虛擬機器。當部署含多個虛擬機器時，必須設定虛擬機器 (vm-port) 與負載平衡器 (lb-port) 之間的連接埠對應。

**vm endpoint create &lt;vm-name> &lt;lb-port> [vm-port]**

此命令會建立虛擬機器端點。您也可以使用 -u 或 --enable-direct-server-return 指定是否在此端點上啟用直接伺服器傳回，預設為停用。

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [options] &lt;vm-name> &lt;lb-port>[:&lt;vm-port>[:&lt;protocol>[:&lt;enable-direct-server-return>[:&lt;lb-set-name>[:&lt;probe-protocol>[:&lt;probe-port>[:&lt;probe-path>[:&lt;internal-lb-name>]]]]]]]] {1-*}**

建立多個 vm 端點。

**vm endpoint delete [options] &lt;vm-name> &lt;endpoint-name>**

此命令會刪除虛擬機器端點。

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;vm-name>**

此命令會列出所有虛擬機器端點。--json 選項會指定要以原始 JSON 格式傳回結果。

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [options] &lt;vm-name> &lt;endpoint-name>**

此命令會使用下列選項將 vm 端點更新為新值。

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [options] &lt;vm-name>**

此命令會顯示 vm 上各端點的詳細資料

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

## 管理 Azure 虛擬機器映像的命令

虛擬機器映像是已設定之虛擬機器的快照，可方便複寫到需要的虛擬機器上。

**vm image list [options]**

此命令會取得虛擬機器映像的清單。映像類型可分為三種：Microsoft 建立的映像 (名稱開頭為 "MSFT")、協力廠商建立的映像 (名稱開頭通常為廠商的名稱)，以及您自己建立的映像。若要建立映像，您可以擷取現有的虛擬機器，也可以從上傳至 Blob 儲存體的自訂 .vhd 來建立映像。如需關於使用自訂 .vhd 的詳細資訊，請參閱 vm image create。--json 選項會指定要以原始 JSON 格式傳回結果。

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-zh-tw-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.zh-tw.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.zh-tw.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-zh-tw-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-zh-tw-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-zh-tw-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [options] &lt;name>**

這個命令會顯示虛擬機器映像的詳細資料。

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [options] &lt;name>**

此命令會刪除虛擬機器映像。

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;name> [source-path]**

此命令會建立虛擬機器映像。您的自訂 .vhd 檔會上傳至 Blob 儲存體，而系統就會從中建立虛擬機器映像。然後，您便可以使用此虛擬機器映像來建立虛擬機器。需要 Location 與 OS 參數。

某些系統會設有每一程序的檔案描述元限制。如果超過此限制，工具將顯示檔案描述元限制錯誤。您可以使用 -p &lt;number> 參數再次執行此命令，減少平行上傳數上限。預設的平行上傳數上限為 96。

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

## 管理 Azure 虛擬機器資料磁碟的命令

資料磁碟是 Blob 儲存體中可由虛擬機器使用的 .vhd 檔。如需關於如何將資料磁碟部署至 Blob 儲存體的詳細資訊，請參閱稍早所示的 Azure 技術圖。

連接資料磁碟的命令 (azure vm disk attach 及 azure vm disk attach-new) 會按照 SCSI 通訊協定的要求，將邏輯單元編號 (LUN) 指派給連接的資料磁碟。連接至虛擬機器的第一個資料磁碟將被指派 LUN 0，下一個將被指派 LUN 1，依此類推。

使用 azure vm disk detach 命令卸離資料磁碟時，請使用 &lt;lun&gt; 參數指出要卸離的磁碟。

> [AZURE>NOTE] 請注意，您應該務必按照反向順序卸離資料磁碟，亦即從被指派最高編號 LUN 的資料磁碟開始。Linux SCSI 層不支援在較高編號的 LUN 仍然連接的情況下，將較低編號的 LUN 卸離。例如，如果 LUN 1 仍然連接，您就不應該卸離 LUN 0。

**vm disk show [options] &lt;name>**

此命令會顯示 Azure 磁碟的詳細資料。

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-zh-tw-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [options] [vm-name]**

這個命令會列出 Azure 磁碟 (或連接至指定虛擬機器的磁碟)。如果搭配虛擬機器名稱參數執行，則會傳回所有連接至虛擬機器的磁碟。Lun 1 會隨虛擬機器建立，而其他任何列出的磁碟則需另外連接。

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

執行此命令而不加上虛擬機器名稱參數將傳回所有磁碟。

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [options] &lt;name>**

此命令會從個人存放庫中刪除 Azure 磁碟。刪除磁碟前，必須先從虛擬機器卸離磁碟。

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;name> [source-path]**

此命令會上傳並註冊 Azure 磁碟。必須指定 --blob-url、--location，或 --affinity-group。如果您使用此命令並加上 [source-path]，則會上傳指定的 .vhd 檔，並建立新的映像。您可以接著使用 vm disk attach，將此映像連接至虛擬機器。

某些系統會設有每一程序的檔案描述元限制。如果超過此限制，工具將顯示檔案描述元限制錯誤。您可以使用 -p &lt;number> 參數再次執行此命令，減少平行上傳數上限。預設的平行上傳數上限為 96。

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [options] &lt;source-path> &lt;blob-url> &lt;storage-account-key>**

此命令可讓您上傳 vm 磁碟

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;vm-name> &lt;disk-image-name>**

此命令會將 Blob 儲存體中的現有磁碟連接至雲端服務中部署的現有虛擬機器。

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;vm-name> &lt;size-in-gb> [blob-url]**

此命令會將資料磁碟連接至 Azure 虛擬機器。在此範例中，20 (GB) 是要連接之新磁碟的大小。您可以選擇性地使用 Blob URL 作為最後一個引數，明確指定要建立的目標 Blob。如果不指定 Blob URL，則會自動產生 Blob 物件。

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;vm-name> &lt;lun>**

此命令會卸離連接至 Azure 虛擬機器的資料磁碟。&lt;lun> 會識別要卸離的磁碟。若要在卸離磁碟前取得與磁碟相關聯之磁碟的清單，請使用 vm disk-list &lt;vm-name>。

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

## 管理 Azure 雲端服務的命令

Azure 雲端服務是 Web 角色與工作角色上裝載的應用程式和服務。下列命令可用來管理 Azure 雲端服務。

**service create [options] &lt;serviceName>**

此命令會建立新的雲端服務

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [options] &lt;serviceName>**

此命令會顯示 Azure 雲端服務的詳細資料

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [options]**

此命令會列出 Azure 雲端服務。

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [options] &lt;name>**

此命令會刪除 Azure 雲端服務。

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

若要強制刪除，請使用 `-q` 參數。


## 管理 Azure 憑證的命令

Azure 服務憑證是與您 Azure 帳戶連線的 SSL 憑證。如需 Azure 憑證的詳細資訊，請參閱[管理憑證](http://msdn.microsoft.com/library/azure/gg981929.aspx)。

**service cert list [options]**

此命令會列出 Azure 憑證。

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;dns-prefix> &lt;file> [password]**

此命令會上傳憑證。如果憑證沒有密碼保護，請將密碼提示留空。

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [options] &lt;thumbprint>**

此命令會刪除憑證。

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK

## 管理 Web 應用程式的命令

Azure Web 應用程式是一種可透過 URI 存取的 Web 組態。Web 應用程式是在虛擬機器中託管，但您不需要親自考量虛擬機器的建立和部署細節。Azure 會為您處理這些細節。

**site list [options]**

此命令會列出您的 Web 應用程式。

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [options] [name]**

此命令會設定您 Web 應用程式 [name] 的組態選項

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [options]**

此命令會產生自訂部署指令碼

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [options] [name]**

此命令會建立新的 Web 應用程式和本機目錄。

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE]網站名稱必須是唯一的。您無法建立與現有網站的 DNS 同名的網站。

**site browse [options] [name]**

此命令會在瀏覽器中開啟您的 Web 應用程式。

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [options] [name]**

此命令會顯示 Web 應用程式的詳細資料。

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [options] [name]**

此命令會刪除 Web 應用程式。

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [options] [name]**

這個命令會交換兩個 Web 應用程式位置。

此命令還支援下列其他選項：

**-q or **--quiet**：不顯示確認提示。請在自動化指令碼中使用此選項。


**site start [options] [name]**

此命令會啟動 Web 應用程式。

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [options] [name]**

此命令會停止 Web 應用程式。

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [options] [name]

這個命令會停止再啟動指定的 Web 應用程式。

此命令還支援下列其他選項：

**--slot** &lt;slot>：要重新啟動之位置的名稱。


**site location list [options]**

此命令會列出您的 Web 應用程式位置。

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###管理 Web 應用程式的應用程式設定的命令

**site appsetting list [options] [name]**

此命令會列出新增至 Web 應用程式的應用程式設定。

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [options] &lt;keyvaluepair> [name]**

此命令會將應用程式設定新增至您的 Web 應用程式成為金鑰值組。

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [options] &lt;key> [name]**

這個命令會從 Web 應用程式中刪除指定的應用程式設定。

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [options] &lt;key> [name]**

此命令會顯示所指定應用程式設定的詳細資料

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###管理 Web 應用程式憑證的命令

**site cert list [options] [name]**

此命令會顯示 Web 應用程式憑證的清單。

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [options] &lt;certificate-path> [name]**

**site cert delete [options] &lt;thumbprint> [name]**

**site cert show [options] &lt;thumbprint> [name]**

此命令會顯示憑證詳細資料

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###管理 Web 應用程式連接字串的命令

**site connectionstring list [options] [name]**

**site connectionstring add [options] &lt;connectionname> &lt;value> &lt;type> [name]**

**site connectionstring delete [options] &lt;connectionname> [name]**

**site connectionstring show [options] &lt;connectionname> [name]**

###管理 Web 應用程式預設文件的命令

**site defaultdocument list [options] [name]**

**site defaultdocument add [options] &lt;document> [name]**

**site defaultdocument delete [options] &lt;document> [name]**

###管理 Web 應用程式部署的命令

**site deployment list [options] [name]**

**site deployment show [options] &lt;commitId> [name]**

**site deployment redeploy [options] &lt;commitId> [name]**

**site deployment github [options] [name]**

**site deployment user set [options] [username] [pass]**

###管理 Web 應用程式網域的命令

**site domain list [options] [name]**

**site domain add [options] &lt;dn> [name]**

**site domain delete [options] &lt;dn> [name]**

###管理 Web 應用程式處理常式對應的命令

**site handler list [options] [name]**

**site handler add [options] &lt;extension> &lt;processor> [name]**

**site handler delete [options] &lt;extension> [name]**

###管理 Web 工作的命令

**site job list [options] [name]**

此命令會列出 Web 應用程式下的所有 Web 工作。

此命令還支援下列其他選項：

+ **--job-type** &lt;job-type>：選用。Web 工作的類型。有效值為 "triggered" 或 "continuous"。預設會傳回所有類型的 Web 工作。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job show [options] &lt;jobName> &lt;jobType> [name]**

這個命令會顯示特定 Web 工作的詳細資料。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--job-type** &lt;job-type>：必要。Web 工作的類型。有效值為 "triggered" 或 "continuous"。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job delete [options] &lt;jobName> &lt;jobType> [name]**

這個命令會刪除指定的 Web 工作。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--job-type** &lt;job-type>：必要。Web 工作的類型。有效值為 "triggered" 或 "continuous"。
+ **-q** 或 **--quiet**：不顯示確認提示。請在自動化指令碼中使用此選項。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job upload [options] &lt;jobName> &lt;jobType> <jobFile> [name]**

這個命令會刪除指定的 Web 工作。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--job-type** &lt;job-type>：必要。Web 工作的類型。有效值為 "triggered" 或 "continuous"。
+ **--job-file** &lt;job-file>：必要。工作檔案。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job start [options] &lt;jobName> &lt;jobType> [name]**

這個命令會啟動指定的 Web 工作。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--job-type** &lt;job-type>：必要。Web 工作的類型。有效值為 "triggered" 或 "continuous"。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job stop [options] &lt;jobName> &lt;jobType> [name]**

這個命令會停止指定的 Web 工作。只可停止連續工作。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

###管理 Web 工作歷程記錄的命令

**site job history list [options] [jobName] [name]**

這個命令會顯示指定 Ｗeb 工作的執行歷程記錄。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

**site job history show [options] [jobName] [runId] [name]**

這個命令會取得指定 Ｗeb 工作所執行之工作的詳細資料。

此命令還支援下列其他選項：

+ **--job-name** &lt;job-name>：必要。Web 工作的名稱。
+ **--run-id** &lt;run-id>：選用。執行歷程記錄的 ID。如果未指定，則會顯示最近的執行。
+ **--slot** &lt;slot>：要重新啟動之位置的名稱。

###管理 Web 應用程式診斷的命令

**site log download [options] [name]**

下載包含 Web 應用程式診斷的 .zip 檔。

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [options] [name]**

這個命令會將您的終端機連線至記錄檔串流服務。

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [options] [name]**

此命令會設定 Web 應用程式的診斷選項。

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###管理 Web 應用程式儲存機制的命令

**site repository branch [options] &lt;branch> [name]**

**site repository delete [options] [name]**

**site repository sync [options] [name]**

###管理 Web 應用程式調整的命令

**site scale mode [options] &lt;mode> [name]**

**site scale instances [options] &lt;instances> [name]**


## 管理 Azure 行動服務的命令

Azure 行動服務整合了一組為應用程式啟用後端功能的 Azure 服務。行動服務命令可分為下列類別：

+ [管理行動服務執行個體的命令](#Mobile_Services)
+ [管理行動服務組態的命令](#Mobile_Configuration)
+ [管理行動服務資料表的命令](#Mobile_Tables)
+ [管理行動服務指令碼的命令](#Mobile_Scripts)
+ [管理排程工作的命令](#Mobile_Jobs)
+ [調整行動服務的命令](#Mobile_Scale)

下列選項適用於大部分行動服務命令：

+ **-h** 或 **--help**：顯示輸出用法資訊。
+ **-s `<id>`** 或 **--subscription `<id>`**：使用特定訂用帳戶 (以 `<id>` 形式指定)。
+ **-v** 或 **--verbose**：寫出詳細資訊輸出。
+ **--json**：寫出 JSON 輸出。

### <a name="Mobile_Services"></a>管理行動服務執行個體的命令

**mobile locations [options]**

此命令會列出行動服務支援的地理位置。

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [options] [servicename] [sqlAdminUsername] [sqlAdminPassword]**

此命令會建立行動服務以及相關的 SQL Database 和伺服器。

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

此命令還支援下列其他選項：

+ **-r `<sqlServer>`** 或 **--sqlServer `<sqlServer>`**：使用現有 SQL Database 伺服器 (以 `<sqlServer>` 形式指定)。
+ **-d `<sqlDb>`** 或 **--sqlDb `<sqlDb>`**：使用現有 SQL Database (以 `<sqlDb>` 形式指定)。
+ **-l `<location>`** 或 **--location `<location>`**：在特定位置 (以 `<location>` 形式指定) 建立服務。請執行 azure mobile locations 取得可用的位置。
+ **--sqlLocation `<location>`**：在特定 `<location>` 中建立 SQL Server；預設為行動服務的位置。

**mobile delete [options] [servicename]**

此命令會刪除行動服務以及相關的 SQL Database 和伺服器。

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

此命令還支援下列其他選項：

+ **-d** 或 **--deleteData**：從資料庫中刪除所有來自此行動服務的資料。
+ **-a** 或 **--deleteAll**：刪除 SQL Database 和伺服器。
+ **-q** 或 **--quiet**：不顯示確認提示。請在自動化指令碼中使用此選項。

**mobile list [options]**

此命令會列出您的行動服務。

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [options] [servicename]**

此命令會顯示行動服務的詳細資料。

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [options] [servicename]**

此命令會重新啟動行動服務執行個體。

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [options] [servicename]**

此命令會傳回行動服務記錄檔，其中已篩選掉 `error` 以外的所有記錄檔類型。

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

此命令還支援下列其他選項：

+ **-r `<query>`** 或 **--query `<query>`**：執行指定的記錄查詢。
+ **-t `<type>`** 或 **--type `<type>`**：依 `<type>` 項目 (可以是 `information`、`warning` 或 `error`) 篩選傳回的記錄檔。
+ **-k `<skip>`** 或 **--skip `<skip>`**：略過 `<skip>` 指定的資料列數目。
+ **-p `<top>`** 或 **--top `<top>`**：傳回 `<top>` 指定的特定資料列數目。

> [AZURE.NOTE]**--query** 參數的優先順序高於 **--type**、**--skip** 和 **--top**。

**mobile recover [options] [unhealthyservicename] [healthyservicename]**

這個命令會透過移至其他區域中健全的行動服務，來復原不健全的行動服務。

此命令還支援下列其他選項：

**-q** 或 **--quiet**：隱藏確認復原的提示。

**mobile key regenerate [options] [servicename] [type]**

此命令會重新產生行動服務應用程式金鑰。

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

金鑰類型可分為 `master` 和 `application`。

> [AZURE.NOTE]當您重新產生金鑰時，使用舊金鑰的用戶端可能無法存取您的行動服務。重新產生應用程式金鑰時，您應該以新的金鑰值更新您的應用程式。

**mobile key set [options] [servicename] [type] [value]**

這個命令會將行動服務金鑰設為特定值。


### <a name="Mobile_Configuration"></a>管理行動服務組態的命令

**mobile config list [options] [servicename]**

此命令會列出行動服務的組態選項。

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [options] [servicename] [key]**

此命令會取得行動服務的特定組態選項 (在此例中為動態結構描述)。

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [options] [servicename] [key] [value]**

此命令會設定行動服務的特定組態選項 (在此例中為動態結構描述)。

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


### <a name="Mobile_Tables"></a>管理行動服務資料表的命令

**mobile table list [options] [servicename]**

此命令會列出您行動服務中的所有資料表。

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [options] [servicename] [tablename]**

此命令會顯示特定資料表的傳回詳細資料。

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [options] [servicename] [tablename]**

此命令會建立資料表。

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

此命令還支援下列其他選項：

+ **-p `&lt;permissions>`** 或 **--permissions `&lt;permissions>`**：`<operation>`=`<permission>` 組的逗號分隔清單，其中 `<operation>` 是`insert`、`read`、`update` 或 `delete`，而 `&lt;permissions>` 是 `public`、`application` (預設值)、`user` 或 `admin`。

**mobile data read [options] [servicename] [tablename] [query]**

此命令會讀取資料表的資料。

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

此命令還支援下列其他選項：

+ **-k `<skip>`** 或 **--skip `<skip>`**：略過 `<skip>` 指定的資料列數目。
+ **-t `<top>`** 或 **--top `<top>`**：傳回 `<top>` 指定的特定資料列數目。
+ **-l** 或 **--list**：以清單格式傳回資料。

**mobile table update [options] [servicename] [tablename]**

此命令會將資料表的刪除權限變更為僅限管理員。

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

此命令還支援下列其他選項：

+ **-p `&lt;permissions>`** 或 **--permissions `&lt;permissions>`**：`<operation>`=`<permission>` 組的逗號分隔清單，其中 `<operation>` 是`insert`、`read`、`update` 或 `delete`，而 `&lt;permissions>` 是 `public`、`application` (預設值)、`user` 或 `admin`。
+ **--deleteColumn `<columns>`**：要刪除之欄的逗號分隔清單 (`<columns>` 形式)。
+ **-q** 或 **--quiet**：刪除欄而不顯示確認提示。
+ **--addIndex `<columns>`**：要包含到索引中之欄的逗號分隔清單。
+ **--deleteIndex `<columns>`**：要從索引中排除之欄的逗號分隔清單。

**mobile table delete [options] [servicename] [tablename]**

此命令會刪除資料表。

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

指定 -q 參數來刪除資料表而不顯示確認。這麼做可避免妨礙自動化指令碼執行。

**mobile data truncate [options] [servicename] [tablename]**

此命令會從資料表中移除所有列。

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>管理指令碼的命令

本節中的命令可用來管理屬於行動服務的伺服器指令碼。如需詳細資訊，請參閱[在行動服務中使用伺服器指令碼](../mobile-services/mobile-services-how-to-use-server-scripts.md)。

**mobile script list [options] [servicename]**

此命令會列出註冊的指令碼，包括資料表和排程器指令碼。

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [options] [servicename] [scriptname]**

此命令會將 insert 指令碼從 TodoItem 資料表下載到 `table` 子資料夾中名為 `todoitem.insert.js` 的檔案。

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

此命令還支援下列其他選項：

+ **-p `<path>`** 或 **--path `<path>`**：檔案中要儲存指令碼的位置，其中以目前的工作目錄為預設目錄。
+ **-f `<file>`** 或 **--file `<file>`**：儲存指令碼所在檔案的名稱。
+ **-o** 或 **--override**：覆寫現有的檔案。
+ **-c** 或 **--console**：將指令碼寫入至主控台而非檔案。

**mobile script upload [options] [servicename] [scriptname]**

此命令會從 `table` 子資料夾上傳名為 `todoitem.insert.js` 的新指令碼。

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

檔案的名稱必須由資料表和作業名稱組成，而且必須位在與執行命令的位置相對的 table 子資料夾中。您也可以使用 **-f `<file>`** 或 **--file `<file>`** 參數，指定不同的檔案名稱和路徑來指向含所要註冊指令碼的檔案。


**mobile script delete [options] [servicename] [scriptname]**

此命令會從 TodoItem 資料表中移除現有的 insert 指令碼。

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>管理排程工作的命令

本節中的命令可用來管理屬於行動服務的排程工作。如需詳細資訊，請參閱[排程作業](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx)。

**mobile job list [options] [servicename]**

此命令會列出排程工作。

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [options] [servicename] [jobname]**

此命令會建立名為 `getUpdates` 且排定為每小時執行一次的新工作。

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

此命令還支援下列其他選項：

+ **-i `<number>`** 或 **--interval `<number>`**：工作間隔 (以整數表示)；預設值為 `15`。
+ **-u `<unit>`** 或 **--intervalUnit `<unit>`**：_interval_ 的單位，這可以是下列其中一個值：
	+ **minute** (預設值)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (隨選工作)
+ **-t `<time>`** **--startTime `<time>`** 指令碼第一次執行的開始時間 (以 ISO 格式表示)；預設值為 `now`。

> [AZURE.NOTE]建立的新工作會是停用狀態，因為仍然必須上傳指令碼。請使用 **mobile script upload** 命令上傳指令碼，並使用 **mobile job update** 命令啟用工作。

**mobile job update [options] [servicename] [jobname]**

下列命令會將停用的 `getUpdates` 工作啟用。

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

此命令還支援下列其他選項：

+ **-i `<number>`** 或 **--interval `<number>`**：工作間隔 (以整數表示)；預設值為 `15`。
+ **-u `<unit>`** 或 **--intervalUnit `<unit>`**：_interval_ 的單位，這可以是下列其中一個值：
	+ **minute** (預設值)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (隨選工作)
+ **-t `<time>`** **--startTime `<time>`** 指令碼第一次執行的開始時間 (以 ISO 格式表示)；預設值為 `now`。
+ **-a `<status>`** 或 **--status `<status>`**：工作狀態，這可以是 `enabled` 或 `disabled`。

**mobile job delete [options] [servicename] [jobname]**

此命令會從 TodoList 伺服器中移除 getUpdates 排程工作。

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE]刪除工作也會連上傳的指令碼一起刪除。

### <a name="Mobile_Scale"></a>調整行動服務的命令

本節中的命令可用來調整行動服務。如需詳細資訊，請參閱[調整行動服務](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx) (英文)。

**mobile scale show [options] [servicename]**

此命令會顯示規模資訊，包括目前的計算模式和執行個體數目。

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [options] [servicename]**

此命令會將行動服務的規模從免費模式變更為進階模式。

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

此命令還支援下列其他選項：

+ **-c `<mode>`** 或 **--computeMode `<mode>`**：計算模式必須是 `Free` 或 `Reserved`。
+ **-i `<count>`** 或 **--numberOfInstances `<count>`**：以保留模式執行時使用的執行個體數目。

> [AZURE.NOTE]當您將計算模式設定為 `Reserved` 時，您在相同區域中的所有行動服務都會以進階模式執行。


###啟用行動服務預覽功能的命令

**mobile preview list [options] [servicename]**

這個命令會顯示指定服務上可用的預覽功能，以及是否已啟用這些功能。

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [options] [servicename] [featurename]**

這個命令會啟用行動服務的指定預覽功能。請注意，行動服務的預覽功能一旦啟用，便無法停用。

###管理行動服務 API 的命令

**mobile api list [options] [servicename]**

這個命令會顯示您為行動服務建立的行動服務自訂 API 清單。

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [options] [servicename] [apiname]**

建立行動服務自訂 API。

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

此命令還支援下列其他選項：

**-p** 或 **--permissions** &lt;permissions>：&lt;method>=&lt;permission> 組的逗號分隔清單。

**mobile api update [options] [servicename] [apiname]**

這個命令會更新指定的行動服務自訂 API。

此命令還支援下列其他選項：

此命令還支援下列其他選項：

+ **-p** 或 **--permissions** &lt;permissions>：&lt;method>=&lt;permission> 組的逗號分隔清單。
+ **-f** 或 **--force**：覆寫對權限中繼資料檔案所做的任何自訂變更。

**mobile api delete [options] [servicename] [apiname]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

這個命令會刪除指定的行動服務自訂 API。

###管理行動應用程式之應用程式設定的命令

**mobile appsetting list [options] [servicename]**

這個命令會顯示指定服務之行動應用程式的應用程式設定。

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [options] [servicename] [name] [value]**

這個命令會加入行動服務的自訂應用程式設定。

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [options] [servicename] [name]**

這個命令會移除行動服務的指定應用程式設定。

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [options] [servicename] [name]**

這個命令會移除行動服務的指定應用程式設定。

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

## 管理工具本機設定

本機設定是您的訂閱 ID 和預設儲存體帳戶名稱。

**config list [options]**

此命令會顯示組態設定。

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [options] &lt;name&gt;,&lt;value&gt;**

此命令會變更組態設定。

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

## 管理服務匯流排的命令

使用下列命令來管理您的服務匯流排帳戶

**sb namespace check [options] &lt;name>**

檢查服務匯流排命名空間是否合法且可供使用。

**sb namespace create &lt;name> &lt;location>**

建立新的服務匯流排命名空間。

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;name>**

移除命名空間。

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

列出已為您的帳戶建立的所有命名空間。

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

顯示所有可用命名空間位置的清單。

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;name>**

顯示特定命名空間的詳細資料。

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;name>**

檢查命名空間是否可供使用。

## 管理儲存體物件的命令

###管理儲存體帳戶的命令

**storage account list [options]**

這個命令會顯示您的訂閱上的儲存體帳戶。

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [options] <name>**

這個命令會顯示指定儲存體帳戶的相關資訊，包括 URI 和帳戶屬性。

**storage account create [options] <name>**

這個命令會根據提供的選項來建立儲存體帳戶。

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

此命令還支援下列其他選項：

+ **-e** 或 **--label** &lt;label>：儲存體帳戶的標籤。
+ **-d** 或 **--description** &lt;description>：儲存體帳戶的描述。
+ **-l** 或 **--location** &lt;name>：要建立儲存體帳戶所在的區域。
+ **-a** 或 **--affinity-group** &lt;name>：要與儲存體帳戶相關聯的同質群組。
+ **--type**：指出要建立的帳戶類型：包含備援選項 (LRS/ZRS/GRS/RAGRS) 的「Standard 儲存體」或「Premium 儲存體 (PLRS)」。

**storage account set [options] <name>**

這個命令會更新指定的儲存體帳戶。

	~$ azure storage account set mybasestorage --type GRS
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

此命令還支援下列其他選項：

+ **-e** 或 **--label** &lt;label>：儲存體帳戶的標籤。
+ **-d** 或 **--description** &lt;description>：儲存體帳戶的描述。
+ **-l** 或 **--location** &lt;name>：要建立儲存體帳戶所在的區域。
+ **--type**: 指出新的帳戶類型：包含備援選項 (LRS/ZRS/GRS/RAGRS) 的「Standard 儲存體」或「Premium 儲存體 (PLRS)」。

**storage account delete [options] <name>**

這個命令會刪除指定的儲存體帳戶。

此命令還支援下列其他選項：

**-q** 或 **--quiet**：不顯示確認提示。請在自動化指令碼中使用此選項。

###管理儲存體帳戶金鑰的命令

**storage account keys list [options] <name>**

這個命令會列出指定儲存體帳戶的主要和次要金鑰。

**storage account keys renew [options] <name>**

###管理儲存體容器的命令

**storage container list [options] [prefix]**

這個命令會顯示指定儲存體帳戶的儲存體容器清單。這個儲存體帳戶是以連接字串，或儲存體帳戶名稱和帳戶金鑰來指定。

此命令還支援下列其他選項：

+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯模式中執行 storage 命令。

**storage container show [options] [container]** **storage container create [options] [container]**

這個命令會建立指定儲存體帳戶的儲存體容器。這個儲存體帳戶是以連接字串，或儲存體帳戶名稱和帳戶金鑰來指定。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串
+ **--debug**：在偵錯模式中執行 storage 命令。

**storage container delete [options] [container]**

這個命令會刪除指定的儲存體容器。這個儲存體帳戶是以連接字串，或儲存體帳戶名稱和帳戶金鑰來指定。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯模式中執行 storage 命令。

**storage container set [options] [container]**

這個命令會設定儲存體容器的存取控制清單。這個儲存體帳戶是以連接字串，或儲存體帳戶名稱和帳戶金鑰來指定。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯模式中執行 storage 命令。

###管理儲存體 Blob 的命令

**storage blob list [options] [container] [prefix]**

這個命令會傳回指定儲存體容器中的儲存體 Blob 清單。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯模式中執行 storage 命令。

**storage blob show [options] [container] [blob]**

這個命令會顯示指定儲存體 Blob 的詳細資料。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-p** 或 **-prefix** &lt;prefix>：儲存體容器名稱前置詞。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯中執行 storage 命令。

**storage blob delete [options] [container] [blob]**

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-b** 或 **--blob** &lt;blobName>：要刪除的儲存體 Blob 的名稱。
+ **-q** 或 **--quiet**：移除指定的儲存體 Blob 而不進行確認。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯中執行 storage 命令。

**storage blob upload [options] [file] [container] [blob]**

這個命令會將指定的檔案上傳至指定的儲存體 Blob。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-b** 或 **--blob** &lt;blobName>：要上傳的儲存體 Blob 的名稱。
+ **-t** 或 **--blobtype** &lt;blobtype>：儲存體 blob 類型：分頁或區塊。
+ **-p** 或 **--properties** &lt;properties>：已上傳檔案的儲存體 Blob 屬性。屬性為 key=value 組並以分號 (;) 分隔。可用的屬性為 contentType、contentEncoding、contentLanguage 和 cacheControl。
+ **-m** 或 **--metadata** &lt;metadata>：已上傳檔案的儲存體 Blob 中繼資料。中繼資料為 key=value 組並以分號 (;) 分隔。
+ **--concurrenttaskcount** &lt;concurrenttaskcount>：並行上傳要求的最大數目。
+ **-q** 或 **--quiet**：複寫指定的儲存體 Blob 而不進行確認。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯中執行 storage 命令。

**storage blob download [options] [container] [blob] [destination]**

這個命令會下載指定的儲存體 Blob。

此命令還支援下列其他選項：

+ **--container** &lt;container>：要建立的儲存體容器的名稱。
+ **-b** 或 **--blob** &lt;blobName>：儲存體 Blob 名稱。
+ **-d** 或 **--destination** [destination]：下載的目的地檔案或目錄路徑。
+ **-m** 或 **--checkmd5**：已下載檔案的 check md5sum。
+ **--concurrenttaskcount** &lt;concurrenttaskcount> 並行上傳要求的最大數目
+ **-q** 或 **--quiet**：複寫目的地檔案而不進行確認。
+ **-a** 或 **--account-name** &lt;accountName>：儲存體帳戶名稱。
+ **-k** 或 **--account-key** &lt;accountKey>：儲存體帳戶金鑰。
+ **-c** 或 **--connection-string** &lt;connectionString>：儲存體連接字串。
+ **--debug**：在偵錯中執行 storage 命令。

## 管理 SQL Database 的命令

使用下列命令來管理您的 Azure SQL Database

###管理 SQL Server 的命令。

使用下列命令來管理您的 SQL Server

**sql server create &lt;administratorLogin> &lt;administratorPassword> &lt;location>**

建立新的資料庫伺服器

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;name>**

顯示伺服器詳細資料。

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

取得伺服器的清單。

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;name>**

刪除伺服器

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###管理 SQL Database 的命令

使用下列命令來管理您的 SQL Database。

**sql db create [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

建立新的資料庫執行個體

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

顯示資料庫詳細資料。

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [options] &lt;serverName> &lt;administratorPassword>**

列出資料庫。

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

刪除資料庫。

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###管理 SQL Server 防火牆規則的命令

使用下列命令來管理您的 SQL Server 防火牆規則

**sql firewallrule create [options] &lt;serverName> &lt;ruleName> &lt;startIPAddress> &lt;endIPAddress>**

建立 SQL Server 的新防火牆規則。

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [options] &lt;serverName> &lt;ruleName>**

顯示防火牆規則詳細資料。

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [options] &lt;serverName>**

列出防火牆規則。

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [options] &lt;serverName> &lt;ruleName>**

這個命令會刪除防火牆規則。

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

## 管理虛擬網路的命令

使用下列命令來管理您的虛擬網路

**network vnet create [options] &lt;location>**

建立新的虛擬網路。

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;name>**

顯示虛擬網路的詳細資料。

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**vnet list**

列出所有現有的虛擬網路。

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;name>**

刪除指定的虛擬網路。

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [file-path]**

對於進階網路組態，您可以將網路組態匯出到本機。請注意，匯出的網路組態包括 DNS 伺服器設定、虛擬網路設定、本機網站設定和其他設定。

**network import [file-path]**

匯入本機網路組態。

**network dnsserver register [options] &lt;dnsIP>**

在您的網路組態中註冊您打算用於進行名稱解析的 DNS 伺服器。

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

列出您網路組態中註冊的所有 DNS 伺服器。

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [options] &lt;dnsIP>**

從網路組態中移除 DNS 伺服器項目。

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=July15_HO1-->