<properties
	pageTitle="自動化內部部署 VMM 網站及使用 PowerShell 的 Azure 之間的保護"
	description="使用 PowerShell 自動部署 Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="lauraa"/>

#  使用 PowerShell 部署 Azure Site Recovery
Windows PowerShell® 是以工作為基礎的命令列殼層和指令碼語言，特別為系統管理所設計。支援在 VMM 管理的 Hyper 網站與 Azure 之間，為 Azure Site Recovery 使用 PowerShell Cmdlet。

## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

本文說明如何使用 PowerShell 來自動化部署 Azure Site Recovery 的一般工作，包括為執行於虛擬機器 (位於 VMM 私人雲端中的 Hyper-V 主機伺服器上) 的工作負載協調和自動化保護。在這個案例中，虛擬機器會使用 Hyper-V 複本，從主要 VMM 站台複寫到 Azure 中。

本文包含案例的必要條件，並示範如何設定 Site Recovery 保存庫、在來源 VMM 伺服器上安裝 Azure Site Recovery 提供者、在保存庫註冊伺服器、加入 Azure 儲存體帳戶、在 Hyper-V 主機伺服器上安裝 Azure Site Recovery 代理程式、設定將套用到所有受保護虛擬機器之 VMM 雲端的保護設定、然後啟用那些虛擬機器的保護。測試容錯移轉，確認一切如預期般運作以完成動作。

