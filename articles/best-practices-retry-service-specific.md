<properties
   pageTitle="重試服務的特定指引 | Microsoft Azure"
   description="用於設定重試機制的服務特定指引。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# 重試服務的特定指引

![](media/best-practices-retry-service-specific/pnp-logo.png)

## 概觀

大多數的 Azure 服務與用戶端 SDK 皆包含重試機制，但各有不同，因為每個服務有不同的特性與需求，也因此系統會根據特定的服務調整每個重試機制。本指南摘要說明大多數 Azure 服務的重試機制功能，並提供一些資訊幫助您使用、調整，或擴充該服務的重試機制。

如需處理暫時性錯誤，及對服務與資源重試連接與作業的一般指引，請參閱[重試指引](best-practices-retry-general.md)。

下表摘要說明此指引中所述 Azure 服務的重試功能。

| **服務** | **重試功能** | **原則組態** | **範圍** | **遙測功能** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | 用戶端原生 | 程式設計 | 用戶端與個別作業 | TraceSource |
| **[使用 Entity Framework 的 SQL Database](#sql-database-using-entity-framework-6-retry-guidelines)** | 用戶端原生 | 程式設計 | 每個 AppDomain 全域 | 無 |
| **[使用 ADO.NET 的 SQL Database](#sql-database-using-ado-net-retry-guidelines)** | Topaz * | 宣告與程式設計 | 單一陳述式或程式碼區塊 | 自訂 |
| **[服務匯流排](#service-bus-retry-guidelines)** | 用戶端原生 | 程式設計 | 命名空間管理員、傳訊處理站及用戶端 | ETW |
| **[快取](#cache-redis-retry-guidelines)** | 用戶端原生 | 程式設計 | 用戶端 | TextWriter |
| **[DocumentDB](#documentdb-pre-release-retry-guidelines)** | 服務原生 | 不可設定 | 全域 | TraceSource |
| **[搜尋](#search-retry-guidelines)** | Topaz* (具有自訂偵測策略) | 宣告與程式設計 | 程式碼區塊 | 自訂 |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (具有自訂偵測策略) | 宣告與程式設計 | 程式碼區塊 | 自訂 |
*Topaz 是 <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a> 中所包含暫時性錯誤處理應用程式區塊的好記名稱。您可以將自訂偵測策略與 Topaz 搭配，用於大多數的服務，如本指引所述。Topaz 的預設策略顯示在本指引結尾的[暫時性錯誤處理應用程式區塊 (Topaz) 策略](#transient-fault-handling-application-block-topaz-strategies)一節中。請注意，區塊現在是開放原始碼的架構，且 Microsoft 未直接支援。

> [AZURE.NOTE]對於大多數的 Azure 內建重試機制而言，目前還無法為重試原則中所包含功能之外的不同類型錯誤或例外狀況套用不同的重試原則。因此在撰寫本文時的最佳指引是，設定一個可提供最佳平均效能和可用性的原則。微調原則的一種方法，就是分析記錄檔來判斷正在發生的暫時性錯誤類型。例如，如果大部分的錯誤與網路連線問題有關，您可能會嘗試立即重試，而非等待一段時間後才第一次重試。

## Azure 儲存體重試指引

Azure 儲存體服務包括資料表、Blob 儲存體、檔案，以及儲存體佇列。

### 重試機制

重試發生在個別的 REST 作業層級，是用戶端 API 實作不可或缺的一部分。用戶端儲存體 SDK 使用實作 [IExtendedRetryPolicy 介面](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx)的類別。

有不同的實作的介面。儲存體用戶端會選擇特別針對存取資料表、Blob 與佇列設計的原則。每個實作會使用不同的重試策略，這些策略基本上定義重試間隔與其他詳細資料。

內建類別支援線性 (固定延遲) 重試間隔和隨機指數重試間隔。當另一個處理序在更高的層級處理重試時，沒有重試原則可使用。但如果內建類別未提供您的特定需求，您可以實作您自己的重試類別。

如果您正在使用讀取權限異地備援儲存體服務位置 (RA-GRS)，替代重試會在主要與次要儲存體服務位置之間切換，要求的結果是可重試的錯誤。如需詳細資訊，請參閱 [Azure 儲存體備援選項](http://msdn.microsoft.com/library/azure/dn727290.aspx)。

### 原則組態 (Azure 儲存體)

以程式設計方式設定重試原則。一般程序是建立和填入 **TableRequestOptions**、**Useflatbloblisting**、**FileRequestOptions**，或 **QueueRequestOptions** 執行個體。

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

接著可以在用戶端上設定要求選項執行個體，涉及用戶端的所有作業都將使用指定的要求選項。

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

您可以將填入的要求選項類別執行個體做為參數傳遞至作業方法，以覆寫用戶端要求選項。

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

使用 **OperationContext** 執行個體指定當發生重試時與作業完成時，要執行的程式碼。此程式碼可以收集用於記錄與遙測中的作業相關資訊。

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

除了指出錯誤是否適合重試外，擴充的重試原則會傳回 **RetryContext** 以表示重試次數、最後一個要求的結果，以及下一個重試會在主要或次要位置發生 (請參閱下表以取得詳細資料)。**RetryContext** 物年的屬性可用於判定是否與何時嘗試重試。如需詳細資料，請參閱 [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx)。

下表顯示內建重試原則的的預設設定。

| **內容** | **設定** | **預設值** | **意義** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 資料表 / Blob / 檔案<br />QueueRequestOptions | MaximumExecutionTime<br /><br />ServerTimeout<br /><br /><br /><br /><br />LocationMode<br /><br /><br /><br /><br /><br /><br />RetryPolicy | 120 秒<br /><br />無<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />ExponentialPolicy | 要求的執行時間上限，包括所有的可能重試嘗試。<br />要求的伺服器逾時間隔 (以秒為單位捨入值)。如果未指定，則會對伺服器的所有要求使用預設值。通常，最好的選擇是略過此設定，讓系統使用伺服器預設值。<br />如果使用讀取權限異地備援儲存體 (RA-GRS) 複寫選項建立儲存體帳戶，您可以使用位置模式來指出哪個位置應接收要求。例如，如果指定 **PrimaryThenSecondary**，則一律先將要求傳送至主要位置。如果要求失敗，就會傳送到次要位置。<br />如需每個選項的詳細資訊，請參閱下列內容。 |
| 指數原則 | maxAttempt<br />deltaBackoff<br /><br /><br />MinBackoff<br /><br />MaxBackoff | 3<br />4 秒<br /><br /><br />3 秒<br /><br />30 秒 | 重試嘗試次數。<br />重試之間的倒退間隔。後續的重試嘗試將使用此時間範圍 (包含隨機元素) 的倍數。<br />加上使用 deltaBackoff 計算出的所有重試間隔。此值無法變更。<br />如果計算出的重試間隔大於 MaxBackoff，則使用 MaxBackoff。無法變更此值。 |
| 線性原則 | maxAttempt<br />deltaBackoff | 3<br />30 秒 | 重試嘗試次數。<br />重試之間的倒退間隔。 |

### 重試使用指引
當使用儲存體用戶端 API 存取 Azure 儲存體服務時，請考量下列指引：

* 使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空間的內建重試原則，此命名空間中的原則適合您的需求。在大多數的狀況中，這些原則已經足夠。
* 在批次作業、 背景工作或非互動式案例中使用 **ExponentialRetry** 原則。在這些案例中，您通常會允許服務有更多的時間復原，藉此增加作業最後成功的機會。
* 請考慮指定 **RequestOptions** 參數的 **MaximumExecutionTime** 屬性，以限制總執行時間，但在選擇逾時值時要考量到作業的類型與大小。
* 如果您需要實作自訂重試，請避免建立儲存體用戶端類別周圍的包裝函式。相反地，使用一些功能透過 **IExtendedRetryPolicy** 介面來擴充現有的原則。
* 如果您使用的是讀取權限異地備援儲存體 (RA-GRS)，您可以使用 **LocationMode** 指定如果主要存取失敗，重試嘗試將會存取存放區的次要唯讀複本。不過使用這個選項時，若尚未完成從主要存放區複寫，則必須確定應用程式可以成功使用過時的資料。

請考慮從重試作業的下列設定開始。這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試原則** | **設定** | **值** | **運作方式** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| 互動式、UI <br />或前景 | 2 秒 | 線性 | maxAttempt<br />deltaBackoff | 3<br />500 毫秒 | 嘗試 1 - 延遲 500 毫秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 背景<br />或批次 | 30 秒 | 指數 | maxAttempt<br />deltaBackoff | 5<br />4 秒 | 嘗試 1 - 延遲 ~3 毫秒<br />嘗試 2 - 延遲 ~7 毫秒<br />嘗試 3 - 延遲 ~15 毫秒 |

## 遙測

重試嘗試會記錄到 **TraceSource**。您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。您可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 將資料寫入記錄檔，使用 **EventLogTraceListener** 寫入 Windows 事件記錄檔，或使用 **EventProviderTraceListener** 將追蹤資料寫入 ETW 子系統。您也可以設定自動排清緩衝區，並設定所記錄事件的詳細資訊 (例如錯誤、警告、資訊和詳細資訊)。如需詳細資訊，請參閱[使用 .NET 儲存體用戶端程式庫在用戶端記錄](http://msdn.microsoft.com/library/azure/dn782839.aspx)。

作業會接收 **OperationContext** 執行個體，以公開用於附加自訂遙測邏輯的 **Retrying** 事件。如需詳細資訊，請參閱 [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx)。

## 範例 (Azure 儲存體)

下列程式碼範例示範如何建立兩個具有不同重試設定的 **TableRequestOptions** 執行個體，一個用於互動式要求，一個用於背景要求。然後此範例會在用戶端上設定這兩個重試原則，讓這些原則適用於所有要求，此外也在特定要求上設定互動式策略，讓該策略覆寫套用到用戶端的預設設定。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## 詳細資訊

- [Azure 儲存體用戶端程式庫重試原則建議](http://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [儲存體用戶端程式庫 2.0 – 實作重試原則](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## 使用 Entity Framework 6 的 SQL Database 重試指引

SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。Entity Framework 是物件關聯式對應程式，可讓 .NET 開發人員使用網域特有的物件來處理關聯式資料。有了 Entity Framework，開發人員便不再需要撰寫大多數必須撰寫的資料存取程式碼。

## 重試機制

透過名為[連接恢復/重試邏輯](http://msdn.microsoft.com/data/dn456835.aspx)的機制使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。如需完整的規格，請參閱 Codeplex 上的 [.NET Entity Framework 維基百科](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec)。重試機制的主要功能有：

* 主要抽象層是 **IDbExecutionStrategy** 介面。此介面會：
  * 定義同步和非同步 **Execute*** 方法。
  * 定義類別以直接使用或在資料庫內容上設定以做為預設策略、對應至提供者名稱，或對應至提供者名稱和伺服器名稱。在內容上設定時，重試是在個別資料庫作業層級發生的，其中可能有數個重試是針對指定的內容作業。
  * 定義何時要重試失敗的連接，以及如何重試。
* 它包含 **IDbExecutionStrategy** 介面的數個內建實作：
  * 預設值 - 無重試。
  * SQL Database 的預設值 (自動) - 沒有重試，但會檢查例外狀況，並以使用 SQL Database 策略的建議來包裝例外狀況。
  * SQL Database 的預設值 - 指數 (繼承自基底類別) 再加上 SQL Database 偵測邏輯。
* 它會實作包含隨機的指數退避策略。
* 內建的重試類別是可設定狀態，且非安全執行緒。不過，在目前的作業完成之後可重複執行這些類別。
* 如果超出指定的重試計數，則會以新的例外狀況包裝結果。它不會冒出目前的例外狀況。

## 原則組態 (使用 Entity Framework 6 的 SQL Database)

使用 Entity Framework 6.0 或更新版本存取 SQL Database 時，會提供重試支援。以程式設計方式設定重試原則。無法根據預先作業變更組態。

在內容上將策略設定為預設值時，您要指定一個會視需要建立新策略的功能。下列程式碼示範如何建立重試組態類別來延伸 **DbConfiguration** 基底類別。

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

當應用程式開啟時，您可以使用 **DbConfiguration** 執行個體的 **SetConfiguration** 方法將此指定為所有作業的預設重試策略。依預設，EF 將自動探索和使用組態類別。

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

您可以使用 **DbConfigurationType** 屬性來標註內容類別，以指定內容的重試組態類別。不過，如果您只有一個組態類別，EF 會使用它，而不需要標註內容。

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

如果您需要針對特定作業使用不同的重試策略，或針對特定作業停用重試，您可以建立組態類別來讓您透過在 **CallContext** 中設定旗標，來暫停或切換策略。組態類別可使用此旗標來切換策略，或停用您提供的策略並使用預設策略。如需詳細資訊，請參閱「使用重試執行策略限制 (EF6 之後的版本)」一頁上的[暫停執行策略](http://msdn.microsoft.com/dn307226#transactions_workarounds)。

針對個別作業使用特定重試策略的另一個方法，就是建立必要策略類別的執行個體，並透過參數提供所需的設定。然後叫用其 **ExecuteAsync** 方法。

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

使用 **DbConfiguration** 類別最簡單的方式，就是在與 **DbContext** 類別相同的組件中找到該類別。不過，這不適用在不同案例中需要相同的內容時，例如不同的互動式和背景重試策略。如果不同的內容在不同的 AppDomain 中執行，您可以使用內建支援在組態檔案中指定組態類別，或使用程式碼明確地設定組態類別。如果不同的內容必須在相同的 AppDomain 中執行，則需要自訂解決方案。

如需詳細資訊，請參閱[程式碼式組態 (EF6 之後版本)](http://msdn.microsoft.com/data/jj680699.aspx)。

下表顯示使用 EF6 時內建重試原則的預設設定。

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## 重試使用指引

使用 EF6 存取 SQL Database 時，請考慮下列指引：

* 選擇適當的服務選項 (共用或高階)。共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。如果需要可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。
* 不建議將固定間隔策略搭配 Azure SQL Database 使用。相反地，應使用指數退避策略，因為服務可能會過載，且延遲時間越長，復原所需的時間也越長。
* 定義連接時，為連接與命令逾時選擇合適的值。讓逾時以您的商務邏輯設計與直通測試為基礎。您可能需要隨時間修改此值，因為資料量或商務程序會改變。逾時值過短，會造成資料庫忙碌時連接永遠失敗。逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。逾時值是端對端延遲的元件，雖然您無法輕易判斷儲存內容時會執行幾個命令。您可以透過設定 **DbContext** 執行個體的 **CommandTimeout** 屬性，來變更預設逾時。
* Entity Framework 支援在組態檔中定義的重試組態。不過，為了讓 Azure 有最大的彈性，您應該考慮在應用程式內以程式設計方式建立組態。重試原則的特定參數 (例如重試次數與重試間隔) 可以儲存在服務組態檔中，並在執行階段用於建立適當的原則。這會讓設定需要應用程式重新啟動才會變更。

請考慮從重試作業的下列設定開始。您無法指定重試嘗試之間的延遲 (它是固定的，並產生成為指數序列)。您可以只指定最大值，如此處所示，除非您建立自訂重試策略。這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試原則** | **設定** | **值** | **運作方式** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| 互動式、UI <br />或前景 | 2 秒 | 指數 | MaxRetryCount<br />MaxDelay | 3<br />750 毫秒 | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 750 毫秒<br />嘗試 3 - 延遲 750 毫秒 |
| 背景<br />或批次 | 30 秒 | 指數 | MaxRetryCount<br />MaxDelay | 5<br />12 秒 | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~1 秒<br />嘗試 3 - 延遲 ~3 秒<br />嘗試 4 - 延遲 ~7 秒<br />嘗試 5 - 延遲 12 秒 |

> [AZURE.NOTE]端對端延遲目標會假設服務連接的預設逾時值。如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。

## 範例 (使用 Entity Framework 6 的 SQL Database)

下列程式碼範例會定義使用 Entity Framework 的簡單資料存取解決方案。它會設定特定的重試策略，做法是為會延伸 **DbConfiguration** 的 **BlogConfiguration** 類別定義執行個體。

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

使用 Entity Framework 重試機制的更多範例位於[連接恢復/重試邏輯](http://msdn.microsoft.com/data/dn456835.aspx)中。

## 詳細資訊

* [Azure SQL Database 效能和彈性指南 (英文)](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## 使用 ADO.NET 的 SQL Database 重試指引

SQL Database 是託管的 SQL 資料庫，有各種大小，並以標準 (共用) 與高階 (非共用) 服務提供。

### 重試機制

SQL Database 在使用 ADO.NET 存取時，沒有內建的重試支援。不過，要求的傳回碼可用來判斷要求失敗的原因。[Azure SQL Database 節流](http://msdn.microsoft.com/library/dn338079.aspx)一頁說明節流如何防止連接、特定情況下的傳回碼，以及如何處理這些項目和重試作業。

您可以將「暫時性錯誤處理應用程式區塊」(Topaz) 與 Nuget 封裝 EnterpriseLibrary.TransientFaultHandling.Data (**SqlAzureTransientErrorDetectionStrategy** 類別) 搭配使用，為 SQL Database 實作重試機制。

此區塊也提供 **ReliableSqlConnection** 類別，此類別會實作舊的 ADO.NET 1.0 API (* * IDbConnection** ，而不是 **DbConnection**)，並在內部執行的重試和連接管理。雖然很方便，但需要您使用一組不同的方法來叫用含重試的作業，因此並不是簡單的直接取代。它不支援非同步執行 (當實作及使用 Azure 服務時，建議非同步執行)。此外，此類別使用的是 ADO.NET 1.0，因此並未因近期對 ADO.NET. 的改善與更新獲益。

### 原則組態 (使用 ADO.NET 的 SQL Database)

暫時性錯誤處理應用程式區塊支援檔案式組態與程式設計組態。一般而言，您應該使用程式設計組態以獲得最大的彈性 (請參閱下一節的說明以取得詳細資訊)。下列程式碼會在應用程式啟動時執行一次，然後建立 **RetryManager** 並填入清單，清單中有四個適合搭配 Azure SQL Database 使用的重試策略。它也會為 **RetryManager** 設定預設策略。如果在建立連接或命令時未指定替代策略，則會為連接與命令使用這些策略。

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

如需如何使用您在存取 Azure SQL Database 時設定的重試原則，請參閱以下的[範例](#examples-sql-database-using-ado-net-)一節。

有關暫時性錯誤處理應用程式區塊的預設策略，請參閱在本指引結尾處的＜[暫時性錯誤處理應用程式區塊 (Topaz) 策略](#transient-fault-handling-application-block-topaz-strategies)＞一節。

### 重試使用指引

使用 ADO.NET 存取 SQL Database 時，請考慮下列指引：

* 選擇適當的服務選項 (共用或高階)。共用的執行個體可能會有延遲時間比一般連接長，及因為共用伺服器有其他租用戶使用而節流的問題。如果需要更可預測的效能和可靠的低延遲作業，請考慮選擇高階選項。
* 請確定您是在適當的層級或範圍執行重試，以避免非等冪作業導致資料不一致。理想的狀況是，所有的作業應等冪，而因此能夠重複執行，而不會造成不一致。若不是此狀況，則應在如果一個作業失敗，則允許復原所有相關變更的層級或範圍中執行重試；例如，從交易範圍內。如需詳細資訊，請參閱[雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。
* 互動式案例除外，不建議將固定間隔策略搭配 Azure SQL Database 使用；互動式案例中只有一些間隔非常短的重試。相反地，請考慮為大部分的案例使用指數退避策略。
* 定義連接時，為連接與命令逾時選擇合適的值。逾時值過短，會造成資料庫忙碌時連接永遠失敗。逾時值過長，可能會因為在偵測失敗連接之前等待過久，而造成重試邏輯無法正確運作。逾時值是端對端延遲的元件；它會有效地加到在重試原則中為每個重試嘗試指定的重試延遲。
* 在重試特定次數後即關閉連接，即使使用的是指數退避策略，然後在新的連接上重試作業。在同一個連接上多次重試同一個作業，也是造成連接問題的因素之一。有關此技術的範例，請參閱[雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。
* 當使用連接共用時 (預設值)，有可能會從共用中選擇同一個連接，即使是在關閉並重新開啟連接後。如果發生這種情況，則解決的技術是呼叫 **SqlConnection** 類別的 **ClearPool** 方法將連接標示為不可重複使用。但只應在數次連接嘗試皆失敗後，及碰到與錯誤連接有關的特定類別暫時性錯誤時，如 SQL 逾時 (錯誤碼 -2)，才這麼做。
* 如果資料存取程式碼使用交易做為起始的 **TransactionScope** 執行個體，則重試邏輯應重新開啟連接並啟動新的交易範圍。基於這個原因，可以重試的程式碼區塊應包含交易的整個範圍。
* 暫時性錯誤處理應用程式區塊支援在組態檔中完整定義的重試組態。不過，為了讓 Azure 有最大的彈性，您應該考慮在應用程式內以程式設計方式建立組態。重試原則的特定參數 (例如重試次數與重試間隔) 可以儲存在服務組態檔中，並在執行階段用於建立適當的原則。這會讓設定需要應用程式重新啟動才會變更。

請考慮從重試作業的下列設定開始。這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。

| **內容** | **範例目標 E2E<br />最大延遲** | **重試策略** | **設定** | **值** | **運作方式** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 互動式、UI <br />或前景 | 2 秒 | FixedInterval | 重試計數<br />重試間隔<br />第一個快速重試 | 3<br />500 毫秒<br />true | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 背景<br />或批次 | 30 秒 | ExponentialBackoff | 重試計數<br />最小退避<br />最大退退避<br />差異退退避<br />第一個快速重試 | 5<br />0 秒<br />60 秒<br />2 秒<br />false | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~2 秒<br />嘗試 3 - 延遲 ~6 秒<br />嘗試 4 - 延遲 ~14 秒<br />嘗試 5 - 延遲 ~30 秒 |

> [AZURE.NOTE]端對端延遲目標會假設服務連接的預設逾時值。如果您指定較長的連接逾時值，則系統會為每個重試嘗試增加這段時間，來延長端對端延遲。

### 範例 (使用 ADO.NET 的 SQL Database)

本節描述如何使用暫時性錯誤處理應用程式區塊，以透過使用一組您在 **RetryManager** 中設定的重試原則 (上一節＜[原則組態](#policy-configuration-sql-database-using-ado-net-)＞中所示) 來存取 Azure SQL Database。使用區塊最簡單的方式，是透過 **ReliableSqlConnection** 類別或者是呼叫擴充方法，例如連接上的 **OpenWithRetry** (請參閱[暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/hh680934.aspx)以取得詳細資訊)。

但在暫時性錯誤處理應用程式區塊的目前版本中，這些方法不是天生就支援對 SQL Database 的非同步作業。好的做法會要求您使用唯一的非同步技術來存取 Azure 服務，例如 SQL Database，所以您應該考慮採用下列技術，將暫時性錯誤處理應用程式區塊搭配 SQL Database 使用。

您可以使用第 5 版 C# 語言中簡化的非同步支援，來建立區塊提供的非同步版本方法。例如，下列程式碼顯示要如何建立 **ExecuteReaderWithRetry** 擴充方法的非同步版本。原始程式碼中的變更與新增內容會反白顯示。有關 Topaz 的原始程式碼，請參閱 GitHub 上的[暫時性錯誤處理應用程式區塊 ("Topaz")](http://topaz.codeplex.com/SourceControl/latest)。

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

這個新非同步擴充方法的使用方式，與區塊中包含的同步版本相同。

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

不過，這種方法僅限處理個別的作業或命令，而不處理有適當定義交易界限的陳述式區塊。此外，它無法解決從連接共用移除錯誤連接，讓系統不會為後續的嘗試選取錯誤連接的情況。有關解決這些問題的同步範例，請參閱[雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management)。除了重試任意序列的資料庫指令外，它會清除連接共用以移除無效的連接，並檢測整個程序。雖然此範例中顯示的程式碼是同步的，但將它轉換成非同步程式碼相對簡單。

### 詳細資訊

如需使用暫時性錯誤處理應用程式區塊的詳細資訊，請參閱：

* [將暫時性錯誤處理應用程式區塊搭配 SQL Azure 使用](http://msdn.microsoft.com/library/hh680899.aspx)
* [堅持，是所有成功的秘方：使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719.aspx)
* [雲端服務基礎資料存取層 – 暫時性錯誤處理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。

有關充分使用 SQL Database 的一般指引，請參閱：

* [Azure SQL Database 效能和彈性指南 (英文)](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [降低 SQL Azure 中的連接共用錯誤 (英文)](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## 服務匯流排重試指引

服務匯流排是雲端傳訊平台，能夠提供鬆散結合的訊息交換，以及為在雲端託管或內部部署的應用程式元件改善擴充性和恢復能力。

### 重試機制

服務匯流排使用 [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) 基底類別的實作來實作重試。所有的服務匯流排用戶端皆會公開 **RetryPolicy** 屬性，此屬性可設為 **RetryPolicy** 基底類別的其中一個實作。內建實作如下：

* [RetryExponential 類別](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx)。這會公開控制退避間隔的屬性、重試計數，以及用來限制作業完成總時間的 **TerminationTimeBuffer** 屬性。
* [NoRetry 類別](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx)。這用於當不需要在服務匯流排 API 層級重試時，例如當在批次或多步驟作業期間由另一個處理序管理重試時。

服務匯流排動作會傳回某個範圍的例外狀況，如[附錄：傳訊例外狀況](http://msdn.microsoft.com/library/hh418082.aspx)中所列的清單。此清單會提供這些重試作業的表示是否適合的資訊。例如，[ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) 表示用戶端應等待一段時間後，再重試作業。**ServerBusyException** 的發生也會導致服務匯流排切換成不同的模式，在該模式中會在計算出的重試延遲中額外加上 10 秒的延遲。此模式會在短時間後重設。

從服務匯流排傳回的例外狀況會公開 **IsTransient** 屬性，以表示用戶端是否應該重試作業。內建的 **RetryExponential** 原則依賴 **MessagingException** 類別中的 **IsTransient** 屬性，也就是所有服務匯流排例外狀況的基底類別。如果您建立 **RetryPolicy** 基底類別的自訂實作，您可以使用例外狀況類型和 **IsTransient** 屬性的組合，藉此更細微地控制重試動作。例如，您可能會偵測到 **QuotaExceededException**，並先採取動作清空佇列，再重試將訊息傳送到佇列。

### 原則組態 (服務匯流排)

重試原則是以程式設計的方式設定的，並可設為 **NamespaceManager** 與 **MessagingFactory** 兩者的預設原則，或為每個傳訊用戶端個別設為預設原則。若要設定傳訊工作階段的預設重試原則，請設定 **NamespaceManager** 的 **RetryPolicy**。

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             deltaBackoff: TimeSpan.FromSeconds(2),
	                                                             terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                             maxRetryCount: 3);

請注意為了清楚起見，此程式碼使用具名參數。或者您可以省略名稱，因為沒有任何參數是選擇性。

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

若要為所有從傳訊處理站建立的用戶端設定預設重試原則，請設定 **MessagingFactory** 的 **RetryPolicy**。

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    deltaBackoff: TimeSpan.FromSeconds(2),
	                                                    terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                    maxRetryCount: 3);

若要為傳訊用戶端設定重試原則，或覆寫其預設原則，請使用所需原則類別的執行個體設定其 **RetryPolicy** 屬性：

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        deltaBackoff: TimeSpan.FromSeconds(2),
	                                        terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                        maxRetryCount: 3);
```

無法在個別的作業層級設定重試原則。它適用於傳訊用戶端的所有作業。下表顯示內建重試原則的預設設定。

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### 重試使用指引

使用服務匯流排時請考慮下列指引：

* 當使用內建 **RetryExponential** 實作時，若原則回應伺服器忙碌例外狀況並自動切換到適當的重試模式時，請不要實作後援作業。
* 服務匯流排支援名為「配對命名空間」的功能，如果主要命名空間中的佇列失敗，此功能會實作自動容錯移轉到備份的佇列。主要佇列復原時，次要佇列中的訊息會傳回主要佇列。此功能有助於處理暫時性錯誤。如需詳細資訊，請參閱[非同步傳訊模式和高可用性](http://msdn.microsoft.com/library/azure/dn292562.aspx)。

請考慮從重試作業的下列設定開始。這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### 遙測

服務匯流排使用 **EventSource**，將重試記錄成 ETW 事件。您必須將 **EventListener** 附加到事件來源，以擷取事件並在效能檢視器中檢視事件。您可以使用[語意記錄應用程式區塊](http://msdn.microsoft.com/library/dn775006.aspx)來達成。重試事件的格式如下：

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### 範例 (服務匯流排)

下列範例程式碼顯示如何為下列項目設定重試原則：

* 命名空間管理員。此原則適用於該管理員上的所有作業，而且無法針對個別作業覆寫。
* 傳訊處理站。此原則適用於從該處理站建立的所有用戶端，當建立個別的用戶端時無法覆寫此原則。
* 個別的傳訊用戶端。建立用戶端之後，您可以為該用戶端設定重試原則。此原則適用於該用戶端上的所有作業。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(1.75),
		                terminationTimeBuffer: TimeSpan.FromSeconds(4),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## 詳細資訊

* [非同步傳訊模式和高可用性](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## 快取 (Redis) 重試指引

Azure Redis 快取是快速資料存取與低延遲性的快取服務，以普遍的開放原始碼 Redis 快取為基礎。它很安全，且由 Microsoft 管理，可使用 Azure 的任何應用程式存取。

本節中的指引以使用 StackExchange.Redis 用戶端來存取快取為基礎。有關其他適合的用戶端清單，請參閱 [Redis 網站](http://redis.io/clients)，這些用戶端可能有不同的重試機制。

請注意 StackExchange.Redis 用戶端透過單一連接使用多工。建議用法是在應用程式啟動時建立用戶端的執行個體，並對快取的所有作業使用此執行個體。基於這個理由，對快取只連接一次，且因此本節中指引的所有一切都與此初始連接的重試原則有關，不適用於存取快取的每個作業。

### 重試機制

StackExchange.Redis 用戶端會使用透過一組選項設定的連接管理員類別。這些選項包括 **ConnectRetry** 屬性，此屬性會指定重試快取失敗連接的次數。不過，重試原則僅用於初始連接動作，且不會在重試之間等待。

### 原則組態 (Azure Redis 快取)

重試原則是以程式設計的方式設定的，做法是先設定用戶端的選項，再連接至快取。做法是建立 **ConfigurationOptions** 類別的執行個體、填入其屬性，並將它傳遞給 **Connect ** 方法。

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

請注意，**ConnectTimeout** 屬性會指定等待時間上限 (以毫秒為單位)，而非指定重試之間的延遲。

或者，您可以將選項指定為字串，並將此字串傳遞到 **Connect** 方法。

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

此外當您連接到快取時，也可以直接指定選項。

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

下表顯示內建重試原則的預設設定。

| **內容** | **設定** | **預設值**<br />(v 1.0.331) | **意義** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConfigurationOptions | ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout | 3<br /><br />最多 5000 毫秒加上 SyncTimeout<br />1000 | 初始連接作業期間重複連接嘗試的次數。<br />連接作業的逾時 (毫秒)。非重試嘗試之間的延遲。<br />允許同步作業的時間 (毫秒)。 |

> [AZURE.NOTE]SyncTimeout 會造成作業的端對端延遲。不過一般而言，不建議使用同步作業。如需詳細資訊，請參閱[管線與多工器](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md)。

## 重試使用指引

使用 Azure Redis 快取時請考慮下列指引：

* StackExchange Redis 用戶端會管理自己的重試，但只有在應用程式第一次啟動時對快取建立連接時才會。您可以設定連接逾時與重試嘗試次數來建立這個連接，但重試原則不適用於對快取的作業。
* 重試機制在重試嘗試之間沒有延遲。它只是在指定的連接逾時過後重試失敗的連接，並重試指定的次數。
* 請不要使用大量的重試嘗試，而是考慮改為存取原始的資料來源來回復。

## 遙測

您可以使用 **TextWriter** 收集連接 (而非其他作業) 的資訊。

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

這產生的輸出範例如下所示。

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## 範例 (Azure Redis 快取)

下列程式碼範例顯示如何設定當初始化 StackExchange.Redis 用戶端在應用程式啟動時存取 Azure Redis 快取時的連接逾時設定和重試次數。請注意連接逾時是您願意等待連接到快取的期間，它不是重試嘗試之間的延遲。

此範例示範如何使用 **ConfigurationOptions** 的執行個體來設定組態。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

此範例示範如何透過將選項指定為字串來設定組態。

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

如需其他範例，請參閱專案網站上的[組態](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration)。

## 詳細資訊

* [Redis 網站](http://redis.io/)

## DocumentDB (發行前版本) 重試指引

DocumentDB 是完全受管理的文件資料庫即服務，透過無結構描述的 JSON 資料模型而擁有豐富的查詢與編製索引功能。它提供可設定且可靠的效能、原生的 JavaScript 交易處理，而且是針對可彈性延展的雲端建置而成。

## 重試機制

DocumentDB 用戶端的發行前版本包含內部重試機制和非可設定的重試機制 (在後續發行的版本中可能會變更)。此預設設定會視使用的內容而不同。某些作業會將指數退避策略搭配硬式編碼的參數使用。其他作業則僅指定應嘗試多少次的重試，並在從服務傳回的 [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx) 執行個體中使用重試延遲。如果沒有指定延遲，則會使用五秒的延遲。

## 原則組態 (DocumentDB)

無。所有用來實作重試的類別皆為內部。重試參數是常數，或者會使用設定至類別建構函式。

下表顯示內建重試原則的預設設定。

| **內容** | **設定** | **值** | **運作方式** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (內部) | MaxRetryAttemptsOnQuery<br /><br />MaxRetryAttemptsOnRequest | 3<br /><br />0 | 文件查詢的重試嘗試次數。無法變更此值。<br />針對其他要求的重試嘗試次數。無法變更此值。 |

## 重試使用指引

使用 DocumentDB 時請考慮下列指引：

* 您無法變更預設的重試原則。
* 請參閱 [TBD] 以取得預設設定的詳細資訊。

## 遙測

系統會透過 .NET **TraceSource** 將重試嘗試記錄成非結構化的追蹤訊息。您必須設定 **TraceListener** 來擷取事件，並將事件寫入適當的目的地記錄中。

## 搜尋重試指引

Azure 搜尋服務可用來在網站或應用程式中加入強大且複雜的搜尋功能、快速輕鬆地微調搜尋結果，並建構豐富且微調的排名模型。

### 重試機制

此搜尋服務沒有內建的重試機制，因為一般用法是透過 HTTP 要求進行的。若要實作重試，您可以使用 REST 用戶端的泛型實作，並決定何時和是否要根據來自服務的回應重試作業。如需詳細資訊，請參閱本指引稍後的＜[一般 REST 與重試指引](#general-rest-and-retry-guidelines)＞一節。

### 重試使用指引

使用 Azure 搜尋服務時請考慮下列指引：

* 您可以使用服務所傳回的狀態碼來判斷失敗的類型。狀態碼是在 [HTTP 狀態碼 (Azure 搜尋服務)](http://msdn.microsoft.com/library/dn798925.aspx) 中定義的。狀態碼 503 (服務無法使用) 表示服務負載過重時，且無法立即處理要求。適當的動作是只有在允許復原服務的時間過後才重試作業。若在過短的延遲間隔之後重試，可能會延長無法使用的時間。
* 請參閱本指引稍後的＜[一般 REST 與重試指引](#general-rest-and-retry-guidelines)＞一節，以取得有關重試 REST 作業的一般資訊。

## 詳細資訊

* [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/dn798935.aspx)

## Azure Active Directory 重試指引

Azure Active Directory (AD) 是完整的身分識別與存取管理雲端解決方案，結合了核心目錄服務、進階身分識別控管、安全性，以及應用程式存取管理。Azure AD 也提供給開發人員一個身分識別管理平台，以根據集中化的原則和規則將存取控制傳遞給其應用程式。

### 重試機制

Active Directory 驗證程式庫 (ADAL) 中沒有內建的 Azure Active Directory 重試機制。您可以使用暫時性錯誤處理應用程式區塊來實作重試策略，其中包含 Active Directory 所傳回例外狀況的自訂偵測機制。

### 原則組態 (Azure Active Directory)

將暫時性錯誤處理應用程式區塊搭配 Azure Active Directory 使用時，您可以根據定義所要使用的偵測策略來建立 **RetryPolicy** 執行個體。

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

接著您可以呼叫重試原則的 **ExecuteAction** 或 **ExecuteAsync** 方法，並以您想要執行的作業傳遞。

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

當錯誤發生時，偵測策略類別會收到例外狀況，且必須偵測此例外狀況可能是暫時性錯誤或永久性錯誤。通常做法是檢查例外狀況類型和狀態碼。例如，「服務無法使用」回應會指出應該重試嘗試。暫時性錯誤處理應用程式區塊不包含適用於搭配 ADAL 用戶端使用的偵測策略類別，但會在以下的＜[範例](#examples-azure-active-directory-)＞一節中提供自訂偵測策略的範例。使用自訂偵測策略是使用區塊所提供的策略相同。

有關暫時性錯誤處理應用程式區塊的預設策略，請參閱在本指引結尾處的＜[暫時性錯誤處理應用程式區塊 (Topaz) 策略](#transient-fault-handling-application-block-topaz-strategies)＞一節。

## 重試使用指引

使用 Azure Active Directory 時請考慮下列指引：

* 如果您為 Azure Active Directory 使用 REST API，則只有在下列狀況才應重試作業：結果是 5xx 範圍中的錯誤 (例如 500 內部伺服器錯誤、502 錯誤的閘道、503 服務無法使用，以及 504 閘道器逾時)。若為任何其他錯誤，請勿重試。
* 如果您使用的是 Active Directory 驗證程式庫 (ADAL)，則無法輕易地存取 HTTP 代碼。您必須建立自訂偵測策略，其中包含用來檢查 ADAL 特有例外狀況屬性的邏輯。請參閱以下的＜[範例](#examples-azure-active-directory-)＞一節。
* 建議將指數退避原則搭配 Azure Active Directory 用於批次案例中。

請考慮從重試作業的下列設定開始。這些是一般用途設定，您應該監視作業並微調其值以符合您自己的需求。


| **內容** | **範例目標 E2E<br />最大延遲** | **重試策略** | **設定** | **值** | **運作方式** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 互動式、UI <br />或前景 | 2 秒 | FixedInterval | 重試計數<br />重試間隔<br />第一個快速重試 | 3<br />500 毫秒<br />true | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 500 毫秒<br />嘗試 3 - 延遲 500 毫秒 |
| 背景或<br />批次 | 60 秒 | ExponentialBackoff | 重試計數<br />最小退避<br />最大退退避<br />差異退退避<br />第一個快速重試 | 5<br />0 秒<br />60 秒<br />2 秒<br />false | 嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~2 秒<br />嘗試 3 - 延遲 ~6 秒<br />嘗試 4 - 延遲 ~14 秒<br />嘗試 5 - 延遲 ~30 秒 |

## 範例 (Azure Active Directory)

下列程式碼範例顯示如何使用暫時性錯誤處理應用程式區塊 (Topaz) 來定義適合搭配 ADAL 用戶端使用的自訂暫時性錯誤偵測策略。程式碼會根據 **AdalDetectionStrategy** 類型的自訂偵測策略，建立新的 **RetryPolicy** 執行個體，如下方所列程式碼中的定義。Topaz 的自訂偵測策略會實作 **ITransientErrorDetectionStrategy** 介面，如果應嘗試重試，則傳回 true，如果錯誤似乎是非暫時性且不應嘗試重試，則傳回 **false**。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

如需重試 Active Directory 圖形 API 作業的相關資訊，以及傳回的錯誤代碼，請參閱：

* [代碼範例： 重試邏輯](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Azure AD 圖形錯誤代碼](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## 詳細資訊

* [實作自訂偵測策略](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [實作自訂重試策略](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [權杖發行與重試指引](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## 一般 REST 與重試指引

存取 Azure 或協力廠商服務時請考量下列事項：

* 使用系統化的方法管理重試，或許是做為可重複使用的程式碼，讓您可以在所有的用戶端與所有的解決方案之間套用一致的方法。
* 如果目標服務或用戶端有沒有內建的重試機制，請考慮使用重試架構 (例如暫時性錯誤處理應用程式區塊) 來管理重試。這可以幫助您實作一致的重試行為，且可能會為目標服務提供適合的預設重試策略。不過，您可能需要為具有非標準行為的服務建立自訂重試程式碼，非標準行為不依例外狀況來表示暫時性錯誤，或如果您想要使用 **Retry-Response** 來管理重試行為，也必須建立自訂重試程式碼。
* 暫時性偵測邏輯將取決於您用來叫用 REST 呼叫的實際用戶端 API。有些用戶端 (例如較新的 **HttpClient** 類別)，不會為已完成的要求擲回狀態碼為 HTTP 不成功的例外狀況。這會改善效能，但會防止使用暫時性錯誤處理應用程式區塊。在此情況下，您無法使用會為 HTTP 不成功的狀態碼產生例外狀況的程式碼，來包裝對 REST API 的呼叫，接著會由區塊來處理該狀態碼。或者，您可以使用不同的機制來驅動重試。
* 從服務傳回的 HTTP 狀態碼可協助指出錯誤是否為暫時性。您可能需要檢查用戶端產生的例外狀況，或檢查重試架構，以存取狀態碼或決定等同的例外狀況類型。下列 HTTP 代碼通常表示重試是適當的：
  * 408 要求逾時
  * 500 內部伺服器錯誤
  * 502 錯誤的閘道
  * 503 服務無法使用
  * 504 閘道逾時
* 如果您讓重試邏輯以例外狀況為基礎，則下列項目通常表示無法建立連接的暫時性錯誤：
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* 在服務無法使用狀態下，服務可能會先指出適當的延遲，然後在 **Retry-After** 回應標頭或不同的自訂標頭中重試 (如 DocumentDB 服務所示)。服務也可能傳送額外的資訊做為自訂標頭，或內嵌在回應的內容中。暫時性錯誤處理應用程式區塊無法使用標準或任何自訂的「retry-after」標頭。
* 408 要求逾時除外，不要重試代表用戶端錯誤 (4xx 範圍中的錯誤) 的狀態碼。
* 在各種條件下 (例如不同的網路狀態及各種的系統負載) 徹底測試您的重試策略和機制。

## 重試策略

以下為一般類型的重試策略間隔：

* **指數**：此重試原則會執行指定的重試次數，並使用隨機的指數退避方法來決定重試之間的間隔。例如：

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **累加**：此重試策略具有指定的重試嘗試次數，並在重試之間使用累加時間間隔。例如：

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **線性重試**：此重試原則會執行指定的重試次數，並在重試之間使用指定的固定時間間隔。例如：

		retryInterval = this.deltaBackoff;

## 詳細資訊

* [斷路器策略](http://msdn.microsoft.com/library/dn589784.aspx)

## 暫時性錯誤處理應用程式區塊 (Topaz) 策略

暫時性錯誤處理應用程式區塊有下列預設策略。

| **策略** | **設定** | **預設值** | **意義** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **指數** | retryCount<br />minBackoff<br /><br />maxBackoff<br /><br />deltaBackoff<br /><br />fastFirstRetry | 10<br />1 秒<br /><br />30 秒<br /><br />10 秒<br /><br />true | 重試嘗試次數。<br />最短退避時間。系統會使用此值或計算出的退避值兩者中較大的值做為重試延遲。<br />最短退避時間。系統會使用此值或計算出的退避值兩者中較小的值做為重試延遲。<br />此值用於計算重試之間指數延遲的隨機差異。<br />是否將立即進行第一次重試。 |
| **累加** | retryCount<br />initialInterval<br />increment<br /><br />fastFirstRetry<br />| 10<br />1 秒<br />1 秒<br /><br />true | 重試次數。<br />初始間隔，適用於第一個重試<br />累加時間值，用來計算重試之間的漸進式延遲。<br />是否立即進行第一次重試嘗試。 |
| **線性 (固定間隔)** | retryCount<br />retryInterval<br />fastFirstRetry<br /> | 10<br />1 秒<br />true | 重試次數。<br />重試之間的延遲。<br />是否立即進行第一次重試嘗試。 |
如需使用暫時性錯誤處理應用程式區塊的範例，請參閱本指引中稍早＜範例＞各節中有關使用 ADO.NET 和 Azure Active Directory 的 Azure SQL Database 說明。

<!---HONumber=62-->