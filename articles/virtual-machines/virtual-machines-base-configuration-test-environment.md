<properties 
	pageTitle="基本設定測試環境" 
	description="了解如何建立簡單的開發/測試環境來模擬 Azure 中的簡化內部網路。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="josephd"/>

# 基本設定測試環境

本主題會逐步解說如何利用 Windows Server 2012 R2 電腦，在 Microsoft Azure 虛擬網路中建立「基本設定」測試環境。產生的測試環境可以用於：

- 開發和測試應用程式。
-  [模擬混合式雲端環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。
- 添加其他虛擬機器和 Azure 服務，打造自己的測試環境。
 
「基本設定」測試環境是由 TestLab 純雲端 Azure 虛擬網路中的公司子網路構成的，可以模擬簡化的私人內部網路。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

它包含：

- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 DC1 而且被設定成內部網路網域控制站和網域名稱系統 (DNS) 伺服器。
- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 APP1 而且被設定成一般應用程式和網頁伺服器。
- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 CLIENT1 而且充當內部網路用戶端。

這種設定可以讓 DC1、APP1、CLIENT1 和其他公司網路子網路電腦變成：

- 連線到網際網路來安裝更新、即時存取網際網路資源，以及參與公用雲端技術，例如 Microsoft Office 365 和其他 Azure 服務。 
- 利用遠端桌面連線，從連接網際網路或公司網路的電腦進行遠端管理。 

在 Azure 中設定 Windows Server 2012 R2 基本設定測試環境的公司子網路，共分 4 個階段。。

1.	建立 Azure 虛擬網路。
2.	設定 DC1。 
3.	設定 APP1。 
4.	設定 CLIENT1。

如果您還沒有 Azure 帳戶，請到[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 申請免費試用。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

> [AZURE.NOTE]Azure 中的虛擬機器執行時，需要支付相關費用。這項成本是按照您的免費試用版、MSDN 訂閱或付費訂閱進行計算。如需詳細了解 Azure 虛擬機器的執行成本，請參閱[虛擬機器定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/)和 [Azure 定價計算機](http://azure.microsoft.com/pricing/calculator/)。為了降低成本，請參閱[將 Azure 的測試環境虛擬機器費用降至最低](#costs)。

## 階段 1：建立 Azure 虛擬網路

首先，您可以建立 TestLab Azure 虛擬網路，用它來架設公司子網路基本設定。

1.	在 Azure 管理入口網站的工作列上，依序按一下 **[新增]** > **[網路服務]** > **[虛擬網路]** > **[自訂建立]**。
2.	在 [虛擬網路詳細資料] 頁面中的 **[名稱]** 中，輸入 **TestLab**。
3.	在 **[位置]** 中，選取適當的區域。
4.	按 [下一步] 箭頭。
5.	在 [DNS 伺服器和 VPN 連線] 頁面的 [DNS 伺服器]**** 的 [選取或輸入名稱]**** 中，輸入 **DC1**，然後在 [IP 位址]**** 中輸入 **10.0.0.4**，最後按 [下一步] 箭頭。
6.	在 [虛擬網路位址空間] 頁面的 **[子網路]** ，按一下 **subnet-1**，然後將名稱改成 **Corpnet**。 
7.	在公司子網路的 **[CIDR (位址計數)]** 欄中，按一下 **[/24 (256)]**。
8.	按一下 [完成] 圖示。等候虛擬網路建立後再繼續。

接下來，按照[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md) 中的操作方法，在本機電腦安裝 Azure PowerShell。開啟 Azure PowerShell 命令提示字元。

首先，利用以下命令選取正確的 Azure 訂用帳戶。將引號裡面的字元 (包括 < and > 字元) 換成正確的名稱。

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

您可以從 **Get-AzureSubscription** 命令顯示的 **SubscriptionName** 屬性，取得正確的訂閱帳戶名稱。

接下來，建立 Azure 雲端服務。雲端服務可以充當虛擬網路中，各個虛擬機器的安全屏障以及邏輯容器。雲端服務也可以讓您從遠端連接和管理公司網路子網路上的虛擬機器。

您必須為雲端服務選擇唯一的名稱。*雲端服務名稱可以包含字母、數字和連字號。欄位中的第一個和最後一個字元，必須是字母或數字。*

例如，雲端服務的名稱可以是 TestVNET-*UniqueSequence*，其中的 *UniqueSequence* 是貴公司的縮寫。例如，如果貴公司名稱是 Tailspin Toys，則可以將雲端服務命名為 TestVNET-Tailspin。

您可以使用這個 Azure PowerShell 命令，測試名稱是否重複。

	Test-AzureName -Service <Proposed cloud service name>

如果這個命令傳回「False」，表示您設定的名稱不重複。然後，使用以下命令來建立雲端服務。

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

記錄雲端服務的實際名稱。。

接下來，您可以設定儲存體帳戶，它將包含虛擬機器的磁碟和以及其他資料磁碟。*您選取的名稱不可以和其他名稱重複而且只能使用小寫字母和數字。* 您可以使用這個 Azure PowerShell 命令，測試儲存體帳戶名稱是否重複。

	Test-AzureName -Storage <Proposed storage account name>

如果這個命令傳回「False」，表示您設定的名稱不重複。然後，使用以下命令來建立儲存體帳戶，以及設定訂閱來使用儲存體帳戶。

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

這是您目前的組態。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## 階段 2：設定 DC1。

DC1 是 corp.contoso.com Active Directory 網域服務 (AD DS) 網域的網域控制站，也是 TestLab 虛擬網路的虛擬機器 DNS 伺服器。

首先，填寫雲端服務的名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 DC1 建立 Azure 虛擬機器。

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

接著，連接到 DC1 虛擬機器。

1.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後在 DC1 虛擬機器的 [狀態] 欄中，按一下 [已啟動]。  
2.	在工作列上，按一下 [連接]。 
3.	提示開啟 DC1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用下列：
- 名稱：**DC1**[本機系統管理員帳戶名稱]
- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]****。

接著，將額外的資料磁碟新增為磁碟機代號 F: 的新磁碟區。

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]****，然後按一下 [磁碟]****。
2.	在 [內容] 窗格的 [磁碟]**** 群組中，按一下 [磁碟 2]** ** ([磁碟分割]**** 設為 [不明]****)。
3.	按一下 [工作]****，然後按一下 [新增磁碟區]****。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]****。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]****，然後按 [下一步]****。出現提示時，按一下 **[確定]**。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]****。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]****。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]****。
9.	在 [確認選取項目] 頁面上，按一下 [建立]****。
10.	完成時，按一下 [關閉]****。

