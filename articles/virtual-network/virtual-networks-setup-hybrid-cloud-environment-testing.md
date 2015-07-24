<properties 
	pageTitle="設定用於測試的混合式雲端環境" 
	description="了解如何建立 IT 專業或開發測試的混合式雲端環境。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# 設定用於測試的混合式雲端環境

本主題會引導您逐步建立 Microsoft Azure 的混合式雲端環境進行測試。以下是產生的組態。

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

這會從您在網際網路上的位置模擬實際的混合式生產環境。其中包括：

-  簡化的內部部署網路 (公司網路子網路)。
-  Azure (TestVNET) 代管的跨單位部署虛擬網路。
-  站對站 VPN 連線。
-  TestVNET 虛擬網路中的第二個網域控制站。

這個組態一般可供您開始：

-  在混合式雲端環境中開發和測試應用程式。
-  建立電腦的測試組態，一些在公司網路子網路上，一些在 TestVNET 虛擬網路內，用於以雲端為基礎的混合式 IT 工作負載。

設定此混合式雲端測試環境分為五個主要階段：

1.	設定公司網路子網路上的電腦。
2.	設定 RRAS1。
3.	建立跨單位 Azure 虛擬網路。
4.	建立站對站 VPN 連線。
5.	設定 DC2。 

如果您仍沒有 Azure 訂用帳戶，可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 上註冊免費試用版。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

