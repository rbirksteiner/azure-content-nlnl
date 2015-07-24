<properties
   pageTitle="使用 Azure 資源管理員範本部署應用程式"
   services="azure-portal"
   description="使用 Azure 資源管理員部署應用程式至 Azure。範本是 JSON 檔案，並可從入口網站、PowerShell、適用於 Mac、Linux 和 Windows 的 Azure 命令列介面，或 REST 來使用。"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員範本部署應用程式

本主題說明如何使用 Azure 資源管理員範本，將您的應用程式部署至 Azure。它會說明如何使用 Azure PowerShell、Azure CLI、REST API 或 Microsoft Azure Preview 入口網站部署您的應用程式。

Azure 資源管理員範本可讓您透過宣告式 JSON，快速且輕鬆地在 Azure 佈建您的應用程式。在單一 JSON 範本中，您可以部署多個服務，例如虛擬機器、虛擬網路、儲存體、應用程式服務和資料庫。您可以使用相同的範本，在應用程式生命週期的每個階段，重複且一致地部署應用程式。

若要簡化您應用程式的管理，您可將共用相同生命週期的所有資源，組織成單一的資源群組。資源群組可讓您輕鬆地將相關的資源一起部署、更新及刪除。在大部分情況下，資源群組會對應至單一應用程式或應用程式層 (適用於大型應用程式)。透過範本部署的資源會位在單一的資源群組內，但它們可以包括其他資源群組中的相依資源。

在資源群組中，您可以追蹤部署的執行狀況，並查看部署的狀態和從範本執行輸出的任何結果。

使用範本部署應用程式時，您可以提供參數值，以自訂建立資源的方式。您可以透過內嵌方式或在參數檔案中指定這些參數的值。

## 概念

- 資源群組 - 共用相同生命週期的實體集合
- 資源管理員範本 - 定義部署之目標狀態的宣告式 JSON 檔案
- 部署 - 追蹤資源管理員範本執行狀況的作業
- 參數 - 使用者執行部署時提供的值，以自訂部署的資源
- 參數檔案 - 儲存參數名稱和值的 JSON 檔案 

## 案例

您可以使用資源管理員範本來：

- 部署複雜的多層式應用程式，例如 Microsoft SharePoint。
- 一致且重複地部署您的應用程式。
- 支援開發、測試及生產環境。
- 檢視部署狀態。
- 使用部署稽核記錄檔來疑難排解部署失敗。

## 使用 Preview 入口網站部署

您知道嗎？ 資源庫中的每個應用程式的基礎都是 Azure 資源管理員範本！ 只要透過入口網站建立虛擬機器、虛擬網路、儲存體帳戶、App Service 或資料庫，您就已經充分利用 Azure 資源管理員的優點，不多費心力。

若要透過 Preview 入口網站疑難排解部署，請按一下 [瀏覽] -> [資源群組] -> 您的資源群組名稱。在這裡，按一下 [監視] 鏡頭底下的 [事件] 標題。最後，您可以選取個別的 [作業] 和 [事件] 來檢視詳細資料。

## 使用 PowerShell 部署

如果您之前未曾搭配使用Azure PowerShell 與資源管理員，請參閱[將 Azure PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager.md)。

1. 登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的帳戶的相關資訊。

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切換至 Azure 資源管理員模組。

        PS C:\> Switch-AzureMode AzureResourceManager

4. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. 若要建立資源群組的新部署，請執行 **New-AzureResourceGroupDeployment** 命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL，以及您的案例所需的任何其他參數。
   
     您有下列選項可以用來提供參數值：
   
     - 使用內嵌參數。

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - 使用參數物件。

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - 使用參數檔案。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     部署資源群組之後，您會看到部署的摘要。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. 取得部署失敗的相關資訊。

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. 取得部署失敗的詳細資訊。

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

## 以適用於 Mac、Linux 和 Windows 的 Azure CLI 部署

如果您之前未曾搭配使用 Azure CLI 與資源管理員，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。

1. 登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的登入的結果。

        azure login
  
        ...
        info:    login command OK

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組。您會收到新模式的確認。

        azure config mode arm
   
        info:     New mode is arm

4. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 若要建立資源群組的新部署，請執行下列命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL，以及您的案例所需的任何其他參數。
   
     您有下列選項可以用來提供參數值：

     - 使用內嵌參數和本機範本。

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - 使用內嵌參數和範本的連結。

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - 使用參數檔案。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     部署資源群組之後，您會看到部署的摘要。
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 取得最新部署的相關資訊。

         azure group log show -l ExampleResourceGroup

7. 取得部署失敗的詳細資訊。
      
         azure group log show -l -v ExampleResourceGroup

## 使用 REST API 部署
1. 設定[一般參數和標頭](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) (包括驗證權杖)。
2. 如果您沒有現有資源群組，請建立新的資源群組。提供您的訂用帳戶識別碼、新資源群組的名稱，以及解決方案需要的位置。如需詳細資訊，請參閱[建立資源群組](https://msdn.microsoft.com/library/azure/dn790525.aspx)。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. 建立新的資源群組部署。提供您的訂用帳戶識別碼、要部署之資源群組的名稱、部署的名稱，以及範本的位置。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。如需以 REST API 建立資源群組的相關詳細資訊，請參閱 [建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)。
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. 取得範本部署的狀態。如需詳細資訊，請參閱 [取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## 參數檔案

如果您在部署期間，使用參數檔案傳遞參數值到您的範本，您需要建立格式類似以下範例的 JSON 檔案。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

## 後續步驟
- [Azure 資源管理員概觀](../resource-group-overview.md)
- [使用 .NET 程式庫和範本部署資源](../arm-template-deployment.md)
- [透過可預測方式在 Azure 中部署複雜應用程式](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [撰寫範本](../resource-group-authoring-templates.md)
- [範本函式](../resource-group-template-functions.md)
- [進階範本作業](../resource-group-advanced-template.md)  

 

<!---HONumber=62-->