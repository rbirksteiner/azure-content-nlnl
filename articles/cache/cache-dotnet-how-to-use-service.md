<properties 
	pageTitle="如何使用 Azure 受管理快取服務" 
	description="了解如何改善與 Azure 受管理快取服務的 Azure 應用程式的效能" 
	services="cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="sdanie"/>

# 如何使用 Azure 受管理快取服務

本指南示範如何開始使用 **Azure 受管理的快取服務**。這些範例均以 C# 程式碼撰寫，並使用 .NET API。涵蓋的案例包括**建立和設定快取**、**設定快取用戶端**、**新增和移除快取中的物件、將 ASP.NET 工作階段狀態儲存在快取中**，以及**使用快取啟用 ASP.NET 頁面輸出快取**。如需使用 Azure 快取的詳細資訊，請參閱[後續步驟][]一節。

>如需為應用程式選擇正確 Azure 快取提供項目的相關指引，請參閱[適合我的 Azure 快取提供項目][]。

<a name="what-is"></a>
## 何謂 Azure 受管理快取服務？

Azure 受管理快取服務是一種分散式、記憶體內的可擴充解決方案，藉由提供超快速的資料存取，可讓您建置可高度擴充和回應的應用程式。

「Azure 受管理快取服務」包括下列功能：

-   為工作階段狀態及頁面輸出快取所預先建置的 ASP.NET 提供者，可讓 Web 應用程式加速，卻不必修改應用程式的程式碼。
-   快取任何可序列化的受管理物件，例如：CLR 物件、資料列、XML 和二進位資料。
-   讓 Azure 和 Windows Server AppFabric 的開發模型保持一致。

受管理的快取服務可讓您存取由 Microsoft 管理的專用安全快取。您可從 Azure 網站、Web 和背景工作角色以及虛擬機器上執行的 Azure 應用程式，存取使用受管理的快取服務建立的快取。

受管理的快取服務有三個階層：

-	基本 - 大小從 128MB 到 1GB 的快取
-	標準 - 大小從 1GB 到 10GB 的快取
-	高級 - 大小從 5GB 到 150GB 的快取

每一個階層都有不同的功能和定價。本指南稍後將探討這些功能，如需定價的詳細資訊，請參閱[快取定價詳細資料][]。

本指南提供開始使用受管理的快取服務的概觀。如需本入門指南涵蓋範圍外之功能的詳細資訊，請參閱 [Azure 受管理快取服務概觀][]。

<a name="getting-started-cache-service"></a>
## 開始使用快取服務

開始使用受管理的快取服務相當簡單。若要開始，請佈建並設定快取。接著，設定快取用戶端，以便它們可以存取快取。一旦設定了快取用戶端，就可以開始使用它們。

-	[建立快取][]
-	[設定快取][]
-	[設定快取用戶端][]

<a name="create-cache"></a>
## 建立快取

您可以使用 PowerShell Cmdlet，在受管理的快取服務中建立快取執行個體。

>一旦使用 PowerShell Cmdlet 來建立受管理的快取服務執行個體，就能在 [Azure 管理入口網站][]中檢視這些執行個體。

若要建立受管理的快取服務執行個體，請開啟 Azure PowerShell 命令視窗。

>如需安裝和使用 Azure PowerShell 的指示，請參閱[如何安裝和設定 Azure PowerShell][]。

叫用 [Add-AzureAccount][] Cmdlet，然後輸入與您帳戶相關聯的電子郵件地址和密碼。叫用 [Add-AzureAccount][] Cmdlet 之後，預設會選擇並顯示一個訂用帳戶。若要變更訂用帳戶，請叫用 [Select-AzureSubscription][] Cmdlet。

>如果您已使用帳戶的憑證來設定 Azure PowerShell，那麼就可以跳過此步驟。如需將 Azure PowerShell 與 Azure 帳戶連接的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][]。

預設會選擇並顯示一個訂用帳戶。若要變更訂用帳戶，請叫用 [Select-AzureSubscription][] Cmdlet。

叫用 [New-AzureManagedCache][] Cmdlet，然後指定快取的名稱、區域、快取提供項目和大小。

在 [名稱] 中輸入要用於快取端點的子網域名稱。端點必須是介於 6 到 20 個字元之間的字串、僅包含小寫數字和字母，而且必須以字母開頭。

針對 [位置]，請指定快取的地區。為獲得最佳效能，請在與快取用戶端應用程式相同的區域中建立快取。

快取大小是由 [SKU] 和 [記憶體] 共同決定。受管理的快取服務共有下列三個階層：