接著，將 DC1 設定為 corp.contoso.com 網域的網域控制站和 DNS 伺服器。在系統管理員層級 Windows PowerShell 命令提示字元，執行下列命令。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

DC1 重新啟動之後，重新連接到 DC1 的虛擬機器。

1.	在 Azure 管理入口網站的 [虛擬機器] 頁面上，按一下 DC1 虛擬機器 [狀態]**** 欄中的 [執行]****。
2.	在工作列上，按一下 **[連接]**。 
3.	提示開啟 DC1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用下列：
- 名稱：**CORP**[本機系統管理員帳戶名稱]
- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]****。

接下來，在 Active Directory 中建立一個當使用者登入 CORP 網域成員電腦時，就會用到的使用者帳戶。在系統管理員層級 Windows PowerShell 命令提示字元執行下列命令，一次執行一個。
 
	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false 
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

請注意，第一個命令會提示使用者輸入 User1 帳戶密碼。因為這個帳戶會用於所有 CORP 網域成員電腦的遠端桌面連線，所以請選取強式密碼。要檢查密碼強度，請參閱[密碼檢查程式：使用強式密碼](https://www.microsoft.com/security/pc-security/password-checker.aspx)。記錄 User1 帳戶的密碼，然後儲存在安全的位置。

使用 CORP\\User1 帳戶，重新連線到 DC1 的虛擬機器。

接下來，在系統管理員層級 Windows PowerShell 命令提示字元，執行這個命令，允許 Ping 工具連接網路。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

這是您目前的組態。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## 階段 3：設定 APP1

APP1 提供網頁和檔案共用服務。

首先，填寫雲端服務的名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 APP1 建立 Azure 虛擬機器。

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

接下來，使用 CORP\\User1 認證連接到 APP1 虛擬機器，然後開啟系統管理員層級 Windows PowerShell 命令提示字元。

要檢查 APP1 和 DC1 之間的名稱解析和網路通訊，請執行 **ping dc1.corp.contoso.com** 命令，然後確定顯示 4 個回覆。

接下來，在 Windows PowerShell 命令提示字元，使用這個命令，將 APP1 變成網頁伺服器。

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

接下來，使用下列命令，在 APP1 的資料夾裡面建立共用的資料夾和文字檔案。

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

這是您目前的組態。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## 階段 4：設定 CLIENT1

CLIENT1 充當 Contoso 內部網路上的一般膝上型電腦、平板電腦或桌上型電腦。

首先，填寫雲端服務的名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 CLIENT1 建立 Azure 虛擬機器。

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

然後，以 CORP\\User1 認證連接到 CLIENT1 虛擬機器。

要檢查 CLIENT1 和 DC1 之間的名稱解析和網路通訊，請在 Windows PowerShell 命令提示字元執行 **ping dc1.corp.contoso.com** 命令，然後確定顯示 4 個回覆。

接下來，確定您可以從 CLIENT1 存取在 APP1 上的網頁及檔案共用資源。

1.	在 [伺服器管理員] 的樹狀目錄窗格中，按一下 [本機伺服器]。 
2.	在 [CLIENT1 的屬性] 中，按一下 [IE 增強式安全性設定] 旁的 [開啟]。
3.	在 [Internet Explorer 增強式安全性設定] 中，按一下 [系統管理員] 和 [使用者] 的 **[關閉]**，然後按一下 **[確定]**。
4.	從 [開始] 畫面中，按一下 [Internet Explorer]，然後按一下 [確定]。
5.	在網址列中，輸入 **http://app1.corp.contoso.com/**，然後按下 ENTER。您應該會看到 APP1 的預設網際網路資訊服務網頁。6.	按一下桌面工作列中的 [檔案總管] 圖示。
7.	在網址列中，輸入 **\\\\app1\\Files**，然後按下 ENTER。
8.	您應該會看到一個資料夾視窗，裡面有檔案共用資料夾的內容。
9.	在 [檔案] 共用資料夾視窗中，按兩下 **Example.txt** 檔案。您應該會看到 Example.txt 檔案的內容。
10.	關閉 [example.txt - 記事本] 和 [檔案] 共用資料夾視窗。

這是最終的設定。

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Azure 基本設定現在可以用於應用程式開發、測試或其他測試環境，例如[模擬混合式雲端環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。

## 其他資源

[混合式雲端測試環境](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

 
## <a id="costs"></a>將 Azure 的測試環境虛擬機器費用降至最低

為了將測試環境虛擬機器的執行成本降到最低，您可以執行下列其中一項：

- 建立測試環境，並盡快執行所需的測試和示範。完成時，刪除測試環境虛擬機器。
- 關閉測試環境虛擬機器，進入已取消配置狀態。 

要利用 Azure PowerShell 關閉虛擬機器，請填寫雲端服務名稱並執行以下命令。

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


如果想保證虛擬機器從已停止 (取消配置) 狀態啟動後，它們都可以正常運作，請按照下列順序啟動：

1.	DC1
2.	APP1
3.	CLIENT1 

要利用 Azure PowerShell 依序啟動虛擬機器，請填寫雲端服務名稱並執行以下命令。

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!--HONumber=52-->
 