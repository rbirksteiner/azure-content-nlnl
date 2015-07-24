<properties 
   pageTitle="將 Azure 自動化 Runbook 加入至復原計劃" 
   description="本文說明 Azure Site Recovery 現在讓您使用 Azure 自動化擴充復原計畫，以便在復原至 Azure 期間，完成複雜的工作" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="07/03/2015"
   ms.author="ruturajd@microsoft.com"/>

  
   

# 將 Azure 自動化 Runbook 加入至復原計劃


本教學課程說明如何將 Azure Site Recovery 與 Azure 自動化整合在一起，以提供復原計畫的擴充性。復原計畫可以協調使用 Azure Site Recovery 保護的虛擬機器復原，以便同時複寫至次要雲端和 Azure 案例。復原計畫也有助於讓復原**一致精確**、**可重複**而且**自動化**。如果您要將虛擬機器容錯移轉至 Azure，與 Azure 自動化整合可擴充復原計畫，並讓您能夠執行 Runbook，進而允許進行功能強大的自動化工作。

如果您還沒聽過 Azure 自動化，請在[這裡](http://azure.microsoft.com/services/automation/)註冊，並在[這裡](http://azure.microsoft.com/documentation/scripts/)下載其範例指令碼。使用[這裡](http://azure.microsoft.com/blog/?p=166264)的復原計畫進一步了解 [Azure Site Recovery](http://azure.microsoft.com/services/site-recovery/)，以及如何將復原協調到 Azure。

在本教學課程中，我們將會探討如何將 Azure 自動化 Runbook 整合到復原計畫。我們會將先前需要手動介入的簡單工作自動化，並了解如何將多步驟復原轉換成單鍵復原動作。我們也將探討簡單的指令碼發生錯誤時如何進行疑難排解。

## 將應用程式保護到 Azure

讓我們從兩部虛擬機器所組成的簡單應用程式開始。在這裡，我們有 Fabrikam 的 HRweb 應用程式。Fabrikam-HRweb-frontend 和 Fabrikam-Hrweb-backend 是使用 Azure Site Recovery 保護到 Azure 的兩部虛擬機器。若要使用 Azure Site Recovery 保護虛擬機器，請遵循下列步驟進行。

1.  對虛擬機器啟用保護。

2.  請確定虛擬機器已經完成初始複寫，而且將要複寫。

3.  請等到初始複寫完成，且複寫狀態變成 [受保護]。

![](media/site-recovery-runbook-automation/01.png)
---------------------

在本教學課程中，我們將建立 Fabrikam HRweb 應用程式的復原計畫，以便將應用程式容錯移轉至 Azure。接著，我們會將它與將在容錯移轉的 Azure 虛擬機器上建立端點的 Runbook 整合，以便在連接埠 80 為網頁服務。

首先，讓我們為應用程式建立一個復原計畫。

## 建立復原計畫

若要將應用程式復原至 Azure，您必須建立一個復原計畫。您可以使用復原計畫指定虛擬機器的復原順序。放在群組 1 中的虛擬機器將會第一個復原並啟動，然後群組 2 中的虛擬機器將會接著進行。

建立一個如下的復原計畫。

![](media/site-recovery-runbook-automation/12.png)

若要深入了解復原計畫，請在[這裡](https://msdn.microsoft.com/library/azure/dn788799.aspx "開始")閱讀文件。

接下來，讓我們在 Azure 自動化中建立所需的構件。

## 建立自動化帳戶及其資產

您需要有 Azure 自動化帳戶才能建立 Runbook。如果您還沒有帳戶，請瀏覽至以 ![](media/site-recovery-runbook-automation/02.png) 表示的 [Azure 自動化] 索引標籤，然後建立一個新帳戶。

1.  為帳戶授與一個可識別的名稱。

2.  指定您要放置帳戶所在的地理區域。

建議您將帳戶放在與 ASR 保存庫相同的區域中。

![](media/site-recovery-runbook-automation/03.png)

接下來，在帳戶中建立下列資產。

### 將訂用帳戶名稱新增為資產

1.  在 Azure 自動化資產中加入新的設定 ![](media/site-recovery-runbook-automation/04.png)，然後選擇 ![](media/site-recovery-runbook-automation/05.png)

2.  將變數類型選取為 [字串]

3.  將變數名稱指定為 **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  將您實際的 Azure 訂用帳戶名稱指定為變數值。

	![](media/site-recovery-runbook-automation/07_1.png)

您可以從 Azure 入口網站上您帳戶的 [設定] 頁面識別您的訂用帳戶名稱。

### 將 Azure 登入認證新增為資產

Azure 自動化使用 Azure PowerShell 連線到訂用帳戶，並在該處的構件上運作。因此，您必須使用您的 Microsoft 帳戶或工作或學校帳戶進行驗證。您可以將帳戶認證儲存在 Runbook 可安全地使用的資產中。

1.  在 Azure 自動化資產中加入新的設定 ![](media/site-recovery-runbook-automation/04.png)，然後選取 ![](media/site-recovery-runbook-automation/09.png)

2.  將 [認證類型] 選取為 [Windows PowerShell 認證]

3.  將名稱指定為 **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  指定用於登入的使用者名稱和密碼。

現在，這兩個設定都可以在您的資產中使用。

![](media/site-recovery-runbook-automation/11.png)

[這裡](../install-configure-powershell.md)提供有關如何透過 PowerShell 連線到您的訂用帳戶的詳細資訊。

接下來，您將可以在 Azure 自動化中建立能夠在容錯移轉之後，為前端虛擬機器加入端點的 Runbook。

## Azure 自動化內容

ASR 將內容變數傳遞至 Runbook，以協助您撰寫具有決定性的指令碼。其中一個雲端主張雲端服務和虛擬機器的名稱是可預測的，但有時候不一定可以預測，例如，當虛擬機器名稱可能因為在 Azure 中不支援某些字元而變更時。因此，這項資訊會傳遞到 ASR 復原計畫，做為*內容*的一部分。

以下是內容變數外觀的範例。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


下表包含內容中每個變數的名稱和描述。
  
<table border="1"> <tr><th>變數名稱</th><th>變數描述</th></tr> <tr><td>RecoveryPlanName</td><td>正在執行的復原計畫的名稱。<p> 此變數可協助您使用相同的指令碼，根據復原計畫名稱採取不同的動作。</td></tr> <tr><td>FailoverType</td><td>指定執行是 [測試]、[計劃中] 還是 [非計劃中]。<p> 此變數可協助您根據容錯移轉類型，採取不同的動作。</td></tr> <tr><td>FailoverDirection</td><td>指定復原是從主要端到復原，或反之亦然。<p>它所採用的兩個值分別是 **PrimaryToSecondary** 和 **SecondaryToPrimary** </td></tr> <tr><td>GroupId</td><td> 會識別 Runbook 執行所在的復原計畫內的群組編號。<p> 例如，如果 Runbook 是 post group 2，則 GroupId 將會是 2。</td></tr> <tr><td>VmMap</td><td> 這是群組中所有虛擬機器的陣列。</td></tr> <tr><td>VmMap 索引鍵</td><td>每部虛擬機器都有一個由 GUID 識別的唯一索引鍵。此 GUID 與虛擬機器的 VMM ID 相同。<p> 您可以使用此 GUID，以決定性的方式指定您想要操作的虛擬機器。</td></tr> <tr><td>RoleName</td><td>這會指定要復原的 Azure 虛擬機器的名稱。</td></tr> <tr><td>CloudServiceName</td><td> 這會指定建立虛擬機器時所使用的 Azure 雲端服務名稱。</td></tr> </table><br />

若要識別內容中的 VmMap 索引鍵，您也可以移至 ASR 中的 [VM 屬性] 頁面，查看 VM GUID 屬性。

![](media/site-recovery-runbook-automation/13.png)

## 撰寫自動化 Runbook

現在建立 Runbook，以開放前端虛擬機器上的連接埠 80。

1.  在 Azure 自動化帳戶中，使用名稱 **OpenPort80** 建立新的 Runbook

![](media/site-recovery-runbook-automation/14.png)

2.  瀏覽至 Runbook 的 [撰寫] 檢視，然後進入草稿模式。

3.  首先，指定要當做復原計畫內容使用的變數

```
	param (
		[Object]$RecoveryPlanContext
	)

```
  

4.  接下來，使用認證和訂用帳戶名稱，連線到訂用帳戶

```
	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
```

> 請注意，您在這裡使用的是 Azure 的資產 – **AzureCredential** 和 **AzureSubscriptionName**。

5.  現在，指定端點詳細資料以及您要公開端點所在虛擬機器的 GUID，在這個案例中為前端虛擬機器。

```
	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
```

這會指定 Azure 端點通訊協定、VM 上的本機連接埠及其對應的公用連接埠。這些變數是將端點加入至 VM 的 Azure 命令所需的參數。VMGUID 保留您操作所需的虛擬機器的 GUID。

6.  此指令碼現在會針對給定的 VM GUID 擷取內容，並在所參考的虛擬機器上建立端點。

```
	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
```

7. 這項操作完成之後，按 [發佈 ![](media/site-recovery-runbook-automation/20.png)] 可讓您的指令碼可供執行。 

以下提供完整的指令碼供您參考

```
  workflow OpenPort80
  {
	param (
		[Object]$RecoveryPlanContext
	)

	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
	
	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
  }
```

## 將指令碼加入至復原計畫

一旦指令碼就緒之後，您就可以將它加入到您稍早建立的復原計畫。

1.  在您建立的復原計畫中，選擇將指令碼加入到群組 2 後面。![](media/site-recovery-runbook-automation/15.png)

2.  指定指令碼名稱。這只是此指令碼在復原計畫內用於顯示的易記名稱。

3.  在容錯移轉至 Azure 指令碼時 – 選取 Azure 自動化帳戶名稱。

4.  在 Azure Runbook 中，選取您所撰寫的 Runbook。

![](media/site-recovery-runbook-automation/16.png)

## 主要端指令碼

執行容錯移轉至 Azure 時，您也可以選擇執行主要端指令碼。這些指令碼將在容錯移轉期間於 VMM 伺服器上執行。主要端指令碼僅適用於關機前及關機後階段。這是因為我們預期在發生災害事件時，通常無法使用主要網站。在未規劃的容錯移轉期間，只有選擇主要網站作業時，才會嘗試執行主要端指令碼。如果它們無法連線或逾時，則容錯移轉會繼續復原虛擬機器。容錯移轉至 Azure 時，若未針對 Azure 執行 VMM 保護，就無法為 VMware/實體/Hyper-v 網站使用主要端指令碼。不過，當您從 Azure 容錯回復到內部部署時，主要端指令碼 (Runbooks) 可用於 VMware 以外的所有目標。

## 測試復原計畫

一旦您將 Runbook 加入至計畫之後，您可以起始一個測試容錯移轉，並觀看其運作。建議您一律執行測試容錯移轉來測試您的應用程式與復原計畫，以確保沒有任何錯誤。

1.  選取復原計畫，並起始測試容錯移轉。

2.  在執行計劃期間，您可以透過其狀態查看 Runbook 是否已執行。

    ![](media/site-recovery-runbook-automation/17.png)

3.  您也可以在 Runbook 的 [Azure 自動化工作] 頁面上查看詳細的 Runbook 執行狀態。

    ![](media/site-recovery-runbook-automation/18.png)

4.  完成容錯移轉之後，除了 Runbook 執行結果之後，您還可以瀏覽 Azure 虛擬機器頁面並查看端點，以了解查看執行是否成功。

![](media/site-recovery-runbook-automation/19.png)

## 範例指令碼

雖然我們在本教學課程中逐步說明如何將端點加入至 Azure 虛擬機器的其中一個常用工作自動化，但是您可以使用 Azure 自動化執行其他許多其他功能強大的自動化工作。Microsoft 和 Azure 自動化社群會提供範例 Runbook，協助您開始建立自己的解決方案和公用程式 Runbook，而且您可以將這些 Runbook 做當大型自動化工作的建置組塊使用。從資源庫開始使用這些 Runbook，並使用 Azure Site Recovery，為您的應用程式建置功能強大的單鍵復原計畫。

## 其他資源

[Azure 自動化概觀](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自動化概觀")

[Azure 自動化範例指令碼](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Azure 自動化範例指令碼")

 

<!---HONumber=July15_HO2-->