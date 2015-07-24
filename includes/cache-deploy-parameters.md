
### redisCacheName

要建立的 Azure Redis 快取的名稱。

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

新的 Azure Redis 快取的定價層。

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

此範本定義此參數允許的值 (基本或標準)，並指派未指定任何值時的預設值 (基本)。「基本」提供單一節點，有多種大小可用，最大為 53 GB。「標準」提供雙節點「主要/複本」，有多種大小可用，最大為 53 GB，還有高達 99.9% 的 SLA。

### redisCacheFamily

Sku 系列。

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

新的 Azure Redis 快取執行個體的大小。

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

此範本定義此參數允許的值 (0、1、2、3、4、5 或 6)，並指派未指定任何值時的預設值 (0)。這些數字對應到下列快取大小：0 = 250 MB、1 = 1 GB、2 = 2.5 GB、3 = 6 GB、4 = 13 GB、5 = 26 GB、6 = 53 GB

### redisCacheVersion

新的快取的 Redis 伺服器版本。

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=July15_HO1-->