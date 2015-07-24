<properties
   pageTitle="如何搭配使用 Azure Redis 快取與 Python"
   description="開始搭配使用 Azure Redis 快取與 Python"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# 如何搭配使用 Azure Redis 快取與 Python

本主題說明如何搭配使用 Azure Redis 快取與 Python。


## 必要條件

安裝 [redis-py](https://github.com/andymccurdy/redis-py)。


## 在 Azure 上建立 Redis 快取

在 [Azure 管理入口網站預覽](http://go.microsoft.com/fwlink/?LinkId=398536)中，依序按一下 [新增] 和 [資料 + 儲存體]，然後選取 [Redis 快取]。

  ![][1]

輸入 DNS 主機名稱。它的形式為 `<name>.redis.cache.windows.net`。按一下 [建立]。

  ![][2]

建立快取之後，請在入口網站中按一下它，以檢視快取設定。您將需要：

- **主機名稱。** 這是您在建立快取時輸入的內容。
- **連接埠。** 按一下 [連接埠] 下方的連結，以檢視連接埠。請使用 SSL 連接埠。
- **存取金鑰。** 按一下 [金鑰] 下方的連結，並複製主要金鑰。

## 在快取中加入項目並擷取該項目

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

將 *&lt;name&gt;* 取代為您的快取名稱，並將 *&lt;key&gt;* 取代為您的存取金鑰。


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=July15_HO1-->