<properties 
   pageTitle="異地批次處理層 (Azure 架構模式)" 
   description="異地批次處理層模式是基礎結構區域的一部分，CPIF 架構文件中有相關的詳細說明。" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 異地批次處理層 (Azure 架構模式)

[雲端平台整合架構 (CPIF)](azure-architectures-cpif-overview.md) 提供工作負載整合指引，供您將應用程式上架到 Microsoft 雲端解決方案。

CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。

**異地批次處理層**模式是**基礎結構**區域的一部分，CPIF 架構文件中有相關的詳細說明。

##  異地批次處理層

異地批次處理層設計模式詳述供應容錯且可擴充之後端資料處理能力所需的 Azure 功能和服務。您可以將這些服務理解為 Azure 雲端服務中的背景工作角色，這些角色目前可供部署在任何 Azure 資料中心內。

批次處理工作負載是獨特的，原因在於它們通常只提供少量或不提供使用者介面。在 Windows Server 上運作的 Windows Service 即是這類內部部署工作負載的範例。在雲端環境中考量這類工作負載時，部署整部伺服器來執行工作負載是很浪費的事，因為它真正需要的是計算、儲存及網路連線。背景工作角色是這類工作負載在 Azure 上的實作。

根據定義，在 Azure 中執行的批次處理工作是連接資源、提供某些商務邏輯 (計算) 及提供某些輸出的工作負載。輸入和輸出資源乃由使用者定義，因此一般檔案、Azure Blob 儲存體中的 Blob、NoSQL 資料庫或關聯式資料庫均能成為這類資源。

商務邏輯乃是在 Azure 背景工作角色中實作，通常是透過在 .NET 程式庫中定義所需商務邏輯的方法來實現。雖然將背景工作角色部署到 Azure 是簡單的作業，不過部署可容錯且可擴充的背景工作角色則需要顧及服務在 Azure 中的執行和維護方式。本模式將詳述顧及以上需求的設計，並描述需求的實作方式。

## 架構模式概觀 

本文件描述使用 Azure 雲端服務包含之背景工作角色執行個體來進行異地批次處理的模式。這項設計的關鍵元件如下所示。本圖說明實現容錯至少應具備的執行個體數目。您可以部署額外的執行個體來提升服務效能。此外，啟用自動調整也有助於依照時間或其他伺服器度量調整執行個體。

##  其他資源
[批次處理層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## 另請參閱
[CPIF 架構](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[全域負載平衡 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負載平衡資料層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[混合式網路功能](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 搜尋服務層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO1-->