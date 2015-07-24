<properties 
	pageTitle="佈建 Web 應用程式和 Redis 快取" 
	description="使用 Azure 資源管理員範本來部署 Web 應用程式和 Redis 快取。" 
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

# 使用範本建立 Web 應用程式和 Redis 快取

在本主題中，您將學習如何建立 Azure 資源管理員範本，以部署 Azure Web 應用程式和 Redis 快取。您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需關於建立範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需完整的範本，請參閱 [Web 應用程式與 Redis 快取 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)。

## 部署內容

在此範本中，您將部署：

- Azure Web 應用程式
- Azure Redis 快取.

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## 要指定的參數

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[AZURE.INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]



## 要部署的資源

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Redis 快取

建立與 Web 應用程式搭配使用的 Azure Redics 快取。快取的名稱指定於 **redisCacheName** 參數中。

此範本會在與 Web 應用程式相同的位置中建立快取，而這項建議可達到最佳效能。

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('siteLocation')]",
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

### Web 應用程式

使用 **siteName** 參數中所指定的名稱來建立 Web 應用程式。

請注意，Web 應用程式是使用應用程式設定屬性所設定，可讓它使用 Redis 快取。此應用程式設定是根據部署期間所提供的值動態建立。
        
    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "name": "[parameters('siteName')]",
        "serverFarm": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2014-04-01",
          "type": "config",
          "name": "web",
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
          ],
          "properties": {
            "appSettings": [
              {
                "name": "REDIS_HOST",
                "value": "[concat(parameters('siteName'), '.redis.cache.windows.net:6379')]"
              },
              {
                "name": "REDIS_KEY",
                "value": "[listKeys(resourceId('Microsoft.Cache/Redis', parameters('siteName')), '2014-04-01').primaryKey]"
              }
            ]
          }
        }
      ]
    }     



## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=July15_HO1-->