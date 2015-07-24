<properties 
	pageTitle="設定用於測試的模擬混合式雲端環境" 
	description="了解如何使用兩個 Azure 虛擬網路和 VNet 對 VNet 連線，建立 IT 專業或開發測試的模擬混合式雲端環境。" 
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

# 設定用於測試的模擬混合式雲端環境

本主題會引導您使用兩個不同的 Azure 虛擬網路逐步建立 Microsoft Azure 的模擬混合式雲端環境進行測試。當您沒有直接的網際網路連線和可用的公用 IP 位址時，使用此組態做為[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)的替代方案。以下是產生的組態。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

這會模擬混合式雲端生產環境。其中包括：

- Azure 虛擬網路 (TestLab 虛擬網路) 中代管的模擬和簡化內部部署網路。
- Azure (TestVNET) 代管的模擬跨單位部署虛擬網路。
- 兩個虛擬網路之間的 VNet 對 VNet 連線。
- TestVNET 虛擬網路中的第二個網域控制站。

這一般可供您開始：

- 在模擬混合式雲端環境中開發和測試應用程式。
- 建立電腦的測試組態，一些在 TestLab 虛擬網路內，一些在 TestVNET 虛擬網路內，模擬以雲端為基礎的混合式 IT 工作負載。

設定此混合式雲端測試環境分為四個主要階段：

1.	設定 TestLab 虛擬網路。
2.	建立跨單位虛擬網路。
3.	建立 VNet 對 VNet VPN 連線。
4.	設定 DC2。 

如果您仍沒有 Azure 訂用帳戶，可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 上註冊免費試用版。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

>[AZURE.NOTE]Azure 中的虛擬機器和虛擬網路閘道會在執行時持續耗用成本。這項成本是按照您的免費試用版、MSDN 訂閱或付費訂閱進行計算。若要在您不使用時降低執行這個測試環境的成本，請參閱此主題中的[將這個環境的持續成本降至最低](#costs)，以取得更多資訊。


## 第 1 階段：設定 TestLab 虛擬網路

使用[基底組態測試環境 ](../virtual-machines/virtual-machines-base-configuration-test-environment.md)中的指示，在名稱為 TestLab 的 Azure 虛擬網路中設定 DC1、APP1 和 CLIENT1 電腦。

從本機電腦上的 Azure 管理入口網站中，使用 CORP\\User1 認證連線到 DC1。若要設定 CORP 網域，讓電腦和使用者使用其本機網域控制站進行驗證，請從系統管理員層級 Windows PowerShell 命令提示字元執行這些命令。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET"

這是您目前的組態。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## 第 2 階段：建立 TestVNET 虛擬網路

首先，建立名為 TestVNET 的新虛擬網路。

1.	在 Azure 管理入口網站的工作列上，依序按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]。
2.	在 [虛擬網路詳細資料] 頁面上，於 [名稱] 中輸入 **TestVNET**。
3.	在 [位置] 中，選取適當的位置。
4.	按 [下一步] 箭頭。
5.	在 [DNS 伺服器和 VPN 連線] 頁面上，於 [DNS 伺服器] 的 [選取或輸入名稱] 中輸入 **DC1**，然後按 [下一步] 箭頭。
6.	在 [虛擬網路位址空間] 頁面上：
	- 在 [位址空間] 的 [起始 IP] 中，選取或輸入 **192.168.0.0**。
	- 在 [子網路] 中，按一下 [Subnet-1] 並使用 **TestSubnet** 來取代名稱。 
	- 在 TestSubnet 的 [CIDR (位址計數)] 欄中，按一下 [/24 (256)]。
7.	按一下 [完成] 圖示。等候虛擬網路建立後再繼續。

接下來，按照[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md) 中的操作方法，在本機電腦安裝 Azure PowerShell。

接著，建立 TestVNET 虛擬網路的新雲端服務。您必須選擇唯一的名稱。例如，您可以將它命名為 **TestVNET-***UniqueSequence*，其中的 *UniqueSequence* 是貴公司的縮寫。例如，如果貴公司名稱為 Tailspin Toys，您可以將雲端服務命名為 **TestVNET-Tailspin**。

