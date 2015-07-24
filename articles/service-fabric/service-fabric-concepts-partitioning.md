<properties
   pageTitle="分割 Service Fabric 服務"
   description="描述如何分割 Service Fabric 服務"
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

# 分割 Service Fabric 服務
Service Fabric 支援服務狀態的資料分割，並在總狀態子集上進行每個資料分割，可讓您輕鬆地建置可調整的可設定狀態。每個資料分割會變成一個[高可用性](service-fabric-availability-services.md)的單位。資料分割的複本會分散於叢集中節點，並達到平衡狀態。

> [AZURE.NOTE]雖然無狀態服務也可以進行資料分割，但這種情況非常少見，且對於多數 Service Fabric 服務來說並非必要。

您可以使用三種不同的資料分割配置。

## 單一資料分割配置
此功能用來指定不需要進行資料分割的服務。

## 具名資料分割配置
此功能用來針對服務的每個資料分割指定固定的名稱集。個別資料分割可以透過其名稱進行查詢。

## 範圍資料分割配置
此功能用來指定整數範圍 (透過低和高索引鍵來識別) 和資料分割數目 (n)。它會建立 n 個資料分割，每個皆負責一個非重疊的子範圍。範例：範圍資料分割配置 (適用於具有三個複本的服務) 具有低索引鍵值 0，和高索引鍵值 99，以及 4 個分割計數，則會建立 4 個資料分割，如下所示。

![範圍資料分割](./media/service-fabric-concepts-partitioning/range-partitioning.png)

最常見的情況是在資料集內建立唯一索引鍵的雜湊。索引鍵的某些常見範例可能是汽車識別號碼 (VIN)、員工識別碼或唯一字串。接著您可以使用該唯一索引鍵來建立很長的雜湊程式碼，以及要作為索引鍵使用的索引鍵範圍模數。您可以指定可允許索引鍵範圍的上限和下限。

此外，您應該估計夠高的資料分割數目，以便處理最壞情況的資源載入 (例如記憶體限制或網路頻寬)，但要避免設定太高數目而導致資料分割極度疏鬆。

### 選取雜湊演算法
雜湊中的重要部分即為選取雜湊演算法。無論目標是分組彼此靠近的類似索引鍵 (位置機密雜湊)，或應該廣泛分散到所有資料分割的活動 (分散雜湊)，皆屬於重要的考量。

一般選擇雜湊程式碼演算法的絕佳資源是[雜湊函數的維基百科頁面](http://en.wikipedia.org/wiki/Hash_function)。

> [AZURE.NOTE]您無法變更資料分割的數目，或建立後用於服務的資料分割配置類型。

## 後續步驟

如需 Service Fabric 概念的資訊，請參閱下列項目：

- [Service Fabric 服務的可用性](service-fabric-availability-services.md)

- [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)
 

<!---HONumber=July15_HO2-->