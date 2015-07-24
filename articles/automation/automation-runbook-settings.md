<properties 
   pageTitle="Runbook 設定"
   description="描述 Azure 自動化中 Runbook 的組態設定，以及如何使用 Azure 管理入口網站和 Windows PowerShell 來加以變更。"
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Runbook 設定

Azure 自動化中的每個 Runbook 具備多個有助於識別其本身及變更其記錄行為的設定。以下會說明這些設定，後面則是如何加以修改的程序。

## Settings

### 名稱和描述

建立 Runbook 之後，您即無法變更其名稱。描述是選擇性的，而且最多可以是 512 個字元。

### 標記

標記可讓您指派不同的單字和片語，有助於識別 Runbook。例如，提交 Runbook 到 [Runbook 資源庫](https://msdn.microsoft.com/library/dn781422.aspx)時，您會指定特定的標記來識別應該列出此 Runbook 的分類。您可以為 Runbook 指定多個標記，使用逗號分隔。

### 記錄

依預設，詳細資訊和進度記錄不會寫入工作歷程記錄。您可以變更特定 Runbook 的設定來記錄這些記錄。如需有關這些記錄的詳細資訊，請參閱 [Runbook 輸出和訊息](https://msdn.microsoft.com/library/dn879148.aspx)。

## 變更 Runbook 設定

### 使用 Azure 管理入口網站變更 Runbook 設定

您可以在 Azure 管理入口網站中 Runbook 的 [**設定**] 頁面變更 Runbook 的設定。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 選取 [**Runbook**] 索引標籤。
1. 按一下 Runbook 的名稱。
1. 選取 [設定] 索引標籤。

### 使用 Windows PowerShell 變更 Runbook 設定

您可以使用 [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) Cmdlet 來變更 Runbook 的設定。如果您想要指定多個標記，可以對標記參數提供陣列或單一字串 (使用逗號分隔值)。您可以使用 [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx) 取得目前的標記。

下列命令範例示範如何設定 Runbook 的屬性。此範例會將三個標記加入至現有的標記，並指定應該記錄詳細記錄。

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## 相關文章
- [Runbook 輸出和訊息](../automation-runbook-output-and-messages) 
- [建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=62-->