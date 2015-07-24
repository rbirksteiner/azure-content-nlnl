<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## 搭配使用 Azure CLI 與 Azure 資源管理員 (ARM)

您必須具有 Azure 的帳戶，才能搭配使用 Azure CLI 與資源管理員命令和範本，使用資源群組來部署 Azure 資源和工作負載 (理所當然)。如果您沒有帳戶，可以取得[此處免費試用的 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE]如果您還沒有 Azure 帳戶，但是擁有 MSDN 訂用帳戶的訂用帳戶，您可以藉由啟用[此處的 MSDN 訂閱者權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)來取得免費的 Azure 點數，或者您可以使用免費帳戶。這其中一種方式將可用來存取 Azure。

### 步驟 1：確認 Azure CLI 版本

若要針對命令式指令和 ARM 範本使用 Azure CLI，您至少必須擁有版本 0.8.17。若要確認您的版本，請輸入 `azure --version`。您應該會看到如下的內容：

    $ azure --version
    0.8.17 (node: 0.10.25)

如果您需要更新 Azure CLI 版本，請參閱 [Azure CLI](https://github.com/Azure/azure-xplat-cli)。

### 步驟 2： 確認您和 Azure 搭配使用的是工作或學校身分識別

如果您使用的是 [Azure Active Directory 租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)或[服務主體名稱](https://msdn.microsoft.com/library/azure/dn132633.aspx)，可以只使用 ARM 命令模式(這些也稱為*組織識別碼*)。

若要查看您是否擁有一個組織識別碼，請輸入 `azure login` 並在出現提示時使用您的工作或學校使用者名稱和密碼來登入。當您確實擁有一個時，應該會看到如下的內容：

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

如果您未看到此內容，就必須使用您的 Microsoft 帳戶身分識別來建立新的租用戶 (或服務主體)(這通常是個人 MSDN 訂閱或免費試用版訂閱的情況)。 若要從您使用 Microsoft 識別碼建立的 Azure 帳戶建立工作或學校識別碼，請參閱[將 Azure AD 目錄關聯至新的 Azure 訂用帳戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)。如果您認為應該已經擁有組織識別碼，則您可能需要洽詢為您建立帳戶的人員。

### 步驟 3：選擇您的 Azure 訂用帳戶

如果您在 Azure 帳戶中只擁有一個訂用帳戶，Azure CLI 預設會將其本身關聯至該訂用帳戶。如果您擁有一個以上的訂用帳戶，就需要輸入 `azure account set <subscription id or name> true` 來選取要使用的訂用帳戶，其中 _subscription id or name_ 是您想要在目前工作階段中使用的訂用帳戶識別碼或訂用帳戶名稱。

您應該會看到如下的內容：

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### 步驟 4：使 Azure CLI 處於 ARM 模式

若要搭配使用 Azure 資源管理 (ARM) 模式和 Azure CLI，請輸入 `azure config mode arm`。您應該會看到如下的內容：

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]您可以輸入 `azure config mode asm`，切換回使用 Azure 服務管理命令。

<!---HONumber=58_postMigration-->