-	基本 - 大小從 128MB 到 1GB 的快取 (每次增量 128MB)，具有一個預設具名快取
-	標準 - 大小從 1GB 到 10GB 的快取 (每次增量 1GB)，支援通知和最多 10 個具名快取
-	高級 - 大小從 5GB 到 150GB 的快取 (每次增量 5GB)，支援通知、高可用性和最多 10 個具名快取

請選擇符合應用程式需求的 [SKU] 和 [記憶體]。請注意，有些快取功能 (例如通知和高可用性) 只有特定快取提供項目才會提供。如需選擇應用程式最適用的快取提供項目和大小的詳細資訊，請參閱[快取提供項目][]。

 在下列範例中，基本 128MB 快取是透過位於美國中南部地理區域、名稱為 contosocache 所建立。

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>如需建立快取時可使用之參數和值的完整清單，請參閱 [New-AzureManagedCache][] Cmdlet 文件。

叫用 PowerShell Cmdlet 之後，可能需要數分鐘的時間建立快取。建立快取之後，您的新快取的狀態將是 `Running`，且適用於預設設定，還可透過 [Azure 管理入口網站][]檢視和設定。若要自訂快取的組態，請參閱下面的[設定快取][]一節。

您可以在 Azure PowerShell 視窗中監視建立進度。快取可供使用之後，[New-AzureManagedCache][] Cmdlet 會顯示快取資訊，如下列範例所示。

	PS C:\> Add-AzureAccount
	VERBOSE: Account "user@domain.com" has been added.
	VERBOSE: Subscription "MySubscription" is selected as the default subscription.
	VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
	VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
	PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
	VERBOSE: Intializing parameters...
	VERBOSE: Creating prerequisites...
	VERBOSE: Verify cache service name...
	VERBOSE: Creating cache service...
	VERBOSE: Waiting for cache service to be in ready state...


	Name     : contosocache
	Location : South Central US
	State    : Active
	Sku      : Basic
	Memory   : 128MB



	PS C:\>




<a name="enable-caching"></a>
## 設定快取

管理入口網站中快取的 [**設定**] 索引標籤可供您設定快取的選項。每個快取都有**預設**具名快取，而且標準和高級快取提供項目最多支援 9 個額外的具名快取，總共可支援 10 個。每個具名快取都有自己的選項集，可讓您以高度彈性方式設定快取。

![NamedCaches][NamedCaches]

若要建立具名快取，請將新快取的名稱鍵入 [**名稱**] 方塊、指定所需選項、按一下 [**儲存**]，然後按一下 [**是**] 進行確認。若要取消任何變更，請按一下 [捨棄]。

## 到期原則和時間 (分鐘) ##

[**到期原則**] 與 [**時間 (分鐘)**] 設定會共同決定快取項目何時到期。總共有三種類型的到期原則：**絕對**，**滑動**和**永不**。

如果指定 [**絕對**]，則在將項目新增至快取時，[**時間 (分鐘)**] 指定的到期間隔即會開始。在過了 [時間 (分鐘)] 所指定的間隔之後，項目即過期。

如果指定 [**滑動**]，則每次在快取中存取項目時，即會重設 [**時間 (分鐘)**] 指定的到期間隔。直到從前次存取項目後經過了 [時間 (分鐘)] 所指定的間隔時，項目才會過期。

當指定 [**永不**] 時，[**時間 (分鐘)**] 必須設為 **0**，項目才會永不過期。

[**絕對**] 為預設到期原則，其 [**時間 (分鐘)**] 的預設設定為 10 分鐘。具名快取中每個項目的到期原則都是固定的，但是可對每個項目自訂 [時間 (分鐘)]，方法為使用 **Add** 和 **Put** 超載來採用逾時參數。

如需收回和到期原則的詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 到期與收回][]。

## 通知 ##

快取通知可讓應用程式在快取叢集發生各種快取作業時，收到非同步通知。快取通知也提供使本機快取的物件自動失效的功能。如需詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 通知][]。

>標準和高級快取提供項目才會提供通知功能，基本快取提供項目並不會提供。如需詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 快取提供項目][]。

## 高可用性 ##

啟用高可用性時，會由每一個新增至快取的項目組成備份複本。如果項目的主要複本發生非預期的失敗，則仍有備份複本可用。

依定義，使用高可用性時，每個快取項目所需的記憶體數量會翻倍。請在進行容量計劃時將此記憶體影響納入考量。如需詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 高可用性][]。

>高級快取提供項目才會提供高可用性功能，基本或標準快取提供項目並不會提供。如需詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 快取提供項目][]。

