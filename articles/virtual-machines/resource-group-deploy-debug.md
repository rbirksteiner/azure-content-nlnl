<properties
   pageTitle="Azure 中的資源群組部署疑難排解"
   description="描述在 Azure 中部署資源的一般問題，並顯示如何使用 Azure 入口網站、適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI) 和 PowerShell 來檢查部署，並偵測問題。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Azure 中的資源群組部署疑難排解

事先檢查幾件事，就可以更輕鬆地防止部署錯誤，但部署有時候會因為許多原因而失敗。本文件描述多個工具和作業，來防止簡單錯誤、下載範本檔案，並檢查部署記錄。它也會討論檢查部署記錄中是否有失敗時要注意的主要區域。

## 與 Azure 互動的有用工具
AzureResourceManager 模組包括 Cmdlet，而從命令列中使用 Azure 資源時，用來收集可協助您進行工作的工具。Azure 資源群組範本為 JSON 文件，而且 Azure 資源管理 API 會接受並傳回 JSON，因此 JSON 剖析工具用來協助您瀏覽資源的相關資訊，以及設計範本和範本參數檔案或與之互動。

### Mac、Linux 和 Windows 工具
如果您使用 Mac、Linux 和 Windows 的 Azure 命令列介面，則可能熟悉標準下載工具 (例如 **[curl](http://curl.haxx.se/)** 和 **[wget](https://www.gnu.org/software/wget/)** 或 **[Resty](https://github.com/beders/Resty)**) 和 JSON 公用程式 (例如 **[jq](http://stedolan.github.io/jq/download/)**、**[jsawk](https://github.com/micha/jsawk)** 以及一併處理 JSON 的語言程式庫) (許多這些工具也具有 Windows 的連接埠，例如 [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)；事實上，有幾種方法可以取得 Linux 以及在 Windows 上執行的其他開放原始碼軟體工具)。

本主題包括一些您可以與 **jq** 搭配使用的 Azure CLI 命令，以精確地取得您想要更有效率的資訊。您應該選擇熟悉的工具組，協助了解 Azure 資源使用。

### Windows PowerShell

Windows PowerShell 有幾個基本命令來執行相同的程序。

- 使用 **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** Cmdlet 可下載檔案 (例如資源群組範本或參數 JSON 檔案)。
- 使用 **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** Cmdlet 可將 JSON 字串轉換成自訂物件 ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx))，而自訂物件具有 JSON 字串中每個欄位的屬性。

## 防止適用於 Mac、Linux 和 Windows 之 Azure CLI 中的錯誤

Azure CLI 有數個命令，可協助防止錯誤，並偵測發生的問題。

- **azure location list**。此命令會取得支援每種資源類型的位置 (例如虛擬機器的提供者)。在您輸入資源的位置之前，請使用此命令來確認該位置是否支援此資源類型。

    因為位置清單可能很長，而且有多個提供者，所以您可以先使用工具來檢查提供者和位置，再使用尚未使用的位置。下列指令碼使用 **jq** 探索 Azure 虛擬機器之資源提供者可用的位置。

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**.這個命令會在您使用範本和範本參數之前，先驗證它們。請輸入自訂或資源庫範本，以及您打算使用的範本參數值。

    下列範例顯示如何驗證範本和任何必要參數；Azure CLI 會提示您輸入所需的參數值。

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## 取得資訊來修正 Azure CLI 部署問題

- **azure group log show <resource group>**：此命令會取得每個資源群組部署記錄中的項目。如果出現問題，便會首先檢查部署記錄。

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

使用 **--last-deployment** 選項，只擷取最新部署的記錄。下列指令碼會使用 **--json** 選項和 **jq**，來搜尋部署失敗的記錄。

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **-verbose 和 -vv 選項**：使用 **--verbose** 選項，將模式設為詳細資訊，以顯示作業在 `stdout` 上經歷的步驟。如果完整要求歷程包括 **--verbose** 所啟用的步驟，請使用 **-vv** 選項。這些訊息通常會提供任何失敗原因的重要線索。

- **您的 Azure 認證未設定或已過期**：若要在 Azure CLI 工作階段中重新整理認證，只需要輸入 `azure login`。如需驗證錯誤的協助，請確定您[已正確地設定 Azure CLI](../xplat-cli-connect.md)。

## 防止 Windows PowerShell 中的錯誤

AzureResourceManager 模組包含可協助您防止錯誤的 Cmdlet。


- **Get-AzureLocation**：此 Cmdlet 會取得支援每種資源類型的位置。在您輸入資源的位置之前，請使用此 Cmdlet 來確認該位置是否支援此資源類型。


- **Test-AzureResourceGroupTemplate**：使用範本和範本參數之前，請先進行測試。請輸入自訂或資源庫範本，以及您打算使用的範本參數值。此 Cmdlet 會測試範本是否達到內部的一致性，以及您的參數值設定是否與範本相符。

## 取得資訊來修正 Windows PowerShell 中的部署問題

- **Get-AzureResourceGroupLog**：此 Cmdlet 會取得每個資源群組部署記錄中的項目。如果出現問題，便會首先檢查部署記錄。

- **詳細資訊和偵錯**：AzureResourceManager 模組中的 Cmdlet 會呼叫實際執行工作的 REST API。若要查看 API 傳回的訊息，請將 $DebugPreference 變數設為 "Continue"，並在您的命令中使用 Verbose 命令參數。這些訊息通常會提供任何失敗原因的重要線索。