您可以在本機電腦使用這個 Azure PowerShell 命令，測試名稱是否不重複。

	Test-AzureName -Service <Proposed cloud service name>

如果這個命令傳回「False」，表示您設定的名稱不重複。使用這個命令建立雲端服務。

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

這是您目前的組態。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##第 3 階段：建立 VNet 對 VNet 連線

首先，您可以建立區域網路代表每個虛擬網路的位址空間。

1.	從本機電腦上的 Azure 管理入口網站中，按一下 [新增 > 網路服務 > 虛擬網路 > 新增區域網路]。
2.	在 [指定區域網路的詳細資料] 頁面中，於 [名稱] 中輸入 **TestLabLNet**，並且於 [VPN 裝置 IP 位址] 中輸入 **131.107.0.1**，然後按一下向右箭號。
3.	在 [指定位址空間] 頁面的 [啟動 IP] 中，輸入 **10.0.0.0**。
4.	在 [CIDR (位址計數)] 中，選取 **/24 (256)**，然後按一下核取記號。
5.	按一下 [新增 > 網路服務 > 虛擬網路 > 新增區域網路]。
6.	在 [指定區域網路的詳細資料] 頁面中，於 [名稱] 中輸入 **TestVNETLNet**，並且於 [VPN 裝置 IP 位址] 中輸入 **131.107.0.2**，然後按一下向右箭號。
7.	在 [指定位址空間] 頁面的 [啟動 IP] 中，輸入 **192.168.0.0**。
8.	在 [CIDR (位址計數)] 中，選取 **/24 (256)**，然後按一下核取記號。

請注意，131.107.0.1 和 131.107.0.2 的 VPN 裝置 IP 位址都只是暫時預留位置值，您可以設定兩個虛擬網路的閘道。

接著，您可以設定每個虛擬網路使用站對站 VPN 連線以及對應於另一個虛擬網路的本機網路。

1.	從本機電腦上的 Azure 管理入口網站中，按一下左窗格中的 [網路]，然後確認 [TestLab] 的 [狀態] 欄是設定為 [已建立]。
2.	按一下 [TestLab]，然後按一下 [設定]。在 TestLab 頁面的 [站台對站連線] 區段中，按一下 [連線到區域網路]。 
3.	在 [區域網路] 中，請選取 [TestVNETLNet]。
4.	按一下工作列中的 [儲存]。在某些情況下，您可能需要按一下 [新增閘道子網路] 以建立由 Azure VPN 閘道使用的子網路。
5.	按一下左窗格中的 [網路]，然後確認 TestVNET 的 [狀態] 欄是設定為 [已建立]。
6.	按一下 [TestVNET]，然後按一下 [設定]。在 TestVNET 頁面的 [站台對站連線] 區段中，按一下 [連線到區域網路]。 
7.	在 [區域網路] 中，請選取 [TestLabLNet]。
8.	按一下工作列中的 [儲存]。在某些情況下，您可能需要按一下 [新增閘道子網路] 以建立由 Azure VPN 閘道使用的子網路。

接著，您將建立兩個虛擬網路的虛擬網路閘道。

1.	從 Azure 管理入口網站的 [網路] 頁面上，按一下 [TestLab]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
2.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
3.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestLab 虛擬網路的 Azure VPN 閘道公用 IP 位址。記錄此 IP 位址，您將需要此項來設定 VNet 對 VNet 連線。
4.	在工作列上，按一下 [管理金鑰]，然後按一下金鑰旁邊的複製圖示將它複製到剪貼簿。將這個金鑰貼入文件並加以儲存。您需要此金鑰值來設定 VNet 對 VNet 連線。
5.	在 [網路] 頁面中，按一下 [TestVNET]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
6.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
7.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestVNET 虛擬網路的 Azure VPN 閘道公用 IP 位址。記錄此 IP 位址，您將需要此項來設定 VNet 對 VNet 連線。

接著，您將使用從建立虛擬網路閘道取得的公用 IP 位址，設定 TestLabLNet 和 TestVNETLNet 區域網路。

