<properties
	pageTitle="管理註冊與保護" 
	description="Azure Site Recovery 可將內部部署伺服器上的虛擬機器複寫、容錯移轉及復原協調至 Azure 或次要資料中心。使用本文取消註冊 Site Recovery 保存庫中的伺服器，並停用虛擬機器和實體伺服器的保護。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/08/2015" 
	ms.author="raynew"/>

# 管理註冊與保護

本文說明如何從 Site Recovery 保存庫取消註冊伺服器，以及如何停用 Site Recovery 保護的虛擬機器保護。如果您在閱讀本文後有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 取消註冊 VMM 伺服器

您可以在 Azure Site Recovery 入口網站中的 [伺服器] 索引標籤上刪除伺服器，以便從保存庫中取消註冊 VMM 伺服器。請注意：

-  **連接的 VMM 伺服器**：建議您在連線至 Azure 時，取消註冊 VMM 伺服器。這可確保正確清除內部部署 VMM 伺服器及其相關聯的 VMM 伺服器上的設定 (包含雲端的 VMM 伺服器，這些雲端會對應至您想要刪除的伺服器上的雲端)。建議您只在連線有永久問題時，才移除未連線的伺服器。
- **未連接的 VMM 伺服器**：如果 VMM 伺服器未連線，當您刪除該伺服器時，您必須手動執行指令碼，才能執行清除作業。指令碼位於 [Microsoft 資源庫](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439)中。請記下伺服器的 VMM 識別碼，以完成手動清除程序。
- **叢集中的 VMM 伺服器**：如果您需要取消註冊在叢集中部署的 VMM 伺服器，請執行下列操作：

	- 如果伺服器已連線，請刪除 [伺服器] 索引標籤上已連線的 VMM 伺服器。若要解除安裝伺服器上的提供者，登入每個叢集節點，然後從 [控制台] 解除安裝該提供者。針對叢集中的所有被動節點執行上一節中所參考的清除指令碼，以刪除註冊項目。
	- 如果伺服器未連線，您必須針對所有叢集節點執行清除指令碼。

### 取消註冊未連線的 VMM 伺服器

在您想要移除的 VMM 伺服器上：

1. 取消註冊 Azure 入口網站中的 VMM 伺服器。
2. 在 VMM 伺服器上，下載清除指令碼。
3. 使用 [以系統管理員身分執行] 選項開啟 PowerShell，以變更預設 (LocalMachine) 範圍的執行原則。
4. 依照指令碼中的指示進行。 

在所包含雲端與您要移除的伺服器上的雲端配對的 VMM 伺服器上：

1. 執行清除指令碼，然後依照步驟 2 到 4 進行。
2. 指定已取消註冊之 VMM 伺服器的 VMM 識別碼。 
3. 此指令碼將會移除 VMM 伺服器的註冊資訊以及雲端配對資訊。


## 取消註冊 Hyper-V 站台中的 Hyper-V 伺服器

部署 Azure Site Recovery 來保護 Hyper-V 站台中 Hyper-V 伺服器 (不含任何 VMM 伺服器) 上的虛擬機器時，可以從保存庫取消註冊 Hyper-V 伺服器，如下所示：

1. 對 Hyper-V 伺服器上的虛擬機器停用保護。
2. 在 Azure Site Recovery 入口網站中的 [伺服器] 索引標籤上選取伺服器，然後選取 [刪除]。進行時，伺服器不需要連線到 Azure。
3. 執行下列指令碼來清除伺服器上的設定，並從保存庫取消註冊。 

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## 停止保護 Hyper-V 虛擬機器

如果您想要停止保護 Hyper-V 虛擬機器，您必須移除它的保護。視您要如何移除保護，您可能需要以手動方式清除機器上的保護設定。

### 移除保護

1. 在雲端屬性的 [虛擬機器] 索引標籤中選取虛擬機器，然後選取 [移除]。
2. 在 [確認移除虛擬機器] 頁面上有幾個選項：

	- **停用保護**—如果您啟用並儲存此選項，虛擬機器將不再受到 Site Recovery 的保護。系統將會自動清除虛擬機器的保護設定。
	- **從保存庫移除**—如果您選擇此選項，虛擬機器將只會從 Site Recovery 保存庫中移除。虛擬機器的內部部署保護設定不受影響。您必須以手動方式清除設定，以移除保護設定、移除 Azure 訂用帳戶的虛擬機器，以及移除保護設定，即必須使用下列指示以手動清除的設定。

