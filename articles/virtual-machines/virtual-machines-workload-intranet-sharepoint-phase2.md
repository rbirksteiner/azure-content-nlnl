<properties 
	pageTitle="SharePoint 內部網路伺服器陣列工作負載第 2 階段：設定網域控制站" 
	description="在 Azure 基礎結構服務中，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 的第二階段中，您建立並設定了兩個 Active Directory 網域控制站。" 
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
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# SharePoint 內部網路伺服器陣列工作負載第 2 階段：設定網域控制站

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 的這個階段中，您需要在 Azure 虛擬網路中設定兩個網域控制站，才能在 Azure 虛擬網路中驗證 SharePoint 伺服器陣列的用戶端 web 要求，而不用跨 VPN 或 ExpressRoute 連線將驗證流量送至您的內部部署網路。

您必須先完成這個階段才能前往[第 3 階段](virtual-machines-workload-intranet-sharepoint-phase3.md)。如需所有階段的詳細資訊，請參閱[在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)。

## 在 Azure 中建立網域控制站虛擬機器

首先，您必須填寫資料表 M 的 [**虛擬機器名稱**] 資料行，並視需要在 [**最小大小**] 資料行中修改虛擬機器大小。

項目 | 虛擬機器名稱 | 資源庫映像 | 最小大小 
--- | --- | --- | --- 
1. | ______________ (第一個網域控制站，範例 DC1) | Windows Server 2012 R2 Datacenter | A2 (中型)
2. | ______________ (第二個網域控制站，範例 DC2) | Windows Server 2012 R2 Datacenter | A2 (中型)
3. | ______________ (第一部 SQL 電腦， 範例 SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
4. | ______________ (第二部 SQL 電腦， 範例 SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
5. | ______________ (叢集多數節點見證，範例 MN1) | Windows Server 2012 R2 Datacenter | A1 (小型)
6. | ______________ (第一部 SharePoint 應用程式伺服器，範例 APP1) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
7. | ______________ (第二部 SharePoint 應用程式伺服器，範例 APP2) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
8. | ______________ (第一部 SharePoint 網頁伺服器，範例 WEB1) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)
9. | ______________ (第二部 SharePoint 網頁伺服器，範例 WEB2) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | A4 (特大型)

**資料表 M – SharePoint 2013 內部網路伺服器陣列在 Azure 中的虛擬機器**

如需虛擬機器大小的完整清單，請參閱 [Azure 的虛擬機器和雲端服務大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

使用下列方塊中的 PowerShell 命令為上述兩個網域控制站建立虛擬機器。指定變數的值，並移除 < and > 字元。請注意，此 PowerShell 命令集使用下方的值：

- 資料表 M，適用於您的虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 A，適用於可用性設定組
- 資料表 C ，適用於雲端服務

還記得您在[第 1 階段：設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) 定義了資料表 V、S、A 和 C。

填入所有適當值當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>
	
	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## 設定第一個網域控制站

使用本機系統管理員帳戶認證登入第一個網域控制站電腦。

### <a id="logon"></a>使用遠端桌面連線登入虛擬機器

1.	在 Azure 管理入口網站中，按一下左側窗格中的 [**虛擬機器**]。
2.	若要連線到虛擬機器，按一下機器名稱旁 [**狀態**] 資料行中的 [**執行**]。
3.	在頁面底部的命令列中按一下 [**連線**]。
4.	管理入口網站會通知您正在擷取 .rdp 檔案。按一下 [確定]。
5.	瀏覽器對話方塊隨即出現，並詢問：「您想開啟或儲存來自 manage.windowsazure.com 的 ComputerName.rdp 嗎？」。 按一下 [開啟]。
6.	在 [**遠端桌面連線**] 對話方塊中按一下 [**連線**]。
7.	在 [**Windows 安全性**] 對話方塊中按一下 [**使用其他帳戶**]。
8.	在 [**使用者名稱**] 中，輸入虛擬機器名稱以及使用該虛擬機器建立的本機系統管理員帳戶名稱 (本機電腦帳戶)。  
  
請使用下列格式： 
- *ComputerName*\\*本機系統管理員帳戶名稱*
9.	在 [**密碼**] 中輸入本機系統管理員帳戶的密碼。
10.	按一下 [確定]。
11.	在 [**遠端桌面連線**] 對話方塊中按一下 [**是**]。新機器的桌面會顯示在遠端桌面工作階段視窗中。

接下来，您需要在第一個網域控制站新增額外的資料磁碟。

### <a id="datadisk"></a>初始化空磁碟

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [**磁碟**] 群組中，按一下 [磁碟 **2**] ([**磁碟分割**] 設為 [**未知**])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 中按一下 [**磁碟 2**]，然後按一下 [**下一步**]。出現提示時，按一下 [確定]。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接下來，測試第一個網域控制站對組織網路中各個位置的連線能力。

### <a id="testconn"></a>測試連線能力

1.	從桌面開啟 Windows PowerShell 命令提示字元。
2.	在 Windows PowerShell 命令提示字元中，使用 **ping** 命令來 ping 您組織網路中各個資源的名稱和 IP 位址。

此程序可確保 DNS 名稱解析正常運作 (即虛擬機器已使用內部部署 DNS 伺服器正確設定)，且此封包可在跨單位虛擬網路內往來傳送。

接下來，從第一個網域控制站的 Windows PowerShell 命令提示字元執行下列命令：

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

電腦會重新啟動。

## 設定第二個網域控制站

使用第二個網域控制站電腦的本機系統管理員帳戶認證登入該電腦。如需指示，請參閱[使用遠端桌面連線登入虛擬機器](#logon)。

接下来，您需要在第二個網域控制站新增額外的資料磁碟。請參閱[初始化空磁碟程序](#datadisk)。

接下來，測試第二個網域控制站對組織網路中各個位置的連線能力。請參閱[測試連線能力程序](#testconn)。使用此程序確保 DNS 名稱解析正常運作 (即虛擬機器已使用內部部署 DNS 伺服器正確設定)，且此封包可在跨單位虛擬網路內往來傳送。

接下來，從第二個網域控制站的 Windows PowerShell 命令提示字元執行下列命令：

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

電腦會重新啟動。

## 設定 SharePoint 伺服器陣列帳戶和權限

SharePoint 伺服器陣列需要下列使用者帳戶：

- sp_farm：管理 SharePoint 伺服器陣列的使用者帳戶。
- sp_farm_db：對 SQL Server 執行個體具有 sysadmin 權限的使用者帳戶。
- sp_install：具有安裝角色和功能所需之網域管理權限的使用者帳戶。
- sqlservice：可據以執行 SQL 執行個體的使用者帳戶。

接下來，使用網域控制台為所屬成員之網域的網域系統管理員帳戶登入任一台電腦，開啟系統管理員層級 Windows PowerShell 命令提示字元，*一次一個*執行下列命令：

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-	ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

在執行每個命令時，系統都會提示您輸入密碼。請記下這些帳戶名稱和密碼，並將其儲存在安全的位置。

接下来，執行下列步驟，為新的使用者帳戶新增其他帳戶屬性。

1.	在 [開始] 畫面中輸入 **Active Directory Users**，然後按一下 [**Active Directory 使用者和電腦**]。 
2.	在樹狀目錄窗格中開啟您的網域，然後按一下 [**使用者**]。 
3.	在 [內容] 窗格中，以滑鼠右鍵按一下 [**sp_install**]，然後按一下 [**新增至群組**]。
4.	在 [**選取群組**] 對話方塊中輸入 **domain admins**，然後按兩次 [**確定**]。
5.	在對話方塊中按一下 **[檢視]，再按一下 [進階功能]**。這個選項可讓您在 [屬性] 視窗中看到 AD 物件所有隱藏的容器以及隱藏的索引標籤。
6.	以滑鼠右鍵按一下您的網域名稱，然後按一下 [**屬性**]。
7.	在 [**屬性**] 對話方塊中按一下 [**安全性**] 索引標籤，然後按一下 [**進階**] 按鈕。
8.	在 [**<YourDomain> 的進階安全性設定**] 視窗中按一下 [ **新增**]。
9.	在 [**<YourDomain> 的權限項目**] 視窗按一下 [**選取一個主體**]。
10.	在文字方塊中輸入 **<YourDomain>\\sp_install** ，然後按一下 [**確定**]。
11.	在 [**建立電腦物件**] 中選取 [**允取**]，然後按三次 [**確定**]。

接下來，為您的虛擬網路更新 DNS 伺服器，讓 Azure 將兩個新網域控制站的 IP 位址指派給虛擬機器，做為他們的 DNS 伺服器。請注意，此程序使用資料表 V 的值 (用於您虛擬網路的設定)。

1.	在 Azure 管理入口網站的左側窗格中按一下 [**網路**]，然後按一下您虛擬網路的名稱 (資料表 V – 項目 1 – 值資料行)。
2.	按一下 [設定]。
3.	在 **[DNS 伺服器**] 中，移除與位於內部部署網路中與 DNS 伺服器對應的項目。
4.	在 **[DNS 伺服器**] 中，新增兩個具有易記名稱以及下列兩個資料表項目之 IP 位址的項目。
- 資料表 V – 項目 6 – 值資料行
- 資料表 V – 項目 7 – 值資料行
5.	在底部的命令列中按一下 [**儲存**]。
6.	在 Azure 管理入口網站的左側窗格中按一下 [**虛擬機器**]，然後按一下第一個網域控制站名稱旁邊的 [**狀態**] 資料行。
7.	在命令列中按一下 [**重新啟動**]。
8.	當第一個網域控制站啟動時，按一下第二個網域控制站名稱旁邊的 [**狀態**] 資料行。
9.	在命令列中按一下 [**重新啟動**]。等待第二個網域控制站啟動。

請注意，重新啟動兩個網域控制站後，他們才不會由內部部署 DNS 伺服器設定做為 DNS 伺服器。由於他們本身都是 DNS 伺服器，因此當升級為網域控制站時，它們會自動由內部部署 DNS 伺服器設定做為 DNS 轉寄站。

接下來，我們需要建立 AD 複寫站台，以確保 Azure 虛擬網路中的伺服器使用本機網域控制站。使用 sp_install 帳戶登入主要網域控制站，自系統管理員層級 Windows PowerShell 命令提示字元執行下列命令：

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

此處顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## 下一步

如要繼續設定此工作負載，請前往[第 3 階段：設定 SQL Server 基礎結構](virtual-machines-workload-intranet-sharepoint-phase3.md)。

## 其他資源

[在 Azure 中部署含有 SQL Server AlwaysOn 可用性群組的 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->