1.	從 Azure 管理入口網站的 [網路] 頁面上，按一下 [區域網路]。 
2.	按一下 [TestLabLNet]，然後按一下工作列中的 [編輯]。
3.	在 [指定區域網路的詳細資料] 頁面的 [VPN 裝置 IP 位址 (選擇性)] 中，輸入 (從上一個程序的步驟 3) TestLab 虛擬網路的虛擬網路閘道 IP 位址，然後按一下向右箭號。
4.	在 [指定位址空間] 頁面中，按一下核取記號。
5.	在 [區域網路] 頁面中，按一下 [TestVNETLNet]，然後按一下工作列中的 [編輯]。
6.	在 [指定區域網路的詳細資料] 頁面的 [VPN 裝置 IP 位址 (選擇性)] 中，輸入 (從上一個程序的步驟 7) TestVNET 虛擬網路的虛擬網路閘道 IP 位址，然後按一下向右箭號。
7.	在 [指定位址空間] 頁面中，按一下核取記號。

接著，您將設定兩個閘道的預先共用金鑰使用相同的值，也就是 Azure 管理入口網站對於 TestLab 虛擬網路決定的金鑰值。在本機電腦上，從 Azure PowerShell 命令提示字元執行這些命令，填入 TestLab 預先共用金鑰的值。

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet –SharedKey $preSharedKey

接著，在本機電腦上，於 Azure 管理入口網站的 [網路] 頁面上，按一下 [TestLab] 虛擬網路，並按一下 [儀表板]，然後按一下工作列中的 [連線]。等候 TestLab 虛擬網路顯示連線狀態。

這是您目前的組態。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## 第 4 階段：設定 DC2

首先，建立 DC2 的 Azure 虛擬機器。在本機電腦的 Azure PowerShell 命令提示字元下執行下列命令：

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

接著，登入新的 DC2 虛擬機器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後按一下 DC2 [狀態] 欄中的 [執行中]。
2.	在工作列上，按一下 [連接]。 
3.	當系統提示開啟 DC2.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用這些：
- 名稱：**DC2\**[本機系統管理員帳戶名稱]
- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DC2 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.4 的 4 次成功回覆。這是 Vnet 對 Vnet 連線的流量測試。

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

請注意，系統會提示您提供 CORP\\User1 密碼和目錄服務還原模式 (DSRM) 密碼，並重新啟動 DC2。

由於 TestVNET 虛擬網路有自己的 DNS 伺服器 (DC2)，因此您必須設定 TestVNET 的虛擬網路使用這個 DNS 伺服器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [網路]，然後按一下 [TestVNET]。
2.	按一下 [設定]。
3.	在 [DNS 伺服器] 中，移除 10.0.0.4 項目。
4.	在 [DNS 伺服器] 中，加入含 **DC2** 的項目做為名稱，並加入 **192.168.0.4** 做為 IP 位址。 
5.	在底部的命令列中，按一下 [儲存]，然後在出現提示時按一下 [是]。等候 TestVNet 網路更新完成。

這是您目前的組態。

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
模擬混合式雲端環境到此準備就緒，可以進行測試。

您也可以在此測試環境中建置下列組態：

