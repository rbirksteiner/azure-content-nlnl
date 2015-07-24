<properties 
   pageTitle="Azure 自動化資料保留"
   description="描述 Azure 自動化的資料保留原則。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure 自動化資料保留

當您刪除 Azure 自動化中的資源時，會將它保留 90 天作為稽核用途，之後才永久移除。在這段期間您無法看到或使用該資源。此原則也適用於屬於已刪除的自動化帳戶的資源。

Azure 自動化會自動刪除並永久移除超過 90 天的工作。

下表摘要說明不同的資源的保留原則。

|資料|原則|
|:---|:---|
|帳戶|刪除使用者帳戶 90 天後永久移除。|
|Assets|使用者刪除資產後 90 天，或使用者刪除持有資產的帳戶 90 天後永久移除。|
|模組|使用者刪除模組後 90 天，或使用者刪除持有模組的帳戶 90 天後永久移除。|
|Runbook|使用者刪除資源後 90 天，或使用者刪除持有資源的帳戶 90 天後永久移除。|
|作業|在上次修改日期的 90 天後刪除並永久移除。這可以是在工作完成、停止或暫止之後。|

保留原則適用於所有使用者，而且目前無法自訂。

## 相關文章
- [備份 Azure 自動化](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=62-->