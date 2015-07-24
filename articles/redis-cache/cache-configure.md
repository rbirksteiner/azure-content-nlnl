<properties 
   pageTitle="如何設定 Azure Redis 快取"
   description="了解 Azure Redis 快取的預設 Redis 組態，以及了解如何設定您的 Azure Redis 快取執行個體"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="06/29/2015"
   ms.author="sdanie" />

# 如何設定 Azure Redis 快取

本主題描述如何檢視並更新您的 Azure Redis 快取執行個體的組態，並涵蓋 Azure Redis 快取執行個體的預設 Redis 伺服器組態。

## 設定 Redis 快取設定

可在 [Microsoft Azure Preview 入口網站](https://portal.azure.com)使用 [**瀏覽**] 刀鋒視窗來存取快取。

![Azure Redis 快取瀏覽刀鋒視窗](./media/cache-configure/IC796920.png)

按一下 [Redis 快取] 來檢視您的快取。

![Azure Redis 快取瀏覽快取清單](./media/cache-configure/IC796921.png)

選取所需的快取，來檢視該快取的屬性。

![Redis 快取所有設定](./media/cache-configure/IC808312.png)

按一下 [**設定**] 或 [**所有設定**] 來檢視並設定您的快取。

![Redis 快取設定](./media/cache-configure/IC808313.png)

## 屬性

按一下 [**屬性**]，來檢視您的快取的相關資訊,，包括快取端點和連接埠。

![Redis 快取屬性](./media/cache-configure/IC808314.png)

## 存取金鑰

按一下 [**存取金鑰**] 來檢視或重新產生您的快取的存取金鑰。用戶端會使用這些金鑰與來自 [**屬性**] 刀鋒視窗的主機名稱和連接埠搭配，以連接到您的快取。

![Redis 快取存取金鑰](./media/cache-configure/IC808315.png)

## 存取連接埠

根據預設，新的快取會停用非 SSL 存取。若要啟用非 SSL 連接埠，請按一下 [**存取連接埠**] 刀鋒視窗，然後按一下 [**否**]。

![Redis 快取存取連接埠](./media/cache-configure/IC808316.png)

## 診斷

按一下 [**診斷**] 來設定用來儲存快取診斷的儲存體帳戶。

![Redis 快取診斷](./media/cache-configure/IC808317.png)

如需詳細資訊，請參閱[如何監視 Azure Redis 快取](cache-how-to-monitor.md)。

## Maxmemory-policy 和 maxmemory-reserved

按一下 [**Maxmemory 原則**] 來設定快取的記憶體原則。 **maxmemory-policy** 設定會設定快取的收回原則，而 **maxmemory-reserved** 設定則會設定保留給非快取程序的記憶體。

![Redis 快取 Maxmemory 原則](./media/cache-configure/IC808318.png)

**Maxmemory 原則**可讓您從下列收回原則中選擇。

-	volatile-lru - 這是預設值。
-	allkeys lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

如需 maxmemory 原則的詳細資訊，請參閱[收回原則](http://redis.io/topics/lru-cache#eviction-policies)。

**maxmemory-reserved** 設定會設定保留給非快取作業的記憶體數量 (MB)，例如容錯移轉期間的複寫。當具有高片段比率時，也可使用它。設定此值可讓您在負載變動時具有更一致的 Redis 伺服器體驗。對於頻繁寫入的工作負載，此值應該設定為更高的值。當記憶體保留給這類作業時，無法用於儲存快取的資料。

>[AZURE.IMPORTANT]**maxmemory-reserved** 設定只適用於標準快取。

## Keyspace 通知 (進階設定)

按一下 [**進階設定**] 來設定 Redis Keyspace 通知。Keyspace 通知可讓用戶端在特定事件發生時收到通知。

![Redis 快取進階設定](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Keyspace 通知和 **notify-keyspace-events** 設定只適用於標準快取。

如需詳細資訊，請參閱 [Redis Keyspace 通知](http://redis.io/topics/notifications)。如需範例程式碼，請參閱 [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 範例中的 [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) 檔案。

## 使用者和標記

![Redis 快取使用者和標記](./media/cache-configure/IC808320.png)

[**使用者**] 區段會在入口網站中提供角色型存取控制 (RBAC) 的支援，以協助組織輕鬆又準確地滿足其存取管理需求。如需詳細資訊，請參閱 [Microsoft Azure Preview 入口網站中的角色型存取控制](http://go.microsoft.com/fwlink/?LinkId=512803)。

[**標記**] 區段可協助您組織您的資源。如需詳細資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

## 預設 Redis 伺服器組態

新的 Azure Redis 快取執行個體是以下列的預設 Redis 組態值設定。

>[AZURE.NOTE]無法使用 `StackExchange.Redis.IServer.ConfigSet` 方法變更本區段中的設定。如果在本區段中利用其中一個命令呼叫此方法，則會擲回如下的例外狀況：
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>任何可設定的值 (例如 **max-memory-policy**) 可以透過入口網站設定。

|設定|預設值|說明|
|---|---|---|
|資料庫|16|預設資料庫為 DB 0。您可以根據每個連線使用 connection.GetDataBase(dbid) 選取一個不同的資料庫，其中 dbid 是介於 0 與 15 之間的數字。|
|maxclients|10,000|這是允許同時連線的用戶端數目上限。一旦達到限制，Redis 將關閉所有新的連接，同時傳送「達到用戶端的數目上限」錯誤。|
|maxmemory-policy|volatile-lru|Maxmemory 原則可設定當達到 maxmemory (建立快取時所選取之快取提供項目的大小) 時 Redis 將如何選取要移除的具目。Azure Redis 快取的預設設定為 volatile-lru，其會移除使用 LRU 演算法設定到期日的金鑰。可以在入口網站中設定此設定。如需詳細資訊，請參閱 [Maxmemory-policy 和 maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)。|
|maxmemory-samples|3|LRU 和最小 TTL 演算法不是精確的演算法，而是近似的演算法 (為了節省記憶體)，因此您也可以選取要檢查的範例大小。例如，預設為 Redis 將檢查三個金鑰，並挑選最近較少使用的金鑰。|
|lua-time-limit|5,000|Lua 指令碼的最大執行時間 (以毫秒為單位)。如果已到達最大執行時間，Redis 會記錄指令碼在最大允許的時間之後仍在執行中，並開始回覆查詢發生錯誤。|
|lua-event-limit|500|這是指令碼事件佇列的最大大小。|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|用戶端輸出緩衝區限制可用來強制中斷基於某些原因而無法足夠快地從伺服器讀取資料之用戶端的連線 (常見的原因是 Pub/Sub 用戶端使用訊息的速度無法與發佈者產生這些訊息的速度一樣快)。如需詳細資訊，請參閱 [http://redis.io/topics/clients](http://redis.io/topics/clients)。|

>[AZURE.IMPORTANT]因為 Azure Redis 快取執行個體的設定與管理是使用 Azure 入口網站來進行，所以會停用下列命令。如果嘗試叫用它們，您會收到與 `"(error) ERR unknown command"` 類似的錯誤訊息。
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SAVE
>-	SHUTDOWN
>-	SLAVEOF

如需 Redis 命令的詳細資訊，請參閱 [http://redis.io/commands](http://redis.io/commands)。

## 後續步驟
-	如需使用 Redis 命令的詳細資訊，請參閱[如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)。

<!---HONumber=July15_HO2-->