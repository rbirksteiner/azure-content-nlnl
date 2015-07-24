<properties 
   pageTitle="Azure 自動化中的憑證資產"
   description="憑證可以安全地儲存在 Azure 自動化中，使得 Runbook 可以存取憑證，以向 Azure 和協力廠商資源進行驗證。這篇文章說明憑證的詳細資料，以及如何以文字和圖形化編寫形式加以使用。"
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
   ms.date="06/14/2015"
   ms.author="bwren" />

# Azure 自動化中的憑證資產

憑證可以安全地儲存在 Azure 自動化中，使得 Runbook 可以透過使用 **Get-AutomationCertificate** 活動來存取憑證。這可讓您建立使用憑證進行驗證的 Runbook，或將它們加入至 Runbook 可能會建立或設定的 Azure 或協力廠商的資源。

>[AZURE.NOTE]Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。

## Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化憑證資產。它們是隨著 [Azure PowerShell 模組](../powershell-install-configure.md)的一部分推出，可供在自動化 Runbook 中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|擷取憑證的相關資訊。您只能從 Get-AutomationCertificate 活動擷取憑證本身。|
|[New-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|將新憑證匯入 Azure 自動化。|
|[Remove-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|從 Azure 自動化中移除憑證。|
|[Set-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|設定現有的憑證，包括上傳憑證檔案和設定 .pfx 的密碼屬性。|

## Runbook 活動

下表中的活動可用來在 Runbook 中存取憑證。

|活動|說明|
|:---|:---|
|Get-AutomationCertificate|取得要在 Runbook 中使用的憑證。|

>[AZURE.NOTE]您應該避免在 Get-AutomationCertificate 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 與憑證資產之間的相依性變得複雜。

## 建立新憑證

建立新憑證時，您會將 cer 或 pfx 檔案上傳到 Azure 自動化。如果您將憑證標示為可匯出，那麼您可以將它傳送到 Azure 自動化憑證存放區外部。如果無法匯出，則只可以將它用在 Runbook 內的簽署。

### 使用 Azure 入口網站建立新憑證

1. 從您的自動化帳戶，在視窗的頂端按一下 [**資產**]。
1. 在視窗的底端按一下 [**加入設定**]。
1. 按一下 [**加入認證**]。
2. 在 [**認證類型**] 下拉式清單中，選取 [**憑證**]。
3. 在 [**名稱**] 方塊中輸入憑證的名稱，然後按一下向右箭頭。
4. 瀏覽 .cer 或 .pfx 檔案。如果您選取 .pfx 檔案，請指定密碼，以及是否應該允許匯出它。
1. 按一下核取記號來上傳憑證檔案，並儲存新的憑證資產。


### 使用 Azure 預覽入口網站建立新憑證

1. 從您的自動化帳戶，按一下 [**資產**] 部分，以開啟 [**資產**] 分頁。
1. 按一下 [**憑證**] 部分，以開啟 [**憑證**] 分頁。
1. 在分頁的頂端按一下 [**加入憑證**]。
2. 在 [**名稱**] 方塊中輸入憑證的名稱。
2. 在 [**上傳憑證檔案**] 下按一下 [**選取檔案**]，以瀏覽 .cer 或 .pfx 檔案。如果您選取 .pfx 檔案，請指定密碼，以及是否應該允許匯出它。
1. 按一下 [**建立**] 以儲存新的憑證資產。


### 使用 Windows PowerShell 建立新憑證

下列範例命令顯示如何建立新的自動化憑證，並將其標示為可匯出。這樣會匯入現有的 pfx 檔案。

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## 在 Runbook 中使用憑證

您必須使用 **Get-AutomationCertificate** 活動在 Runbook 中使用憑證。您不能使用 [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) Cmdlet，因為它會傳回憑證資產的相關資訊，而不是憑證本身。

### 文字式 Runbook 範例

下列範例程式碼示範如何將憑證新增到 Runbook 中的雲端服務。在此範例中，密碼是擷取自加密的自動化變數。

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### 圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的憑證上按一下滑鼠右鍵，然後選取 [**加入至畫布**]，即可加入 **Get-AutomationCerticiate** 至圖形化 Runbook。

![](media/automation-certificates/certificate-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。這是如上所示，用於從文字式 Runbook 加入憑證至雲端服務的相同範例。

這個範例會對使用連接物件向服務驗證的 Send-**TwilioSMS 活動** 使用 **UseConnectionObject** 參數集。必須在這裡使用[管線連結](automation-graphical-authoring-intro.md#links-and-workflow)，因為順序連結將會傳回一個集合，其中包含 Connection 參數未預期的單一物件。

![](media/automation-certificates/add-certificate.png)


## 另請參閱

- [圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=62-->