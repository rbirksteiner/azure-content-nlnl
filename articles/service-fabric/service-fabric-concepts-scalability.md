<properties
   pageTitle="Service Fabric 服務的延展性"
   description="描述如何調整 Service Fabric 服務"
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
   ms.date="04/23/2015"
   ms.author="aprameyr"/>

# 調整 Service Fabric 應用程式
Service Fabric 可讓您透過負載平衡服務、資料分割和叢集中所有節點上的複本，輕鬆建置可調整的應用程式。這可最大化資源使用率。

Service Fabric 應用程式的高延展性可以透過兩種方式來達成：

1. 調整資料分割層級

2. 調整服務名稱層級

## 調整資料分割層級
Service Fabric 支援將個別服務分割為多個小型的資料分割。[資料分割概觀](service-fabric-concepts-partitioning.md)提供所支援資料分割配置類型的相關資訊。每個資料分割的複本會散佈到叢集中的節點。假設有一個使用範圍資料分割配置的服務，其中低索引鍵值為 0、高索引鍵值為 99 且分割計數為 4。在 3 個節點叢集中，服務可能使用四個複本進行配置，並共用每個節點上的資源，如下所示。

![使用三個節點分割配置](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

增加節點數目可讓 Service Fabric 透過將某些複本移動至空白節點，以利用新的節點上的資源。因此，將節點數目增加至四個時，現在會在每個節點上執行三個複本 (屬於不同的資料分割)，可達成更佳的資源使用率和效能。

![使用四個節點分割配置](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## 調整服務名稱層級
服務執行個體是應用程式名稱和服務類型名稱的特定具名執行個體 (請參閱＜[Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md)＞)。您可以在服務建立期間指定要使用的資料分割配置 (＜[分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)＞)。

第一層的調整比例是根據服務名稱。當您較舊的服務執行個體變得忙碌時，您可以建立新的服務執行個體，並包含不同的資料分割層級。這可讓新的服務取用者使用較不忙碌的服務執行個體，以取代忙碌的服務執行個體。

此選項可增加容量，或增加或減少資料分割計數，以建立使用新資料分割配置的新服務執行個體。複雜度在於任何取用的用戶端需要了解何時及如何使用不同名稱的服務。

### 範例案例 – 內嵌的日期
一個可能的案例是使用日期資訊作為服務名稱的一部分。例如，您可以針對 2013 年加入的所有客戶使用具有特定名稱的服務執行個體，而 2014 年加入的客戶則使用其他名稱。此命名配置可以允許名稱根據日期 (2014年的服務執行個體可以透過隨選方式建立來作為 2014 年的方法) 而以程式設計方式增加。

不過，此方法是根據使用應用程式特定命名資訊的用戶端，超出 Service Fabric 的知識領域之外。

- *使用命名慣例*：當您在 2013 年啟動應用程式時，將建立一個稱為 fabric:/app/service2013 的服務。若來到 2013 年的第二季，則將建立另一個稱為 fabric:/app/service2014 的服務。這兩個服務都屬於相同的服務類型。在此方法中，您的用戶端必需具有可根據年份建構適當服務名稱的邏輯。

- *使用查閱服務*：另一個模式是提供次要的「查閱服務」，可提供所需索引鍵的服務名稱。接著新的服務執行個體可透過查閱服務來建立。查閱服務本身不會保留任何應用程式資料，僅保留其所建立的服務名稱相關資料。因此在以上相同年份範例中，用戶端會先連絡查閱服務以針對特定年份找出服務處理資料的名稱，然後使用該服務名稱以執行實際的作業。第一次的查閱結果可以進行快取。

## 後續步驟

如需 Service Fabric 概念的資訊，請參閱下列項目：

- [Service Fabric 服務的可用性](service-fabric-availability-services.md)

- [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

- [定義和管理狀態](service-fabric-concepts-state.md)
 

<!---HONumber=July15_HO2-->