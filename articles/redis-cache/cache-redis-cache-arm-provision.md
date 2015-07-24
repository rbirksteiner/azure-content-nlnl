<properties 
	pageTitle="佈建 Redis 快取" 
	description="使用 Azure 資源管理員範本來部署 Azure Redis 快取。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tomfitz"/>

# 使用範本建立 Redis 快取

在本主題中，您將學習如何建立 Azure 資源管理員範本，以部署 Azure Redis 快取。您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需關於建立範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需完整範本，請參閱 [Redis 快取範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

## 部署內容

在此範本中，您將部署 Azure Redis 快取。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## 參數

透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 Parameters 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Redics 快取的位置。針對最佳效能，使用要與快取搭配使用之應用程式相同的位置。

    "redisCacheLocation": {
      "type": "string"
    }

    
## 要部署的資源

### Redis 快取

建立 Azure Redis 快取。

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }
     



## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=July15_HO1-->