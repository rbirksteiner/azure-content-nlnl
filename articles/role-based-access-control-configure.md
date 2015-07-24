<properties 
	pageTitle="Microsoft Azure 入口網站中的角色型存取控制" 
	description="說明角色型存取控制的運作和設定方式" 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="05/05/2015" 
	ms.author="justinha"/>

# Microsoft Azure 入口網站中的角色型存取控制 

我們在 Microsoft Azure 入口網站中新增了對角色型存取控制 (RBAC) 的支援，希望協助組織輕鬆又準確地滿足其存取管理需求。此[部落格文章](http://go.microsoft.com/fwlink/?LinkId=511576)將快速介紹各項功能，讓您有初步了解。本主題詳細說明各項概念，並涵蓋其他使用案例。


## Azure 中的 RBAC
                                                                   
每一個 Azure 訂閱都與 Azure Active Directory 相關聯。透過 Microsoft Azure 管理入口網站或是 Azure 資源管理員 API 來存取訂閱資源的使用者與服務，都必須先向 Azure Active Directory 進行驗證。

![][1]

Azure 角色型存取控制可讓您將角色指派給訂閱、資源群組或個別資源層級中的 Azure AD 使用者、群組與服務，授予其適當的存取權限。被指派的角色可定義 Azure 資源上的使用者、群組或服務的存取等級。

### 角色

角色是指可以在 Azure 資源上執行的動作集合。當使用者或服務受指派為內含某項動作的角色，便可在 Azure 資源上執行該動作。如需內建角色的清單與他們的 [動作] 和 [非動作] 屬性，請參閱[內建角色](#builtinroles)。

### 角色指派

您可以在 Azure 資源上為 Azure AD 使用者與服務指派適當的角色，以授予其存取權限。

#### Azure AD 安全性主體

您可以將角色指派給下列的 Azure AD 安全性主體類型：

+ **使用者**：您可以將角色指派給 Azure AD 內與 Azure 訂閱相關聯的組織使用者。您也可以透過 Invite 動作為使用者指派 Azure 入口網站內的某個角色，以將角色指派給外部 Microsoft 帳戶使用者 (例如 joe@outlook.com)。將角色指派給外部 Microsoft 帳戶使用者會導致 Azure AD 中為該角色建立來賓帳戶。當目錄停用此來賓帳戶時，外部使用者將無法存取該使用者已經獲得存取授權的任何 Azure 資源。
+ **群組**：您可以將角色指派給 Azure AD 安全性群組。當使用者成為具有存取權限的群組成員時，系統會自動為該使用者授予資源存取權限。但當系統將該使用者從群組中移除時，該使用者也會自動喪失對資源的存取權限。將角色指派給群組並將使用者新增至這些群組，而非將角色直接指派給使用者，是透過群組來管理存取權限的最佳方式。Azure RBAC 不允許將角色指派給散佈清單。將角色指派給群組的能力，可讓組織將其現有的存取控制模型從內部部署目錄延伸到雲端，這樣您便可重複使用之前建立來控制存取內部部署環境的安全性群組來控制 Azure 入口網站中的資源存取權限。如需從內部部署目錄同步使用者與群組的不同選項詳細資訊，請參閱[目錄整合](http://technet.microsoft.com/library/jj573653.aspx)。Azure AD Premium 同時提供了[委派的群組管理功能](http://msdn.microsoft.com/library/azure/dn641267.aspx)相關文章，讓您得以建立並管理可以從 Azure AD 委派給非管理員使用者的群組。
+ **服務主體**：目錄中會以服務主體來代表服務身分。這些身分負責向 Azure AD 驗證，並會安全地與彼此通訊。您可以透過 Azure module for Windows PowerShell，將角色指派給代表該服務的 Azure AD 服務主體，為服務授予 Azure 資源的存取權限。 

#### 資源範圍

您無須將存取權限授予整個訂閱。角色可以同時指派給資源群組與個別資源。在 Azure RBAC 中，資源會繼承來自其父系資源的角色指派。因此如果使用者、群組或服務只獲得訂閱中某項資源群組的存取權限，他們便只能存取其中的資源群組或資源，而非該訂閱內的其他資源群組。再舉另一例說明，安全性群組可以新增至資源群組的讀取者角色，但可以新增至該資源群組內某個資料庫的參與者角色。

![][2]

## RBAC 與訂閱共同管理員的並存機制

訂閱管理員與共同管理員將繼續擁有對 Azure 入口網站與管理 API 的完整存取權限。在 RBAC 模型中，它們會獲得訂閱層級的擁有者角色。不過，只有 Azure 入口網站和 Azure 資源管理員 API 支援新的 RBAC 模型。獲得 RBAC 角色指派的使用者與服務，無法存取 Azure 管理入口網站與服務管理 API。將使用者新增到 Azure 入口網站中訂閱的擁有者角色之後，並不會使該使用者變成完整的 Azure 入口網站裡的訂閱共同管理員。

如果您希望將存取權限授予 Azure 資源的使用者 (但該資源尚無法透過 Azure 入口網站管理)，您應該透過 Azure 管理入口網站，將他們新增至訂閱共同管理員中。服務匯流排與雲端服務都是目前尚無法透過 RBAC 來管理的資源範例。

## 管理授權與資料作業

角色型存取控制僅支援對 Azure 入口網站的 Azure 資源與 Azure 資源管理員 API 進行管理作業。並非所有 Azure 資源的資料作業都可以透過 RBAC 授權。舉例來說，儲存體帳戶的建立/讀取/更新/刪除作業可以透過 RBAC 控制，但是儲存體帳戶內的 Blob 或資料表的建立/讀取/更新/刪除作業，卻還無法透過 RBAC 來控制。同理，SQL DB 的建立/讀取/更新/刪除作業可以透過 RBAC 來控制，但是 DB 內的 SQL 資料表之建立/讀取/更新/刪除作業卻還無法透過 RBAC 來控制。

## 如何新增與移除存取

讓我們舉例說明組織內的資源擁有者如何管理存取權限。在此案例中，有多位使用者正在進行使用 Azure 資源建立的各項測試與生產專案。您想要遵照最佳實作來授予存取權限。使用者現在應該對所需的所有資源具備存取權限，但沒有額外的存取權限。您想要重複利用之前在流程中的所有投資與工具，以便使用內部部署 Active Directory 中所建立的安全性群組。以下各節涵蓋如何針對這些資源設定存取權限：

* [新增存取](#add-access)
* [移除存取](#remove-access)
* [為外部使用者新增或移除存取權限](#add-or-remove-access-for-external-user)

### 新增存取

以下摘要說明存取需求以及如何在 Azure 中設定這些存取需求。

使用者/群組 | 存取需求 | 存取角色與範圍	
------------- | -------------  | ------------
Jill Santos 的整個團隊 | 讀取所有 Azure 資源 | 將代表 Jill Santos 團隊的 AD 群組新增至 Azure 訂閱的讀取者角色
Jill Santos 的整個團隊 | 建立並管理 Test 資源群組中的所有資源 | 將代表 Jill Santos 團隊的 AD 群組新增至 Test 資源群組的參與者角色
Brock | 建立並管理 Prod 資源群組中的所有資源 | 將 Brock 新增至 Prod 資源群組的參與者角色


首先，讓我們為訂閱裡的所有資源新增讀取存取權限。按一下 [瀏覽 > 全部 > 訂閱]。

![][3]

按一下 [*您的訂閱名稱* ** > 讀取者 > 新增**]。從使用者與群組清單中，選取或是輸入 Active Directory 群組的名稱。

![][4]

接著將相同團隊新增至 Test 資源群組的參與者角色中。按一下資源群組以開啟其屬性分頁。在 [角色] 下方按一下 [參與者 > 新增]，然後輸入團隊名稱。

![][5]

若要將 Brock 新增至 Prod 資源群組的參與者角色中，依序按一下資源群組與 [參與者 > 新增]，然後輸入 Brock 名稱。

![][6]

角色指派也可以透過 Microsoft Azure module for Windows PowerShell 進行管理。以下舉例說明如何使用 New-AzureRoleAssignment Cmdlet (而非透過入口網站)，新增 Brock 帳戶：

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

如需使用 Windows PowerShell 來新增並移除存取權限的詳細資訊，請參閱[使用 Windows PowerShell 管理角色型存取控制](role-based-access-control-powershell.md) (英文)。

### 移除存取

您也可以輕易地移除指派。假設您想要針對名為 TestDB 的資源群組，從讀取者角色中移除名為 Brad Adams 的使用者。開啟資源群組刀鋒視窗，並按一下 [讀取者 > Brad Adams > 移除]。

![][7]

以下舉例說明如何使用 Remove-AzureRoleAssignment Cmdlet 移除 Brad Adams：

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 為外部使用者新增或移除存取權限

目錄內的 [設定] 索引標籤內含可控制外部使用者存取權限的選項。這些選項只可以由目錄全域管理員在完整 Azure 入口網站的 UI (沒有 Windows PowerShell 或 API 方法) 中進行變更。若要在 Azure 入口網站中開啟 [**設定**] 索引標籤，請按一下 [**Active Directory**]，然後按一下目錄的名稱。

![][10]

接著您可以編輯選項來控制外部使用者的存取權限。

![][8]

根據預設，來賓無法列舉目錄裡的內容，因此他們不會看到 [成員清單] 裡的任何使用者或群組。他們可以輸入使用者的完整電子郵件地址來搜尋使用者，然後授予存取權限。以下是預設的來賓限制集合：

- 他們無法列舉目錄中的使用者與群組。
- 他們可以查看有限的使用者詳細資料 (前提是他們知道使用者的電子郵件地址)。
- 他們可以查看有限的群組詳細資料 (但必須知道群組名稱)。

來賓得以查看使用者或群組有限之詳細資料的能力，可方便他們邀請其他人並查看與他們協同合作的人員詳細資料。

讓我們逐一示範整個流程，為外部使用者新增存取權限。我們將把一位外部使用者新增到 TestDB 資源群組中的相同讀取者角色上，以便該使用者能夠協助進行偵錯。開啟資源群組刀鋒視窗，按一下 [讀取者 > 新增 > 邀請]，然後輸入您想要新增的使用者電子郵件地址。

![][9]

當您新增外部使用者時，目錄會自動建立一位來賓。此後，系統會將該名來賓新增至群組或從群組中將之移除，或者您可以從角色中加以個別新增或移除，就像您對其他任何目錄使用者所做的處理一樣。

您也可以從任何角色中移除來賓，就像您移除其他使用者一樣。從資源角色中移除來賓，並不會從目錄中一併移除該來賓。
 
## 使用角色型存取控制的已知問題

如果您在使用角色型存取控制功能時碰到問題，請參閱[為角色型存取控制進行疑難排解](role-based-access-control-troubleshooting.md)，了解與該問題相關的任何已知問題。


## 內建角色

Azure 角色型存取控制會隨附三個內建的角色，供您指派給使用者、群組與服務。您無法修改內建角色定義。在即將發佈的 Azure RBAC 版本中，您將能從可在 Azure 資源上執行的可用動作清單中撰寫一系列動作，藉此定義自訂角色。

按一下對應的連結，請參閱角色定義的 [動作] 和 [非動作] 屬性。[動作] 屬性會指定 Azure 資源上允許的動作。動作字串可以使用萬用字元。角色定義的 [非動作] 屬性可指定必須從允許的動作中排除的動作。


角色名稱 | 說明  	
------------- | -------------  
[API 管理服務參與者](#api-management-service-contributor) | 可讓您管理 API 管理服務，但無法存取它們。
[Application Insights 元件參與者](#application-insights-component-contributor) | 可讓您管理 Application Insights 元件，但無法存取它們。
[BizTalk 參與者](#biztalk-contributor) | 可讓您管理 BizTalk 服務，但無法存取它們。
[ClearDB MySQL DB 參與者](#cleardb-mysql-db-contributor) | 可讓您管理 ClearDB MySQL 資料庫，但無法存取它們。
[參與者](#contributor) | 參與者可以管理存取以外的所有內容。
[Data Factory 參與者](#data-factory-contributor) | 可讓您管理資料的處理站，但無法存取它們。
[Document DB 帳戶參與者](#document-db-account-contributor) | 可讓您管理 DocumentDB 帳戶，但無法存取它們。
[Intelligent Systems 帳戶參與者](#intelligent-systems-account-contributor) | 可讓您管理 Intelligent Systems 帳戶，但無法存取它們。
[NewRelic APM 帳戶參與者](#newrelic-apm-account-contributor) | 可讓您管理 New Relic 應用程式效能管理帳戶和應用程式，但無法存取它們。
[擁有者](#owner) | 擁有者可以管理所有項目，包括存取。
[讀取者](#reader) | 讀取者可以檢視所有項目，但是無法進行變更。
[Redis 快取參與者](#redis-cache-contributor) | 可讓您管理 Redis 快取，但無法存取它們。
[SQL DB 參與者](#sql-db-contributor) | 可讓您管理 SQL 資料庫，但無法存取它們。此外，您無法管理它們的安全性相關原則或它們的父系 SQL Server。
[SQL 安全性管理員](#sql-security-manager) | 可讓您管理 SQL Server 和資料庫的安全性相關原則，但無法存取它們。
[SQL Server 參與者](#sql-server-contributor) | 可讓您管理 SQL Server 和資料庫，但無法存取它們與它們的安全性相關原則。
[排程器工作集合參與者](#scheduler-job-collections-contributor) | 可讓您管理排程器工作集合，但無法存取它們。
[搜尋服務參與者](#search-service-contributor) | 可讓您管理搜尋服務，但無法存取它們。
[儲存體帳戶參與者](#storage-account-contributor) | 可讓您管理儲存體帳戶，但無法存取它們。
[使用者存取系統管理員](#user-access-administrator) | 可讓您管理 Azure 資源的使用者存取。
[虛擬機器參與者](#virtual-machine-contributor) | 可讓您管理虛擬機器，但無法存取它們，與它們所連接的虛擬網路或儲存體帳戶。
[虛擬網路參與者](#virtual-network-contributor) | 可讓您管理虛擬網路，但無法存取它們。
[Web 方案參與者](#web-plan-contributor) | 可讓您管理網站的 Web 方案，但無法存取它們。
[網站參與者](#website-contributor) | 可讓您管理網站 (不是 Web 方案)，但無法存取它們。


### API 管理服務參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>建立和管理 API 管理服務</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### Application Insights 元件參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>建立和管理 Insights 元件</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>建立和管理 Web 測試</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### BizTalk 參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>建立和管理 BizTalk 服務</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### ClearDB MySQL DB 參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>建立和管理 ClearDB MySQL 資料庫</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>*</td>
<td>建立和管理所有類型的資源</td>
</tr>
<tr>
<th colspan="2">非動作</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>無法建立角色和角色指派 </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>無法刪除角色和角色指派</td>
</tr>
</table>

### Data Factory 參與者

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>建立和管理資料處理站</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### Document DB 帳戶參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>建立及管理 DocumentDB 帳戶</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### Intelligent Systems 帳戶參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>建立及管理 Intelligent Systems 帳戶</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### NewRelic APM 帳戶參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>建立及管理 NewRelic 應用程式效能管理帳戶</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 擁有者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>*</td>
<td>建立和管理所有類型的資源</td>
</tr>
</table>

### 讀取者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>*/read</td>
<td>讀取所有類型的資源。無法讀取密碼。</td>
</tr>
</table>

### Redis 快取參與者

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>建立和管理 Redis 快取</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### SQL DB 參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>讀取 SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>建立和管理 SQL 資料庫</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>讀取訂閱資源</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>讀取資源群組資源</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
<tr>
<th colspan="2">非動作</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>無法管理 SQL 資料庫稽核原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>無法管理 SQL 資料庫連線原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>無法管理 SQL 資料庫資料遮罩原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>無法管理 SQL 資料庫安全性標準</td>
</tr>
</table>

### SQL 安全性管理員

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>讀取 SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>建立和管理 SQL Server 稽核原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>讀取 SQL 資料庫</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>建立和管理 SQL 資料庫稽核原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>建立和管理 SQL 資料庫連線原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>建立和管理 SQL 資料庫資料遮罩原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>建立和管理 SQL 資料庫安全性標準</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### SQL Server 參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>建立和管理 SQL Server</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>讀取訂閱資源</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>讀取資源群組資源</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
<tr>
<th colspan="2">非動作</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>無法管理 SQL Server 稽核原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>無法管理 SQL 資料庫稽核原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>無法管理 SQL 資料庫連線原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>無法管理 SQL 資料庫資料遮罩原則</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>無法管理 SQL 資料庫安全性標準</td>
</tr>
</table>

### 排程器工作集合參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>建立和管理排程器工作集合</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 搜尋服務參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>建立和管理搜尋服務</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 儲存體帳戶參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>建立及管理儲存體帳戶</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 使用者存取系統管理員

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>*/read</td>
<td>讀取所有類型的資源</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>建立及管理角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 虛擬機器參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>讀取儲存體帳戶</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>擷取儲存體帳戶金鑰</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>讀取虛擬網路</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>加入虛擬網路</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>讀取保留 IP 位址</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>連結至保留的 IP 位址</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>建立和管理雲端服務</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>建立和管理虛擬機器</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 虛擬網路參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>建立和管理虛擬網路</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### Web 方案參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>建立和管理 Web 方案</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>

### 網站參與者

<table style=width:100%">
<tr>
<th colspan="2">動作</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>讀取 Web 方案</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>加入 Web 方案</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>建立和管理網站</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>建立和管理網站憑證</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>讀取角色和角色指派</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>讀取資源群組</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>建立和管理資源群組部署</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>建立及管理警示規則</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>建立及管理支援票證</td>
</tr>
</table>


## 如何提供意見

請嘗試使用 Azure RBAC 並告知我們您的[意見](http://aka.ms/azurerbacfeedback)。


## 後續步驟

以下是可協助您使用角色型存取控制的其他資源：

+ [使用 Windows PowerShell 管理角色型存取控制](role-based-access-control-powershell.md)
+ [使用 Azure CLI 管理角色型存取控制](role-based-access-control-xplat-cli.md)
+ [為角色型存取控制進行疑難排解](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium 與 Basic](active-directory-editions.md)
+ [Azure 訂用帳戶如何與 Azure AD 產生關聯](active-directory-how-subscriptions-associated-directory.md)
+ 如需安全性群組的自我服務群組管理介紹，請參閱 [Active Directory 團隊部落格](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx) (英文)

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=62-->