<properties
   pageTitle="Azure Redis-cache gebruiken met behulp van Java | Microsoft Azure"
    description="Aan de slag met Azure Redis-cache met behulp van Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Azure Redis-cache gebruiken met behulp van Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis-cache geeft u toegang tot een toegewezen Redis-cache, beheerd door Microsoft. Uw cache is toegankelijk vanuit elke toepassing in Microsoft Azure.

In dit onderwerp wordt beschreven hoe u aan de slag kunt met Azure Redis-cache met gebruik van Java.

## Vereisten

[Jedis](https://github.com/xetorthio/jedis): Java-client voor Redis

In deze zelfstudie wordt Jedis gebruikt, maar u kunt elke andere Java-client gebruiken die wordt vermeld op [http://redis.io/clients](http://redis.io/clients).

## Een Redis-cache maken op Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## De hostnaam en toegangssleutels ophalen

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Het eindpunt zonder SSL-beveiliging inschakelen

Sommige Redis-clients bieden geen ondersteuning voor SSL. De [poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe exemplaren van Azure Redis-cache](cache-configure.md#access-ports). Op het moment van publicatie van dit artikel biedt de [Jedis](https://github.com/xetorthio/jedis)-client geen ondersteuning voor SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Iets toevoegen aan de cache en dit ophalen

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
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


## Volgende stappen

- [Schakel de diagnostische gegevens van de cache in](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), zodat u de status van de cache kunt [bewaken](https://msdn.microsoft.com/library/azure/dn763945.aspx).
- Lees de officiële [Redis-documentatie](http://redis.io/documentation).




<!--HONumber=Jun16_HO2-->


