<properties 
	pageTitle="使用 PowerShell | Azure 的 Stream Analytics 監視和管理作業" 
	description="了解如何使用 Azure PowerShell Cmdlet 監視和管理串流分析工作" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# 使用 Azure PowerShell 監視和管理串流分析工作

了解如何使用 Azure PowerShell 管理 Azure 串流分析資源。

## 執行 Azure PowerShell Cmdlet 進行串流分析的必要條件

1.	安裝並設定 Azure PowerShell。

	按照[如何安裝並設定 Azure PowerShell][powershell-install] 的指示安裝 Azure PowerShell。

	若要使用 Azure Active Directory 方法連線到 Azure 訂用帳戶：

		Add-AzureAccount

	若要選取已啟用 Azure 串流分析服務的 Azure 訂用帳戶：

		Select-AzureSubscription

	>[AZURE.NOTE]下列錯誤訊息指出該訂用帳戶並未啟用 Azure 串流服務：
	>
		Error Code: InvalidResourceType.  Error Message: The resource type 'streamingjobs' could not be found in the namespace 'Microsoft.StreamAnalytics'.  
	
	>若要解決這個問題，請在訂用帳戶啟用串流分析預覽，然後執行下列 Cmdlet 以切換訂用帳戶：
	>
		Select-AzureSubscription –SubscriptionId xxxxxxxx

2.	設定 Azure 模式。

	安裝 Azure PowerShell 之後，請執行 [Switch-AzureMode][msdn-switch-azuremode] Cmdlet 設定適當的 Azure 模式來存取串流分析的各種 Cmdlet：

		Switch-AzureMode AzureResourceManager

>[AZURE.NOTE]透過 Azure PowerShell 建立的 Stream Analytics 作業有一項未啟用監視的暫時限制。若要解決這個問題，請瀏覽至 Azure 入口網站中工作的 [監視]**** 頁面，然後按一下 [啟用]**** 按鈕。

## 用於串流分析的 Azure PowerShell Cmdlet
下表列出可用來監視和管理 Azure 串流分析工作的 Azure PowerShell Cmdlet。

### Get-AzureStreamAnalyticsJob
列出 Azure 訂用帳戶或指定的資源群組中定義的所有串流分析工作，或取得資源群組內與特定作業相關的工作資訊。

**範例 1**

	Get-AzureStreamAnalyticsJob

此命令傳回 Azure 訂用帳戶中所有 Stream Analytics 作業的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
此命令傳回資源群組 StreamAnalytics-Default-Central-US 中所有串流分析工作的相關資訊。

**範例 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
此命令傳回資源群組 StreamAnalytics-Default-Central-US 中串流分析工作 StreamingJob 的相關資訊。

### Get-AzureStreamAnalyticsInput
列出指定串流分析工作中定義的所有輸入，或取得特定輸入的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

此命令傳回在工作 StreamingJob 中定義之所有輸入的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
此命令傳回在工作 StreamingJob 中定義，名為 EntryStream 之輸入的相關資訊。

### Get-AzureStreamAnalyticsOutput
列出指定串流分析工作中定義的所有輸出，或取得特定輸出的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
此命令傳回在工作 StreamingJob 中定義之輸出的相關資訊。

**範例 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
此命令傳回在工作 StreamingJob 中定義，名為 Output 之輸出的相關資訊。

### Get-AzureStreamAnalyticsQuota
取得指定區域之串流處理單位配額的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
此命令傳回美國中部區域之串流處理單位配額和使用量的相關資訊。

### Get-AzureStreamAnalyticsTransformation
取得在串流分析工作中定義之特定轉換的相關資訊。

**範例 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
此命令傳回在工作 StreamingJob 中稱為 StreamingJob 之轉換的相關資訊。

### New-AzureStreamAnalyticsInput
在串流分析工作內建立新的輸入，或更新現有的指定輸入。
  
您可以在 .json 檔案中或命令列上指定輸入的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸入，而且沒有指定 –Force 參數，Cmdlet 會詢問是否要取代現有的輸入。

如果您指定 –Force 參數，並指定現有的輸入名稱，則不經過確認就會取代輸入。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立輸入 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-input] 一節。

**範例 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
此命令從檔案 Input.json 建立新的輸入。如果輸入定義檔中已經定義指定名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
此命令在稱為 EntryStream 的工作中建立新的輸入。如果已經定義此名稱的現有輸入，Cmdlet 會詢問是否要取代它。

**範例 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
此命令使用檔案的定義取代稱為 EntryStream 之現有輸入來源的定義。

### New-AzureStreamAnalyticsJob
在 Microsoft Azure 中建立新的串流分析工作，或更新現有指定工作的定義。

