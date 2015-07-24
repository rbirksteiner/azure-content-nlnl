<properties 
	pageTitle="SQL Server 與 Azure Site Recovery 的災害復原" 
	description="Azure Site Recovery 可以將 SQL Server 的複寫、容錯移轉及復原作業協調至次要內部部署站台或 Azure。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# SQL Server 與 Azure Site Recovery 的災害復原 

Site Recovery 是一種 Azure 服務，可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 策略做出貢獻。Site Recovery 支援多種複寫機制，以持續保護、複寫和容錯移轉機器至 Azure，或次要資料中心。在 [Azure Site Recovery 概觀](site-recovery-overview.md)中概要了解所有部署案例。

 本文說明如何使用 SQL Server BCDR 技術和 Site Recovery的組合，保護應用程式的 SQL Server 後端。您應該先充分了解 SQL Server BCDR 功能 (容錯移轉叢集，AlwaysOn 可用性群組、資料庫鏡像、記錄傳送) 和 Site Recovery，然後再部署本文中所述的案例。



## 概觀

許多工作負載都使用 SQL Server 做為基礎。SharePoint、Dynamics 和 SAP 之類的應用程式會使用 SQL Server 實作資料服務。SQL Server 高可用性和災害復原具備 SQL Server 之類的可用性群組，以保護和復原 SQL Server 資料庫。

Site Recovery 可以保護當做 Hyper-V 虛擬機器、VMware 虛擬機器，或實體伺服器執行的 SQL Server。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>實體伺服器</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>內部部署至內部部署</td>
		<td>內部部署至 Azure</td>
		<td>內部部署至內部部署</td>
		<td>內部部署至 Azure</td>
		<td>內部部署至內部部署</td>
		<td>內部部署至 Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>是</td>
		<td>是</td>
		<td>是</td>
		<td>敬請期待</td>
		<td>是</td>
		<td>敬請期待</td>
    </tr>
    </tbody>
    </table>

## 支援與整合

Site Recovery 可以與資料表中摘要說明的原生 SQL Server BCDR 技術整合，以提供災害復原解決方案。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>功能</b></td>
		<td><b>詳細資料</b></td>
		<td><b>SQL Server 版本</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>AlwaysOn 可用性群組</b></td>
		<td><p>多個 SQL Server 獨立執行個體中，每個都在含有多個節點的容錯移轉叢集中執行。</p> <p>資料庫可以分組到可以在 SQL Server 執行個體上複製 (鏡像) 的容錯移轉群組，因此不需要任何共用儲存體。</p> <p>在主要站台與一或多個次要站台之間提供災害復原功能。使用同步複寫與自動容錯移轉在可用性群組中設定 SQL Server 資料庫時，可以在不共用任何內容的叢集中設定兩個節點。</p></td>
		<td>SQL Server 2014/2012 Enterprise Edition</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>容錯移轉叢集 (AlwaysOn FCI)</b></td>
		<td><p>SQL Server 會針對內部部署 SQL Server 工作負載的高可用性，運用 Windows 容錯移轉叢集。</p><p>使用共用磁碟執行 SQL Server 執行個體的節點是在容錯移轉叢集中設定。如果執行個體關閉，叢集會容錯移轉至另一個節點。</p> <p>叢集無法防止共用儲存體失敗或中斷。共用磁碟可以使用 iSCSI、光纖通道或共用 VHDX 實作。</p></td>
		<td><p>SQL Server Enterprise Edition</p> <p>SQL Server Standard Edition (僅限兩個節點)</p></td>
	<tr align="left" valign="top">
		<td><b>高安全性模式下的資料庫鏡像</b></td>
		<td>將單一資料庫保護為單一次要複本。提供高安全性 (同步) 和高效能 (非同步) 複寫模式。不需要容錯移轉叢集。</td>
		<td><p>SQL Server 2008 R2</p><p>SQL Server Enterprise 所有版本</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>獨立式 SQL Server</b></td>
		<td>SQL Server 和資料庫裝載在單一伺服器 (實體或虛擬) 上。如果是虛擬伺服器，則主機叢集用於高可用性。沒有來賓層級的高可用性。</td>
		<td>Enterprise 或 Standard Edition</td>
 
    </tbody>
    </table>

