### App Service 方案

建立主控 Web 應用程式的服務方案。您可以透過 **hostingPlanName** 參數提供方案名稱。方案的位置與用於 Web 應用程式的位置相同。定價層和背景工作大小指定於 **sku** 和 **workerSize** 參數

    {
       "apiVersion":"2015-04-01",
       "name":"[parameters('hostingPlanName')]",
       "type":"Microsoft.Web/serverfarms",
       "location":"[parameters('siteLocation')]",
       "properties":{
         "name":"[parameters('hostingPlanName')]",
         "sku":"[parameters('sku')]",
         "workerSize":"[parameters('workerSize')]",
         "numberOfWorkers":1
       }
    }

<!---HONumber=July15_HO1-->