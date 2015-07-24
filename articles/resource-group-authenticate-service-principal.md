<properties
   pageTitle="使用 Azure 資源管理員驗證服務主體"
   description="描述如何透過角色存取控制授與服務主體的存取權，並驗證服務主體。顯示如何使用 PowerShell 和 Azure CLI 執行這些工作。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/15/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員驗證服務主體

本主題顯示如何允許服務主體 (例如自動化程序、應用程式或服務) 存取您訂用帳戶中的其他資源。使用 Azure 資源管理員，您可以使用角色存取控制將允許的動作授與服務主體，並驗證該服務主體。本主題顯示如何使用 PowerShell 和 Azure CLI 將角色指派給服務主體，並驗證服務主體。


## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務。如需詳細資訊，請參閱[什麼是 Azure Active Directory](active-directory/active-directory-whatis.md)。
2. 服務主體 - 必須存取其他資源之目錄中應用程式的執行個體。
3. AD 應用程式 - 向 AAD 識別應用程式的目錄記錄。如需詳細資訊，請參閱 [Azure AD 中的驗證基本概念](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)。

## 使用 PowerShell 將存取權授與服務主體並驗證服務主體

如果您未安裝 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](./powershell-install-configure.md)。

您將從建立服務主體開始。若要這麼做，我們必須在目錄中建立應用程式。本節將逐步引導如何在目錄中建立新的應用程式。

1. 執行 **New-AzureADApplication** 命令，以建立新的 AAD 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     傳回 Azure AD 應用程式。需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。儲存輸出，或將它擷取到變數中。

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. 建立應用程式的服務主體。

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色存取控制的詳細資訊，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)。

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 擷取用來建立角色指派的訂用帳戶。稍後會使用此訂用帳戶，來取得服務主體角色指派所在租用戶的 **TenantId**。

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     如果您已在非目前所選取訂用帳戶的訂用帳戶中建立角色指派，則可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂用帳戶。

5. 執行 **Get-Credential** 命令，以建立含有您認證的新 **PSCredential** 物件。

        PS C:\> $creds = Get-Credential

     系統會提示您輸入認證。

     ![][1]

     針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。針對密碼，使用您在建立帳戶時所指定的密碼。

6. 使用您輸入的認證做為 **Add-AzureAccount** Cmdlet 的輸入，以將服務主體登入：

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     您現在應該驗證為您所建立 AAD 應用程式的服務主體。


## 使用 Azure CLI 將存取權授與服務主體並驗證服務主體

如果您未安裝適用於 Mac、Linux 和 Windows 的 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli-install.md)。

1. 執行 **azure ad app create** 命令，以建立新的 AAD 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    傳回 Azure AD 應用程式。需要有 ApplicationId 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色存取控制的詳細資訊，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)。

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. 列出帳戶，並尋找輸出中的 **TenantId** 屬性，以判斷服務主體角色指派所在租用戶的 **TenantId**。

        azure account list

5. 使用服務主體做為您的身分識別來進行登入。針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId**。針對密碼，使用您在建立帳戶時所指定的密碼。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    您現在應該驗證為您所建立 AAD 應用程式的服務主體。

## 後續步驟
開始使用

- [Azure 資源管理員概觀](./resource-group-overview.md)  
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](./powershell-azure-resource-manager.md)
- [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [使用 Azure 入口網站管理 Azure 資源](azure-portal/resource-group-portal.md)

  
建立和部署應用程式
  
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)  
- [使用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)  
- [Azure 中的資源群組部署疑難排解](virtual-machines/resource-group-deploy-debug.md)  
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)  
- [進階範本作業](./resource-group-advanced-template.md)  
- [使用 .NET 程式庫和範本部署 Azure 資源](virtual-machines/arm-template-deployment.md)
  
組織資源
  
- [使用標記來組織您的 Azure 資源](./resource-group-using-tags.md)  
  
管理和稽核存取權
  
- [管理和稽核資源存取權](azure-portal/resource-group-rbac.md)  
- [使用 Azure 入口網站建立新的 Azure 服務主體](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=July15_HO1-->