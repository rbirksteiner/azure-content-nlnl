<properties
   pageTitle="Azure 資源管理員進階範本作業"
   description="描述將應用程式部署至 Azure 時，如何在 Azure 資源管理員範本中使用巢狀範本和複製作業。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# 進階範本作業

本主題描述將資源部署至 Azure 時可用來執行更進階工作的複製作業和巢狀範本。

## copy

可讓您在部署資源時逐一查看指定的次數。
   
使用陣列時，複製作業會特別有幫助，因為您可以逐一查看陣列中的每個項目。**copyIndex()** 函數會傳回目前的反覆項目值。您可以部署下列三個名稱的網站：

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

搭配下列範本。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

您也可以使用不含陣列的複製作業。例如，您可能想要將遞增的數字新增至每個部署之資源名稱的結尾。您可以部署下列三個名稱的網站：

- examplecopy-0
- examplecopy-1
- examplecopy-2.

搭配下列範本。

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

您會在上述範例中注意到，索引值從零到 2。若要位移索引值，您可以傳遞 **copyIndex()** 函數中的值，例如 **copyIndex(1)**。要執行的反覆項目數仍然在複製項目中指定，但 copyIndex 的值會由指定的值位移。因此，使用和前一個範例相同的範本，但指定 **copyIndex(1)** 會部署下列三個名稱的網站：

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 巢狀範本

您可能需要合併兩個範本，或從父系範本啟動子系範本。做法是在指向巢狀範本的主版範本內使用部署資源。您將 **templateLink** 屬性設為巢狀範本的 URI。您可以指定直接在範本中的值，或連結至參數檔案，以提供巢狀範本的參數值。第一個範例會使用 **parameters** 屬性直接指定參數值。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

下一個範例會使用 **parametersLink** 屬性連接至參數檔案。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## 後續步驟
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)
- [使用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)
- [Azure 資源管理員概觀](./resource-group-overview.md)

<!---HONumber=July15_HO1-->