<properties 
	pageTitle="在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列" 
	description="了解如何在混合式雲端環境中建立 SharePoint 內部網路伺服器陣列進行開發或 IT 專業測試。" 
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


# 在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列

本主題將逐步引導您建立混合式雲端環境測試 Microsoft Azure 代管的內部網路 SharePoint 伺服器陣列。以下是產生的組態。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
這個組態會從您在網際網路上的位置模擬 Azure 生產環境中的 SharePoint。其中包括：

- 簡化的內部部署網路 (公司網路子網路)。
- Microsoft Azure (TestVNET) 代管的跨單位部署虛擬網路。
- 站對站 VPN 連線。
- 雙層式 SharePoint 伺服器陣列以及 TestVNET 虛擬網路中的第二個網域控制站。

這個組態一般可供您開始：

- 在混合式雲端環境中的 SharePoint 內部網路伺服器陣列開發和測試應用程式。
- 執行此混合式雲端型 IT 工作負載的測試。

設定此混合式雲端測試環境分為三個主要階段：

1.	設定混合式雲端環境進行測試。
2.	設定 SQL 伺服器電腦 (SQL1)。
3.	設定 SharePoint server (SP1)。

如果您還沒有 Azure 訂用帳戶，則可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 註冊免費試用版。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## 第 1 階段：設定混合式雲端環境

使用[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)主題中的指示。由於這個測試環境不需要公司網路子網路上的 APP1 伺服器，因此可以暫時將它關閉。

這是您目前的組態。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)

