<properties
   pageTitle="授權 Azure 資源管理員範本"
   description="使用宣告式 JSON 語法建立 Azure 資源管理員範本，藉此將應用程式部署至 Azure。"
   services="multiple"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# 授權 Azure 資源管理員範本

Azure 應用程式通常需要將資源 (如資料庫伺服器、資料庫或網站等) 結合在一起，以達到所需的目標。您不是分開部署與管理每個資源，而是建立一個 Azure 資源管理員範本，藉此經由單一、協調的作業署與佈建應用程式有的資源。在範本中，您會定義應用程式所需的資源，並指定部署參數以針對不同的環境輸入值。範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

## 範本格式

下列範例顯示組成範本基本結構的區段。

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| $schema | 是 | JSON 結構描述檔案的位置，說明範本語言的版本。
| contentVersion | 是 | 範本版本 (例如 1.0.0.0)。使用範本部署資源時，這個值可用來確定使用的是正確的範本。
| 參數 | 否 | 執行部署以自訂資源部署時所提供的值。
| 變數 | 否 | 範本中做為 JSON 片段以簡化範本語言運算式的值。
| 資源 | 是 | 在資源群組中部署或更新的服務類型。
| 輸出 | 否 | 部署後傳回的值。

本主題稍後處將會檢視範本的區段。現在，我們將檢視組成範本的一些語法。

## 運算式和函式

範本的基本語法是 JSON，但運算式與函式能夠延伸範本中的 JSON，讓您能夠建立不是嚴格常數值的值。運算式以方括號 ([ 與 ]) 括住，並會在部署範本後評估。運算式可出現在 JSON 字串值中的任何一處，並一律傳回另一個 JSON 值。如果您必須使用開頭為括號 [ 的常數字串，您必須使用兩個括號 [[。

一般而言，您可以將運算式搭配函數使用，以執行可設定部署的作業。正如同在 JavaScript 中，函數呼叫的格式為 **functionName(arg1,arg2,arg3)**。您可以使用點與 [index] 運算子來參考屬性。

下列清單顯示常見的函式。

- **parameters(parameterName)**

    傳回執行部署時提供的參數值。

- **variables(variableName)**

    傳回範本中定義的變數。

- **concat(arg1,arg2,arg3,...)**

    結合多個字串值。此函數可以接受任意數目的引數。

- **base64(inputString)**

    傳回輸入字串的 base64 表示法。

- **resourceGroup()**

    傳回代表目前資源群組的結構化物件 (包括 ID、名稱及位置屬性)。

- **resourceId([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

    傳回資源的唯一識別碼。可用於擷取另一個資源群組中的資源。

下列範例將示範如何在結構化值時使用數個函數：
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

現在您知道有關運算式和函數的相關資訊，以便了解範本區段。如需有關所有範本函式的詳細資訊，包括參數和傳回值的格式，請參閱 [Azure 資源管理員範本函數](./resource-group-template-functions.md)。


## 參數

在範本的參數區段中，您會指定使用者可在部署資源時輸入的值。您可以在整個範本中使用這些參數值，為所部署的資源設定值。只有在參數區段中宣告的參數可以用於範本的其他區段中。

在參數區段內，您無法使用參數值來建構另一個參數值。該類型的作業通常在變數區段中發生。

您會定義結構如下的參數：

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ]
       }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| parameterName | 是 | 參數名稱。必須是有效的 JavaScript 識別碼。
| 類型 | 是 | 參數值類型。請參閱下列允許類型清單。
| defaultValue | 否 | 如果未提供參數值，則會使用參數的預設值。
| allowedValues | 否 | 參數的允許值陣列，確保提供正確的值。

允許的類型和值為：

- string 或 secureString - 任何有效的 JSON 字串
- int - 任何有效的 JSON integer
- bool - 任何有效的 JSON 布林值
- object - 任何有效的 JSON 物件
- array - 任何有效的 JSON 陣列


>[AZURE.NOTE]所有密碼、金鑰和其他密碼都應該使用 **secureString** 類型。部署資源後，無法讀取類型為 secureString 的範本參數。

下列範例示範如何定義參數：

    "parameters": {
       "siteName": {
          "type": "string"
       },
       "siteLocation": {
          "type": "string"
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       }
    }

## 變數

在變數區段中，您會建構可用來簡化範本語言運算式的值。一般而言，這些變數將會以參數所提供的值為根據。

下列範例說明如何定義由兩個參數值建構的變數：

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

接下來的範例說明複雜 JSON 類型的變數，以及由其他變數建構的變數：

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## 資源

在資源區段中，您會定義要部署或更新資源。

您會定義結構如下的資源：

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| 元素名稱 | 必要 | 說明
| :----------------------: | :------: | :----------
| apiVersion | 是 | 支援資源的 API 版本。
| 類型 | 是 | 資源類型。這個值是資源提供者的命名空間與資源提供者所支援資源類型的組合。
| 名稱 | 是 | 資源名稱。此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。
| location | 否 | 所提供資源的支援地理位置。
| tags | 否 | 與資源相關聯的標記。
| dependsOn | 否 | 正在定義的資源所相依的資源。評估資源與依相依順序部署資源之間的相依性。資源若不互相依賴，則會嘗試平行部署資源。值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。
| 屬性 | 否 | 資源特定的組態設定。
| 資源 | 否 | 與正在定義的資源相依的下層資源。

如果資源名稱不是唯一，您可以使用 **resourceId** Helper 函式 (如下所述) 來取得任何資源的唯一識別碼。

下列範例顯示 **Microsoft.Web/serverfarms** 資源，以及含巢狀 **Extensions** 資源的 **Microsoft.Web/sites** 資源：

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## 輸出

在輸出區段中，您可以指定從部署傳回的值。例如，您可以傳回 URI 以存取所部署的資源。

下列範例顯示輸出定義的結構：

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| outputName | 是 | 輸出值的名稱。必須是有效的 JavaScript 識別碼。
| 類型 | 是 | 輸出值的類型。輸出值支援與範本輸入參數相同的類型。
| value | 是 | 將進行評估並傳回做為輸出值的範本語言運算式。


下列範例顯示在輸出區段中傳回的值。

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## 進階案例。
本主題介紹範本。然而，您的案例可能需要更進階的工作。

您可能必須將兩個範本合併在一起，或在上層範本中使用下層範本。如需詳細資訊，請參閱[巢狀範本](../resource-group-advanced-template#nested-template)。

您可能需要使用不同資源群組內的資源。這常見於使用多個資源群組之間所共用的儲存體帳戶或虛擬網路時。如需詳細資訊，請參閱 [resourceId 函式](../resource-group-template-functions#resourceid)。

## 完成範本
下列範本會部署 Web 應用程式，並使用 .zip 檔中的程式碼來佈建。

    {
       "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## 後續步驟
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)
- [使用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)
- [進階範本作業](./resource-group-advanced-template.md)
- [透過可預測方式在 Azure 中部署複雜應用程式](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Azure 資源管理員概觀](./resource-group-overview.md)

<!---HONumber=July15_HO1-->