下表摘要說明我們將 SQL Server BCDR 技術整合到 Site Recovery 部署的建議。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>版本</b></td>
		<td><b>版本</b></td>
		<td><b>部署</b></td>
		<td><b>內部部署至內部部署</b></td>
		<td><b>內部部署至 Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 或 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>容錯移轉叢集執行個體</td>
		<td>AlwaysOn 可用性群組</td>
		<td>AlwaysOn 可用性群組</td>
    </tr>
	<tr align="left" valign="top">
		<td>高可用性的 AlwaysOn 可用性群組</td>
		<td>AlwaysOn 可用性群組</td>
		<td>AlwaysOn 可用性群組</td>
    </tr>
	<tr align="left" valign="top">
		<td>標準</td>
		<td>容錯移轉叢集執行個體</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise 或 Standard</td>
		<td>獨立</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise 或 Standard</td>
		<td>獨立</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
		<td>包含本機鏡像的 Site Recovery 複寫</td>
    </tr>
    </tbody>
    </table>


## 部署必要條件

以下是您在開始之前的必要條件：


- 執行支援的 SQL Server 版本的內部部署 SQL Server 部署。通常您也需要適用於您 SQL Server 的 Active Directory。
- 您要部署之案例的必要條件。每個部署文件中都可以找到必要條件。在 [Site Recovery 概觀](site-recovery-overview.md)中會列出並連結這些必要條件。
- 如果您想要在 Azure 中設定復原，必須在您的 SQL Server 虛擬機器上執行 [Azure 虛擬機器整備評估](http://www.microsoft.com/download/details.aspx?id=40898)工具，以確定它們與 Azure 和 Site Recovery 相容。

## 設定保護

您必須執行幾個步驟：

- 設定 Active Directory
- 設定 SQL Server 叢集或獨立伺服器的保護

### 設定 Active Directory

您在次要復原站台上必須有適用於 SQL Server 的 Active Directory 才能正常運作。其中有幾個選項：

- **小型企業** — 如果您有少數的應用程式和適用於內部部署站台的單一網域控制站，而且您想要容錯移轉整個網站，建議您使用 Site Recovery 複寫，將網域控制站複寫至次要資料中心或 Azure。

- **中大型企業** — 如果您有大量的應用程式、您執行的是 Active Directory 樹系，而且您想要依應用程式或工作負載容錯移轉，建議您在次要資料中心或 Azure 中設定其他網域控制站。請注意，如果您要使用 AlwaysOn 可用性群組復原至遠端站台，建議您在次要站台或 Azure 上設定其他不同的網域控制站，以供已復原 SQL Server 執行個體使用。

本文件中的指示假設在次要位置會提供網域控制站。

### 設定獨立式 SQL Server 的保護


在此設定中，建議您使用 Site Recovery 複寫保護 SQL Server 電腦。確切步驟將取決於 SQL Server 是設定為虛擬機器還是實體伺服器，以及您想要複寫至 Azure 還是次要內部部署站台。在 [Site Recovery 概觀](site-recovery-overview.md)中取得所有部署案例的指示。


### 設定 SQL Server 叢集的保護 (2012 或 2014 Enterprise)

如果 SQL Server 為高可用性使用可用性群組或容錯移轉叢集執行個體，建議您也在復原站台上使用可用性群組。請注意，本指南是供不使用分散式交易的應用程式使用。

##### 內部部署至內部部署

1. 在可用性群組中[設定資料庫](https://msdn.microsoft.com/library/hh213078.aspx)。
2. 在次要站台上建立新的虛擬網路。
3. 在新的虛擬網路與主要站台之間設定站台對站台 VPN。
4. 在復原站台上建立虛擬機器，並在其上安裝 SQL Server。
5. 將現有的 AlwaysOn 可用性群組擴充為新的 SQL Server 虛擬機器。將此 SQL Server 執行個體設定為非同步複本。
6. 建立可用性群組接聽程式，或更新現有的接聽程式，以包含非同步複本虛擬機器。
7. 請確定應用程式伺服器陣列是使用接聽程式設定的。如果是使用資料庫伺服器名稱設定，請將其更新為使用接聽程式設定，如此您就不需要在容錯移轉後重新加以設定。

#### 內部部署至 Azure

當您複寫至 Azure 時，設定多個可用性群組非常具有挑戰性，因為每個可用性群組都需要一個專用的接聽程式，而且設定每個接聽程式需要一個個別的雲端服務。建議您設定一個可用性群組，其中包含所有資料庫。

1. 建立 Azure 虛擬網路。
2. 在內部部署站台與此網路之間設定站台對站台 VPN。
3. 在網路中建立一個新的 SQL Server Azure 虛擬機器，並將其設定為非同步可用性群組複本。如果您在容錯移轉至 Azure 後，需要適用於 SQL Server 層的高可用性，則在 Azure 中設定兩個非同步複本。
4. 在虛擬網路中設定網域控制站的複本。
5. 請確定已在虛擬機器上啟用虛擬機器擴充功能。若要在復原計畫中推送 SQL Server 專屬的指令碼，則需要進行此動作。
6. 使用 Azure 的內部負載平衡器設定可用性群組的 SQL Server 接聽程式。
7. 設定應用程式層使用接聽程式存取資料庫層。對於使用分散式交易的應用程式，建議您使用 Site Recovery 搭配 SAN 複寫或 VMWare 站台對站台複寫。

### 為 SQL Server 叢集設定保護 (Standard 或 2008 R2)

對於執行 SQL Server Standard Edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫保護 SQL Server。

#### 內部部署至內部部署

- 對於 Hyper-V 環境，如果應用程式使用分散式交易，建議您部署[包含 SAN 複寫的 Site Recovery](site-recovery-vmm-san.md)。

- 對於 VMware 環境，建議您部署 [VMware 至 VMware](site-recovery-vmware-to-vmware.md) 保護。

#### 內部部署至 Azure

複寫至 Azure 時，Site Recovery 不支援來賓叢集支援。SQL Server 也不會為 Standard Edition 提供低成本的災害復原解決方案。建議您將內部部署 SQL Server 叢集保護至獨立式 SQL Server，並在 Azure 中復原。


1. 在內部部署站台中設定其他獨立式 SQL Server 執行個體。
2. 設定此執行個體做為需要保護的資料庫的鏡像。在高安全性模式下設定鏡像。
3.	根據環境 ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) 或 [VMware](site-recovery-vmware-to-azure.md)) 在內部部署站台上設定 Site Recovery。
4.	使用 Site Recovery 複寫將新的 SQL Server 執行個體複寫至 Azure。它是高安全性鏡像副本，因此它將會與主要叢集同步處理，但是它會使用 Site Recovery 複寫，複寫至 Azure。