>[AZURE.NOTE]Azure 中的虛擬機器和虛擬網路閘道會在執行時持續耗用成本。這項成本是按照您的免費試用版、MSDN 訂閱或付費訂閱進行計算。若要在您不使用時降低執行這個測試環境的成本，請參閱此主題中的[將這個環境的持續成本降至最低](#costs)，以取得更多資訊。

此設定要求一個測試子網路，其中最多包含四部使用公用 IP 位址直接連線到網際網路的電腦。如果您沒有這些資源，您也可以[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。模擬的混合式雲端測試環境僅要求 Azure 訂用帳戶。

## 第 1 階段：設定 Corpnet 子網路上的電腦

使用[測試實驗室指南：適用於 Windows Server 2012 R2 的基本組態](http://www.microsoft.com/download/details.aspx?id=39638)的＜設定 Corpnet 子網路的步驟＞一節中的指示，在名為 Corpnet 的子網路上設定 DC1、APP1 和 CLIENT1 電腦。**這個子網路必須與貴公司的網路隔離，因為它會透過 RRAS1 電腦直接連線到網際網路。**

接著，使用 DC1 CORP\\User1 認證登入。若要設定 CORP 網域，讓電腦和使用者使用其本機網域控制站進行驗證，請從系統管理員層級 Windows PowerShell 命令提示字元執行這些命令。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET

這是您目前的組態。

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## 第 2 階段：設定 RRAS1

RRAS1 提供公司網路子網路上的電腦與 TestVNET 虛擬網路之間的流量路由和 VPN 裝置服務。RRAS1 必須安裝兩張網路介面卡。

首先，在 RRAS1 上安裝作業系統。

1.	開始安裝 Windows Server 2012 R2。
2.	遵循指示完成安裝，指定本機系統管理員帳戶的強式密碼。使用本機系統管理員帳戶登入。
3.	將 RRAS1 連線到具有網際網路存取的網路，並執行 Windows Update 以安裝 Windows Server 2012 R2 的最新更新。
4.	將一個網路介面卡連接到公司網路子網路，並且將另一個直接連接到網際網路。RRAS1 可以位於網際網路防火牆後方，但是不可位在網路位址轉譯器 (NAT) 後方。

接著，設定 RRAS1 的 TCP/IP 內容。您將需要公開的 IP 位址組態，包括位址、子網路遮罩 (或前置長度)，以及網際網路服務提供者 (ISP) 的預設閘道和 DNS 伺服器。

在 RRAS1 上的系統管理員層級 Windows PowerShell 命令提示字元下使用下列命令。在執行這些命令之前，先填入變數值並移除 < and > 字元。您可以從 **Get-NetAdapter** 命令的顯示，取得目前的網路介面卡名稱。

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter –Name $corpnetAdapterName –NewName Corpnet
	Rename-NetAdapter –Name $internetAdapterName –NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength –DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
	New-NetFirewallRule –DisplayName “Allow ICMPv4-Out” –Protocol ICMPv4 –Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

對於最後一個命令，請確認有四個來自 IP 位址 10.0.0.1 的回應。

這是您目前的組態。

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## 第 3 階段：建立跨單位 Azure 虛擬網路。

首先，使用您的 Azure 訂用帳戶認證登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並建立名為 TestVNET 的虛擬網路。

1.	在 Azure 管理入口網站的工作列上，依序按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]。
2.	在 [虛擬網路詳細資料] 頁面上，於 [名稱] 中輸入 **TestVNET**。
3.	在 [位置] 中，為您的所在位置選取適合的資料中心。
4.	按 [下一步] 箭頭。
5.	在 [DNS 伺服器和 VPN 連線] 頁面上，於 [DNS 伺服器] 的 [選取或輸入名稱] 中輸入 **DC1**，並於 [IP 位址] 中輸入 **10.0.0.1**，然後選取 [設定網站間 VPN]。
6.	在 [區域網路] 中，選取 [指定新的區域網路]。 
7.	按 [下一步] 箭頭。
8.	在 [站台對站連線] 頁面上：
	- 在 [名稱] 中，輸入 **Corpnet**。 
	- 在 [VPN 裝置 IP 位址] 中，輸入指派給 RRAS1 網際網路介面的公用 IP 位址。
	- 在 [位址空間] 的 [起始 IP] 欄中，輸入 **10.0.0.0**。在 [CIDR (位址計數)] 中，選取 [/8]，然後按一下 [新增位址空間]。
9.	按 [下一步] 箭頭。
10.	在 [虛擬網路位址空間] 頁面上：
	- 在 [位址空間] 的 [起始 IP] 中，選取 [192.168.0.0]。
	- 在 [子網路] 中，按一下 [Subnet-1] 並使用 **TestSubnet** 來取代名稱。 
	- 在 TestSubnet 的 [CIDR (位址計數)] 欄中，按一下 [/24 (256)]。
	- 按一下 [新增閘道子網路]。
11.	按一下 [完成] 圖示。等候虛擬網路建立後再繼續。

接下來，按照[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md) 中的操作方法，在本機電腦安裝 Azure PowerShell。

接著，建立 TestVNET 虛擬網路的新雲端服務。您必須選擇唯一的名稱。例如，您可以將它命名為 TestVNET-*UniqueSequence*，其中的 *UniqueSequence* 是貴公司的縮寫。例如，如果貴公司名稱為 Tailspin Toys，您可以將雲端服務命名為 TestVNET-Tailspin。

您可以在本機電腦使用下列 Azure PowerShell 命令，測試名稱是否不重複。

	Test-AzureName -Service <Proposed cloud service name>

如果這個命令傳回「False」，表示您設定的名稱不重複。使用這個命令建立雲端服務。

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

接著，建立 TestVNET 虛擬網路的新儲存體帳戶。您必須選擇唯一的名稱。您可以使用這個命令測試儲存體帳戶名稱是否不重複。

	Test-AzureName -Storage <Proposed storage account name>

如果這個命令傳回「False」，表示您設定的名稱不重複。使用這些命令建立並設定儲存體帳戶。

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

這是您目前的組態。

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## 第 4 階段：建立網站間 VPN 連線。

首先，您將建立虛擬網路閘道。

1.	在本機電腦的 Azure 管理入口網站中，按一下左窗格中的 [網路]，然後確認 TestVNET 的 [狀態] 已設定為 [已建立]。
2.	按一下 [TestVNET]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
3.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
4.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestVNET 虛擬網路的 Azure VPN 閘道公用 IP 位址。您需要這個 IP 位址才能設定 RRAS1。
5.	在工作列上，按一下 [管理金鑰]，然後按一下金鑰旁邊的複製圖示，將它複製到 [剪貼簿]。將這個金鑰貼入文件並加以儲存。您需要這個金鑰值才能設定 RRAS1。 

接著，使用路由及遠端存取服務設定 RRAS1，做為公司網路子網路的 VPN 裝置。以本機系統管理員身分登入RRAS1，並在 Windows PowerShell 命令提示字元下執行這些命令。

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

接著，設定 RRAS1 接收來自 Azure VPN 閘道的站對站 VPN 連線。重新啟動 RRAS1，然後以本機系統管理員身分登入RRAS1，並在 Windows PowerShell 命令提示字元下執行這些命令。您需要提供 Azure VPN 閘道的 IP 位址和金鑰值。

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

接著，移至本機電腦上的 Azure 管理入口網站，等候 TestVNET 虛擬網路顯示 [已連線] 狀態。

接著，設定 RRAS1 支援網際網路位置的轉譯流量。在 RRAS1：

1.	從 [開始] 畫面，輸入 **rras**，然後按一下 [路由及遠端存取]。 
2.	在主控台樹狀結構中，開啟伺服器名稱，然後按一下 [IPv4]。
3.	以滑鼠右鍵按一下 [一般]，然後按一下 [新增路由通訊協定]。
4.	按一下 [NAT]，然後按一下 [確定]。
5.	在主控台樹狀結構中，以滑鼠右鍵按一下 [NAT]、依序按一下 [新增介面] 和 [Corpnet]，然後按一下 [確定] 兩次。
6.	以滑鼠右鍵按一下 [NAT]，然後依序按一下 [新增介面]、[網際網路] 及 [確定]。
7.	在 [NAT] 索引標籤上，按一下 [連線到網際網路的公用介面]、選取 [在此介面上啟用 NAT]，然後按一下 [確定]。


接著，設定 DC1、APP1 和 CLIENT1 使用 RRAS1 作為其預設閘道。
 
在 DC1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue –Router 10.0.0.2

如果介面的名稱不是乙太網路，請使用 **Get-NetAdapter** 命令來決定介面名稱。

在 APP1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2

在 CLIENT1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	ipconfig /renew

這是您目前的組態。
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## 第 5 階段：設定 DC2。

首先，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 DC2 的 Azure 虛擬機器。

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


接著，登入新的 DC2 虛擬機器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後按一下 DC2 [狀態] 欄中的 [執行中]。
2.	在工作列上，按一下 [連接]。 
3.	當系統提示開啟 DC2.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用下列：
	- 名稱：**DC2\**[本機系統管理員帳戶名稱]
	- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DC2 的系統管理員層級 Windows PowerShell 命令提示字元下，執行：

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.1 的 4 次成功回覆。這是站對站 VPN 連線的流量測試。

接著，將額外的資料磁碟新增為磁碟機代號 F: 的新磁碟區。

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [磁碟] 群組中，按一下 [磁碟 2] ([磁碟分割] 設為 [不明])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 **[確定]**。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接著，將 DC2 設定為 corp.contoso.com 網域的複本網域控制站。從 DC2 的 Windows PowerShell 命令提示字元執行下列命令。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

請注意，系統將提示您提供 CORP\\User1 密碼和目錄服務還原模式 (DSRM) 密碼，並重新啟動 DC2。

由於 TestVNET 虛擬網路有自己的 DNS 伺服器 (DC2)，因此您必須設定 TestVNET 的虛擬網路使用這個 DNS 伺服器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [網路]，然後按一下 [TestVNET]。
2.	按一下 [設定]。
3.	在 [DNS 伺服器] 中，移除 **10.0.0.1** 項目。
4.	在 [DNS 伺服器] 中，加入含 **DC2** 的項目做為名稱，並加入 **192.168.0.4** 做為 IP 位址。 
5.	在底部的命令列中按一下 [**儲存**]。
6.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後按一下 DC2 旁邊的 [狀態] 欄。
7.	在命令列中按一下 [**重新啟動**]。等候 DC2 重新啟動。


這是您目前的組態。

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
混合式雲端環境到此準備就緒，可以進行測試。

## 其他資源

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 進行測試](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure 混合式雲端測試環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure 基礎結構服務實作指導方針](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## 將此環境的持續成本降至最低

若要將在此環境中執行虛擬機器的成本降至最低，請盡速執行所需的測試和示範，然後在不使用時予以刪除或關閉虛擬機器。例如，您可以使用 Azure 自動化和 Runbook 在每個營業日結束時自動關閉 Test_VNET 虛擬網路中的虛擬機器。如需詳細資訊，請參閱[開始使用 Azure 自動化](../automation-create-runbook-from-samples.md)。

Azure VPN 閘道會實作為一組會產生持續成本的兩個 Azure 虛擬機器。如需詳細資訊，請參閱[定價 - 虛擬網路](http://azure.microsoft.com/pricing/details/virtual-network/)。若要將這個 VPN 閘道的成本降至最低，請使用這些步驟建立測試環境，並盡速執行所需的測試和示範或刪除閘道。

1.	從本機電腦的 Azure 管理入口網站中，依序按一下左窗格中的 [網路]、[TestVNET] 及 [儀表板]。
2.	在工作列中，按一下 [刪除閘道]。出現提示時，按一下 [是]。等候閘道刪除而且其狀態變更為 [尚未建立閘道]。

如果您刪除閘道，而且要還原測試環境，您必須先建立新的閘道。

1.	從本機電腦的 Azure 管理入口網站中，按一下左窗格中的 [網路]，然後按一下 [TestVNET]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
2.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
3.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestVNET 虛擬網路的 Azure VPN 閘道新公用 IP 位址。您需要這個 IP 位址才能重新設定 RRAS1。
4.	在工作列上，按一下 [管理金鑰]，然後按一下金鑰旁邊的複製圖示，將它複製到 [剪貼簿]。將這個金鑰值貼入文件並加以儲存。您需要這個金鑰值才能重新設定 RRAS1。 

接著，以本機系統管理員身分登入 RRAS1，並在系統管理員層級 Windows PowerShell 命令提示字元下執行這些命令，使用新的公用 IP 位址和預先共用金鑰重新設定 RRAS1。

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

接著，移至您本機電腦上的 Azure 管理入口網站，等候 TestVNET 虛擬網路顯示連線狀態。
 

<!---HONumber=July15_HO2-->