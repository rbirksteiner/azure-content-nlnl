<properties
    pageTitle="Azure Redis-cache gebruiken met behulp van Python | Microsoft Azure"
    description="Aan de slag met Azure Redis-cache met behulp van Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Azure Redis-cache gebruiken met behulp van Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

In dit onderwerp wordt beschreven hoe u aan de slag kunt met Azure Redis-cache met behulp van Python.


## Vereisten

Installeer [redis-py](https://github.com/andymccurdy/redis-py).


## Een Redis-cache maken op Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## De hostnaam en toegangssleutels ophalen

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Het eindpunt zonder SSL-beveiliging inschakelen

Sommige Redis-clients bieden geen ondersteuning voor SSL. De [poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe exemplaren van Azure Redis-cache](cache-configure.md#access-ports). Op het moment van publicatie van dit artikel biedt de [redis-py](https://github.com/andymccurdy/redis-py) geen ondersteuning voor SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Iets toevoegen aan de cache en dit ophalen


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Vervang `<name>` door de cachenaam en `key` door uw toegangssleutel.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png



<!--HONumber=Jun16_HO2-->


