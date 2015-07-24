<properties 
   pageTitle="Azure 自動化中的變數資產"
   description="變數資產是可用於 Azure 自動化中所有 Runbook 的值。這篇文章說明變數的詳細資料，以及如何以文字式和圖形化編寫形式加以使用。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/21/2015"
   ms.author="bwren" />

# Azure 自動化中的變數資產

變數資產是可用於自動化帳戶中所有 Runbook 的值。可以從 Azure 入口網站、Windows PowerShell 和 Runbook 建立修改並擷取。自動化變數對下列案例很實用：

- 在多個 Runbook 之間共用值。

- 在來自相同 Runbook 的多個工作之間共用值。

- 從入口網站或 Windows PowerShell 命令列管理 Runbook 使用的值。

自動化變數會保存，即使 Runbook 失敗也可持續使用變數。這也可讓一個 Runbook 設定某個值，然後由另一個 Runbook 使用，或者由相同的 Runbook 於下次執行時使用該值。

建立變數時，您可以指定將其加密儲存。變數加密時，會安全地儲存在 Azure 自動化中，而無法透過隨附於 Azure PowerShell 模組的 [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) Cmdlet 擷取其值。可以擷取加密值的唯一方法是從 Runbook 中的 **Get-AutomationVariable** 活動。

>[AZURE.NOTE]Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。

## 變數型別

使用 Azure 入口網站建立變數時，您必須從下拉式清單中指定資料型別，使得入口網站可以對輸入變數的值顯示適當的控制項。變數不會受限於這個資料型別，但如果您想要指定不同型別的值，您必須使用 Windows PowerShell 來設定變數。如果您指定**未定義**，則變數的值會設為 **$null**，而且您必須使用 [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) Cmdlet 或 **Set-AutomationVariable** 活動設定該值。您無法在入口網站中建立或變更複雜變數型別的值，但是您可以使用 Windows PowerShell 提供任何型別的值。複雜型別會傳回為 [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx)。

您可以藉由建立陣列或雜湊表，並將它儲存到變數中，來儲存多個值到單一變數。

## Cmdlet 和工作流程活動

下表中的 Cmdlet 是用來使用 Windows PowerShell 建立和管理自動化變數。它們是隨著 [Azure PowerShell 模組](../powershell-install-configure.md)的一部分推出，可供在自動化 Runbook 中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|擷取現有變數的值。|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|建立新的變數並設定其值。|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|移除現有的變數。|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|設定現有的變數的值。|

下表中的工作流程活動可用來在 Runbook 中存取自動化變數。它們僅供在 Runbook 中使用，並且不會隨著 Azure PowerShell 模組的一部分推出。

|工作流程活動|說明|
|:---|:---|
|Get-AutomationVariable|擷取現有變數的值。|
|Set-AutomationVariable|設定現有的變數的值。|

>[AZURE.NOTE]您應該避免在 Runbook 中 **Get-AutomationVariable** 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 與自動化變數之間的相依性變得複雜。

## 建立新自動化變數

### 使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，在視窗的頂端按一下 [**資產**]。
1. 在視窗的底端按一下 [**加入設定**]。
1. 按一下 [**加入變數**]。
1. 完成精靈，然後按一下核取方塊以儲存新變數。


### 使用 Azure 預覽入口網站建立新變數

1. 從您的自動化帳戶，按一下 [**資產**] 部分，以開啟 [**資產**] 分頁。
1. 按一下 [**變數**] 部分，以開啟 [**變數**] 分頁。
1. 在分頁的頂端按一下 [**加入變數**]。
1. 完成表單，然後一下 [**建立**] 以儲存新變數。


### 使用 Windows PowerShell 建立新變數

[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) Cmdlet 會建立新變數，並設定其初始值。您可以使用 [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) 來擷取值。如果值為簡單型別，則會傳回該相同的型別。如果它是複雜型別，則會傳回 **PSCustomObject**。

下列範例命令顯示如何建立字串型別的變數，然後傳回它的值。


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

下列範例命令顯示如何建立具有複雜型別的變數，然後傳回其屬性。在此情況下，會使用來自 **Get-AzureVM** 的虛擬機器物件。

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## 在 Runbook 中使用變數

