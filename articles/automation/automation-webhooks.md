<properties 
   pageTitle="Azure 自動化 Webhook"
   description="可讓用戶端透過 HTTP 呼叫在 Azure 自動化中啟動 Runbook 的 Webhook。本文說明如何建立 Webhook，以及如何進行呼叫以啟動 Runbook。"
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
   ms.date="06/03/2015"
   ms.author="bwren" />

# Azure 自動化 Webhook

*Webhook* 可讓您在 Azure 自動化中透過單一 HTTP 要求啟動特定的 Runbook。這可讓外部服務，例如 Visual Studio Online、 GitHub 或自訂應用程式啟動 Runbook，而不需使用 Azure 自動化 API 實作完整的解決方案。

![Webhook](media/automation-webhooks/webhooks-overview.png)

您可以透過[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 中比較 Webhook 與其他啟動 Runbook 的方法

## Webhook 的詳細資料

下表描述您必須為 Webhook 設定的屬性。

| 屬性 | 說明 |
|:---|:---|
|名稱 | 您可以為 Webhook 提供任何想要的名稱，因為這並不會向用戶端公開。該名稱僅供您用來識別 Azure 自動化中的 Runbook。<br> 最佳做法是您給予 Webhook 的名稱應該與會使用其的用戶端相關。 |
|URL |Webhook 的 URL 是一種唯一性的位址，即用戶端用來呼叫 HTTP POST 以啟動連結至 Webhook的 Runbook。當您建立 Webhook 時其會自動產生。您無法指定自訂 URL。<br> <br> URL 包含可讓協力廠商系統不需進一步驗證即可叫用 Runbook 的安全性權杖。基於這個原因，應該將其視為一種密碼。基於安全性原因，您僅能於建立 Webhook 時在 Azure preview 入口網站中檢視 URL。您應該在安全的位置記下 URL 以供日後使用。 |
|到期日期 | 例如憑證，每個 Webhook 都會有一個到期日期，到期後便無法再使用。建立 Webhook 後無法變更此到期日期，且 Webhook 也無法於到期日期後再次啟用。在此情況下，您必須建立另一個 Webhook 以取代目前的 Webhook，並更新用戶端以使用新的 Webhook。 |
| 已啟用 | 建立 Runbook 時 Webhook 會預設為啟用。如果您將其設定為 [停用]，則任何用戶端皆無法使用。當您建立 Webhook 時或在建立後的任何時候，您可以設定 [**啟用**] 屬性。 |


### 參數
Webhook 可以定義由該 Webhook 啟動 Runbook 時所使用的 Runbook 參數值。Webhook 必須包含任何 Runbook 的強制參數值，且可能包含選擇性的參數值。多個 Webhook 連結至單一 Runbook 時，可以個別使用不同的參數值。

>[AZURE.NOTE]建立 Webhook 之後，即無法變更目前 Webhook 所設定的參數值。您必須建立使用不同參數值的其他 Webhook。

當用戶端使用 Webhook 啟動 Runbook 時，其無法覆寫 Webhook 中定義的參數值。若要從用戶端接收資料，Runbook 可以接受單一參數，稱之為 **$WebhookData** 的 [物件] 類型，其中包含用戶端在 POST 要求中所包含的資料。

![Webhookdata](media/automation-webhooks/webhookdata.png)

**$WebhookData** 物件具有下列屬性：

| 屬性 | 說明 |
|:--- |:---|
| WebhookName | Webhook 的名稱。 |
| RequestHeader | 雜湊表包含傳入 POST 要求的標頭。 |
| RequestBody | 傳入 POST 要求的本文。這會保留任何的格式，如字串、JSON、XML，或表單已編碼的資料。Runbook 必須撰寫用來搭配預期的資料格式。|


支援 **$WebhookData** 參數不需要 Webhook 的組態，且 Runbook 不需要接受其。若 Runbook 並未定義參數，則會忽略從用戶端所傳送要求的任何詳細資料。

若您在建立 Webhook 時指定 $WebhookData 的值，即使用戶端在要求本文中並未包含任何資料，該值將會在 Webhook 使用用戶端 POST 要求的資料來啟動 Runbook 時遭到覆寫。若您使用 Webhook 以外的方式啟動具有 $WebhookData 的 Runbook，您可以提供可讓 Runbook 識別的 $WebhookData 值。此值應為與 $WebhookData 具有相同屬性的物件，以便 Runbook 可以使用其進行正確的工作。

>[AZURE.NOTE]所有輸入參數的值會使用 Runbook 工作進行記錄。這表示，任何由用戶端在 Webhook 要求中提供的輸入將會進行記錄，並可讓任何有自動化工作存取權的人使用。基於這個原因，您在 Webhook 呼叫中包含機密資訊時應該特別謹慎。

## 安全性

Webhook 的安全性仰賴其 URL 的隱私權，其中包含可允許其接受叫用的安全性權杖。只要針對正確的 URL 進行要求，Azure 自動化就不會對要求執行任何驗證。基於這個原因，若不使用驗證要求的替代方式時，Webhooks 不應用於執行高度機密功能的 Runbook。

您可以透過檢查 $WebhookData 參數的 **WebhookName** 屬性，在 Runbook 中包含邏輯來判斷其是否由 Webhook 所呼叫。Runbook 可以透過尋找 **RequestHeader** 或 **RequestBody** 屬性中的特定資訊來執行進一步的驗證。

另一個策略是當 Runbook 收到 Webhook 要求時，使其執行某些外部條件的驗證。例如，當 GitHub 儲存機制出現新的認可時，考慮使用由 GitHub 呼叫的 Runbook。Runbook 可能會連接到 GitHub 以驗證在繼續之前實際上已出現新的認可。

## 建立 Webhook

使用下列程序以在 Azure Preview 入口網站中建立連結至 Runbook 的新 Webhook。

1. 在 Azure Preview 入口網站中的 **Runbook 刀鋒視窗**，按一下 [Runbook]，Webhook 便會開始檢視其詳細資料刀鋒視窗。 
3. 按一下刀鋒視窗頂端的 [**Webhook**] 以開啟 [**新增 Webhook**] 刀鋒視窗。<br> ![Webhook 按鈕](media/automation-webhooks/webhooks-button.png)
4. 按一下 [**建立新的 Webhook**] 以開啟 [**建立 Webhook 刀鋒視窗**]。
5. 指定 Webhook 的 [**名稱**]、[**到期日期**] 以及是否應該啟用。請參閱 [Webhook 的詳細資料](#details-of-a-webhook)以取得這些屬性的詳細資訊。
6. 按一下複製圖示，然後按 Ctrl + C 以複製 Webhook 的 URL。然後將其記錄在安全的地方。**一旦您建立 Webhook，即無法再次擷取 URL。** <br> ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
3. 按一下 [參數] 來提供 Runbook 的參數值。如果 Runbook 有強制參數，除非提供該值，否則您將無法建立 Webhook。
1. 按一下 [**建立**] 來建立 Webhook。


## 使用 Webhook

建立 Webhook 之後若要使用它，您的用戶端應用程式必須使用 Webhook 的 URL 來發出 HTTP POST。Webhook 的語法將遵循以下列格式。

	http://<Webhook Server>/token?=<Token Value>

用戶端將會從 POST 要求中接收下列其中一個傳回代碼。

| 代碼 | 文字 | 說明 |
|:---|:----|:---|
| 202 | 已接受 | 已接受要求，且 Runbook 已經成功排入佇列。 |
| 400 | 不正確的要求 | 基於下列原因之一不接受此要求。<ul> <li>Webhook 已過期。</li> <li>Webhook 已停用。</li> <li>在 URL 中的權杖無效。</li> </ul>|
| 500 | 內部伺服器錯誤 | URL 有效，但發生錯誤。請重新提交要求。 |

假設要求成功，Webhook 會回應包含 JSON 格式的工作識別碼，如下所示。它會包含單一的工作識別碼，但是 JSON 格式允許未來可能的增強功能。

	{"JobIds":["<JobId>"]}  

用戶端無法從 Webhook 判斷 Runbook 的工作何時完成或完成狀態。這項資訊可使用工作識別碼搭配其他方法 (例如 [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) 或 [Azure 自動化 API](https://msdn.microsoft.com/library/azure/mt163826.aspx)) 來判斷。

### 範例

下列範例使用 Windows PowerShell 搭配 Webhook 來啟動 Runbook。請注意，任何可以進行 HTTP 要求的語言都可以使用 Webhook；Windows PowerShell 在這裡僅作為範例使用。

Runbook 預期在要求的主體中有 JSON 格式的虛擬機器清單。我們也在要求的標頭中，列入了有關於啟動 Runbook 的人員、啟動的日期和時間資訊。

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


下圖顯示來自要求的標頭資訊 (使用 [Fiddler](http://www.telerik.com/fiddler) 追蹤)。這包括了 HTTP 要求的標準標頭，除此之外，還有我們加入的自訂「日期」和「時間」標頭。這些值都可在 **WebhookData** 的**RequestHeaders** 屬性中提供給 Runbook 使用。

![Webhook 按鈕](media/automation-webhooks/webhook-request-headers.png)

下圖顯示要求的主體 (使用 [Fiddler](http://www.telerik.com/fiddler) 追蹤) 可在 **WebhookData** 的 **RequestBody** 屬性中提供給 Runbook 使用。格式化為 JSON 是因為其格式是要求的主體中所包含之格式。

![Webhook 按鈕](media/automation-webhooks/webhook-request-body.png)

下圖顯示由 Windows PowerShell 送出的要求以及產生的回應。工作識別碼從回應中擷取，再轉換為字串。

![Webhook 按鈕](media/automation-webhooks/webhook-request-response.png)

下列範例 Runbook 接受之前的範例要求，並啟動要求主體中指定的虛擬機器。

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = (ConvertFrom-Json -InputObject $WebhookBody).VirtualMachines
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				Write-Output "Starting $VM.Name."
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}

	

## 相關文章

- [啟動 Runbook](automation-starting-a-runbook.md)
- [檢視 Runbook 工作的狀態](automation-viewing-the-status-of-a-runbook-job.md) 

<!---HONumber=62-->