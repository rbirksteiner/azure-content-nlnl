<properties
   pageTitle="如何搭配使用 Azure Redis 快取與 Java"
   description="開始搭配使用 Azure Redis 快取與 Java"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# 如何搭配使用 Azure Redis 快取與 Java

Azure Redis 快取可讓您存取 Microsoft 所管理的專用安全 Redis 快取。從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何開始搭配使用 Azure Redis 快取與 Java。


## 必要條件

[Jedis](https://github.com/xetorthio/jedis) - Redis 的 Java 用戶端

本教學課程使用 Jedis，但是您可以使用列在 [http://redis.io/clients](http://redis.io/clients) 的任何 Java 用戶端。


## 在 Azure 上建立 Redis 快取

在 [Azure 管理入口網站預覽](http://go.microsoft.com/fwlink/?LinkId=398536)中，依序按一下 [新增] 和 [資料 + 儲存體]，然後選取 [Redis 快取]。

  ![][1]

輸入 DNS 主機名稱。它的形式為 `<name>.redis.cache.windows.net`。按一下 [建立]。

  ![][2]


建立快取之後，請在入口網站中按一下它，以檢視快取設定。按一下 [金鑰] 下方的連結，並複製主要金鑰。您需要有此金鑰才能驗證要求。

  ![][4]


## 啟用非 SSL 端點


按一下 [連接埠] 下方的連結，然後按一下 [否] 表示「只允許透過 SSL 存取」。這會啟用快取的非 SSL 連接埠。Jedis 用戶端目前不支援 SSL。

  ![][3]


## 在快取中加入項目並擷取該項目

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## 後續步驟

- [啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)，以[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)您快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=July15_HO1-->