如果您遇到設定這個案例的問題，請將問題張貼在 [Azure 復原服務論壇](http://go.microsoft.com/fwlink/?LinkId=313628)。


## 開始之前

確認您已備妥這些必要條件：
### Azure 必要條件

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。如果您沒有此帳戶，請先前往[免費試用](http://aka.ms/try-azure)。此外，您可以參閱 [Azure Site Recovery 管理員價格](http://go.microsoft.com/fwlink/?LinkId=378268)。
- 您將需要 Azure 儲存體帳戶才能將複寫的資料儲存至 Azure。此帳戶必須啟用異地複寫。應該與 Azure 站台復原服務位於相同的區或，且與相同的訂閱相關聯。若要深入了解設定 Azure 儲存體，請參閱 [Microsoft Azure 儲存體簡介](http://go.microsoft.com/fwlink/?LinkId=398704)。
- 您必須確認您想要保護的虛擬機器符合 Azure 要求。如需詳細資料，請參閱[虛擬機器支援](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A)。

### VMM 先決條件
- 您將需要在在 System Center 2012 R2 上執行的 VMM 伺服器。
- 任何包含您想要保護之虛擬機器的 VMM 伺服器都必須執行 Azure Site Recovery 提供者。它會在部署 Azure Site Recovery 期間安裝。
- 在您想要保護的 VMM 伺服器上，您至少需要一個雲端。這個雲端應該包含：
	- 一或多個 VMM 主機群組。
	- 每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。
	- 來源 Hyper-V 伺服器上的一或多部虛擬機器。虛擬機器應該為第 1 代。
- 深入了解設定 VMM 雲端：
	- 在[使用 System Center 2012 R2 VMM 的私人雲端新功能](http://go.microsoft.com/fwlink/?LinkId=324952)和 [VMM 2012 和雲端](http://go.microsoft.com/fwlink/?LinkId=324956)中進一步了解私人 VMM 雲端。
	- 深入了解[設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- 在您備妥雲端網狀架構元素之後，[在 VMM 中建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324953)與[逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://go.microsoft.com/fwlink/?LinkId=324954)中瞭解建立私人雲端。

### Hyper-V 的必要條件

- 主機 Hyper-V 伺服器至少必須執行附帶 Hyper-V 角色的 Windows Server 2012，並且已安裝最新更新。
- 如果您在叢集中執行 Hyper-V，請注意，如果您具有靜態 IP 位址叢集，並不會自動建立叢集代理。您必須手動設定叢集代理。如需指示，請參閱[設定 Hyper-V 複本代理人](http://go.microsoft.com/fwlink/?LinkId=403937)。
- 您想要管理保護的任何 Hyper-V 主機伺服計或叢集都必須包含在 VMM 雲端中。

### 網路對應的必要條件
當您在 Azure 網路對應中保護虛擬機器，請對應來源 VMM 伺服器上的 VM 網路和目標 Azure 網路，以啟用下列項目：

- 在相同網路上容錯移轉的所有機器都可以彼此連接，無論它們隸屬於哪個復原計畫都一樣。
- 如果目標 Azure 網路上已設定網路閘道，則虛擬機器可以連接到其他內部部署虛擬機器。
- 如果您未設定網路對應，則只有在相同復原計畫中容錯移轉的虛擬機器才能在容錯移轉到 Azure 之後彼此連接。

如果您想要部署網路對應，您需要下列項目：

- 您想要在來源 VMM 伺服器上保護的虛擬機器應該連線到 VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。
- 複寫的虛擬機器可在容錯移轉之後連線的 Azure 網路。您將在容錯移轉時選取此網路。此網路應該和您的 Azure Site Recovery 訂用帳戶在相同的區域中。
- 深入了解網路對應：
	- [在 VMM 中設定邏輯網路](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [在 VMM 中設定 VM 網路和閘道](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [在 Azure 中設定和監視虛擬網路](http://go.microsoft.com/fwlink/?LinkId=402555)

###PowerShell 必要條件
確定 Azure PowerShell 已經準備就緒。如果您已經使用 PowerShell，您必須升級至 0.8.10 版或更新版本。如需設定 PowerShell 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。一旦已安裝並設定 PowerShell，您可以檢視[這裡](https://msdn.microsoft.com/library/dn850420.aspx)之服務的所有可用的 Cmdlet。

若要深入了解可協助您使用 Cmdlet 的提示，例如在 Azure PowerShell 中處理參數值、輸入和輸出的方式，請參閱 [Azure Cmdlet 使用者入門](https://msdn.microsoft.com/library/azure/jj554332.aspx)。

## 步驟 1：設定訂用帳戶 

在 PowerShell 中，執行這些 Cmdlet：



			$UserName = "<user@live.com>"
	$Password = "<password>"
	$AzureSubscriptionName = "prod_sub1"

	$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
	$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
	Add-AzureAccount -Credential $Cred;
	$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName


以您的特定資訊取代 "< >" 中的元素。

## 步驟 2：建立 Site Recovery 保存庫

在 PowerShell 中，將 "< >" 裡面的元素取代成您的特定資訊，然後執行以下命令：

```

	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"

```


```
	New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
	$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## 步驟 3：產生保存庫註冊金鑰

在保存庫中產生註冊金鑰。下載 Azure Site Recovery 提供者並將其安裝在 VMM 伺服器之後，您將使用此金鑰在保存庫中註冊 VMM 伺服器。

1.	取得保存庫設定檔並設定內容：
	
	```
	
		$VaultName = "<testvault123>"
		$VaultGeo  = "<Southeast Asia>"
		$OutputPathForSettingsFile = "<c:>"
	
		$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	執行下列命令來設定保存庫內容：
	
	```	
		$VaultSettingFilePath = $vaultSetingsFile.FilePath 
		$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
```

## 步驟 4：安裝 Azure Site Recovery 提供者

1.	在 VMM 機器上，執行下列命令來建立目錄：
	
	```
	
		pushd C:\ASR\
	
	```
	
2. 執行下列命令，使用已下載的提供者將檔案解壓縮
	
	```
	
		AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3. 使用下列命令安裝提供者：
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
	                $isNotInstalled = $true;
	                if(Test-Path $installationRegPath)
	                {
	                                $isNotInstalled = $false;
	                }
	}While($isNotInstalled)
	
	```
	
	等待安裝完成。
	
4. 使用下列命令在保存庫中註冊伺服器：
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## 步驟 5：建立 Azure 儲存體帳戶

如果您沒有 Azure 儲存體帳戶，請執行下列命令來建立啟用帳戶的異地複寫：

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

請注意，儲存體帳戶必須與 Azure Site Recovery 服務位於相同的區或，且與相同的訂用帳戶相關聯。


## 步驟 6：安裝 Azure 復原服務代理程式

從 Azure 入口網站，在 VMM 雲端中您要保護的每一個 Hyper-V 主機伺服器上，安裝 Azure 復原服務代理程式。

在所有 VMM 主機上執行下列命令：

```

	marsagentinstaller.exe /q /nu

```


## 步驟 7：設定雲端保護設定

1.	執行下列命令來建立 Azure 的雲端保護設定檔：
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider 	HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName `
	-RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	執行下列命令以取得保護容器：
	
	```
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	啟動與雲端的保護容器關聯：
	
	```
	
		$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -	ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	完成工作之後，執行下列命令：

			$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
			if($job -eq $null -or $job.StateDescription -ne "Completed")
			{
				$isJobLeftForProcessing = $true;
			}
5. 完成工作處理之後，執行下列命令：

		if($isJobLeftForProcessing)
			{
			Start-Sleep -Seconds 60
			}
				}While($isJobLeftForProcessing)
	
	
若要檢查作業是否完成，請依照[監視活動](#monitor)的步驟。

## 步驟 8：設定網路對應
開始網路對應之前，請確認來源 VMM 伺服器上的虛擬機器已連線到 VM 網路。此外，請建立一或多個 Azure 虛擬網路。請注意，多個 VM 網路可對應至單一 Azure 網路。

請注意，如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。

第一個命令會取得目前的 Azure Site Recovery 保存庫的伺服器。命令會將 Microsoft Azure Site Recovery 伺服器儲存在 $Servers 陣列變數。



	$Servers = Get-AzureSiteRecoveryServer


第二個命令會取得 $Servers 陣列中第一部伺服器的站台復原網路。此命令會在 $Networks 變數中儲存網路。

```

	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

第三個命令使用 Get-AzuresubScription Cmdlet 取得 Azure 訂用帳戶，然後再將該值儲存在 $Subscriptions 變數中。

```

	$Subscriptions = Get-AzureSubscription

```

第四個命令會使用 Get-AzureVNetSite Cmdlet，然後是 $AzureVmNetworks 變數中的該值來取得 Azure 虛擬網路。

```

	$AzureVmNetworks = Get-AzureVNetSite

```

最終的 Cmdlet 會在主要網路與 Azure 虛擬機器網路之間建立對應。Cmdlet 會將主要網路指定為 $Networks 的第一個元素。Cmdlet 為使用其識別碼，將虛擬機器網路指定為 $AzureVmNetworks 的第一個元素。此命令包含您的 Azure 訂用帳戶識別碼。

```

PS C:> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## 步驟 9：對虛擬機器啟用保護

正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。請注意：

虛擬機器必須符合 Azure 需求。請在《規劃指南》的<a href="http://go.microsoft.com/fwlink/?LinkId=402602">必要條件和支援</a>中查看這些需求。

若要啟用保護功能，您必須為虛擬機器設定作業系統和作業系統磁碟屬性。當您使用虛擬機器範本在 VMM 中建立虛擬機器時，您可以設定屬性。您也可以在虛擬機器屬性的 [**一般**] 和 [**硬體組態**] 索引標籤上，為現有的虛擬機器設定這些屬性。若未在 VMM 中設定這些屬性，您將可在 Azure 站台復原入口網站中加以設定。


	
1.	若要啟用保護，請執行下列命令以取得保護容器：
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. 執行下列命令以取得保護實體 (VM)：
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
		```
			
3. 執行下列命令以啟用 VM 的 DR：

	
	$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Protection Enable -Force
	

## 測試您的部署

若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。請注意：

- 如果您在容錯移轉之後要使用遠端桌面連接到 Azure 中的虛擬機器，請先在虛擬機器上啟用遠端桌面連線，再執行測試容錯移轉。
- 容錯移轉之後，您將透過遠端桌面使用公用 IP 位址連接到 Azure 中的虛擬機器。如果想要這樣做，請確定沒有任何網域原則禁止您使用公用位址連接到虛擬機器。

若要檢查作業是否完成，請依照[監視活動](#monitor)的步驟。

### 建立復原計畫

1. 使用下列資料，建立 .xml 檔案做為復原計劃範本，然後將它儲存為 "C:\\RPTemplatePath.xml"。
2. 變更 RecoveryPlan 節點識別碼、Name、PrimaryServerId 和 SecondaryServerId。
3. 變更 ProtectionEntity 節點 PrimaryProtectionEntityId (來自 VMM 的 vmid)。
4. 您可以新增更多 ProtectionEntity 節點來新增更多 VM。
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. 在範本中填入資料：
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. 建立 RecoveryPlan：
	
	```
	
		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### 執行測試容錯移轉

1. 執行下列命令以取得 RecoveryPlan 物件：
	
	```
	
		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2. 執行下列命令來啟動測試容錯移轉：
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> 監視活動

使用下列命令來監視活動。請注意，您必須在工作之間等候處理程序完成。

```

Do
{
                $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
                Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
                if($job -eq $null -or $job.StateDescription -ne "Completed")
                {
                                $isJobLeftForProcessing = $true;
                }

```

```

if($isJobLeftForProcessing)
                {
                                Start-Sleep -Seconds 60
                }
}While($isJobLeftForProcessing)

```


##<a id="next" name="next" href="#next"></a>接續步驟
<UL>

<LI>如需 Azure Site Recovery PowerShell Cmdlet 的詳細資訊，請參閱<a href="https://msdn.microsoft.com/library/dn850420.aspx">這裡</a>的文章。

<LI>若要在完整生產環境中規劃及部署 Azure Site Recovery，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 的規劃指南</a>及 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery 的部署指南</a>。</LI>

<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</LI></UL>

<!---HONumber=July15_HO2-->