> [AZURE.NOTE]針對第 1 階段，您也可以設定模擬混合式雲端測試環境。如需指示，請參閱[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。
 
## 第 2 階段：設定 SQL 伺服器電腦 (SQL1)

從 Azure 管理入口網站，視需要啟動 DC2 電腦。

首先，使用 CORP\\User1 認證建立 DC2 的遠端桌面連線。

接著，建立 SharePoint 伺服器陣列管理員帳戶。在 DC2 開啟系統管理員層級 Windows PowerShell 提示字元並執行這個命令。

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

提示提供 SPFarmAdmin 帳戶密碼時，輸入強式密碼並且在記錄後儲存於安全之處。

然後，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 SQL1 的 Azure 虛擬機器。


	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount –StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

接下來，*使用本機系統管理員帳戶*連線到新的 SQL1 虛擬機器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後按一下 SQL1 [狀態] 欄中的 [執行]。
2.	在工作列上，按一下 [連接]。 
3.	提示開啟 SQL1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用這些：
	- 名稱：**SQL1\**[本機系統管理員帳戶名稱]
	- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，設定 Windows 防火牆規則，允許基本連線測試和 SQL Server 的流量。從 SQL1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.1 的 4 次成功回覆。

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

在 SQL1 的 Windows PowerShell 命令提示字元下執行下列命令：

	md f:\Data
	md f:\Log
	md f:\Backup

接著，對於新資料庫和使用者帳戶權限設定 SQL Server 2014 使用 F: 磁碟機。

1.	從 [開始] 畫面中，輸入 **SQL Server 管理**，然後按一下 [SQL Server 2014 Management Studio]。
2.	在 [**連線到伺服器**] 中按一下 [**連線**]。
3.	在 [物件總管] 樹狀結構窗格中，以滑鼠右鍵按一下 [SQL1]，然後按一下 [內容]。
4.	在 [伺服器內容] 視窗中，按一下 [資料庫設定]。
5.	找出 [資料庫預設位置] 並設定下列值： 
	- 對於 [資料]，輸入路徑 **f:\\Data**。
	- 對於 [記錄]，輸入路徑 **f:\\Log**。
	- 對於 [備份]，輸入路徑 **f:\\Backup**。
	- 請注意，只有新的資料庫才會使用這些位置。
6.	按一下 [確定] 關閉視窗。
7.	在 [物件總管] 樹狀結構窗格中，開啟 [安全性]。
8.	以滑鼠右鍵按一下 [登入]，然後按一下 [新增登入]。
9.	在 [登入名稱] 中，輸入 **CORP\\User1**。
10.	在 [伺服器角色] 頁面上，按一下 [sysadmin]，然後按一下 [確定]。
11.	在 [物件總管] 樹狀結構窗格中，以滑鼠右鍵按一下 [登入]，然後按一下 [新增登入]。
12.	在 [一般] 頁面的 [登入名稱] 中，輸入 **CORP\\SPFarmAdmin**。
13.	在 [伺服器角色] 頁面上，選取 **dbcreator**，然後按一下 [確定]。
14.	關閉 Microsoft SQL Server Management Studio。

這是您目前的組態。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## 第 3 階段：設定 SharePoint 伺服器 (SP1)

首先，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 SP1 的 Azure 虛擬機器。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for SP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

然後，以 CORP\\User1 認證連接到 SP1 虛擬機器。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 SP1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.1 的 4 次成功回覆。

接著，對於新的 SharePoint 伺服器陣列及預設團隊網站設定 SP1。

1.	從 [開始] 畫面中，輸入 **SharePoint 2013 產品**，然後按一下 [SharePoint 2013 產品組態精靈]。詢問是否允許程式變更電腦時，按一下 [**是**]。
2.	在 [歡迎使用 SharePoint 產品] 頁面中按一下 [**下一步**]。 
3.	在通知可能需要在組態期間重新啟動某些服務的對話方塊中，按一下 [是]。
4.	在 [連接到伺服器陣列] 頁面上，按一下 [建立新的伺服器陣列]，然後按 [下一步]。
5.	在 [指定組態資料庫設定] 頁面上，於 [資料庫伺服器] 中輸入 **sql1.corp.contoso.com**，於 [使用者名稱] 中輸入 **CORP\\SPFarmAdmin**，於 [密碼] 中輸入 SPFarmAdmin 帳戶密碼，然後按 [下一步]。
6.	在 [指定伺服器陣列安全性設定] 頁面的 [複雜密碼] 和 [確認複雜密碼] 中，輸入 **P@ssphrase**，然後按 [下一步]。7.	在 [設定 SharePoint 管理中心 Web 應用程式] 頁面中，按 [**下一步**]。
8.	在 [完成 SharePoint 產品組態精靈] 頁面上，按 [下一步]。SharePoint 產品組態精靈可能需要幾分鐘才能完成。
9.	在 [組態成功] 頁面中，按一下 [**完成**]。完成之後，Internet Explorer 會啟動，並顯示名稱為「初始伺服器陣列組態精靈」的索引標籤。
10.	在 [協助改善 SharePoint] 對話方塊中，按一下 [否，我不想參加]，然後按一下 [確定]。
11.	對於 [要如何設定 SharePoint 伺服器陣列？]，按一下 [啟動精靈]。
12.	在 [設定您的 SharePoint 伺服器陣列] 頁面的 [服務帳戶] 中，按一下 [使用現有受管理帳戶]。
13.	在 [服務] 中，清除 [狀態服務] 旁邊的方塊以外的所有方塊，然後按 [下一步]。在其網頁上的 [工作] 可能顯示一段時間才會完成。
14.	在 [建立網站集合] 頁面中，於 [標題和描述] 中輸入 **Contoso Corporation**，並且於 [標題] 中指定 URL **http://sp1**/，然後按一下 [確定]。在其網頁上的 [工作] 可能顯示一段時間才會完成。這個步驟會在 URL http://sp1 建立團隊網站。15.	在 [這將完成伺服器陣列組態精靈] 頁面中，按一下 [完成]。[Internet Explorer] 索引標籤將顯示 SharePoint 2013 管理中心網站。
16.	以 CORP\\User1 帳戶認證登入 CLIENT1 電腦，然後啟動 Internet Explorer。
17.	在網址列中，輸入 **http://sp1/**，然後按下 ENTER。您應該會看見 Contoso Corporation 的 SharePoint 團隊網站。網站可能需要一些時間才會呈現。
這是您目前的組態。

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
模擬混合式雲端環境中的 SharePoint 內部網路伺服器陣列到此準備就緒，可以進行測試。

## 其他資源

[在 Azure 基礎結構服務上的 SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[SharePoint Server 伺服器陣列](../virtual-machines/virtual-machines-sharepoint-farm-azure-preview.md)

[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 進行測試](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure 混合式雲端測試環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure 基礎結構服務實作指導方針](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->