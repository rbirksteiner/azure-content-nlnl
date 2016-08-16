<properties
    pageTitle="Azure Redis-cache gebruiken met behulp van Node.js | Microsoft Azure"
    description="Aan de slag met Azure Redis-cache met behulp van Node.js en node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Azure Redis-cache gebruiken met behulp van Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis-cache geeft u toegang tot een beveiligde, toegewezen Redis-cache, beheerd door Microsoft. Uw cache is toegankelijk vanuit elke toepassing in Microsoft Azure.

In dit onderwerp wordt beschreven hoe u aan de slag kunt met Azure Redis-cache met gebruik van Node.js. Zie voor een ander voorbeeld van het gebruik van Azure Redis-Cache met behulp van Node.js [Een Node.js-chattoepassing bouwen met Socket.IO op een Azure-website](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Vereisten

[node_redis](https://github.com/mranney/node_redis) installeren:

    npm install redis

In deze zelfstudie wordt [node_redis](https://github.com/mranney/node_redis) gebruikt, maar u kunt elke andere Node.js-client gebruiken die wordt vermeld op [http://redis.io/clients](http://redis.io/clients).

## Een Redis-cache maken op Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## De hostnaam en toegangssleutels ophalen

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Het eindpunt zonder SSL-beveiliging inschakelen

Sommige Redis-clients bieden geen ondersteuning voor SSL. De [poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe exemplaren van Azure Redis-cache](cache-configure.md#access-ports). Op het moment van publicatie van dit artikel biedt [node_redis](https://github.com/mranney/node_redis) geen ondersteuning voor SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Iets toevoegen aan de cache en dit ophalen

      var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Uitvoer:

    OK
    value


## Volgende stappen

- [Schakel de diagnostische gegevens van de cache in](cache-how-to-monitor.md#enable-cache-diagnostics), zodat u de status van de cache kunt [bewaken](cache-how-to-monitor.md).
- Lees de officiële [Redis-documentatie](http://redis.io/documentation).






<!--HONumber=Jun16_HO2-->


