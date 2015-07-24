<properties 
   pageTitle="Azure 自動化中的認證資產"
   description="Azure 自動化中的認證資產包含可用來驗證由 Runbook 存取資源的安全性認證。本文說明如何建立認證資產和在 Runbook 中使用它們。"
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

# Azure 自動化中的認證資產

自動化認證資產會保存 [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 物件，其中包含安全性認證，例如使用者名稱和密碼。Runbook 可以使用可接受 PSCredential 物件進行驗證的 Cmdlet，否則 Runbook可能會擷取 PSCredential 物件的使用者名稱和密碼，以對需要驗證的一些應用程式或服務提供。認證的屬性會安全地儲存在 Azure 自動化中，並且可在 Runbook 中透過 [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 活動存取。

>[AZURE.NOTE]Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。

## Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化認證資產。它們是隨著 [Azure PowerShell 模組](../powershell-install-configure.md)的一部分推出，可供在自動化 Runbook 中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|擷取認證資產的相關資訊。您只能從 **Get-AutomationCredential** 活動擷取認證本身。|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|建立新的自動化認證。|
|[Remove-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|移除自動化認證。|
|[Set-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|設定現有自動化認證的屬性。|

## Runbook 活動

下表中的活動用來存取中 Runbook 的認證。

|活動|說明|
|:---|:---|
|Get-AutomationPSCredential|取得要在 Runbook 中使用的認證。傳回 [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 物件。|

>[AZURE.NOTE]您應該避免在 Get-AutomationPSCredential 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 與認證資產之間的相依性變得複雜。

## 建立新認證


### 使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，在視窗的頂端按一下 [**資產**]。
1. 在視窗的底端按一下 [**加入設定**]。
1. 按一下 [**加入認證**]。
2. 在 [**認證類型**] 下拉式清單中，選取 [**PowerShell 認證**]。
1. 完成精靈，然後按一下核取方塊以儲存新認證。


### 使用 Azure 預覽入口網站建立新認證

1. 從您的自動化帳戶，按一下 [**資產**] 部分，以開啟 [**資產**] 分頁。
1. 按一下 [**認證**] 部分，以開啟 [**認證**] 分頁。
1. 在分頁的頂端按一下 [**加入認證**]。
1. 完成表單，然後按一下 [**建立**] 以儲存新認證。


### 使用 Windows PowerShell 建立新的 PowerShell 認證

下列範例命令顯示如何建立新的自動化認證。PSCredential 物件是先建立了名稱和密碼，然後用來建立認證資產。您也可以使用 **Get-Credential** Cmdlet 來提示您輸入名稱和密碼。

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## 在 Runbook 中使用 PowerShell 認證

您會使用 **Get-AutomationConnection** 活動在 Runbook 中擷取認證資產。這會傳回 [PSCredential 物件](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx)，您可以對需要 PSCredential 參數的活動或 Cmdlet 搭配使用此物件。您也可以擷取要個別使用的認證物件的屬性。物件具備使用者名稱和安全密碼的屬性，或是您可以使用 **GetNetworkCredential** 方法來傳回將提供不安全版本的密碼的 [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) 物件。

### 文字式 Runbook 範例

下列範例命令顯示如何在 Runbook 中使用 PowerShell 認證。在此範例中，會擷取認證及指派給變數的使用者名稱和密碼。

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### 圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的認證上按一下滑鼠右鍵，然後選取 [**加入至畫布**]，即可加入 **Get-AutomationPSCredential** 活動至圖形化 Runbook。


![加入認證至畫布](media/automation-credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。在此情況下，它是用來向 Azure 資源提供 Runbook 的驗證，如[設定對 Azure 資源的驗證](#automation-configuring.md)中所述。第一個活動會擷取可存取 Azure 訂用帳戶的認證。然後 **Add-AzureAccount** 活動會使用這個認證來為隨後的任何活動提供驗證。因為 **Get-AutomationPSCredential** 需要單一物件，因此推出了[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)。

![加入認證至畫布](media/automation-credentials/get-credential.png)


## 相關文章

- [圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)

 

<!---HONumber=62-->