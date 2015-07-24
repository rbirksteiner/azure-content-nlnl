<properties pageTitle="Azure 搜尋服務管理 REST API 版本 2014-07-31-Preview" description="Azure 搜尋管理 REST API：版本 2014-07-31-Preview" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="05/21/2015" ms.author="heidist" />

# 管理 API：版本 2014-07-31-Preview

本文說明 **2014-07-31-Preview** 版本的 Azure 搜尋管理 REST API。它之後已取代為 MSDN 上的公開上市版本：[Azure 搜尋管理 REST API 2015-02-28](https://msdn.microsoft.com/library/dn832684.aspx)。________________________________________

## 服務管理作業

Azure 搜尋服務管理 REST API 提供以程式設計方式透過入口網站存取許多可用的功能，讓管理員可以自動進行下列作業：

- 建立或刪除 Azure 搜尋服務。
- 建立、變更或擷取 `api-keys` 以定期自動變更系統管理金鑰，用來驗證搜尋資料作業。
- 調整 Azure 搜尋服務級別以回應查詢量或儲存空間需求的變更。

若要完全以程式設計方式管理您的服務，您會需要兩個 API：Azure 搜尋服務管理 REST API，再加上一般 [Azure 資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)。資源管理員 API 用於非服務特定的一般用途作業，例如查詢訂用帳戶資料、列出地理位置等。若要在您的訂用帳戶建立和管理 Azure 搜尋服務，請確定您的 HTTP 要求包含資源管理員端點、訂用帳戶識別碼、提供者 (在此情況下為 Azure 搜尋服務) 及搜尋服務特定的作業。

[開始使用 Azure 搜尋服務管理 REST API](http://go.microsoft.com/fwlink/p/?linkID=516968) 為範例程式碼的逐步解說，示範應用程式組態和服務管理作業。此範例應用程式發出要求給 Azure 資源管理員 API 以及 Azure 搜尋服務管理 API，讓您了解如何將利用此兩種 API 的應用程式組合為緊密結合的應用程式。

### 端點

服務管理作業的端點為 Azure 資源管理員的 URL：`https://management.azure.com`。

請注意所有管理 API 呼叫都必須包含訂用帳戶 ID 和 API 版本。

### 版本

目前的 Azure 搜尋服務管理 REST API 版本為 `api-version=2014-07-31-Preview`。這是此 API 的唯一版本。

### 驗證和存取管理

Azure 搜尋服務管理 REST API 為 Azure 資源管理員的擴充功能並且共享相依性。因此，Active Directory 是 Azure 搜尋服務管理的先決條件。所有來自用戶端程式碼的系統管理要求都必須使用 Azure Active Directory 驗證，之後此要求才會傳達給資源管理員。

請注意，如果您的應用程式程式碼在索引或文件上處理*服務管理作業*以及*資料作業*，則您要對每一個 Azure 搜尋服務 API 使用兩種驗證方法：

- 有鑑於資源管理員上的相依性，服務和金鑰管理需要用到 Active Directory 來驗證。
- 對 Azure 搜尋服務端點的資料要求會在要求標頭使用 `api-key`，例如建立索引或搜尋文件。如需驗證資料要求的相關資訊，請參閱 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

記載於[開始使用 Azure 搜尋管理 REST API](http://go.microsoft.com/fwlink/p/?linkID=516968) 的範例應用程式，示範對於每種類型作業的驗證技術。使用 Active Directory 來設定用戶端應用程式的指示包含於「開始使用」中。

Azure 資源管理員的存取控制會使用內建擁有者、參與者和讀取者角色。根據預設，所有服務管理員都是擁有者角色的成員。如需詳細資訊，請參閱 [Azure Preview 入口網站中的角色型存取控制](../role-based-access-control-configure.md)。


### API 的摘要

作業會包含下列 API：

- <a name="CreateService">建立搜尋服務</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="GetService">取得搜尋服務</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="ListService">列出搜尋服務</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2014-07-31-Preview`

- <a name="DeleteService">刪除搜尋服務</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="UpdateService">更新搜尋服務</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="ListAdminKey">列出系統管理金鑰</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2014-07-31-Preview`

- <a name="RegenAdminKey">重新產生系統管理金鑰</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2014-07-31-Preview`

- <a name="CreateQueryKey">建立查詢金鑰</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2014-07-31-Preview`

- <a name="ListQueryKey">列出查詢金鑰</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2014-07-31-Preview`

- <a name="DeleteQueryKey">刪除查詢金鑰</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2014-07-31-Preview`

<a name="ServiceOps"></a>
## 服務作業

您可以向 Azure 訂用帳戶發出 HTTP 要求，對 Azure 搜尋服務佈建或取消佈建。由這些作業啟用的案例包含建立自訂管理工具，或是打造端對端生產或開發環境 (從建立服務開始，乃至於擴展索引)。同樣的，設計和銷售雲端解決方案的方案廠商也可能想要以自動化、可重複的方式，來佈建服務給每位新客戶。

**服務上的作業**

服務相關的選項包含下列 API：

- <a name="CreateService">建立搜尋服務</a>
- <a name="GetService">取得搜尋服務</a>
- <a name="ListService">列出搜尋服務</a>
- <a name="DeleteService">刪除搜尋服務</a>
- <a name="UpdateService">更新搜尋服務</a>


<a name="CreateService"></a>
## 建立搜尋服務

**建立搜尋服務**作業會以指定的參數來佈建新的搜尋服務。這項 API 還可以用於更新現有的服務定義。

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

### 要求 URI 參數

`subscriptionId`：必要。Azure 使用者的 `subscriptionID`。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。服務名稱只能包含小寫字母、數字或連字號，但不能將連字號用在前兩個或最後一個字元，也不能包含連續的連字號，而且長度必須介於 2 到 15 個字元。由於所有名稱最後都會變成 <name>.search.windows.net，所以服務名稱必須是全域唯一的。訂用帳戶和資源群組之內或之間的任兩個服務名稱都不能相同。服務名稱建立後就不能再變更。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。


### 要求標頭

`Content-Type`：必要。設定此標頭為 application/json。

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。


### 要求本文

{ "位置": "搜尋服務位置", "標記": { "金鑰": "值", ... }, "屬性": { "SKU": { "名稱": "免費 | 標準 | 標準 2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

### 要求本文參數

`location`：必要。一個受支援且已註冊的 Azure 地理區域 (例如：美國西部、美國東部、東南亞等)。請注意，一旦建立資源位置後就不能再變更。

`tags`：選用。描述此資源的金鑰值組清單。這些標記可用來在資源群組間檢視資源，以及對資源分組。對於一項資源可提供最多十個標記。每個標記的金鑰都不得多於 128 個字元，值則不得多於 256 個字元。

`sku`：必要。有效值為 `free` 和 `standard`。`standard2` 也一樣有效，但只當您的 Azure 訂用帳戶由 Microsoft 支援所啟用時才可使用。`free` 會在共用叢集佈建服務。`standard` 會在專用的叢集中佈建服務。您只能在免費定價層建立一項搜尋服務。其他服務必須在標準定價層建立。根據預設，一項服務會以一個分割區和一個複本來建立。其他分割區和複本會依據搜尋單位計價。如需詳細資訊，請參閱[限制條件](http://msdn.microsoft.com/library/azure/dn798934.aspx)。一旦服務建立後，就不能變更 `sku`。

`replicaCount`：選用。預設值為 1。有效值包含 1 到 6。僅當 `sku` 為 `standard` 時才有效。

`partitionCount`：選用。預設值為 1。有效值包含 1、2、3、4、6 或 12。僅當 `sku` 為 `standard` 時才有效。


### Response 

當服務定義更新後，會傳回 HTTP 200 (確定)。當新的服務建立後，會傳回 HTTP 201 (已建立)。


### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。


### 回應本文

對於 HTTP 200 和 201，此回應本文包含服務定義。
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


### 回應本文項目

`id`：此 ID 為這項搜尋服務的 URL (不包含主機名稱和配置)。

`name`：搜尋服務的名稱。

`location`：一個受支援且已註冊的 Azure 地理區域 (例如：美國西部、美國東部、東南亞等)。

`tags`：描述資源的金鑰值組清單，用來在資源群組間檢視資源，以及對資源分組。

`sku`：表示定價層。有效值包含：

- `free`：共用叢集。
- `standard`：專用叢集。
- `standard2`：只在 Microsoft 支援指導之下使用。 

`replicaCount`：表示該服務有多少複本。有效值包含 1 到 6。

`partitionCount`：表示該服務有多少分割區。有效值包含 1、2、3、4、6 或 12。

`status`：呼叫作業時的搜尋服務狀態。有效值包含：

- `running`：搜尋服務已建立。
- `provisioning`：正在佈建搜尋服務。
- `deleting`：正在刪除搜尋服務。
- `degraded`：搜尋服務已降級。當叢集發生不一定會使服務無法正確運作的錯誤時，則產生這些值。
- `disabled`：搜尋服務已停用。在此狀態下，此服務將拒絕所有 API 要求。
- `error`：搜尋服務處於錯誤狀態。 

**請注意**：如果您的服務處於 `degraded`、`disabled` 或 `error` 狀態，這代表 Azure 搜尋服務團隊正主動調查潛在問題。根據已佈建的搜尋單位數目，在這些狀態下的專用服務仍需收費。

`statusDetails`：狀態的詳細資料。

`provisioningState`：表示目前佈建此服務的狀態。有效值包含：

- `succeeded`：佈建已順利完成。
- `provisioning`：正在佈建本服務。
- `failed`：本服務無法佈建。 

佈建是一種過渡狀態，當服務容量正在建立時發生。設定容量後，`provisioningState` 變更為「成功」或「失敗」。用戶端應用程式可以輪詢佈建狀態 (建議的輪詢間隔為 30 秒，最多 1 分鐘)，可藉由使用**取得搜尋服務**作業查看作業何時完成。如果您正在使用免費服務，這個值通常會做為「成功」而直接在呼叫中返回，以建立服務。這是因為免費服務使用早已設定的容量。

<a name="GetService"></a>
## 取得搜尋服務

**取得搜尋服務**作業會傳回指定搜尋服務的屬性。請注意這並不會傳回系統管理金鑰。請使用**取得系統管理金鑰**作業來擷取系統管理金鑰。

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

### 要求 URI 

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

### 要求標頭 

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。


### 要求本文 

無。


### 回應狀態碼 

若成功，則為 HTTP 200 (確定)。


### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

### 回應本文 

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

### 回應本文項目

`id`：此 ID 為這項搜尋服務的 URL (不包含主機名稱和配置)。

`name`：搜尋服務的名稱。

`location`：資源的位置。這會是一個受支援且已註冊的 Azure 地理區域 (例如：美國西部、美國東部、東南亞等)。

`tags`：標記為描述此資源的金鑰值組清單。這些標記可用來在資源群組間檢視資源，以及對資源分組。

`sku`：表示定價層。有效值包含：

- `free`：共用叢集。
- `standard`：專用叢集。
- `standard2`：只在 Microsoft 支援指導之下使用。

`replicaCount`：表示該服務有多少複本。有效值為 1 到 6。

`partitionCount`：表示該服務有多少分割區。有效值包含 1、2、3、4、6 或 12。

`status`：呼叫作業時的搜尋服務狀態。有效值包含：

- `running`：搜尋服務已建立。
- `provisioning`：正在佈建搜尋服務。
- `deleting`：正在刪除搜尋服務。
- `degraded`：搜尋服務已降級。當叢集發生不一定會使服務無法正確運作的錯誤時，則產生這些值。
- `disabled`：搜尋服務已停用。在此狀態下，此服務將拒絕所有 API 要求。
- `error`：搜尋服務處於錯誤狀態。 
 
**請注意**：如果您的服務處於 `degraded`、`disabled` 或 `error` 狀態，這代表 Azure 搜尋服務團隊正主動調查潛在問題。根據已佈建的搜尋單位數目，在這些狀態下的專用服務仍需收費。
 
`statusDetails`：狀態的詳細資料。

`provisioningState`：包含有效值。

- `succeeded`：佈建成功。
- `provisioning`：正在佈建本服務。
- `failed`：佈建失敗。


<a name="ListService"></a>
## 列出搜尋服務

**列出服務**作業會傳回指定資源群組訂用帳戶中的所有搜尋服務清單。此作業會傳回服務定義，但沒有系統管理 API 金鑰。請使用**取得系統管理金鑰**作業來擷取系統管理金鑰。

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2014-07-31-Preview
    
### 要求 URI 參數 

`subscriptionId`：必要。Azure 使用者的 `subscriptionID`。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

### 要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

若成功，則狀態碼為 HTTP 200 (確定)。

### 回應標頭

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。


### 回應本文

回應本文是服務的清單，做為 JSON 陣列傳回，其中每個服務都遵循**取得搜尋服務**作業中的格式。

請注意，`nextLink` 欄位一律為 Null，因為目前版本並不支援分頁。這會以空值返回，以保留未來的相容性。

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
## 刪除服務 

**刪除服務**作業會刪除搜尋服務和搜尋資料，包含所有索引和文件。
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

**請注意：**管理員和開發人員要養成習慣，在從實際伺服器刪除應用程式資料前先行備份。在 Azure 搜尋服務中並沒有備份作業。如果您正使用索引當做應用程式的主要儲存體，您會需要使用搜尋作業來傳回索引中可外部儲存的所有資料。

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

###要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

對於 HTTP 200，回應本文將是空白。如果資源不存在，則 HTTP 200 (確定) 才是正確的回應。

您可以使用**取得搜尋服務 API** 來輪詢刪除服務的狀態。我們建議的輪詢間隔為 30 秒到 1 分鐘。

###回應標頭

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

無。

<a name="UpdateService"></a>
## 更新服務

**更新服務**作業會變更搜尋服務組態。有效的變更包含變更標記、分割區或複本數量，這些會將搜尋單位做為計費事件加入至或從您的服務單位移除。如果您嘗試將分割區減少至低於儲存現有搜尋服務主體所需容量，將會產生錯誤而封鎖作業。需耗費一段時間來變更服務拓撲。重新定位資料以及在資料中心設定或卸除叢集需要時間。

請注意，您不能變更名稱、位置以及 SKU。變更任一屬性都需要建立新的服務。

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

或者，您可使用 PUT。

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的 `subscriptionID`。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

###要求標頭

`Content-Type`：必要。設定此標頭為 application/json。

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###要求本文參數

`tags`：選用。描述此資源的金鑰值組清單。這些標記可用來在資源群組間檢視資源，以及對資源分組。對於一項資源可提供最多十個標記。每個標記的金鑰都不得多於 128 個字元，值則不得多於 256 個字元。

`replicaCount`：選用。預設值為 1。有效值包含 1 到 6。僅當 `sku` 為 `standard` 時才有效。

`partitionCount`：選用。預設值為 1。有效值包含 1、2、3、4、6 或 12。僅當 `sku` 為 `standard` 時才有效。

###Response

如果作業成功，則傳回 HTTP 200 (確定)。您可以使用**取得搜尋服務 API** 來輪詢更新服務的狀態。我們建議的輪詢間隔為 30 秒到 1 分鐘。


### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

### 回應本文 

此回應本文包含更新的服務定義。如需範例，請參閱**取得搜尋服務**。


<a name="KeyOps"></a>
## 金鑰作業

驗證 Azure 搜尋服務時需要兩項資訊：搜尋服務 URL 和 API 金鑰。API 金鑰會在服務建立時產生，並且可在服務佈建完成後，依照需求重新產生。API 金鑰可分為兩種：

- 系統管理金鑰：授與所有作業的存取權 (每個服務最多 2 項)。
- 查詢金鑰：僅限驗證查詢要求 (每個服務最多 50 項)。

以程式設計方式管理 Azure 搜尋服務系統管理和查詢金鑰的功能讓您可建立自訂工具、定期更換金鑰 (如同例行安全性的最佳做法)、當員工離職時更換金鑰，或是當使用指令碼或程式設計方式來部署解決方案時，可在服務佈建過程中產生並取得金鑰。

查詢金鑰可取得、建立和刪除。系統管理金鑰作業限制為取得或重新產生現有金鑰值。刪除系統管理金鑰可能讓您永久鎖定此服務，使得該作業不能使用。

金鑰是由數字和大寫字母組成的隨機字串組合。API 金鑰只能用於已建立並可能要定期變更的服務 (如果您採用換用金鑰策略做為安全性最佳做法的話)。

請務必將 API 金鑰視為敏感性資料，特別是系統管理金鑰。任何取得您系統管理金鑰的人，都可在您的索引中刪除或讀取資料。

**金鑰作業**

金鑰相關作業包含下列 API：

- <a name="ListAdminKey">列出系統管理金鑰</a>
- <a name="RegenAdminKey">重新產生系統管理金鑰</a>
- <a name="CreateQueryKey">建立查詢金鑰</a>
- <a name="ListQueryKey">列出查詢金鑰</a>
- <a name="DeleteQueryKey">刪除查詢金鑰</a>


<a name="ListAdminKey"></a>
## 列出系統管理金鑰 

**列出系統管理金鑰**作業傳回指定搜尋服務的主要和次要系統管理金鑰。因為此動作會傳回讀寫金鑰，所以使用 POST 方法。

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2014-07-31-Preview

此服務會建立系統管理金鑰。一律有主要和次要兩種金鑰。您可以重新產生這些金鑰，但您不能刪除。

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

`listAdminKeys`：必要。這項動作會擷取搜尋服務的主要和次要系統管理金鑰。

###要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

如果作業成功，則傳回 HTTP 200 (確定)。

### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## 重新產生系統管理金鑰 

**重新產生系統管理金鑰**作業會刪除和重新產生主要或次要金鑰。您一次只能重新產生一個金鑰。在重新產生金鑰的時候，請考慮您要如何維護此服務的存取。次要金鑰讓您可在更換主要金鑰時具有可供使用的金鑰。每項服務一律有兩種金鑰。您可以重新產生金鑰，但不能刪除，也不能在沒有金鑰時執行服務。
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2014-07-31-Preview

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。
	
`regenerateKey`：必要。此作業會指定重新產生主要或次要系統管理金鑰。

`keyKind`：必要。指定要產生哪種金鑰。有效值包含：

- `primary`
- `secondary`

###要求標頭

`Content-Type`：必要。設定此標頭為 application/json。

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

如果作業成功，則傳回 HTTP 200 (確定)。

### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###回應本文項目

`primaryKey`：主要系統管理金鑰 (若已重新產生)。

`secondaryKey`：次要系統管理金鑰 (若已重新產生)。



<a name="CreateQueryKey"></a>
## 建立查詢金鑰

**建立查詢金鑰**作業會產生新的搜尋服務查詢金鑰。您最多可以對每個服務建立 50 個查詢金鑰。

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2014-07-31-Preview

###要求 URI 參數###

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

`createQueryKey`：必要。此動作會建立搜尋服務的查詢金鑰。

`name`：必要。新的金鑰名稱。

###要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

如果作業成功，則回應狀態碼為 HTTP 200 (確定)。

### 回應標頭

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

    {
      "name": "name of key",
      "key": "api key"
    }


###回應本文項目

`name`：查詢金鑰的名稱。

`key`：查詢金鑰的值。

<a name="ListQueryKey"></a>
## 列出查詢金鑰 


**列出查詢金鑰**作業會傳回指定搜尋服務的查詢金鑰。查詢金鑰用於傳送查詢 API (唯讀) 呼叫給搜尋服務。每個服務可有最多 50 個查詢金鑰。

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2014-07-31-Preview

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。
	
`listQueryKeys`：必要。此動作會擷取搜尋服務的查詢金鑰。

###要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

如果作業成功，則傳回的回應狀態碼為 HTTP 200 (確定)。

### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###回應本文項目

`name`：查詢金鑰的名稱。

`key`：查詢金鑰的值。


<a name="DeleteQueryKey"></a>
## 刪除查詢金鑰 

**刪除查詢金鑰**作業會刪除指定查詢金鑰。查詢金鑰為選用的，並用於唯讀的查詢。

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2014-07-31-Preview

相對於系統管理金鑰，查詢金鑰無法重新產生。重新產生查詢金鑰的程序為刪除再重新建立。

###要求 URI 參數

`subscriptionId`：必要。Azure 使用者的訂用帳戶 ID。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`resourceGroupName`：必要。使用者訂用帳戶內的資源群組名稱。您可以從 Azure 資源管理員 API 或入口網站取得這個值。

`serviceName`：必要。指定資源群組內的搜尋服務名稱。如果您不知道服務名稱，則您可以使用列出搜尋服務 (Azure 搜尋服務 API) 取得清單。

`api-version`：必要。指定用於此要求的通訊協定版本。當有可用的較新版本時，您可以指定每個要求所使用的版本，以取得版本特定的行為。

`deleteQueryKey`：必要。此動作會刪除搜尋服務現有的查詢金鑰。

`key`：必要。要刪除的金鑰。

###要求標頭

`x-ms-client-request-id`：選用。用戶端產生之可識別此要求的 GUID 值。如果指定，這會包含於回應資訊做為對應要求的方式。

###要求本文

無。

###Response

若成功，則回應狀態碼為 HTTP 200 (確定)。

### 回應標頭 

`Content-Type`：此標頭一律設為 application/json。

`x-ms-request-id`：目前作業的唯一識別碼，由此服務所產生。

###回應本文

無。


 

<!---HONumber=July15_HO1-->