如果您選擇刪除虛擬機器及其硬碟，將會從目標位置移除它們。

### 手動清除保護設定 (VMM 網站之間的設定)

如果您選取 [停止管理虛擬機器]，請手動清除設定：

1. 在主要伺服器上，從 VMM 主控台執行這個指令碼，以清除主要虛擬機器的設定。在 VMM 主控台中，按一下 [PowerShell] 按鈕來開啟 VMM PowerShell 主控台。將 SQLVM1 取代成您的虛擬機器的名稱。

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 在次要 VMM 伺服器上，執行此指令碼來清除次要虛擬機器的設定：

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. 在次要 VMM 伺服器上，在您執行指令碼之後，重新整理 Hyper-V 主機伺服器上的虛擬機器，以便在 VMM 主控台中重新偵測次要虛擬機器。
4. 上述步驟將只清除 VMM 伺服器的複寫設定。如果您想要移除虛擬機器的虛擬機器複寫。您將需要在主要與次要虛擬機器上執行下列步驟。請執行下面的指令碼來移除複寫，並將 SQLVM1 取代成您的虛擬機器的名稱。

	    Remove-VMReplication –VMName “SQLVM1”


### 手動清除保護設定 (內部部署 VMM 網站與 Azure 之間的設定)

1. 在來源 VMM 伺服器上，執行此指令碼來清除主要虛擬機器的設定：

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 上述步驟將只清除 VMM 伺服器的複寫設定。在您從 VMM 伺服器移除複寫之後，請務必使用此指令碼移除在 Hyper-V 主機伺服器上執行之虛擬機器的複寫。將 SQLVM1 取代成您的虛擬機器的名稱，並將 host01.contoso.com 取代成 Hyper-V 主機伺服器的名稱。

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### 手動清除保護設定 (Hyper-V 網站與 Azure 之間的設定)

1. 在來源 Hyper-V 主機伺服器上，使用此指令碼移除虛擬機器的複寫。將 SQLVM1 取代成您的虛擬機器的名稱。

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## 停止保護 VMware 虛擬機器或實體伺服器

如果您想要停止保護 VMware 虛擬機器或實體伺服器，您必須移除它的保護。視您要如何移除保護，您可能需要以手動方式清除機器上的保護設定。

### 移除保護

1. 在雲端屬性的 [虛擬機器] 索引標籤中選取虛擬機器，然後選取 [移除]。
2. 在 [**移除虛擬機器**] 上選取其中一個選項：

	- **停用保護 (使用復原切入和調整大小的磁碟區)**—只要在擁有下列條件，您才會看到及啟用此選項：
		- **調整大小的虛擬機器磁碟區**—當您調整磁碟區大小時，虛擬機器會進入重大狀態。如果發生這種情況，請選取此選項。它會停用保護，同時保留在 Azure 中的復原點。當您重新啟用機器的保護時，重新調整過大小的磁碟區資料會傳輸至 Azure。
		- **執行容錯移轉**—執行從內部部署 VMware 虛擬機器或實體伺服器至 Azure 的容錯移轉來測試環境後，請選取此選項以重新開始保護您的內部部署虛擬機器。這個選項會停用每個虛擬機器，您接著必須重新啟用它們的保護。請注意：
			- 使用這項設定停用虛擬機器，不會影響 Azure 中的複本虛擬機器。
			- 您不得從虛擬機器解除安裝行動服務。
	
	- **停用保護**—如果您啟用並儲存此選項，機器將不再受到 Site Recovery 的保護。系統將會自動清除機器的保護設定。
	- **從保存庫移除**—如果您選擇此選項，機器將只會從 Site Recovery 保存庫中移除。機器的內部部署保護設定不受影響。若要移除機器上的設定，並從 Azure 訂用帳戶移除虛擬機器，而且您將需要解除安裝行動服務來清除設定。![移除選項](./media/site-recovery-manage-registration-and-protection/RegistrationProtection_RemoveVM.png)

<!---HONumber=July15_HO2-->