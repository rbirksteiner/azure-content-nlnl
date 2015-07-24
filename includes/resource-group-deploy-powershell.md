## 如何使用 PowerShell 部署

1. 登入您的 Azure 帳戶。

          Add-AzureAccount

   提供您的認證之後，命令會傳回您的帳戶的相關資訊。

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切換至 Azure 資源管理員模組。

          Switch-AzureMode AzureResourceManager

4. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   隨即傳回新資源群組的摘要。

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

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - 使用參數物件。

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - 使用參數檔案。

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  部署資源群組之後，您會看到部署的摘要。

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. 取得部署失敗的相關資訊。

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. 取得部署失敗的詳細資訊。

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

<!---HONumber=July15_HO2-->