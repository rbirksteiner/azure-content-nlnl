<properties
   pageTitle="Service Fabric 服務的可用性"
   description="描述錯誤偵測、容錯移轉、服務復原"
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

# Service Fabric 服務的可用性
Service Fabric 服務可能是可設定狀態或無狀態。本文章概述 Service Fabric 如何維護服務失敗時的可用性。

## Service Fabric 無狀態服務的可用性
無狀態服務是一種應用程式服務，並不具有任何[本機永續性狀態](service-fabric-concepts-state.md)。

建立無狀態服務需要定義執行個體計數，即應該在叢集中執行的無狀態服務執行個體數目。這些是會在叢集中具現化的應用程式邏輯複本數目。增加執行個體數目是一種相應放大無狀態服務的建議方式。

當無狀態服務的任何執行個體上偵測到錯誤時，系統會在叢集中某些其他適用節點上建立新的執行個體。

## Service Fabric 可設定狀態服務的可用性
可設定狀態服務具有某些與其相關聯的狀態。在 Service Fabric 中，可設定狀態服務會模型化為複本集。每個複本是一種服務程式碼的執行個體，其中具有一個狀態複本。用來執行讀取和寫入作業的複本 (稱為主要複本)。由於寫入作業*複寫*至多個其他複本而變更狀態的複本 (稱為作用中次要複本)。主要和作用中次要複本的組合即為服務中的複本集。

僅能使用一個主要複本提供讀取和寫入要求服務。但可以使用多個作用中次要複本。作用中次要複本的數目可供設定，且較高的複本數可容許較多的並行軟體和硬體失敗。

若發生失敗 (主要複本當機) 時，Service Fabric 會讓其中一個作用中次要複本成為新的主要複本。此作用中次要複本已具有狀態的已更新版本 (透過 *複寫*) 且能夠繼續處理進一步的讀取和寫入作業。

此主要或作用中次要複本的概念又稱為複本角色。

### 複本角色
複本角色用來管理由該複本管理的狀態生命週期。複本角色主要用來提供讀取要求服務。也會透過更新其狀態，並將變更複寫至其複本集中的作用中次要複本來提供寫入要求服務。作用中次要複本負責接收主要複本已複寫並更新其狀態檢視的狀態變更。

>[AZURE.NOTE]更高階的程式設計模型，例如[可靠動作項目架構](service-fabric-reliable-actors-introduction.md)會從開發人員抽離複本角色的概念。

## 後續步驟

如需 Service Fabric 概念的資訊，請參閱下列項目：

- [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)

- [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

- [定義和管理狀態](service-fabric-concepts-state.md)
 

<!---HONumber=July15_HO2-->