使用 **Set-AutomationVariable** 活動來設定 Runbook 中自動化變數的值，並且使用 **Get-AutomationVariable** 來擷取它。您不應該在 Runbook 中使用 **Set-AzureAutomationVariable** 或 **Get-AzureAutomationVariable**，因為它們都不如工作流程活動中的 Cmdlet 有效率。您也無法使用 **Get-AzureAutomationVariable** 擷取安全變數的值。在 Runbook 中建立新變數的唯一方法是使用 [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) Cmdlet。


### 文字式 Runbook 範例

#### 從變數設定和擷取簡單的值

下列範例命令顯示如何設定及擷取文字式 Runbook 中的變數。在此範例中，假設已經建立名稱為 *NumberOfIterations* 和 *NumberOfRunnings* 的整數型別變數，以及名稱為 *SampleMessage* 字串型別變數。

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### 設定和擷取變數中的複雜物件

下列範例程式碼顯示如何在文字式Runbook 中使用複雜值來更新變數。在此範例中，會使用 **Get-AzureVM** 擷取 Azure 虛擬機器並儲存至現有的自動化變數。如[變數型別](#variable-types)中所述，這會儲存為 PSCustomObject。

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


在下列程式碼中，值是從變數擷取，且用來啟動虛擬機器。

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### 設定和擷取變數中的集合

下列範例程式碼顯示如何在文字式Runbook 中使用變數搭配複雜值的集合。在此範例中，會使用 **Get-AzureVM** 擷取多部 Azure 虛擬機器並儲存至現有的自動化變數。如[變數型別](#variable-types)中所述，這會儲存為 PSCustomObject 的集合。

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

在下列程式碼中，集合是從變數擷取，且用來啟動每部虛擬機器。

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### 圖形化 Runbook 範例

在圖形化 Runbook 中，您會加入 **Get-AutomationVariable** 或 **Set-AutomationVariable**，方法是在圖形化編輯器的 [文件庫] 窗格中的變數上按一下滑鼠右鍵，然後選取您想要的活動。

![加入變數至畫布](media/automation-variables/variable-add-canvas.png)

#### 變數中的設定值

下圖顯示的範例活動會在圖形化 Runbook 中使用簡單值更新變數。在此範例中，會使用 **Get-AzureVM** 擷取單一 Azure 虛擬機器，而電腦名稱會儲存至現有的自動化變數，其型別為字串。[連結是管線或順序](automation-graphical-authoring-intro.md#links-and-workflow)都沒關係，因為我們在輸出中只預期單一物件。

![設定簡單變數](media/automation-variables/set-simple-variable.png)

下圖顯示用來在圖形化 Runbook 中以複雜值更新變數的活動。與上一個範例的唯一的變更是在 **Set-AutomationVariable** 活動上不指定**活動輸出**的**欄位路徑**，如此可儲存此物件，而不只是物件的屬性。如[變數型別](#variable-types)中所述，這會儲存為 PSCustomObject。

![設定複雜變數](media/automation-variables/set-complex-variable.png)

下圖顯示與上述範例類似的功能，其中多個虛擬機器儲存到變數。必須在這裡使用[順序連結](automation-graphical-authoring-intro.md#links-and-workflow)，讓 **Set-AutomationVariable** 活動接收整組的虛擬機器做為一個集合。如果使用了[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)，則 **Set-AutomationVariable** 活動會分別針對具有結果的每個物件執行，使得只會儲存集合中的最後一個虛擬機器。如[變數型別](#variable-types)中所述，這會儲存為 PSCustomObject 的集合。

![設定複雜集合變數](media/automation-variables/set-complex-variable-collection.png)

#### 從變數擷取值

下圖顯示會在圖形化 Runbook 中擷取並使用變數的範例活動。第一個活動會擷取儲存至前一個範例中的變數的虛擬機器。連結必須是[管線](automation-graphical-authoring-intro.md#links-and-workflow)，使得 **Start-AzureVM** 活動會對傳送自 **Get-AutomationVariable** 活動的每個物件執行一次。無論單一物件或多個物件是否儲存在變數中，運作方式皆相同。 **Start-AzureVM** 活動會使用代表每個虛擬機器 PSCustomObject 的屬性。

![取得複雜變數](media/automation-variables/get-complex-variable.png)

下圖顯示如何篩選儲存至圖形化 Runbook 中變數的物件。[條件](automation-graphical-authoring-intro.md#links-and-workflow)會加入至上一個範例中的連結，以僅篩選設定變數時停止的虛擬機器。

![篩選複雜的變數](media/automation-variables/get-complex-variable-filter.png)


## 相關文章

- [圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=62-->