## 收回 ##

為了保持快取內可用的記憶體容量，因此會支援最近最少使用的 (LRU) 收回。當記憶體耗用量超出記憶體臨界值時，無論物件是否過期，都會從記憶體收回物件，直到記憶體壓力舒緩為止。依預設會啟用收回功能。如果停用收回功能，則在到達容量上限時，將不會從快取中收回項目，Put 和 Add 作業將會因此失敗。

如需收回和到期原則的詳細資訊，請參閱 [Windows Azure 快取服務 (預覽) 到期與收回][]。

一旦設定了快取，您可以設定快取用戶端，以允許存取快取。

<a name="NuGet"></a>
## 設定快取用戶端

您可從 Azure 網站、Web 和背景工作角色以及虛擬機器上執行的 Azure 應用程式，存取使用受管理的快取服務建立的快取。我們有提供 NuGet 套件，此套件可簡化快取用戶端應用程式的組態作業。

若要使用 Caching NuGet 套件設定用戶端應用程式，請在 [**方案總管**] 中的專案上按一下滑鼠右鍵，然後選擇 [**Manage NuGet Packages**]。

![NuGetPackageMenu][NuGetPackageMenu]

在 [**線上搜尋**] 文字方塊中鍵入 **WindowsAzure.Caching**，然後從結果中選取 **Windows** **Azure** **快取**。按一下 [**安裝**]，然後按一下 [**我接受**]。

![NuGetPackage][NuGetPackage]

NuGet 會執行數項工作：它會將必要的組態新增至應用程式的組態檔，以及新增必要的組件參考。若為雲端服務專案，也會將快取用戶端診斷層級設定新增至雲端服務的 ServiceConfiguration.cscfg 檔案。

>針對 ASP.NET Web 專案，Cache NuGet 封裝也會將兩個已標成註解的區段新增至 web.config 中。第一個區段可讓工作階段儲存在快取中，第二個區段則可讓 ASP.NET 頁面輸出快取處理。如需詳細資訊，請參閱「[做法：將 ASP.NET 工作階段狀態儲存在快取中]」和「[做法：將 ASP.NET 頁面輸出快取儲存在快取中][]」。

NuGet 封裝會將下列組態元素新增至應用程式的 web.config 或 app.config 中。**dataCacheClients** 區段和 **cacheDiagnostics** 區段會新增至 **configSections** 元素之下。如果沒有 **configSections** 元素，則會建立一個，做為 **configuration** 元素的子項。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
  
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


這些新的區段包括 **dataCacheClients** 元素的參考，而此元素也會新增至 **configuration** 元素。

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


在新增組態後，請在剛新增的組態中取代下列兩個項目。

1. 將 **[Cache role name or Service Endpoint]** 取代為管理入口網站中儀表板上顯示的端點。

	![端點][Endpoint]

2. 取消註解 securityProperties 區段，並將 **[Authentication Key]** 取代為驗證金鑰，您可在管理入口網站從快取儀表板按一下 [**管理金鑰**]，來找到此驗證金鑰。

	![AccessKeys][AccessKeys]

>必須適當地配置這些設定，否則用戶端將無法存取快取。

針對「雲端服務」專案，NuGet 封裝也會將 **ClientDiagnosticLevel** 設定新增至 ServiceConfiguration.cscfg 中快取用戶端角色的 **ConfigurationSettings** 中。下列範例是 ServiceConfiguration.cscfg 檔案中的 **WebRole1** 區段，該檔案的 **ClientDiagnosticLevel** 為 1，亦即預設的 **ClientDiagnosticLevel**。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>用戶端診斷層級會設定為每一個快取用戶端收集的快取診斷資訊的層級。如需詳細資訊，請參閱[關於 Windows Azure 快取服務 (預覽) 的 ClientDiagnosticLevel][]。

NuGet 套件也會新增下列組件的參考：

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

如果您的專案為 Web 專案，則也會新增下列組件參考：

-	Microsoft.Web.DistributedCache.dll

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>
## 使用快取

本節中的步驟描述如何利用快取執行常見工作。

-	[做法：建立 DataCache 物件][]
-   [做法：從快取新增和擷取物件][]
-   [做法：指定快取中物件的到期時間][]
-   [做法：將 ASP.NET 工作階段狀態儲存在快取中][]
-   [做法：將 ASP.NET 頁面輸出快取儲存在快取中][]

<a name="create-cache-object"></a>
## 做法：建立 DataCache 物件

