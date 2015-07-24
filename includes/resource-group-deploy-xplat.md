## 如何使用 Azure CLI 部署

1. 登入您的 Azure 帳戶。

        azure login

  提供您的認證之後，命令會傳回您的登入的結果。

        ...
        info:    login command OK

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組

        azure config mode arm

   您會收到新模式的確認。

        info:     New mode is arm

4. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。

        azure group create -n ExampleResourceGroup -l "West US"

   隨即傳回新資源群組的摘要。

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

   - 使用參數檔案。

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

<!---HONumber=July15_HO2-->