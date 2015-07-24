<properties 
   pageTitle="Azure Redis 快取的常見快取模式" 
   description="了解使用 Azure Redis 快取的位置和原因" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="05/21/2015"
   ms.author="riande"/>

# Azure Redis 快取的常見快取模式

此頁面列出使用快取的最常見好處。

## 使用快取最佳化資料存取

使用快取，可以透過從資料存放區擷取而大幅加快資料存取。快取提供高輸送量和低延遲。從快取中提取熱資料時，不只可加快您的應用程式，還可以減少資料存取負載，並提高其他查詢的回應速度。將資訊儲存在快取中有助於節省資源，並隨著應用程式增加的需求而增加延展性。您的應用程式可以有效率地從快取中擷取資料時，將會更有效地回應爆發載入。

## 分散式工作階段狀態
雖然最好是避免使用工作階段狀態，但是有些應用程式使用工作階段資料實際上具有效能/降低複雜度優點，同時其他應用程式都需要工作階段狀態。記憶體提供者中工作階段狀態的預設值不允許向外延展 (執行網站的多個執行個體)。ASP.NET SQL Server 工作階段狀態提供者可讓多個網站使用工作階段狀態，但相較於記憶體提供者，延遲成本也較高。Redis 工作階段狀態快取提供者是很容易設定的低延遲替代方案。如果您的應用程式只使用有限數量的工作階段狀態，則可以使用大部分的快取來快取資料以及少量的工作階段狀態。

## 仍正常運作的服務停機時間 (快取後援)
 將資料儲存在快取中，應用程式即可不受系統失敗 (例如網路延遲、Web 服務問題和硬體故障) 影響。通常最好提供快取的資料，除非您的 Web 服務或資料庫復原，而不是您的應用程式完全失敗。

## 後續步驟
深入了解如何使用 Azure Redis 快取：
 
- [Redis Azure 快取文件](http://azure.microsoft.com/documentation/services/cache/)：此頁面提供許多使用 Redis Azure 快取的不錯連結。
- [使用 Azure Redis 快取的 MVC 電影應用程式 (15 分鐘)](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)：本部落格文章提供在 ASP.NET MVC 應用程式中使用 Azure Redis 快取的快速入門。
- [如何將 ASP.NET 工作階段狀態與 Azure 網站搭配使用](../app-service-web/web-sites-dotnet-session-state-caching.md)：本主題說明如何將 Azure Redis 快取服務用於工作階段狀態。

<!---HONumber=July15_HO1-->