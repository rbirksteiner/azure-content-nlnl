<properties 
	pageTitle="SharePoint 內部網路伺服器陣列工作負載第 3 階段：設定 SQL Server 基礎結構" 
	description="在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 伺服器陣列的這個階段中，您建立了 SQL Server 叢集電腦與叢集。" 
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

# SharePoint 內部網路伺服器陣列工作負載第 3 階段：設定 SQL Server 基礎結構

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 的這個階段中，您需要設定兩部 SQL Server 電腦與叢集多數節點電腦，再將他們併入 Windows Server 叢集。

您必須先完成這個階段才能前往[第 4 階段](virtual-machines-workload-intranet-sharepoint-phase4.md)。如需所有階段的詳細資訊，請參閱[在 Azure 中使用 SQL Server AlwaysOn 可用性群組部署 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)。

## 在 Azure 中建立 SQL Server 叢集虛擬機器

SQL Server 虛擬機器共有兩部。一部 SQL Server 包含可用性群組中主要資料庫複本。另一部 SQL Server 包含次要備份複本。該備份可確保高可用性。另一部額外的虛擬機器是為了叢集多數節點而設。

使用下列方塊中的 PowerShell 命令為上述三個伺服器建立虛擬機器。指定變數的值，並移除 < and > 字元。請注意，此 PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 A，適用於可用性設定組
- 資料表 C ，適用於雲端服務

還記得您在[第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)定義了資料表 M，並在[第 1 階段：設定 Azure ](virtual-machines-workload-intranet-sharepoint-phase1.md)定義了資料表 V、S、A 和 C。

當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"
	
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## 設定 SQL Server 電腦

請為每部 SQL Server 執行以下操作：

