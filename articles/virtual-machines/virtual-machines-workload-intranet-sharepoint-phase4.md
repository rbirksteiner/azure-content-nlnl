<properties 
	pageTitle="SharePoint 內部網路伺服器陣列工作負載第 4 階段：設定 SharePoint Server。" 
	description="在 Azure 基礎結構服務中，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 的這個階段中，您要建立 SharePoint 伺服器虛擬機器和新的 SharePoint 伺服器陣列。" 
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

# SharePoint 內部網路伺服器陣列工作負載第 4 階段：設定 SharePoint Server。

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 伺服器陣列的這個階段中，您需要建立 SharePoint 伺服器陣列的應用程式和 Web 階層，並使用 SharePoint 組態精靈建立伺服器陣列。

您必須先完成這個階段才能前往[第 5 階段](virtual-machines-workload-intranet-sharepoint-phase5.md)。如需所有階段的詳細資訊，請參閱[在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)。

## 在 Azure 中建立 SharePoint 伺服器虛擬機器

SharePoint Server 虛擬機器共有四部。其中兩個 SharePoint Server 虛擬機器用於前端 Web 伺服器，另外兩個用於管理和主控 SharePoint 應用程式。每一層中有兩部 SharePoint Server，可提供高可用性。

使用以下的 PowerShell 命令區塊為上述四個 SharePoint Server 建立虛擬機器。指定變數的值，並移除 < and > 字元。請注意，此 PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 A，適用於可用性設定組
- 資料表 C ，適用於雲端服務

還記得您在[第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)定義了資料表 M，並在[第 1 階段：設定 Azure ](virtual-machines-workload-intranet-sharepoint-phase1.md)定義了資料表 V、S、A 和 C。

當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

