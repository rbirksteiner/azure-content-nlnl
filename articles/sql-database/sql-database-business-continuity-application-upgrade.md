<properties 
   pageTitle="應用程式升級期間的 SQL Database 業務續航力" 
   description="本節提供避免在應用程式升級期間停機之業務續航力的指引。" 
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

#應用程式升級而不需要停機

在 Microsoft Azure 的內容中，「應用程式」一詞指的是前端、部署在雲端服務中的服務，以及用來保存應用程式資料或中繼資料的資料層等元件。雲端應用程式通常會設計成提供全天候不中斷的服務。推出新版應用程式時，在即時網站中套用資料層中的變更可能會造成一些中斷情況，例如減少可用的功能或甚至完全停機。

設計應用程式升級程序時，主要目標應該在於完全避免或大幅縮短應用程式性能降低的持續時間。為了達成該目的，這個程序通常會包括建立應用程式的暫存複本，以在升級失敗時做為備份使用。設計和規劃升級時，應該考慮下列因素：

1.	可接受應用程式性能降低的時間上限。 
2.	升級程序期間可用的最小功能集。
3.	升級期間發生任何錯誤時的回復能力。
4.	所涉及的總金額成本，其中包括建立暫存複本所需的額外應用程式元件成本 (例如用於作用中異地複寫的額外高階資料庫)，以及升級程序所使用之暫時部署的累加成本。 

如果應用程式可以暫時在唯讀模式下運作，則可以設計升級工作流程，有效地完全避免停機時間。若要了解如何實作特定應用程式拓撲的升級工作流程，請參閱[使 Azure SQL Database 在應用程式輪流升級期間的中斷時間最短的最佳作法](https://msdn.microsoft.com/library/azure/dn790385.aspx)
 
 

<!---HONumber=July15_HO2-->