您可以在 .json 檔案中或命令列上指定工作的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的工作名稱，而且沒有指定 –Force 參數，Cmdlet 會詢問是否要取代現有的工作。

如果您指定 –Force 參數，並指定現有的工作名稱，則不經過確認就會取代工作定義。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立串流分析工作][msdn-rest-api-create-stream-analytics-job]一節。

**範例 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
此命令從 JobDefinition.json 中的定義建立新的作業。如果輸出定義檔中已經定義了指定名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
此命令取代 StreamingJob 的作業定義。

### New-AzureStreamAnalyticsOutput
在串流分析工作內建立新的輸出，或更新現有的輸出。

您可以在 .json 檔案中或命令列上指定輸出的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的輸出，而且沒有指定 –Force 參數，Cmdlet 會詢問是否要取代現有的輸出。

如果您指定 –Force 參數，並指定現有的輸出名稱，則不經過確認就會取代輸出。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立輸出 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-output] 一節。

**範例 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
此命令在作業 StreamingJob 中建立稱為 "output" 的新輸出。如果已經定義此名稱的現有輸出，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
此命令取代作業 StreamingJob 中 "output" 的定義。

### New-AzureStreamAnalyticsTransformation
在串流分析工作內建立新的轉換，或更新現有的轉換。
  
您可以在 .json 檔案中或命令列上指定轉換的名稱。若兩者皆指定，命令列上的名稱必須與檔案中的名稱相同。

如果您指定已存在的轉換，而且沒有指定 –Force 參數，Cmdlet 會詢問是否要取代現有的轉換。

如果您指定 –Force 參數，並指定現有的轉換名稱，則不經過確認就會取代轉換。

如需 JSON 檔案結構與內容的詳細資訊，請參閱[串流分析管理 REST API 參考程式庫][stream.analytics.rest.api.reference]的[建立轉換 (Azure 串流分析)][msdn-rest-api-create-stream-analytics-transformation] 一節。

**範例 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
此命令在作業 StreamingJob 中建立稱為 StreamingJobTransform 的新轉換。如果已經使用此名稱定義現有的轉換，Cmdlet 會詢問是否要取代它。

**範例 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 此命令取代作業 StreamingJob 中 StreamingJobTransform 的定義。

### Remove-AzureStreamAnalyticsInput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸入。如果您指定 –Force 參數，則不經過確認就會刪除輸入。

**範例 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
此命令移除作業 StreamingJob 中的輸入 EventStream 。

### Remove-AzureStreamAnalyticsJob
以非同步方式從 Microsoft Azure 中刪除特定的 Stream Analytics 作業。如果您指定 –Force 參數，則不經過確認就會刪除工作。

**範例 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
此命令移除作業 StreamingJob。

### Remove-AzureStreamAnalyticsOutput
以非同步方式從 Microsoft Azure 中的 Stream Analytics 作業刪除特定的輸出。如果您指定 –Force 參數，則不經過確認就會刪除輸出。

**範例 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
此命令移除作業 StreamingJob 中的輸出 Output。

### Start-AzureStreamAnalyticsJob
以非同步方式部署和啟動 Microsoft Azure 中的 Stream Analytics 作業。

**範例 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

此命令會啟動自訂輸出開始時間設為 2012 年 12 月 12 日 12:12:12 UTC 的工作 StreamingJob。


### Stop-AzureStreamAnalyticsJob
以非同步方式停止在 Microsoft Azure 中執行 Stream Analytics 作業，並取消配置正在使用的資源。透過 Azure 入口網站和管理 API 仍可在訂用帳戶內使用工作定義與中繼資料，也因此可以編輯和重新啟動工作。您將不需要對處於已停止狀態的工作支付費用。

**範例 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
此命令停止作業 StreamingJob。

### Test-AzureStreamAnalyticsInput
測試 Stream Analytics 連線到指定輸入的能力。

**範例 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
此命令會測試 StreamingJob 中輸入 EntryStream 的連線狀態。

###Test-AzureStreamAnalyticsOutput
測試 Stream Analytics 連線到指定輸出的能力。

**範例 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
此命令會測試 StreamingJob 中輸出 Output 的連線狀態。

> [AZURE.NOTE]以程式設計方式建立的串流分析工作預設不會啟用監視。您可以在 Azure 入口網站中瀏覽到該工作的 [監視] 頁面，然後按一下 [啟用] 按鈕來手動啟用監視，或是按照[Azure 串流分析 - 以程式設計方式監視串流分析工作](stream-analytics-monitor-jobs.md)中的步驟執行，以程式設計方式來啟用。

## 取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54--> 