1. 使用本機系統管理員帳戶認證，並透過[使用遠端桌面連線程序登入虛擬機器](virtual-machines-workload-intranet-sharepoint-phase2.md#logon)來登入。

2. 使用[初始化空磁碟程序](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk)兩次，分別為兩部 SQL Server 新增額外的資料磁碟。

3. 從 Windows PowerShell 命令提示字元執行下列命令：

		md f:\Data
		md f:\Log
		md f:\Backup

4. 使用[測試連線能力程序](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn)來測試組織網路中各個位置的連線能力。此程序可確保 DNS 名稱解析正常運作 (即虛擬網路中的虛擬機器已由 DNS 伺服器正確設定)，且封包可在跨單位虛擬網路內往來傳送。

使用下列程序兩次，分別為兩部 SQL server 進行設定，讓新的資料庫以及帳戶和權限使用 F: 磁碟機。


1.	在 [開始] 畫面中輸入 **SQL Studio**，然後按一下 [**SQL Server 2014 Management Studio**]。
2.	在 [**連線到伺服器**] 中按一下 [**連線**]。
3.	在左側窗格中，以滑鼠右鍵按一下最上層節點 (以機器名稱命名的預設執行個體)，然後按一下 [**內容**]。
4.	在 [**伺服器內容**] 中按一下 [**資料庫設定**]。
5.	在 [**資料庫預設位置**] 中設定下列值： 
- 將 [**資料**] 路徑設定為 **f:\\Data**。
- 將 [**記錄檔**] 路徑設定為 **f:\\Log**。
- 將 [**備份**] 路徑設定為 **f:\\Backup**。
- 只有新的資料庫才會使用這些位置。
6.	按一下 [**確定**] 關閉視窗。
7.	在左側窗格中展開 [**安全性資料夾**]。
8.	以滑鼠右鍵按一下 [**登入**]，然後按一下 [**新增登入**]。
9.	在 [**登入名稱**] 中輸入*網域*\\sp_farm_db in，其中*網域*是指建立 sp_farm_db 帳戶所在網域的名稱。 
10.	在 [**選取頁面**] 下按一下 [**伺服器角色**]，再按一下 [**系統管理員**] ，然後按一下 [**確定**]。
11.	關閉 SQL Server 2014 Management Studio。

使用下列程序兩次，分別為兩部 SQL server 進行設定，允許使用 sp_farm_db 帳戶進行遠端桌面連線。

1.	在 [開始] 畫面中，以滑鼠右鍵按一下 [**這台電腦**]，然後按一下 [**內容**] 。
2.	在 [**系統**] 視窗中按一下 [**遠端設定**]。
3.	在 [**遠端桌面**] 部分中按一下 [**選取使用者**]，然後按一下 [**新增**]。
4.	在 [輸入要選取的物件名稱] 中輸入「[網域]**\\sp_farm_db**」，然後按三次 [確定]。

SQL Server 需要一個連接埠讓用戶端存取資料庫伺服器，同時也需要連接埠來連線 SQL Server Management Studio，並管理高可用性群組。接下來，在系統管理員層級 Windows PowerShell 命令提示字元中執行下列命令兩次，分別為兩部 SQL server 進行設定，新增允許輸入流量至 SQL Server 的防火牆規則。

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

請在每一部 SQL Server 虛擬機器中，以本機系統管理員的身分登出。

如需關於在 Azure 中將 SQL Server 效能最佳化的資訊，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳做法](https://msdn.microsoft.com/library/azure/dn133149.aspx)。同時您也可以停用 SharePoint 伺服器陣列儲存體帳戶的異地備援儲存體 (GRS)，並使用儲存空間最佳化 IOP。

## 設定叢集多數節點伺服器

透過[使用遠端桌面連線程序登入虛擬機器](virtual-machines-workload-intranet-sharepoint-phase2.md#logon)，讓叢集多數節點使用網域帳戶認證登入。

在叢集多數節點中，使用[測試連線能力程序](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn)來測試組織網路中各個位置的連線能力。

## 建立 Windows 伺服器叢集

SQL Server AlwaysOn 可用性群組依賴 Windows Server 的容錯移轉叢集 (WSFC) 功能。 該功能可讓多部機器在叢集中以群組的方式加入。當其中一部機器故障，第二部機器則可取而代之。因此，第一個工作便是為加入叢集的所有機器啟用容錯移轉叢集功能，其中包括：

- 主要 SQL Server
- 次要 SQL Server
- 叢集多數節點

容錯移轉叢集需要至少三部虛擬機器。其中兩部用來主控 SQL Server。第二部 SQL Server 虛擬機器為同步處理的次要複本，以確保主要機器故障時，不會遺失任何資料。第三部機器不需主控 SQL Server。叢集多數節點在 WSFC 中負責仲裁見證。由於 WSFC 叢集依賴仲裁來監控狀態，因此一定要有多數來確保 WSFC 叢集為上線狀態。若叢集裡只有兩部機器，當其中一部故障時，就沒有多數的情況。如需詳細資訊，請參閱 [WSFC 仲裁模式與投票設定 (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx)。

請在兩部 SQL Server 電腦與叢集多數節點中，於系統管理員層級的 Windows PowerShell 命令提示字元中執行下列命令：

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

由於目前 Azure 中 DHCP 的非 RFC 相容行為，無法成功建立 Windows Server 容錯移轉叢集 (WSFC) 叢集。如需詳細資訊，請在 Azure 虛擬機器中 SQL Server 的「高可用性和災害復原」內搜尋「Azure 網路中的 WSFC 叢集行為」。 不過還是有相對的因應措施。使用下列步驟建立叢集。

1.	使用 **sp_install** 帳戶登入主要 SQL Server 虛擬機器。
2.	在 [開始] 畫面中輸入 **Failover**，然後按一下 [**容錯移轉叢集管理員**]。
3.	在左側窗格中，以滑鼠右鍵按一下 [**容錯移轉叢集管理員**]，然後按一下 [**建立叢集**]。
4.	在 [開始之前] 頁面中按一下 [**下一步**]。
5.	在 [選取伺服器] 頁面中輸入主要 SQL Server 機器的名稱，按一下 [**新增**]，然後按一下 [**下一步**]。
6.	在 [驗證警告] 頁面中，按一下 [**否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。按 [下一步] 後，繼續建立叢集。**]，然後按一下 [**下一步**]。
7.	在 [管理叢集的存取點] 頁面中，請在 [**叢集名稱**] 文字方塊中輸入叢集名稱，然後按一下 [**下一步**]。
8.	在 [確認] 頁面中按一下 [**下一步**] 開始建立叢集。 
9.	在 [摘要] 頁面中按一下 [**完成**]。
10.	在左側窗格中按一下您的新叢集。在 [內容] 窗格的 [**叢集核心資源**] 部分中，開啟您的伺服器叢集名稱。[**IP位址**] 資源的狀態為 [**失敗**]。 由於指派給叢集的 IP 位址與虛擬機器相同，因此該 IP 位址資源無法上線，造成位址重覆的狀況。 
11.	以滑鼠右鍵按一下失敗的 [**IP 位址**]資源，然後按一下 [**內容**]。
12.	在 [**IP 位址內容**] 對話方塊中，按一下 [**靜態 IP 位址**]。
13.	輸入一個未使用的 IP，此 IP 位址範圍需與 SQL Server 所在的子網路相對應，然後按一下 [**確定**]。
14.	以滑鼠右鍵按一下失敗的 [IP 位址] 資源，然後按一下 [**上線**]。等待兩個資源上線。待叢集名稱資源上線後，將使用新的 Active Directory (AD) 電腦帳戶更新網域控制站。此 AD 帳戶稍後將用來執行可用性群組叢集服務。
15.	現在 AD 帳戶已建立，請讓叢集名稱離線。以滑鼠右鍵按一下 [**叢集核心資源**] 中的叢集名稱，然後按一下 [**離線**]。
16.	若要移除叢集 IP 位址，請以滑鼠右鍵按一下 [**IP 位址**]，按一下 [**移除**] ，並在出現提示時按一下 [**是**]。由於叢集資源依賴 IP 位址資源，因此無法再次上線。不過，可用性群組不需依賴叢集名稱或 IP 位址即可正常運作。因此叢集名稱可保持離線狀態。
17.	若要將其餘節點新增至叢集，請以滑鼠右鍵按一下左側窗格中的叢集名稱，然後按一下 [**新增節點**]。
18.	在 [開始之前] 頁面中按一下 [**下一步**]。 
19.	在 [選取伺服器] 頁面中輸入名稱，然後按一下 [**新增**] ，同時將次要 SQL Server 和叢集多數節點新增至叢集。新增兩台電腦後按一下 [**下一步**]。若無法新增機器，並出現錯誤訊息「遠端登錄並未執行」，請執行下列步驟。登入機器、開啟 [服務] 嵌入式管理單元 (services.msc) 並啟用遠端登錄。如需更多資訊，請參閱[無法連線至遠端登陸服務](http://technet.microsoft.com/library/bb266998.aspx)。 
20.	在 [驗證警告] 頁面中，按一下 [**否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。按 [下一步] 後，繼續建立叢集。**]，然後按一下 [**下一步**]。 
21.	在 [確認] 頁面中按一下 [ **下一步**]。
22.	在 [摘要] 頁面中按一下 [**完成**]。
23.	在左側窗格中按一下 [ **節點**]。畫面中應該會列出三台電腦。

## 啟用 AlwaysOn 可用性群組

下一個步驟則是使用 SQL Server 組態管理員啟用 AlwaysOn 可用性群組。請注意，SQL Server 中的可用性群組與 Azure 可用性設定組有所不同。可用性群組包含高可用性和可復原的資料庫。 Azure 可用性設定組會將虛擬機器配置到不同的容錯網域。如需容錯網域的詳細資訊，請參閱[管理虛擬機器的可用性](virtual-machines-manage-availability.md)。

使用這些步驟啟用 SQL Server 上的 AlwaysOn 可用性群組。

1.	使用 **sp_farm_db** 帳戶登入主要 SQL Server，或使用其他在 SQL Server 中具有系統管理員伺服器角色的帳戶。
2.	在 [ 開始] 畫面中輸入 **SQL Server Configuration**，然後按一下 [**SQL Server 組態管理員**]。
3.	在左側窗格中按一下 [**SQL Server 服務**]。
4.	在 [内容] 窗格中按兩下 [**SQL Server (MSSQLSERVER)**]。
5.	在 [**SQL Server (MSSQLSERVER) 屬性**] 中按一下 [**AlwaysOn 高可用性**] 索引標籤、選取 [**啟用 AlwaysOn可用性群組**]，然後按一下 [**套用**]，並在出現提示時按一下 [**確定**]。先不要關閉屬性視窗。 
6.	按一下 [虛擬-機器-管理-可用性] 索引標籤，然後在 [帳戶名稱] 中輸入「[網域]**\\sqlservice**」。在 [**密碼**] 和 [**確認密碼**] 中輸入 sqlservice 帳戶密碼，然後按一下 [**確定**]。
7.	在訊息視窗中按一下 [**是**]，重新開啟 SQL Server 服務。
8.	登入次要 SQL Server 並重複此程序。 

此處顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## 下一步

如要繼續設定此工作負載，請前往[第 4 階段：設定 SharePoint Server](virtual-machines-workload-intranet-sharepoint-phase4.md)。

## 其他資源

[在 Azure 中部署含有 SQL Server AlwaysOn 可用性群組的 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->