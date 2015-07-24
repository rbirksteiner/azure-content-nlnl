<properties 
	pageTitle="針對 Azure Data Factory 事件建立警示" 
	description="了解如何針對 Azure 在 Data Factory 作業上引發的事件建立警示。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="spelluru"/>

# 針對 Azure 事件建立警示
Azure 事件可讓您深入了解 Azure 資源的情況。當建立、更新或刪除 Azure 資料時 (例如 Data Factory)，Azure 會記錄使用者事件。使用 Azure Data Factory 時，下列情況會產生事件：
 
1.	建立/更新/刪除 Azure Data Factory。
2.	資料處理 (稱為「回合」) 開始/完成。
3.	建立和移除隨選 HDInsight 叢集時。

您可以針對這些使用者事件建立警示，並設定它們傳送電子郵件通知給訂用帳戶的管理員和共同管理員。此外，您還可以指定使用者的其他電子郵件地址，當條件符合時，這些使用者需要收到電子郵件通知。

## 指定警示定義
若要指定警示定義，您需要建立 JSON 檔案來描述您想要接獲通知的作業。在下列範例中，警示會針對 **RunFinished** 作業傳送電子郵件通知。具體而言，當 Data Factory 中完成一個回合，而且執行失敗時 (Status = FailedExecution)，就會傳送電子郵件通知。

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

在上述 JSON 定義中，如果不想接獲特定失敗的通知，您可以移除 **subStatus**。

如需作業和狀態 (和子狀態) 的清單，請參閱[可用的作業和狀態](#AvailableOperationsStatuses)。

## 部署警示
若要部署警示，請使用 Azure PowerShell Cmdlet：**New-AzureResourceGroupDeployment**，如下列範例所示：

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

StorageAccountName 指定儲存體帳戶來儲存已部署的警示 JSON 檔案。

成功完成資源群組部署之後，您會看到下列訊息：
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## 擷取 Azure 資源群組部署的清單
若要擷取已部署的 Azure 資源群組部署的清單，請使用 Cmdlet：**Get-AzureResourceGroupDeployment**，如下所範例所示：
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>可用的作業名稱和狀態值

<table>
<th align="left">作業名稱</th>
<th align="left">狀態</th>
<th align="left">子狀態</th>

<tr>
<td>RunStarted</td>
<td>已啟動</td>
<td>啟動中</td>
</tr>

<tr>
<td>RunFinished</td>
<td>Failed / Succeeded</td>
<td>
	<p>FailedResourceAllocation </p>
	<p>Succeeded</p>
	<p>FailedExecution</p>
	<p>TimedOut</p>
	<p>Canceled</p>
	<p>FailedValidation</p>
	<p>Abandoned</p>
</td>
</tr>

<tr>
<td>SliceOnTime</td>
<td>In Progress</td>
<td>Ontime</td>
</tr>

<tr>
<td>SliceDelayed</td>
<td>In Progress</td>
<td>Late</td>
</tr>

<tr>
<td>OnDemandClusterCreateStarted</td>
<td>已啟動</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateSuccessful</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterDeleted</td>
<td>Succeeded</td>
<td></td>
</tr>

</table>

## 使用者事件疑難排解
執行下列命令以查看產生的事件：

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=62-->