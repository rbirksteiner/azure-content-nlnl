<properties 
	pageTitle="Azure Redis 快取常見問題集" 
	description="了解 Azure Redis 快取常見問題、模式和最佳作法的答案" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="sdanie"/>

# Azure Redis 快取常見問題集

了解 Azure Redis 快取常見問題、模式和最佳作法的答案。

<a name="cache-size"></a>
## 應該使用哪個 Redis 快取供應項目和大小？

每個 Azure Redis 快取供應項目提供不同層級的**大小**、**頻寬**、**高可用性**和 **SLA** 選項。

-	基本 SKU - 單一節點、無複寫或 SLA，快取大小從 250 MB 到 53 GB。
-	標準 SKU - 主要/次要節點、含自動複寫、99.9% SLA，快取大小從 250 MB 到 53 GB。

如果您想要高可用性，請選擇具有 99.9% SLA 的標準快取供應項目。針對開發和原型設計，或不需要 SLA 的情況，基本供應項目可能較適當。

快取大小和頻寬大約對應至裝載快取之虛擬機器的大小和頻寬。基本和標準供應項目的 250 MB 大小裝載於「超小 (A0)」虛擬機器大小，其使用共用核心所裝載，而其他大小則使用專用核心進行裝載。1 GB 快取大小裝載於「小 (A1)」虛擬機器大小，其中 1 個專用虛擬核心用來服務作業系統和 Redis 快取。較大的快取大小裝載於具有多個專用虛擬核心的較大 VM 執行個體。

如果您的快取具有高輸送量，請選擇 1 GB 以上的大小，以使用專用核心來執行快取。1 GB 快取大小裝載於 1 個核心虛擬機器。此核心用來服務 OS 和快取。大於 1 GB 的快取執行於含多核心的虛擬機器，而且 Redis 快取使用未與 OS 共用的專用核心。

**Redis 為單一執行緒**，因此兩個以上核心所提供的優點與只有兩個核心相同，但**較大 VM 大小的頻寬通常會多於較小大小**。如果快取伺服器或用戶端達到頻寬限制，則您會在用戶端上收到逾時。

下表顯示從 Iaas VM 使用 `redis-benchmark.exe` 對 Azure Redis 快取端點測試各種 Azure Redis 快取大小時觀察到的最大頻寬值。請注意，不保證這些值，而且沒有這些數字的 SLA，但應該是一般情況。您應該載入測試自己的應用程式，以判斷應用程式的正確快取大小。

<table>
  <tr>
    <th>快取名稱</th>
    <th>快取大小</th>
    <th>Get/秒 (1 KB 值的簡單 GET 呼叫)</th>
    <th>頻寬 (MBits/秒)</th>
  </tr>
  <tr>
    <td>C0</td>
    <td>250 MB</td>
    <td>610</td>
    <td>5</td>
  </tr>
  <tr>
    <td>C1</td>
    <td>1 GB</td>
    <td>12,200</td>
    <td>100</td>
  </tr>
  <tr>
    <td>C2</td>
    <td>2.5 GB</td>
    <td>24,300</td>
    <td>200</td>
  </tr>
  <tr>
    <td>C3</td>
    <td>6 GB</td>
    <td>48,875</td>
    <td>400</td>
  </tr>
  <tr>
    <td>C4</td>
    <td>13 GB</td>
    <td>61,350</td>
    <td>500</td>
  </tr>
  <tr>
    <td>C5</td>
    <td>26 GB</td>
    <td>112,275</td>
    <td>1000</td>
  </tr>
  <tr>
    <td>C6</td>
    <td>53 GB</td>
    <td>153,219</td>
    <td>1000+</td>
  </tr>
</table>

