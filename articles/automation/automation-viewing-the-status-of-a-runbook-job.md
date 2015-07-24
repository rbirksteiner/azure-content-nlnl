<properties 
   pageTitle="在 Azure 自動化中檢視 Runbook 工作的狀態"
   description="當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。本文提供如何追蹤每個工作和檢視其目前狀態的資訊。"
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

# 在 Azure 自動化中檢視 Runbook 工作的狀態


當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。工作是 Runbook 的單一執行執行個體。單一 Runbook 可能會有多個工作，各有自己的 Runbook 參數值集合。您可以針對一個或多個 Runbook，使用多種方式來檢查特定一項工作和所有工作的狀態。

## 工作狀態

下表描述工作可能會有不同的狀態。

| Status| 說明|
|:---|:---|
|Completed|工作已成功完成。|
|Failed|工作結束時發生錯誤。|
|處理失敗，正在等候資源|工作失敗，因為其達到[公平共用](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare)的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。|
|已排入佇列|工作正在等候取得自動化背景工作中的資源，以便可啟動。|
|啟動中|工作已指派給背景工作，並且系統正在進行啟動。|
|繼續中|工作暫停後，系統正在繼續工作。|
|執行中|工作正在執行。|
|執行中，正在等候資源|工作已卸載，因為已達到 [公平共用](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare)上限。工作會很快地從其上一個檢查點繼續。|
|已停止|工作完成之前已由使用者停止。|
|停止中|系統正在停止工作。|
|暫止|工作已由使用者、系統或 Runbook 中的命令暫停。已暫停的工作可以再次啟動，並從其上一個檢查點，或從 Runbook 的開頭繼續 (若無檢查點)。僅在例外狀況時，系統會暫停 Runbook。根據預設，ErrorActionPreference 會設定為 [**繼續**]，代表工作會在發生錯誤時繼續執行。如果此喜好設定變數設定為 [**停止**]，則工作將會在發生錯誤時暫停。|
|暫停中|因使用者要求，系統正在嘗試暫停工作。Runbook 必須達到其下一個檢查點才能暫停。如果其已通過其上一個檢查點，則在可以暫停之前會先完成工作。|

## 使用 Azure 管理入口網站來檢視工作狀態

### 自動化儀表板

自動化儀表板會針對特定的自動化帳戶顯示所有 Runbook 的摘要。其也包含帳戶的使用量概觀。針對經過指定的天數或小時數輸入每個狀態的所有 Runbook，摘要圖表會顯示其工作總數。您可以在圖表的右上角選取時間範圍。圖表的時間軸會根據您選取的時間範圍類型進行變更。您可以選擇是否要在螢幕頂端按一下以顯示特定狀態的列。

您可以使用下列步驟來顯示自動化儀表板。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 選取 [**儀表板**] 索引標籤。

### Runbook 儀表板

Runbook 儀表板會顯示單一 Runbook 的摘要。針對經過指定的天數或小時數輸入每個狀態的 Runbook，摘要圖表會顯示其工作總數。您可以在圖表的右上角選取時間範圍。圖表的時間軸會根據您選取的時間範圍類型進行變更。您可以選擇是否要在螢幕頂端按一下以顯示特定狀態的列。

您可以使用下列步驟來顯示 Runbook 儀表板。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 按一下 Runbook 的名稱。
1. 選取 [**儀表板**] 索引標籤。

### 工作摘要

您可以針對特定 Runbook 及其最新狀態，檢視所有已建立工作的清單。您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。按一下工作的名稱以檢視其詳細的資訊及其輸出。工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 按一下 Runbook 的名稱。
1. 選取 [**工作**] 索引標籤。
1. 按一下工作的 [**已建立工作**] 資料行以檢視其詳細資料和輸出。

## 使用 Windows PowerShell 擷取工作狀態

您可以使用 [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) 擷取針對 Runbook 建立的工作以及特定工作的詳細資料。如果您使用 [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) 並透過 Windows PowerShell 來啟動 Runbook，則其會傳回產生的工作。使用 [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) 輸出以取得工作的輸出。

下列範例命令會針對範例 Runbook 擷取上一個工作並顯示其狀態，該值會提供給 Runbook 參數，以及來自工作的輸出。

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 相關文章

- [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 

<!---HONumber=62-->