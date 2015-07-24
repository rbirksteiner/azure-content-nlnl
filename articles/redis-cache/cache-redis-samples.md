<properties 
	pageTitle="Azure Redis 快取範例" 
	description="了解如何使用 Azure Redis 快取" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="sdanie"/>

# Azure Redis 快取範例 

本主題提供 Azure Redis 快取範例清單，其中涵蓋下列這類案例：連線至快取、在快取中讀取和寫入資料，以及使用 ASP.NET Redis 快取提供者。有些範例是可下載的專案，有些則提供逐步指導並包括程式碼片段，但沒有可下載專案的連結。

## Hello World 範例

本節中的範例說明連線至 Azure Redis 快取執行個體以及使用各種語言和 Redis 用戶端在快取中讀取和寫入資料的基本概念。

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例示範如何使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET 用戶端來執行各種快取作業。

此範例示範如何：

-	使用各種連線選項
-	使用同步和非同步作業在快取中讀取和寫入物件
-	使用 Redis MGET/MSET 命令來傳回所指定金鑰的值
-	執行 Redis 交易作業
-	使用 Redis 清單和已排序的集合
-	使用 JsonConvert 序列化程式儲存 .NET 物件
-	使用 Redis 集合來實作標記

如需詳細資訊，請參閱 github 上的 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 文件，如需使用案例，則請參閱 [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) 單元測試。

[如何搭配使用 Azure Redis 快取與 Node.js](cache-nodejs-get-started.md) 說明如何開始搭配使用 Azure Redis 快取與 Node.js 和 [node_redis](https://github.com/mranney/node_redis) 用戶端。

[如何搭配使用 Azure Redis 快取與 Java](cache-java-get-started.md) 說明如何開始搭配使用 Azure Redis 快取與 Java 和 [Jedis](https://github.com/xetorthio/jedis) 用戶端。

[如何搭配使用 Azure Redis 快取與 Python](cache-python-get-started.md) 說明如何開始搭配使用 Azure Redis 快取與 Python 和 [redis-py](https://github.com/andymccurdy/redis-py) 用戶端。

[PHP 範例](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample)說明如何開始搭配使用 Azure Redis 快取與 PHP 和 [predis](https://github.com/nrk/predis) 用戶端。

[使用快取中的 .NET 物件](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects)說明序列化 .NET 物件的一種方式，以在 Azure Redis 快取執行個體中寫入和讀取它們。

## 使用 Redis 快取做為 ASP.NET SignalR 的向外延展後擋板

[使用 Redis 快取做為 ASP.NET SignalR 的向外延展後擋板](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)範例示範如何使用 Azure Redis 快取做為 SignalR 後擋板。如需後擋板的詳細資訊，請參閱[使用 Redis 的 SignalR 向外延展](http://www.asp.net/signalr/overview/performance/scaleout-with-redis)。

## Redis 快取客戶查詢範例

本範例示範如何比較從快取中存取資料與從持續性儲存體中存取資料之間的效能。此範例有兩個專案。

-	[示範 Redis 快取如何透過快取資料來改善效能](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[植入示範的資料庫和快取](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## ASP.NET 工作階段狀態和輸出快取

[使用 Azure Redis 快取儲存 ASP.NET SessionState 和 OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) 範例示範如何使用 Azure Redis 快取，以使用 Redis 的 SessionState 和 OutputCache 提供者來儲存 ASP.NET 工作階段和輸出快取。

## 使用 MAML 管理 Azure Redis 快取

[使用 Azure 管理庫管理 Azure Redis 快取](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)範例示範如何使用 Azure 管理庫來管理 - (建立/更新/刪除) 快取。

## 自訂監視範例

[存取 Redis 快取監視資料](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)範例示範如何在 Azure 入口網站外部存取 Azure Redis 快取的監視資料。

## 使用 PHP 和 Redis 撰寫的 Twitter 風格複製品

[Retwis](https://github.com/SyntaxC4-MSFT/retwis) 範例是 Redis Hello World。它是透過 [Predis](https://github.com/nrk/predis) 用戶端使用 Redis 和 PHP 撰寫的最小 Twitter 風格社交網路複製品。原始碼的設計十分簡單，並且同時顯示不同的 Redis 資料結構。

## 頻寬監視器

[頻寬監視器](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor)範例可讓您監視用戶端上使用的頻寬。若要測量頻寬，請在快取用戶端電腦上執行這個範例、呼叫快取，並觀察頻寬監視器範例所報告的頻寬。

<!---HONumber=July15_HO1-->