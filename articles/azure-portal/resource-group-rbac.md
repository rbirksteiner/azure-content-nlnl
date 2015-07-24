<properties
   pageTitle="管理和稽核資源存取權"
   description="使用角色型存取控制 (RBAC) 來管理部署至 Azure 之資源的使用者權限。"
   services="azure-portal"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/18/2015"
   ms.author="tomfitz"/>

# 管理和稽核資源存取權

使用 Azure 資源管理員時，您可以確定您組織中的使用者有適當的權限來管理或存取資源。資源管理員會利用角色型存取控制 (RBAC)，以便您可以很容地對個別資源或資源群組套用安全性原則。例如，您可以授與使用者為訂用帳戶中特定虛擬機器的存取權，或讓使用者能夠管理訂用帳戶中的所有網站，但不包括其他資源。

## 概念

以下是幾個角色型存取控制的相關重要概念供您了解：

1. 主體 - 被授與權限的實體，例如使用者、安全性群組或應用程式。
2. 角色 - 一組允許的動作
3. 範圍 - 角色套用的層級，例如訂用帳戶、資源群組或資源。
3. 角色指派 - 將主體加入到角色並設定範圍的程序。

## 角色範例
若要了解 RBAC 的概念，我們來看一些常見的角色定義範例：

| 角色 | 允許的動作 |
| ------- | ----------------- |
| 讀取者 | */read (讀取任何項目) | | Owner | * (讀取/寫入任何項目) |

若要將名為 **ExampleGroup** 之資源群組的「讀取者」角色指派給 **User A**，並將整個訂用帳戶的「擁有者」角色指派給 **User B**，您可以依照以下方式指派：

| 角色 | 主體 | 範圍 |
| --------|-------------|---------- |
| 讀取者 | User A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| 擁有者 | User B | /subscriptions/{subscriptionId} |

## 範例案例

本主題中，您會看到如何針對 透過 Azure PowerShell、適用於 Mac、Linux 及 Windows 的 Azure CLI，以及 REST API，執行下列常見案例。

1. 檢視訂用帳戶中可用的角色，以及允許那些角色執行的動作。
2. 授與「讀取者」權限給整個訂用帳戶的群組成員。
3. 授與「參與者」權限給應用程式，以允許應用程式管理資源群組內的資源。
4. 授與特定網站的「擁有者」權限給單一使用者。
5. 列出資源群組的稽核記錄檔。


## 如何使用 PowerShell 管理存取權
如果您尚未安裝最新版的 Azure PowerShell，請參閱[安裝和設定 Azure PowerShell](../powershell-install-configure.md)。開啟 Azure PowerShell 主控台。

1. 使用您的認證登入您的 Azure 帳戶。命令會傳回您帳戶的相關資訊。

        PS C:\> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切換至 Azure 資源管理員模組。

        PS C:\> Switch-AzureMode AzureResourceManager

### 檢視可用的角色
若要檢視訂用帳戶所有可用的角色，請執行 **Get-AzureRoleDefinition** 命令。

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### 授與「讀取者」權限給訂用帳戶的群組。
1. 執行 **Get-AzureRoleDefinition** 命令時，提供角色名稱以檢閱「讀取者」角色定義。檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. 執行 **Get-AzureADGroup** 命令以取得必要的安全性群組。提供訂用帳戶中群組的實際名稱。ExampleAuditorGroup 會顯示在下面。

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. 建立稽核員安全性群組的角色指派。當命令完成時，會傳回新的角色指派。

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###授與資源群組的「參與者」角色給應用程式。
1. 執行 **Get-AzureRoleDefinition** 命令時，提供角色名稱以檢閱「參與者」角色定義。檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRoleDefinition Contributor

2. 執行 **Get-AzureADServicePrincipal** 命令並提供訂用帳戶中應用程式的名稱，以取得服務主體物件識別碼。ExampleApplication 會顯示在下面。

        PS C:\> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. 執行 **New-AzureRoleAssignment** 命令，以建立服務主體的角色指派。

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

如需設定 Azure Active Directory 應用程式和服務主體的更詳細說明，請參閱[使用 Azure 資源管理員驗證服務主體](../resource-group-authenticate-service-principal.md)。

###授與資源的「擁有者」權限給使用者。
1. 執行 **Get-AzureRoleDefinition** 命令時，提供角色名稱以檢閱「擁有者」角色定義。檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRoleDefinition Owner

2. 建立使用者的角色指派。

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###列出資源群組的稽核記錄檔。
若要取得資源群組的稽核記錄檔，請執行 **Get-AzureResourceGroupLog** 命令。

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## 如何使用適用於 Mac、Linux 和 Windows 的 Azure CLI

如果您沒有適用於 Mac、Linux 和 Windows 的 Azure CLI，或您尚未設定組織帳戶來與 Azure CLI 搭配使用，請參閱[安裝和設定 Azure CLI](../xplat-cli-install.md)。

1. 使用您的認證登入您的 Azure 帳戶。此命令會傳回您登入的結果。

        azure login

        ...
        info:    login command OK

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組。您會收到新模式的確認。

        azure config mode arm
        
        info:     New mode is arm

### 檢視可用的角色
檢視您訂用帳戶的所有可用角色。它會傳回一份角色定義清單。

    azure role list

### 授與「讀取者」權限給訂用帳戶的群組。
1. 取得「讀取者」角色的角色定義。檢查允許的動作是否是您要指派的動作。

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. 根據名稱搜尋群組，以取得必要的安全性群組及其 objectId。就會如以下範例顯示 ExampleAuditorGroup。

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. 建立安全性群組的角色指派。

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### 授與資源群組的「參與者」角色給應用程式。
1. 取得「參與者」角色的角色定義。檢查允許的動作是否是您要指派的動作。

        azure role show Contributor

2. 取得應用程式的 ObjectId。提供要擷取之應用程式的名稱。

        azure ad sp show --search ExampleApplicationName

2. 建立應用程式的角色指派。

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###授與特定網站的「擁有者」權限給使用者。
1. 取得「擁有者」角色的角色定義。檢查允許的動作是否是您要指派的動作。

        azure role show Owner

2. 建立使用者的角色指派。

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###列出資源群組的稽核記錄檔。
若要取得資源群組的稽核記錄檔，請執行 **azure group log show** 命令，並提供所需資源群組的名稱。

         azure group log show ExampleGroupName


## 如何使用 REST API
若要透過 Azure 資源管理員 REST API 管理角色型存取控制，您必須在傳送要求時設定一般的標頭和參數 (包括驗證權杖) 。如需詳細資訊，請參閱[一般參數和標頭](https://msdn.microsoft.com/library/azure/dn906885.aspx)。
   
若要探索支援的 API 版本，請執行：

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

您可以在本主題使用 `2014-10-01-preview` 版本。

###建立角色指派
取得可用的角色定義。

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

建立角色指派。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###刪除角色指派

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## 後續步驟

- [Microsoft Azure 入口網站中的角色型存取控制](../role-based-access-control-configure.md)
- [使用 Azure 傳統入口網站建立新的 Azure 服務主體](../resource-group-create-service-principal-portal.md)
- [透過 Azure 資源管理員驗證服務主體](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=62-->