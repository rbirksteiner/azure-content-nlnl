<properties
	pageTitle="Microsoft Azure 訂用帳戶及服務限制、配額與限制"
	description="提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。這包括如何增加限制和最大值的詳細資訊。"
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="mollybos"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2015"
	ms.author="jroth"/>

# Azure 訂閱和服務限制、配額與限制

## 概觀

本文件說明一些最常見的 Microsoft Azure 限制。請注意，這目前未涵蓋所有 Azure 服務。這些限制將隨著時間擴展並更新以涵蓋更多平台。

> [AZURE.NOTE]如果您想要將限制提升到**預設限制**以上，您可以[免費提出線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。您無法將限制提升到高於下表中所示的**上限**值。如果沒有**上限**欄，指定的資源即沒有可調整的限制。

## 限制和 Azure 資源管理員

現在您可以結合多個 Azure 中的資源到單一的 Azure 資源群組。使用資源群組時的限制是，在全域時會使用 Azure 資源管理員在地區層級管理。如需 Azure 資源群組的詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](resource-group-portal.md)。

在以下的限制中，已加入了新資料表，以反映在使用 Azure 資源管理員時的限制方面的任何差異。例如，有**訂用帳戶限制**資料表和**訂用帳戶限制 - Azure 資源管理員**資料表。當某個限制同時適用於這兩個案例時，只會顯示在第一個資料表中。除非另有說明，限制在所有區域中全域適用。

> [AZURE.NOTE]請務必強調 Azure 資源群組中資源的配額是基於您的訂閱可以存取的每一區域，而不是每一訂閱 (服務管理配額則是)。讓我們以核心配額為例。如果您需要要求增加配額以支援核心，您必須決定您想要在哪些區域中使用多少個核心，然後提出 Azure 資源群組核心配額的特定要求，以取得您想要的數量和區域。因此，如果您需要在西歐使用 30 個核心以在該處執行應用程式，您應該在西歐特別要求 30 個核心。但是您在任何其他區域中的核心配額將不會增加 -- 僅西歐會有 30 個核心配額。<!-- --> 因此，考慮決定每個區域中您的工作負載所需的 Azure 資源群組配額，並在要考慮部署的每個區域中要求該數量可能會有所幫助。請參閱[移難排解部署問題](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues)，以取得探索您特定區域目前的配額的其他說明。

## 訂閱限制

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]


## 訂用帳戶限制 - Azure 資源管理員 

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## 資源群組限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## 虛擬機器限制

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


## 虛擬機器限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## 網路限制

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


## 網路限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## 標準儲存體限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

## 進階儲存體限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
</tr>
<tr>
   <td valign="middle"><p>每一帳戶的磁碟容量總計</p></td>
   <td valign="middle"><p>35 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一帳戶的快照集容量總計</p></td>
   <td valign="middle"><p>10 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一帳戶的最大頻寬 (輸入 + 輸出)</p></td>
   <td valign="middle"><p>50 Gbps</p></td>
</tr>
</table>


## 存放裝置 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## 雲端服務限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Web 應用程式 (網站) 限制

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]


## 批次預覽版限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Mobile Engagement 限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## SQL Database 限制

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]


## 媒體服務限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]


## 服務匯流排限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]


## Active Directory 限制

[AZURE.INCLUDE [azure-active-directory-limits](../includes/azure-active-directory-limits.md)]


## RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## StorSimple 系統限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]
 
## 另請參閱

[了解 Azure 限制和增加](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=62-->