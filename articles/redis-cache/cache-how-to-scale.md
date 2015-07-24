<properties 
	pageTitle="如何調整 Azure Redis 快取" 
	description="了解如何調整 Azure Redis 快取執行個體" 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# 如何調整 Azure Redis 快取

>[AZURE.NOTE]Azure Redis 快取調整功能目前只能預覽。

Azure Redis 快取都有不同的快取提供項目，以提供選擇快取大小和功能的彈性。如果應用程式需求在建立快取之後變更，您可以使用 [Azure 入口網站](https://portal.azure.com)中的 [變更定價層] 分頁來調整快取大小。

>[AZURE.NOTE]調整 Azure Redis 快取時，即可變更大小，但無法從標準變更為基本快取，反之亦然。

## 調整時機

您可以使用 Azure Redis 快取的[監視](cache-how-to-monitor.md)功能監視快取應用程式的健全狀況和效能，以及協助判斷是否需要調整快取。

您可以監視下列度量，以協助判斷是否需要調整。

-	Redis 伺服器負載
-	記憶體使用量
-	網路頻寬
-	CPU 使用率

如果您判斷您的快取不再符合您應用程式的需求，則可以變更為您應用程式適用的較大或較小快取定價層。如需判斷要使用之快取定價層的詳細資訊，請參閱[我應該使用哪些 Redis 快取提供項目和大小](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## 調整快取
若要調整您的快取，請在 [Azure 入口網站](https://portal.azure.com)中[瀏覽至快取](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration)，然後按一下 [Redis 快取] 分頁中的 [標準層] 或 [基本層] 部分。

![定價層][redis-cache-pricing-tier-part]

從 [定價層] 分頁中選取想要的定價層，然後按一下 [選取]。

![定價層][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]快取無法將計畫從基本變更為標準，反之亦然，而且您無法從其中一個較大的大小縮小為 250 MB 快取。您可以從 250 MB 快取擴大為較大的大小，但無法縮小回 250 MB 定價層。如果您需要從基本變更為標準，或縮小為 250 MB 大小，則必須建立新的快取。

快取調整為新的定價層時，[調整] 狀態會顯示在 [Redis 快取] 分頁中。

![調整大小][redis-cache-scaling]

調整完成時，狀態會從 [調整] 變更為 [執行中]。

>[AZURE.IMPORTANT]在調整作業期間，基本快取會離線，並且遺失快取中的所有資料。調整作業完成時，基本快取將會重新上線，但沒有資料。標準快取會在調整作業期間保持上線，而且在將標準快取調整為較大大小時，一般不會遺失資料將標準快取調整為較小大小時，如果新的大小小於快取的資料量，則可能會遺失一些資料。如果縮小時遺失資料，則會使用 [allkeys-lru](http://redis.io/topics/lru-cache) 收回原則來收回金鑰。請注意，標準快取有 99.9% SLA 的可用性時，則資料遺失沒有 SLA。

## 如何自動化調整作業

除了調整 Azure 入口網站中的 Azure Redis 快取執行個體之外，您還可以使用 [Microsoft Azure 管理庫 (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) 進行調整。若要調整您的快取，請呼叫 `IRedisOperations.CreateOrUpdate` 方法，並傳入 `RedisProperties.SKU.Capacity` 的新大小。

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/zh-tw/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

如需詳細資訊，請參閱[使用 MAML 管理 Redis 快取](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)範例。

## 調整常見問題集

下列清單包含 Azure Redis 快取調整常見問題的解答。

## 調整之後，是否必須變更我的快取名稱或存取金鑰？

否，在調整作業期間，您的快取名稱和金鑰不會變更。

## 調整運作方式

**基本**快取在調整時會關閉，並使用新的大小來佈建新的快取。在此期間，快取無法使用，而且快取中的所有資料都會遺失。

調整**標準**快取時，會關閉其中一個複本，並將其重新佈建到新的大小並傳輸資料，然後另一個複本會先執行容錯移轉，再進行重新佈建，這與其中一個快取節點失敗期間發生的程序類似。

## 我是否會在調整期間遺失快取中的資料

調整**基本**快取時，會遺失所有資料，而且無法在調整作業期間使用快取。

將**標準**快取調整為較大大小時，通常會保留所有資料。將**標準**快取縮小為較小大小時，根據調整時快取中與新大小相關的資料量，資料可能會遺失。如果縮小時遺失資料，則會使用 [allkeys-lru](http://redis.io/topics/lru-cache) 收回原則來收回金鑰。請注意，標準快取有 99.9% SLA 的可用性時，則資料遺失沒有 SLA。

## 是否可以在調整期間使用我的快取

**標準**快取在調整作業期間會保持可用。

**基本**快取在調整作業期間會離線。

## 不支援的作業

在調整作業期間，您無法從**基本**變更為**標準**快取，反之亦然。

您可以從 **C0** (250 MB) 快取放大為較大小，但無法將較大大小縮小為 **C0** 快取。

如果調整作業失敗，服務會嘗試還原作業，而且快取會還原成原始大小。

## 調整需要多長的時間

根據快取中的資料量，調整大約需要 20 分鐘。

## 如何分辨調整何時完成

在入口網站中，您可以看到調整作業進行中。調整完成時，快取的狀態會變更為 [執行中]。

## 為什麼這項功能只能預覽

我們發行這項功能來收到意見。根據意見，我們即將正式發行這項功能。





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO1-->