下圖說明此設定。

![標準叢集](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##建立復原計畫

復原計畫會將應該一起容錯移轉的機器群組在一起。開始之前，請先深入了解[復原計畫](site-recovery-create-recovery-plans.md)和[容錯移轉](site-recovery-failover.md)。


### 建立 SQL Server 叢集 (SQL Server 2012/2014 Enterprise) 的復原計畫

#### 設定 SQL Server 指令碼以容錯移轉至 Azure

在此案例中，我們運用適用於復原計畫的自訂指令碼和 Azure 自動化，以設定 SQL Server 可用性群組的指令碼式容錯移轉。

1.	建立指令碼的本機檔案以容錯移轉可用性群組。此範例指令碼會在 Azure 複本上指定可用性群組的路徑，並將其容錯移轉至該複本執行個體。此指令碼會藉由使用自訂指令碼擴充功能傳遞該指令碼，以便在 SQL Server 複本虛擬機器上執行。



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	將指令碼上傳至 Azure 儲存體帳戶中的 Blob。使用範例：

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	建立 Azure 自動化 Runbook，以便在 Azure 中叫用 SQL Server 複本虛擬機器上的指令碼。若要這樣做，請使用此範例指令碼。[深入](site-recovery-runbook-automation.md)如何在復原計畫中使用自動化 Runbook。

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	當您建立應用程式的復原計畫時，加入可叫用自動化 Runbook 的 "pre-Group 1 boot" 指令碼式步驟以容錯移轉可用性群組。

#### 設定 SQL Server 指令碼以容錯移轉至次要網站

1. 將此範例指令碼加入至主要和次要站台上的 VMM 程式庫。

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. 當您建立應用程式的復原計畫時，加入可叫用指令碼的 "pre-Group 1 boot" 指令碼式步驟以容錯移轉可用性群組。

### 建立 SQL Server 叢集 (Standard) 的復原計畫

#### 設定 SQL Server 指令碼以容錯移轉至 Azure

1.	建立指令碼的本機檔案，以容錯移轉 SQL Server 資料庫鏡像。使用此範例指令碼。

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	將指令碼上傳至 Azure 儲存體帳戶中的 Blob。使用此範例指令碼。

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	建立 Azure 自動化 Runbook，以便在 Azure 中叫用 SQL Server 複本虛擬機器上的指令碼。若要這樣做，請使用此範例指令碼。[深入了解](site-recovery-runbook-automation.md)如何在復原計畫中使用自動化 Runbook。請在執行這項操作之前，先確定虛擬機器代理程式在容錯移轉的 SQL Server 虛擬機器上執行。

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. 在復原計畫中加入以下步驟，以容錯移轉 SQL Server 層：

	- 針對計劃性容錯移轉，加入主端指令碼，以便在「群組關閉」之後關閉主要叢集。
	- 將 SQL Server 資料庫鏡像虛擬機器加入至復原計畫，最好是在第一個開機群組中。
3.	加入容錯移轉後指令碼，以便使用上述的自動化指令碼容錯移轉此虛擬機器內部的鏡像副本。請注意，因為資料庫執行個體名稱將會變更，因此必須重新設定應用程式層以使用新的資料庫。


#### 設定 SQL Server 指令碼以容錯移轉至次要網站

1.	將此範例指令碼加入至主要和次要站台上的 VMM 程式庫。

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	將 SQL Server 資料庫鏡像虛擬機器加入至復原計畫，最好是在第一個開機群組中。
3.	加入容錯移轉後指令碼，以便使用上述的 VMM 指令碼容錯移轉此虛擬機器內部的鏡像副本。請注意，因為資料庫執行個體名稱將會變更，因此必須重新設定應用程式層以使用新的資料庫。





## 測試容錯移轉考量

如果您使用的是 AlwaysOn 可用性群組，就無法執行 SQL Server 層的測試容錯移轉。或者，請考慮下列選項：

- 選項 1

	1. 執行應用程式和前端層的測試容錯移轉。
	2. 更新應用程式層，以便在唯讀模式下存取副本，並執行應用程式的唯讀測試。

- 選項 2
1.	建立 SQL Server 虛擬機器執行個體的副本 (使用 VMM 複製進行站台對站台或 Azure 備份)，並將其顯示在測試網路中
2.	使用復原計畫執行測試容錯移轉。

## 容錯回復考量

針對 SQL 標準叢集，非計劃性容錯移轉後的容錯回復將需要從鏡像執行個體 SQL Server 備份並還原至原始叢集，然後再重新建立鏡像。





 

<!---HONumber=July15_HO2-->