為了能夠以程式設計方式使用快取，您需要快取的參考。新增下面這一行至您想要從中使用 Azure 快取之檔案的頂端：

    using Microsoft.ApplicationServer.Caching;

>即使在安裝 Caching NuGet 套件，新增必要參考之後，如果 Visual Studio 還是無法辨識 using 陳述式中的類型，請確定專案的目標設定檔為 .NET Framework 4 或更高版本，並務必選取其中一個未指定「**用戶端設定檔**」的設定檔。如需設定快取用戶端的指示，請參閱[設定快取用戶端][]。

有兩種方式可建立 **DataCache** 物件。第一種方式為僅建立 **DataCache**，並傳入所需快取的名稱。

    DataCache cache = new DataCache("default");

一旦 **DataCache** 具現化，您就可以依下列幾節所述使用它，與快取互動。

若要使用第二種方式，請使用預設建構函式，在應用程式中建立新的 **DataCacheFactory** 物件。這會導致快取用戶端使用組態檔中的設定。呼叫新 **DataCacheFactory** 執行個體的 **GetDefaultCache** 方法，傳回 **DataCache** 物件，或呼叫 **GetCache** 方法，並傳入快取的名稱。這些方法會傳回 **DataCache** 物件，然後可以用來以程式設計方式存取快取。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## 做法：從快取新增和擷取物件

若要將項目新增至快取，可以使用 **Add** 方法或 **Put** 方法。**Add** 方法會將指定的物件新增至快取，並以索引鍵參數值建立索引。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

如果具有相同索引鍵的物件已在快取中，則將擲回 **DataCacheException**，並隨附下列訊息：

> ErrorCode:SubStatus：正在嘗試利用快取中現有的索引鍵建立物件。快取只會接受唯一的物件索引鍵值。

若要擷取具有特定索引鍵的物件，可以使用 **Get** 方法。如果物件存在，則會加以傳回，但如果不存在，則會傳回 null。

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

**Put** 方法會將具有指定索引鍵的物件新增至快取 (如果它不存在) 或取代物件 (如果它存在)。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 做法：指定快取中物件的到期時間

依預設，快取中的項目會在放入快取十分鐘後到期。您可在管理入口網站中快取的 [設定] 索引標籤上，於 [**時間 (分鐘)**] 設定中配置此值。

![NamedCaches][NamedCaches]

總共有三種類型的**到期原則**：**永不**，**絕對**，和**滑動**。這些類型會設定如何使用 [**時間 (分鐘)**] 來決定到期時間。預設 [**到期類型**] 為 [**絕對**]，表示當項目放入快取時，項目到期時間的倒數計時器即會開始倒數。一旦過了項目的指定時間量，項目即到期。如果指定 [**滑動**]，則每次存取快取中的項目，就會重設項目的到期倒數時間，而且項目將不會到期，直到自前次存取後已過了指定的時間量。如果指定 [**永不**]，則 [**時間 (分鐘)**] 必須設為 **0**，項目將不會到期，而且只要它們留在快取中就會一直有效。

如果想要讓逾時間隔比快取屬性中設定的值還長或短，則可使用超載的 **Add** 和 **Put** 來採用 **TimeSpan** 參數，在新增或更新快取中的項目時指定特定持續時間。在下列範例中，字串 **value** 會新增至快取並以 **item** 建立索引，逾時值為 30 分鐘。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

若要檢視快取項目的剩餘逾時間隔，可使用 **GetCacheItem** 方法來擷取 **DataCacheItem** 物件，此物件包含快取項目的相關資訊，包括剩餘逾時間隔。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 做法：將 ASP.NET 工作階段狀態儲存在快取中

Azure 快取的工作階段狀態提供者為 ASP.NET 應用程式的程序外儲存體機制。此提供者可讓您將工作階段狀態儲存在 Azure 快取中，而不是記憶體內或 SQL Server Database 中。若要使用快取工作階段狀態提供者，請先設定快取，然後依照「[開始使用受管理的快取服務][]」中所述的方法透過 Cache NuGet 封裝設定 ASP.NET 應用程式的快取功能。在安裝 Caching NuGet 套件時，它會在 web.config 新增已標成註解的區段，其中包含讓 ASP.NET 應用程式對 Azure 快取使用工作階段狀態提供者所需的組態。

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>在安裝 Caching NuGet 套件之後，如果 web.config 未包含這個已標成註解的區段，請確實從 [NuGet Package Manager 安裝][] (英文) 中安裝最新的 NuGet Package Manager，然後解除安裝並重新安裝套件。