使用[使用遠端桌面連線程序登入虛擬機器](virtual-machines-workload-intranet-sharepoint-phase2.md#logon)四次，以便使用在[第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)所建立的「[網域]\\sp_farm_db」帳戶認證，分別登入四部 SharePoint Server。

使用[測試連線能力程序](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn)四次，分別為四部 SharePoint Server 測試組織網路中各個位置的連線能力。

## 設定 SharePoint 伺服器陣列

使用下列步驟來設定伺服器陣列中的第一部 SharePoint Server。

1.	在第一部 SharePoint 應用程式伺服器桌面上，按兩下 **SharePoint 2013 產品設定精靈**。詢問是否允許程式變更電腦時，按一下 [**是**]。
2.	在 [歡迎使用 SharePoint 產品] 頁面中按一下 [**下一步**]。
3.	**SharePoint 2013 產品設定精靈**對話方塊隨即出現，警告將重新啟動或重設服務 (例如 IIS)。按一下 [是]。
4.	在 [連線至伺服器陣列] 頁面中，選取 [**建立新的伺服器陣列**]，然後按一下 [**下一步**]。
5.	在 [指定組態資料庫設定] 頁面中：
- 在 [**資料庫伺服器**] 中輸入主要 SQL Server 的名稱。 
- 在 [使用者名稱] 中，輸入「[網域]**\\sp_farm_db**」(在[第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)中所建立)。還記得 sp_farm_db 帳戶具有 SQL Server 的系統管理員權限。 
- 在 [**密碼**] 中輸入 sp_farm_db 帳戶密碼。
6.	按 [下一步]。
7.	在 [指定伺服陣列安全性設定] 頁面中，輸入複雜密碼兩次。記下複雜密碼並儲存在安全的位置，以供日後參考。按 [下一步]。
8.	在 [設定 SharePoint 管理中心 Web 應用程式] 頁面中，按 [**下一步**]。
9.	[完成 SharePoint 產品組態精靈] 頁面隨即出現。按 [下一步]。
10.	[設定 SharePoint 產品] 頁面隨即出現。等候組態程序完成，約 8 分鐘。
11.	成功設定伺服器陣列後，按一下 [**完成**] 按鈕。 新的管理網站隨即啟動。
12.	若要開始設定 SharePoint 伺服器陣列，請按一下 [**啟動精靈**]。

在第二個 SharePoint 應用程式伺服器和兩個前端 Web 伺服器上執行下列程序。

1.	從桌面按兩下 [**SharePoint 2013 產品組態精靈**]。詢問是否允許程式變更電腦時，按一下 [**是**]。
2.	在 [歡迎使用 SharePoint 產品] 頁面中按一下 [**下一步**]。
3.	SharePoint 2013 產品設定精靈對話方塊隨即出現，警告將重新啟動或重設服務 (例如 IIS)。按一下 [是]。
4.	在 [連線至伺服器陣列] 頁面中，按一下 [**連線至現有的伺服器陣列**]，然後按 [**下一步**]。
5.	在 [指定組態資料庫設定] 頁面中的 [**資料庫伺服器]** 中，輸入主要 SQL Server 名稱，然後按一下 [**擷取資料庫名稱**]。 
6.	在資料庫名稱清單中按一下 [**SharePoint_Config**]，然後按一下 [**下一步**]。 
7.	在 [指定伺服器陣列安全性設定] 頁面中，輸入先前程序所設定的複雜密碼。按 [下一步]。
8.	[完成 SharePoint 產品組態精靈] 頁面隨即出現。按 [下一步]。
9.	在 [組態成功] 頁面中，按一下 [**完成**]。 

SharePoint 建立伺服器陣列後，會在主要 SQL Server 虛擬機器中設定一組伺服器登入。SQL Server 2012 引入了自主資料庫使用者密碼的概念。自主資料庫本身儲存了資料庫中繼資料和使用者資訊，且在此資料庫中定義的使用者不一定需要對應的登入。可用性群組會複製此資料庫的資訊，容錯移轉之後仍可使用。如需詳細資訊，請參閱[自主資料庫](http://go.microsoft.com/fwlink/p/?LinkId=262794)。

不過根據預設，SharePoint 資料庫並非自主資料庫。因此您必須手動設定次要 SQL Server ，使其與主要 SQL Server 具備同樣的 SharePoint 伺服器陣列登入設定。您可以同時與兩部伺服器連線，從 SQL Server Management Studio 執行此同步設定。

完成此初始設定後，便可使用更多 SharePoint 伺服器陣列功能的組態選項。 如需詳細資訊，請參閱[計劃在 Azure 基礎結構服務上部署 SharePoint 2013](http://msdn.microsoft.com/library/dn275958.aspx)。

## 設定內部負載平衡

設定內部負載平衡，能讓進入 SharePoint 伺服器陣列的用戶端流量平均分佈至兩部前端 Web 伺服器。您需要指定內部負載平衡執行個體 ，包含了名稱和從子網路位址空間指派的所屬 IP 位址 。若要確定您為內部負載平衡器所選的 IP 位址是否可用，請使用下列 Azure PowerShell 命令：

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

若 [**Test-AzureStaticVNetIP**] 命令中的 [**IsAvailable**] 欄位顯示為 [**True**]，您便可使用此 IP 位址。

在本機電腦的 Azure PowerShell 命令提示字元中執行下列命令：

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
	
	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.
	
	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
	
	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

接下來，新增一個 DNS 位址記錄至您組織的 DNS 基礎結構，此基礎結構可將 SharePoint 伺服器陣列完整網域名稱 (例如 sp.corp.contoso.com) 解析給指派給內部負載平衡執行個體的 IP 位址 (先前 Azure PowerShell 命令區塊中 **$IP** 的值)。

此處顯示已成功完成此階段設定。

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## 下一步

若要繼續設定此工作負載，請前往[第 5 階段：建立可用性群組並新增 SharePoint 資料庫](virtual-machines-workload-intranet-sharepoint-phase5.md)。

## 其他資源

[在 Azure 中部署含有 SQL Server AlwaysOn 可用性群組的 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->