<properties
	pageTitle="使用 Azure CLI for Mac、Linux 和 Windows 管理角色型存取控制"
	description="使用 Azure CLI 管理角色型存取控制"
	services=""
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tomfitz"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="tomfitz"/>

# 使用 Azure 命令列介面 (Azure CLI) 管理角色型存取控制#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-rbac.md" title="Azure CLI">Azure CLI</a></div>

在 Azure 入口網站和 Azure 資源管理員 API 中，以角色為基礎的存取控制 (RBAC) 可讓您以精細的層級管理訂用帳戶存取。在這項功能的協助下，您可以在特定範圍內將某些角色指派給 Active Directory 使用者、群組或服務主體，藉此授與存取權限。

在本教學課程裡，您將學習如何使用 Azure CLI 來管理 RBAC。本課程將帶您逐一了解建立與檢查角色指派的程序。

**預估完成時間：**15 分鐘

## 必要條件 ##

在使用 Azure CLI 來管理 RBAC 之前，您必須具備以下項目：

- Azure CLI 0.8.8 版或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[安裝](xplat-cli-install.md)。
- 另請閱讀以下教學課程，以熟悉如何在 Azure CLI 中設定和使用 Azure 資源管理員：[將 Azure CLI 和資源管理員搭配使用](xplat-cli-azure-resource-manager.md)

## 本教學課程內容 ##

* [連線到您的訂用帳戶](#connect)
* [查看現有的角色指派](#check)
* [建立角色指派](#create)
* [驗證權限](#verify)
* [後續步驟](#next)

## <a id="connect"></a>連線到您的訂用帳戶 ##

由於 RBAC 只能搭配 Azure 資源管理員使用，您必須切換至 Azure 資源管理員模式。請輸入：

    azure config mode arm

如需詳細資訊，請參閱[將 Azure CLI 和資源管理員搭配使用](xplat-cli-azure-resource-manager.md)

若要連線到您的 Azure 訂閱，請輸入：

    azure login -u <username>

在命令列提示字元中輸入 Azure 帳戶密碼 (僅支援工作或學校識別碼 -- 也稱為**組織識別碼**)。Azure CLI 會取得此帳戶的所有訂閱，並自行將第一個訂閱當做預設值。請注意，使用 RBAC 時，只有當您具備共同管理員身分或是具有某些角色指派時，才有取得訂閱項目的一些權限。

如果您有多個訂閱，而且想要切換至另一個訂閱，請輸入：

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

如需詳細資訊，請參閱 [Azure CLI 命令](azure-cli-arm-commands.md)。

## <a id="check"></a>查看現有的角色指派 ##

現在，我們來看看訂閱中已有哪些角色指派。輸入：

    azure role assignment list

如此會傳回訂閱中的所有角色指派。請注意以下兩點：

1. 您需要擁有訂閱層級的讀取權限。
2. 如果訂閱內含許多角色指派，您可能需要一點時間才能取得所有訂閱資料。

您也可以在特定範圍中，針對特定角色定義查看指派給特定使用者的現有角色指派。輸入：

    azure role assignment list -g group1 --mail <user's email> -o Owner

此指令會針對 AD 租用戶中具有資源群組「group1」之「Owner」角色指派的特定使用者，傳回該特定使用者的所有角色指派。角色指派可能來自兩個地方：

1. 針對資源群組使用者的「Owner」角色指派。
2. 針對資源群組父系使用者的「Owner」角色指派 (在此案例中為訂閱)，因為如果您在特定層級擁有訂閱，將對所有子系具有相同的權限。

此 Cmdlet 的所有參數都是選擇性參數。您可以組合運用這些參數與不同的篩選器，來檢查角色指派。

## <a id="create"></a>建立角色指派 ##

若要建立角色指派，您需要考慮以下事項：

- 要將角色指派給誰：您可以使用下列 Azure Active Directory Cmdlet 來查看 AD 租用戶中有哪些使用者、群組及服務主體。

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- 您想要指派什麼角色：您可以使用下列 Cmdlet 來查看支援的角色定義。

    `azure role list`

- 您想要指派的範圍：有三個範圍層級

    - 目前的訂用帳戶
    - 資源群組；若要取得資源群組清單，請輸入 `azure group list`
    - 資源；若要取得資源清單，請輸入 `azure resource list`

接下來，使用 `azure role assignment create` 來建立角色指派。例如：

 - 如此會在目前的訂閱層級中，為使用者建立讀者的角色指派。

    `azure role assignment create --mail <user's email> -o Reader`

- 如此會在資源群組層級中建立角色指派

    `PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`

- 如此會在資源層級中建立角色指派

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>驗證權限 ##

確認您的帳戶內含一些角色指派之後，就能執行下列指令來實際看到這些角色指派授予您的權限：

    PS C:\> azure group list
    PS C:\> azure resource list

這兩組 Cmdlet 只會傳回您擁有讀取權限的資源群組或資源。而且會同時顯示您擁有的權限。

接下來，當您嘗試執行 `azure group create` 等其他 Cmdlet 時，如果您沒有權限，將會出現拒絕存取錯誤。

## <a id="next"></a>接續步驟 ##

若要深入了解使用 Azure CLI 管理角色型存取控制的詳細資訊，請參閱下列相關主題：

- [安裝和設定 Azure CLI](xplat-cli-install.md)。
- [將 Azure CLI 和資源管理員搭配使用](xplat-cli-azure-resource-manager.md)
- [使用資源群組以管理您的 Azure 資源](resource-groups-overview.md)：深入了解在 Azure 管理入口網站中建立和管理資源群組。

<!---HONumber=July15_HO1-->