若要對 Azure 快取啟用工作階段狀態提供者，請將指定的區段取消註解。預設快取是在提供的片段中指定。若要使用不同快取，請在 **cacheName** 屬性中指定所需的快取。

如需有關使用「受管理的快取服務」工作階段狀態提供者的詳細資訊，請參閱「[Azure 快取的工作階段狀態提供者][]」。

<a name="store-page"></a>
## 做法：將 ASP.NET 頁面輸出快取儲存在快取中

Azure 快取的輸出快取提供者為輸出快取資料的程序外儲存體機制。此資料特別適用於完整 HTTP 回應 (頁面輸出快取)。提供者插入 ASP.NET 4 中導入的新輸出快取提供者擴充點。若要使用輸出快取提供者，請先設定快取叢集，然後依照「[開始使用受管理的快取服務][]」中所述的方法使用 Cache NuGet 封裝設定 ASP.NET 應用程式進行快取。在安裝 Caching NuGet 套件時，它會在 web.config 新增下列已標成註解的區段，其中包含讓 ASP.NET 應用程式對 Azure 快取使用輸出快取提供者所需的組態。

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>在安裝 Caching NuGet 套件之後，如果 web.config 未包含這個已標成註解的區段，請確實從 [NuGet Package Manager 安裝][] (英文) 中安裝最新的 NuGet Package Manager，然後解除安裝並重新安裝套件。

若要對 Azure 快取啟用輸出快取提供者，請將指定的區段取消註解。預設快取是在提供的片段中指定。若要使用不同快取，請在 **cacheName** 屬性中指定所需的快取。

將 **OutputCache** 指示詞新增至每一個您要快取輸出的頁面。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

在此範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。如需可用選項的詳細資訊，請參閱 [@ OutputCache][]。

如需對 Azure 快取使用輸出快取提供者的詳細資訊，請參閱[建立 Windows Azure 快取服務 (預覽) 頁面輸出快取提供者][]。

<a name="next-steps"></a>
## 後續步驟

了解「受管理的快取服務」的基本概念之後，請參考下列連結以了解如何執行更複雜的快取工作。

-   請參閱 MSDN 參考資料：[受管理的快取服務][]
-	了解如何移轉至受管理的快取服務：[移轉至 Azure 受管理的快取服務][]
-   查看範例：[受管理的快取服務範例][]

<!-- INTRA-TOPIC LINKS -->
[後續步驟]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[開始使用受管理的快取服務]: #getting-started-cache-service
[建立快取]: #create-cache
[設定快取]: #enable-caching
[設定快取用戶端]: #NuGet
[Working with Caches]: #working-with-caches
[做法：建立 DataCache 物件]: #create-cache-object
[做法：從快取新增和擷取物件]: #add-object
[做法：指定快取中物件的到期時間]: #specify-expiration
[做法：將 ASP.NET 工作階段狀態儲存在快取中]: #store-session
[做法：將 ASP.NET 頁面輸出快取儲存在快取中]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  
   
<!-- LINKS -->
[Azure 管理入口網站]: https://manage.windowsazure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Azure 快取的工作階段狀態提供者]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[建立 Windows Azure 快取服務 (預覽) 頁面輸出快取提供者]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure 受管理快取服務概觀]: http://go.microsoft.com/fwlink/?LinkId=320830
[受管理的快取服務]: http://go.microsoft.com/fwlink/?LinkId=320830
[@ OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
[關於 Windows Azure 快取服務 (預覽) 的 ClientDiagnosticLevel]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager 安裝]: http://go.microsoft.com/fwlink/?LinkId=240311
[快取定價詳細資料]: http://www.windowsazure.com/pricing/details/cache/
[Management Portal]: https://manage.windowsazure.com/
[Windows Azure 快取服務 (預覽) 快取提供項目]: http://go.microsoft.com/fwlink/?LinkId=317277
[快取提供項目]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Windows Azure 快取服務 (預覽) 到期與收回]: http://go.microsoft.com/fwlink/?LinkId=317278
[Windows Azure 快取服務 (預覽) 高可用性]: http://go.microsoft.com/fwlink/?LinkId=317329
[Windows Azure 快取服務 (預覽) 通知]: http://go.microsoft.com/fwlink/?LinkId=317276
[移轉至 Azure 受管理的快取服務]: http://go.microsoft.com/fwlink/?LinkId=317347
[受管理的快取服務範例]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[如何安裝和設定 Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[適合我的 Azure 快取提供項目]: http://msdn.microsoft.com/library/azure/dn766201.aspx
 

<!---HONumber=62-->