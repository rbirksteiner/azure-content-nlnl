<properties
   pageTitle="選擇一種架構"
   description="Service Fabric 提供兩個高階架構來建置服務：動作項目架構和服務架構。了解每個值將協助您針對應用程式做出正確的架構決定。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/15/2015"
   ms.author="seanmck"/>

# 選擇您服務的架構

Service Fabric 提供兩個高階架構來建置服務：可靠動作項目 API 和可靠服務 API。雖然兩者都建置於相同的 Service Fabric 核心，但會在並行處理、資料分割以及通訊方面的簡易性和彈性之間進行不同的權衡。了解這兩個模型相當實用，以便您可以在應用程式中選擇特定服務的適當架構。

## 比較可靠動作項目 API 和可靠服務 API

|**可靠動作項目 API**|**可靠服務 API**|
|-----------------------|--------------------------|
|您的問題領域涉及許多小型的獨立狀態和邏輯單位|您需要跨越多個元件維護邏輯|
|您想要使用單一執行緒的物件，同時仍能夠調整和維護一致性|您想要使用可靠的集合 (例如 .NET 字典和佇列) 來儲存和管理您的狀態|
|您想要讓架構管理狀態的並行存取和資料粒度|您想要控制狀態的資料粒度和並行存取|
|您想要讓平台為您管理通訊|您想要管理通訊和控制您服務的資料分割配置|

請記住，在您的應用程式中針對不同的服務使用不同的架構相當合理。例如，您可能擁有可設定狀態的服務，用來彙總由一些動作項目所產生的資料。

## 後續步驟

- [深入了解可靠動作項目 API](service-fabric-reliable-actors-introduction.md)
- [深入了解可靠服務 API](../Service-Fabric/service-fabric-reliable-services-introduction.md)
 

<!---HONumber=July15_HO2-->