- **您的 Azure 認證未設定或已過期**：若要在 Windows PowerShell 工作階段中重新整理認證，請使用 Add-AzureAccount Cmdlet。發行設定檔中的認證無法滿足 AzureResourceManager 模組中 Cmdlet 的需求。

## 驗證、訂用帳戶、角色和配額問題

可能有一個或多個問題防止部署成功，包含驗證和授權以及 Azure Active Directory。不論您如何管理 Azure 資源群組，用來登入您帳戶的身分識別都必須是 Azure Active Directory 物件或服務主體 (也稱為工作或學校帳戶或組織識別碼)。

但是 Azure Active Directory 可讓您或您的系統管理員最精確地控制哪些身分識別可以存取哪些資源。如果您的部署失敗，請檢查要求本身是否有驗證或授權問題，以及檢查您資源群組的部署記錄檔。您可能會發現，當您擁有某些資源的權限時，就沒有其他資源的權限。使用 Azure CLI，您可以利用 `azure ad` 命令檢查 Azure Active Directory 租用戶和使用者 (如需完整的 Azure CLI 命令清單，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](azure-cli-arm-commands.md))。

部署達到預設配額 (可能是根據資源群組、訂用帳戶、帳戶以及其他範圍) 時，也可能會發生問題。確認您有可正確部署的資源。如需完整配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)。

若要檢查您自己訂用帳戶的核心配額，您應該使用 Azure CLI 中的 `azure vm list-usage` 命令以及 PowerShell 中的 `Get-AzureVMUsage` Cmdlet。以下顯示 Azure CLI 中的命令，並說明免費試用帳戶的核心配額為 4：

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4    
    info:    vm list-usage command OK

如果您嘗試部署的範本會在上述訂用帳戶內，於美國西部區域中建立 4 個以上的核心，則會發生部署錯誤，該錯誤看起來如下 (可能是在入口網站中，也可能是調查部署記錄時發現的)：

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

在這些情況下，您應該移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [AZURE.NOTE]請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理核心。如果您需要在任何具有存取權限的區域部署 30 個核心，就應該要求在所有區域中擁有 30 個資源管理核心。<!-- --> 舉例來說，若要更明確地了解核心，您應該使用下列命令來要求適當的配額數目，這個命令可以使用管線傳送到 **jq** 以進行 json 剖析。 <!-- --> Azure 提供者會顯示 Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }
     

## Azure CLI 和 PowerShell 模式問題

您可能發生過，使用資源管理 API 或 Azure 入口網站，卻看不到使用服務管理 API 或使用傳統入口網站所部署的 Azure 資源。請務必使用用來建立資源的相同管理 API 或入口網站來管理資源。如果資源消失，請使用其他管理 API 或入口網站確認資源是否可用。

## Azure 資源提供者註冊問題

資源是由資源提供者所管理，而且啟用帳戶或訂用帳戶可以使用特定提供者。如果您可以使用某個提供者，則也必須註冊該提供者才能使用。Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但非全部。

若要使用 Azure CLI 查看是否註冊提供者以供使用，請使用 `azure provider list` 命令 (下列是截斷的輸出範例)。

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

此外，如果您想要更多關於提供者的資訊，包括它們的區域可用性，請輸入 `azure provider list --json`。下列程式碼只會選取清單中的第一個項目來檢視：

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


如果提供者需要註冊，請使用 `azure provider register <namespace>` 命令，其中 *namespace* 值來自先前的清單。

## 了解自訂範本的部署何時成功

如果使用您所建立的範本，請務必了解 Azure 資源管理系統會在成功從部署傳回所有提供者時報告部署成功。這表示已部署所有範本項目，供您使用。

不過，這不一定表示您的資源群組**作用中且準備好供使用者使用**。例如，大多數的部署都會要求部署下載升級、等候其他升級、非範本資源，或是安裝複雜的指令碼或 Azure 不知道的某個其他可執行活動，因為它不是提供者正在追蹤的活動。在這些情況下，可能需要一些時間，您的資源才能用於實際使用。因此，您應該預期部署狀態成功一段時間後，才能使用部署。

不過，建立自訂範本的自訂指令碼 (例如，使用 [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/))，即可防止 Azure 報告部署成功，而此自訂指令碼知道如何監視整個部署以了解系統是否準備就緒，並只在使用者可以與整個部署互動時才會順利傳回。如果您想要確保最後才執行您的延伸模組，請在範本中使用 **dependsOn** 屬性。您可以在[這裡](https://msdn.microsoft.com/library/azure/dn790564.aspx)看到範例。

## 合併範本

您有時可能需要合併兩個範本，也可能需要從父系範本啟動子系範本。完成方式是在主版範本內使用部署資源來部署子範本。


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## 跨資源群組

通常您可能會想要使用將部署範本之目前資源群組外部的資源。此行為的最常見情況是在替代資源群組中使用儲存體帳戶或虛擬網路。這通常是必要作業，因此，刪除含有虛擬機器的資源群組，並不會刪除 vhd blob 或多個資源群組所使用的 VNet。下列範例顯示如何輕鬆地使用外部資源群組中的資源：


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }

## 後續步驟

若要精通範本的建立，請閱讀[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)，並捲動 [AzureRMTemplates 儲存機制](https://github.com/azurermtemplates/azurermtemplates)以取得可部署的範例。**dependsOn** 屬性範例是[具有輸入 NAT 規則範本的負載平衡器](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json)。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=July15_HO2-->