- [SharePoint 內部網路伺服器陣列](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)
- [Web 型 LOB 應用程式](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)
- [Office 365 Directory 同步處理 (DirSync) 伺服器](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

## 其他資源

[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[設定 VNet 對 VNet 連線](http://msdn.microsoft.com/library/azure/dn690122.aspx)

[基本組態測試環境](../virtual-machines/virtual-machines-base-configuration-test-environment.md)

[Azure 混合式雲端測試環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure 基礎結構服務實作指導方針](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## <a id="costs"></a>將此環境的持續成本降至最低

若要將在此環境中執行虛擬機器的成本降至最低，請盡速執行所需的測試和示範，然後在不使用時予以刪除或關閉虛擬機器。例如，您可以使用 Azure 自動化和 Runbook 在每個營業日結束時自動關閉 TestLab 和 Test_VNET 虛擬網路中的虛擬機器。如需詳細資訊，請參閱[開始使用 Azure 自動化](../automation-create-runbook-from-samples.md)。您再次啟動公司網路子網路上的虛擬機器時，請先啟動 DC1。

Azure VPN 閘道會實作為一組會產生持續成本的兩個 Azure 虛擬機器。如需詳細資訊，請參閱[定價 - 虛擬網路](http://azure.microsoft.com/pricing/details/virtual-network/)。若要將這兩個 VPN 閘道 (一個用於 TestLab，另一個用於 TestVNET) 的成本降至最低，請使用這些步驟建立測試環境，並盡速執行所需的測試和示範或刪除閘道。
 
1.	從本機電腦上的 Azure 管理入口網站中，按一下左窗格中的 [網路]，並按一下 [TestLab]，然後按一下 [儀表板]。
2.	在工作列中，按一下 [刪除閘道]。出現提示時，按一下 [是]。等候閘道刪除而且其狀態變更為 [尚未建立閘道]。
3.	按一下左窗格中的 [網路]，並按一下 [TestVNET]，然後按一下 [儀表板]。
4.	在工作列中，按一下 [刪除閘道]。出現提示時，按一下 [是]。等候閘道刪除而且其狀態變更為 [尚未建立閘道]。

如果您刪除閘道，而且要還原此測試環境，您必須先建立新的閘道。

1.	從本機電腦上的 Azure 管理入口網站中，按一下左窗格中的 [網路]，然後按一下 [TestLab]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
2.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
3.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestLab 虛擬網路的 Azure VPN 閘道新公用 IP 位址。您需要此 IP 位址才能重新設定 TestLabLNet 區域網路。
4.	在工作列上，按一下 [管理金鑰]，然後按一下金鑰旁邊的複製圖示，將它複製到 [剪貼簿]。將這個金鑰值貼入文件並加以儲存。您需要這個金鑰值才能重新設定 TestVNET 虛擬網路的 VPN 閘道。
5.	從本機電腦的 Azure 管理入口網站中，按一下左窗格中的 [網路]，然後按一下 [TestVNET]。在 [儀表板] 頁面上，您應該會看到 [尚未建立閘道] 的狀態。
6.	在工作列中，按一下 [建立閘道]，然後按一下 [動態路由]。出現提示時，按一下 [是]。等候閘道完成且其狀態變更為 [正在連線]。這可能需要幾分鐘的時間。
7.	記下 [儀表板] 頁面中的 [閘道 IP 位址]。這是 TestVNET 虛擬網路的 Azure VPN 閘道新公用 IP 位址。您需要此 IP 位址才能重新設定 TestVNETLNet 區域網路。

接著，您將使用從建立虛擬網路閘道取得的新公用 IP 位址，設定 TestLabLNet 和 TestVNETLNet 區域網路。

1.	從 Azure 管理入口網站的 [網路] 頁面上，按一下 [區域網路]。 
2.	按一下 [TestLabLNet]，然後按一下工作列中的 [編輯]。
3.	在 [指定區域網路的詳細資料] 頁面的 [VPN 裝置 IP 位址 (選擇性)] 中，輸入 (從上一個程序的步驟 3) TestLab 虛擬網路的虛擬網路閘道 IP 位址，然後按一下向右箭號。
4.	在 [指定位址空間] 頁面中，按一下核取記號。
5.	在 [區域網路] 頁面中，按一下 [TestVNETLNet]，然後按一下工作列中的 [編輯]。
6.	在 [指定區域網路的詳細資料] 頁面的 [VPN 裝置 IP 位址 (選擇性)] 中，輸入 (從上一個程序的步驟 7) TestVNET 虛擬網路的虛擬網路閘道 IP 位址，然後按一下向右箭號。
7.	在 [指定位址空間] 頁面中，按一下核取記號。

接著，您將設定兩個閘道的預先共用金鑰使用相同的值，也就是 Azure 管理入口網站對於 TestLab 虛擬網路決定的金鑰值。在本機電腦上，從 Azure PowerShell 命令提示字元執行這些命令，填入 TestLab 預先共用金鑰的值。

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet –SharedKey $preSharedKey

接著，在 Azure 管理入口網站的 [網路] 頁面上，按一下 [TestLab] 虛擬網路，然後按一下工作列中的 [連線]。等候 TestLab 虛擬網路顯示 TestVNET 區域網路的連線狀態。
 

<!---HONumber=July15_HO2-->