如需下載 Redis 工具 (例如，`redis-benchmark.exe`) 的指示，請參閱[如何執行 Redis 命令？](#cache-commands)小節。

<a name="cache-region"></a>
## 我應該在哪個區域找到快取？

為獲得最佳效能和最低延遲，請在與快取用戶端應用程式相同的區域中找到 Azure Redis 快取。

<a name="cache-timeouts"></a>
## 為什麼看到逾時？

用來與 Redis 溝通的用戶端發生逾時。在大多數的情況下，Redis 伺服器不會逾時。將命令傳送到 Redis 伺服器時，會將命令排入佇列，而且 Redis 伺服器最後會挑選並執行命令。不過，用戶端可能會在此程序期間逾時，而且，如果是這樣，則會在呼叫端引發例外狀況。如需疑難排解逾時問題的詳細資訊，請參閱[調查 StackExchange.Redis 中 Azure Redis 快取的逾時例外狀況](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/)。

<a name="cache-monitor"></a>
## 如何監視快取的健全狀況和效能？

Microsoft Azure Redis 快取執行個體可以在 [Azure Preview 入口網站](https://portal.azure.com)中進行監視。您可以檢視度量、將度量圖表釘選到「開始面板」、自訂監視圖表的日期和時間範圍、新增和移除圖表中的度量，以及設定符合特定條件時的警示。這些工具可讓您監視 Azure Redis 快取執行個體的健全狀況，並協助您管理快取應用程式。如需監視快取的詳細資訊，請參閱[監視 Azure Redis 快取](https://msdn.microsoft.com/library/azure/dn763945.aspx)。

<a name="cache-disconnect"></a>
## 我的用戶端為什麼中斷與快取的連線？

下面是快取中斷連線的一些常見原因。

-	用戶端原因
	-	已重新部署用戶端應用程式。
	-	用戶端應用程式已執行調整作業。
		-	如果是雲端服務或 Web Apps，這可能是自動調整所造成。
	-	用戶端上的網路層已變更。
	-	在用戶端或用戶端與伺服器之間的網路節點中，發生暫時性錯誤。
	-	已達頻寬閾值限制。
	-	CPU 繫結作業用了太多時間才完成。
-	伺服器端原因
	-	在標準快取供應項目上，Azure Redis 快取服務會起始從主要節點到次要節點的容錯移轉。
	-	Azure 會修補已部署快取的執行個體
		-	這可能適用於 Redis 伺服器更新或一般 VM 維護。

<a name="cache-configuration"></a>
## StackExchange.Redis 設定選項的作用為何？

StackExchange.Redis NuGet 封裝本節談論一些常見設定。如需 StackExchange.Redis 選項的詳細資訊，請參閱 [StackExchange.Redis 設定](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)。

<table>
  <tr>
    <th>ConfigurationOptions</th>
    <th>說明</th>
    <th>建議</th>
  </tr>
  <tr>
    <td>AbortOnConnectFail</td>
    <td>設定為 true 時，在網路失敗之後不會重新連線。</td>
    <td>設定為 false，並讓 StackExchange.Redis 自動重新連線。</td>
  </tr>
  <tr>
    <td>ConnectRetry</td>
    <td>初始連線期間的重複連線嘗試次數。</td>
    <td></td>
  </tr>
  <tr>
    <td>ConnectTimeout</td>
    <td>連線作業的逾時 (毫秒)。</td>
    <td></td>
  </tr>
</table>

在大部分情況下，用戶端的預設值就已足夠。您可以根據工作負載來微調選項。

-	重試
	-	對於 ConnectRetry 和 ConnectTimeout，一般指引是快速失敗，然後再試一次。這根據您的工作負載以及用戶端發出 Redis 命令以及接收回應所需的平均時間。
	-	讓 StackExchange.Redis 自動重新連線，而不檢查連線狀態，並自行重新連線。**避免使用 ConnectionMultiplexer.IsConnected 屬性**。
	-	雪球處理 - 有時，您可能會在重試此雪球時碰到問題，永遠無法復原。在此情況下，您應該考慮使用指數輪詢重試演算法 (如 Microsoft Patterns & Practices 群組所發佈的[重試一般指引](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md)所述)。
-	逾時值
	-	請考慮您的工作負載，並據此設定值。如果您要儲存大的值，請將逾時設定為較高的值。
		-	將 ABortOnConnectFail 設定為 false，並讓 StackExchange.Redis 重新連線。
-	使用應用程式的單一 ConnectionMultiplexer 執行個體。您可以使用 LazyConnection 建立 Connection 屬性所傳回的單一執行個體 (如[使用 ConnectionMultiplexer 類別連線至快取](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect)所示)。
-	將 `ConnectionMultiplexer.ClientName` 屬性設定為應用程式執行個體唯一名稱，以進行診斷。
-	針對自訂工作負載，使用多個 `ConnectionMultiplexer` 執行個體。
	-	如果您的應用程式中有不同的負載，則可以遵循此模型。例如：
		-	您可以有一個多工器來處理大型索引鍵。 
		-	您可以有一個多工器來處理小型索引鍵。 
		-	您可以設定連線逾時的不同值，以及每個所使用 ConnectionMultiplexer 的重試邏輯。
		-	在每個多工器上設定 `ClientName` 屬性，以協助診斷。 
		-	這會導致每個 `ConnectionMultiplexer` 的更簡化延遲。

<a name="cache-redis-commands"></a>
## 使用常見 Redis 命令時的一些考量為何？

-	如果不了解需要花很長時間才能完成之特定 Redis 命令的影響，則不應該執行這些命令。
	-	例如，不要在生產環境中執行 [KEYS](http://redis.io/commands/keys) 命令，因為根據索引鍵數目，它可能會花很長時間才會傳回。Redis 是單一執行緒伺服器，並且一次處理一個命令。如果您在 KEYS 之後發出其他命令，則除非 Redis 處理 KEYS 命令，否則不會處理它們。
-	索引鍵大小 - 應該使用較小的索引鍵/值還是較大的索引鍵/值？ 一般而言，這取決於案例。如果您的案例需要較大的索引鍵，則可以調整 ConnectionTimeout，以及重試值並調整重試邏輯。從 Redis 伺服器的觀點，觀察到較小的值，即表示有較佳的效能。
	-	這不表示您無法在 Redis 中儲存較大的值；您必須注意下列考量。延遲會比較高。如果您有一個較大的資料集和一個較小的值，則可以使用多個 ConnectionMultiplexer 執行個體，而每個執行個體都會設定一組不同的逾時和重試值 (如先前的 [StackExchange.Redis 設定選項的作用為何](#cache-configuration)小節所述)。


<a name="cache-ssl"></a>
## 何時應該啟用非 SSL 連接埠來連線至 Redis？

Redis 伺服器不支援非預設 SSL，但是 Azure Redis 快取則支援。如果您是連線至 Azure Redis 快取，並且您的用戶端支援 SSL (例如 StackExchange.Redis)，則應該使用 SSL。

請注意，預設會停用新 Azure Redis 快取執行個體的非 SSL 連接埠。如果您的用戶端不支援 SSL，則必須遵循[在 Azure Redis 快取中設定快取](https://msdn.microsoft.com/library/azure/dn793612.aspx)文章之[存取連接埠](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts)小節中的指示來啟用非 SSL 連接埠。

Redis 工具 (例如 `redis-cli`) 未使用 SSL 連接埠，但您可以遵循[宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)部落格文章中的指示，使用公用程式 (例如 `stunnel`) 將工具安全地連線至 SSL 連接埠。

如需下載 Redis 工具的指示，請參閱[如何執行 Redis 命令？](#cache-commands)小節。

<a name="cache-benchmarking"></a>
## 如何效能評定和測試我快取的效能？

-	[啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)，以[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)您快取的健全狀況。您可以在入口網站中檢視度量，也可以使用您選擇的工具[下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)它們。
-	您可以使用 redis-benchmark.exe 對 Redis 伺服器進行負載測試。
	-	請確定負載測試用戶端與 Redis 快取位於相同的區域。
-	使用 redis-cli.exe，並使用 INFO 命令來監視快取。
	-	如果您的負載造成高記憶體分散，則應該相應增加為較大的快取大小。
-	如需下載 Redis 工具的指示，請參閱[如何執行 Redis 命令？](#cache-commands)小節。

<a name="cache-commands"></a>
## 如何執行 Redis 命令？

您可以使用 [Redis 命令](http://redis.io/commands#)中所列的任何命令。若要執行這些命令，您可以使用下列工具。

-	下載 [Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)。
-	使用 `redis-cli.exe` 連線至快取。使用-h 參數傳入快取端點，以及使用 -a 傳入索引鍵 (如下列範例所示)。
	-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
-	請注意，Redis 命令列工具未使用 SSL 連接埠，但您可以遵循[宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)部落格文章中的指示，使用公用程式 (例如 `stunnel`) 將工具安全地連線至 SSL 連接埠。

<a name="cache-common-patterns"></a>
## 一些常見的快取模式和考量為何？

-	Microsoft Patterns & Practices 具有下列指引。
	-	[快取指引](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)。
	-	[Azure 雲端應用程式設計和實作指引](https://github.com/mspnp/azure-guidance)
-	[Azure Redis 快取的常見快取模式](cache-howto-common-cache-patterns/)

<a name="cache-reference"></a>
## Azure Redis 快取為什麼沒有像一些其他 Azure 服務的 MSDN 類別庫參考？

Microsoft Azure Redis 快取是基於受歡迎的開放原始碼 Redis 快取，可讓您存取 Microsoft 所管理的專用安全 Redis 快取。有各種 [Redis 用戶端](http://redis.io/clients)可供許多程式設計語言使用。每個用戶端都有它自己的 API，以使用 [Redis 命令](http://redis.io/commands)來呼叫 Redis 快取執行個體。

因為每個用戶端都不同，所以 MSDN 上沒有一個集中式類別參考；而是每個用戶端都會維護其專屬的參考文件。除了參考文件之外，Azure.com 上還會有數個教學課程，可顯示如何使用 [[Redis 快取文件](http://azure.microsoft.com/documentatgion/services/redis-cache/)] 頁面上的不同語言和快取用戶端來開始使用 Azure Redis 快取。

<!---HONumber=July15_HO1-->