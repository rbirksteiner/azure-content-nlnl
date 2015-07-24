<properties
   pageTitle="定義和管理狀態"
   description="如何定義和管理 Service Fabric 中的服務狀態"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# 服務狀態
**服務狀態**是服務正常運作所需的資料。也是一種透過服務讀取和寫入才能執行工作的資料結構和變數。

例如：假設有一個簡易計算機服務。此服務會採用兩個數字並傳回其總和。這是一種沒有與其相關聯資料的純粹無狀態服務。

現在，假設有一個相同的計算機，但除了運算總和，也有一個傳回最後一個已計算總和的方法。此服務現在成為可設定狀態 - 其中包含某些要寫入 (當其計算新的總和) 和讀取 (當其傳回最後一個已計算總和) 的狀態。

在 Service Fabric 中，第一個服務稱為無狀態服務。第二項服務稱為可設定狀態服務。

## 儲存服務狀態
狀態可進行外部化，或使用處理狀態的程式碼進行共置。狀態的外部化通常是使用外部資料庫或存放區來完成。在我們的計算機範例中，這可能是 SQL 資料庫，即目前資料表中儲存的結果。每個計算總和的要求會在此資料列上執行更新。

狀態也可以使用處理此程式碼的程式碼進行共置。Service Fabric 中的可設定狀態服務是使用此模型來建置。Service Fabric 會提供基礎結構，以確保在發生失敗時此狀態具有高可用性和容錯功能。

## 後續步驟

如需 Service Fabric 概念的資訊，請參閱下列項目：

- [Service Fabric 服務的可用性](service-fabric-availability-services.md)

- [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)

- [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)
 

<!---HONumber=July15_HO2-->