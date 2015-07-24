<properties 
   pageTitle="Azure 儲存體延展性和效能目標 | Microsoft Azure"
   description="了解 Azure 儲存體的延展性和效能目標，包括標準和進階儲存體帳戶的容量、要求率以及輸入和輸出頻寬。了解每一項 Azure 儲存體服務內分割的效能目標。"
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="06/30/2015"
   ms.author="tamram" />

# Azure 儲存體延展性和效能目標

本主題描述 Microsoft Azure 儲存體的延展性和效能。如需其他 Azure 限制的摘要，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。

>[AZURE.NOTE]所有儲存體帳戶都能在一般網路拓撲上執行，並支援下面說明的延展性和效能目標，無論它們在何時建立。如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

<!-- -->

>[AZURE.IMPORTANT]列於此處的延展性和效能目標都是高階目標，但仍可達成。在所有情況下，您的儲存體帳戶所達到的要求率和頻寬取決於已儲存物件的大小、使用的存取模式、應用程式執行的工作負載類型。請務必測試您的服務，以判斷效能是否達到您的要求。如果可能，請避免流量率突增，確保流量在不同分割之間妥善分散。

>當您的應用程式達到分割處理工作負載的限制時，Azure 儲存體會開始傳回錯誤碼 503 (伺服器忙碌) 或錯誤碼 500 (作業逾時) 回應。當發生這種情況時，應用程式應該使用指數輪詢重試原則。指數輪詢讓分割的負載減少，也能減輕該分割流量的尖峰。

如果您的應用程式需求超出單一儲存體帳戶的延展性目標，您可以建置可使用多個儲存體帳戶的應用程式，並將資料物件分割到那些儲存體帳戶中。單一 Azure 訂用帳戶允許 100 個儲存體帳戶。如需批量價格的相關資訊，請參閱[儲存體定價詳細資料](http://azure.microsoft.com/pricing/details/storage/)。

## 標準儲存體帳戶的延展性目標

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Premium 儲存體帳戶延展性目標

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../../includes/azure-storage-limits-premium-storage.md)]

## 存放裝置 - Azure 資源管理員

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Azure 儲存體中的分割

每個在 Azure 儲存體中保存資料的物件 (Blob、訊息、實體和檔案) 都屬於分割，也都由分割索引鍵所識別。分割會判斷 Azure 儲存體要如何在伺服器間取得 Blob、訊息、實體和檔案的負載平衡，以滿足這些物件的流量需求。分割索引鍵在儲存體帳戶中是唯一的，用於尋找 Blob、訊息或實體。

上方位於[標準儲存體帳戶的延展性目標](#scalability-targets-for-standard-storage-accounts)中的表格列出每個服務的單一分割的效能目標。

分割會影響每個儲存體服務的負載平衡和延展性，方式如下：

- **Blob**：Blob 的分割索引鍵為容器名稱 + Blob 名稱。這代表每個 Blob 都有專屬的分割。因此，Blob 可以在許多伺服器間分散，以相應放大對它們的存取。雖然 Blog 可以在 Blog 容器中邏輯分組，但這種分組方式的意涵卻非分割。

- **訊息**：訊息的分割索引鍵為佇列名稱，所以所有佇列中的訊息都能分組到單一分割，並由單一伺服器所服務。不同佇列可能由不同伺服器所處理以平衡負載，無論儲存體帳戶可能有多少佇列。

- **實體**：實體的分割索引鍵為資料表名稱 + 分割索引鍵，其中分割索引鍵是該實體由使用者定義之 **PartitionKey** 屬性所需的值。

	所有具有相同分割區索引鍵的實體都分組成一個相同的分割，並儲存在相同的分割伺服器上。在設計您的應用程式時，這是要了解的重點。藉由將實體分組到單一分割的資料存取優勢，您的應用程式應該會在多個分割中分配實體時取得延展性優勢的平衡。

	將資料表中的一組實體分組成一個單一分割的主要優點在於，這能讓它在相同分割中的實體之間執行不可部分完成的批次作業，因為單一伺服器上存有一個分割。因此如果您想要執行批次作業，請考慮將具有相同的分割區索引鍵的實體分在一組。

	另一方面，位於相同資料表中，但屬於不同分割的實體，可在不同伺服器之間達負載平衡，這可以擁有具有較大延展性的大型資料表。

## 另請參閱

- [儲存體定價詳細資料](http://azure.microsoft.com/pricing/details/storage/)
- [Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)
- [Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal/)
- [Azure 儲存體複寫](storage-redundancy.md)
- [Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)
- [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。



 

<!---HONumber=July15_HO2-->