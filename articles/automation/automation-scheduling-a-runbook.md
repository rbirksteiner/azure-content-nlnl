<properties 
   pageTitle="在 Azure 自動化中排程 Runbook"
   description="描述如何在 Azure 自動化中建立排程，以便以特定時間或循環排程來自動啟動 Runbook。"
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
   ms.date="03/30/2015"
   ms.author="bwren" />

# 在 Azure 自動化中排程 Runbook

若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。排程可以設定為執行一次或在每個指定的小時數或天數重複執行。Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

## 建立排程

您可以使用 Azure 管理入口網站或使用 Windows PowerShell 建立新的排程。當您使用 Azure 管理入口網站將 Runbook 連結至排程時，您也可以選擇建立新的排程。

### 使用 Azure 管理入口網站建立新排程

1. 在 Azure 管理入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
1. 選取 [**資產**] 索引標籤。
1. 在視窗的底端按一下 [**加入設定**]。
1. 按一下 [**加入排程**]。
1. 輸入 [**名稱**] 並選擇性地輸入新排程的 [**描述**]。
1. 選取排程將執行 [**一次**]、[**每小時**] 或 [**每日**]。
1. 視您選取的排程類型而定，指定 [**開始時間**] 和其他選項。

### 使用 Windows PowerShell 建立新排程

您可以使用 [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) Cmdlet，在 Azure 自動化中建立新排程。您必須指定排程的開始時間，以及是要執行一次、每小時或每日。

下列範例命令顯示如何建立新的排程，排程在 2015 年 1 月 20 日開始每日下午 3:30 執行。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## 將排程連結至 Runbook

Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。如果 Runbook 有參數，您可以為它們提供值。您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。每次此排程啟動 Runbook 時將會使用這些值。您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### 使用 Azure 管理入口網站將排程連結至 Runbook

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 選取 [**Runbook**] 索引標籤。
1. 按一下要排程的 Runbook 名稱。
1. 按一下 [**排程**] 索引標籤。
2. 如果 Runbook 目前未連結至排程，則將提供您 [**連結至新排程**] 或 [**連結至現有排程**] 選項。如果 Runbook 目前連結至排程，請按一下視窗底部的**連結**來存取這些選項。
1. 如果 Runbook 有參數，系統會提示您輸入它們的值。  

### 使用 Windows PowerShell 將排程連結至 Runbook

您可以使用 [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) 將排程連結至 Runbook。您可以使用 Parameters 參數來指定 Runbook 參數的值。請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)，以取得指定參數值的詳細資訊。

下列範例命令會顯示如何將排程連結至具有參數的 Runbook。

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## 停用排程

停用排程時，與其連結的任何 Runbook 將無法再依該排程執行。您可以手動停用排程或在建立排程時，將到期時間設定為每小時和每日排程。當到達到期時間時，就會停用排程。

### 使用 Azure 管理入口網站停用排程

您可以在 Azure 管理入口網站中從排程的排程詳細資料頁面停用排程。

1. 在 Azure 管理入口網站中，選取 [自動化]，然後按一下自動化帳戶的名稱。
1. 選取 [資產] 索引標籤。
1. 按一下排程的名稱以開啟其詳細資料頁面。
2. 將 [**已啟用**] 變更為 [**否**]。

### 使用 Windows PowerShell 停用排程

您可以使用 [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) Cmdlet 來變更現有排程的屬性。若要停用排程，請對 **IsEnabled** 參數指定 **false**。

下列範例命令顯示如何停用排程。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## 相關文章

- [Azure 自動化中的排程資產](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 

<!---HONumber=62-->