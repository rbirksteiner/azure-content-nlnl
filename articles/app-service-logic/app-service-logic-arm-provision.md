<properties 
	pageTitle="建立邏輯應用程式" 
	description="使用 Azure 資源管理員範本，部署空的邏輯應用程式以定義工作流程。" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# 使用範本建立邏輯應用程式

在本主題中，您將學習如何建立 Azure 資源管理員範本，以建立可用來定義工作流程的空邏輯應用程式。您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需邏輯應用程式屬性的詳細資料，請參閱[邏輯應用程式工作流程管理 API](https://msdn.microsoft.com/library/azure/dn948513.aspx)。如需定義本身的範例，請參閱[作者邏輯應用程式定義](app-service-logic-author-definitions.md)。

如需關於建立範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需完整範本，請參閱[邏輯應用程式範本](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicApp.json)。

## 部署內容

使用此範本，您將部署邏輯應用程式。

## 參數

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/zh-tw/status/feed/"
      }
    
## 要部署的資源

### App Service 方案

建立 App Service 方案。

它使用與在其中進行部署的資源群組相同的位置。

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### 邏輯應用程式

建立邏輯應用程式。

範本會使用邏輯應用程式名稱的參數值。它會將邏輯應用程式的位置設為與資源群組相同的位置。

這個特定定義會一個小時執行一次，並 Ping **testUri** 參數中所指定的位置。

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "testURI": {
                        "type": "string",
                        "defaultValue": "[parameters('testUri')]"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "recurrence",
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                },
                "actions": {
                    "http": {
                        "type": "Http",
                        "inputs": {
                            "method": "GET",
                            "uri": "@parameters('testUri')"
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -g ExampleDeployGroup


 

<!---HONumber=62-->