<properties
   pageTitle="在 SQL 資料倉儲中規劃業務持續性 | Microsoft Azure"
   description="SQL 資料倉儲中的業務持續性概觀。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="sahajs"/>


# 在 SQL 資料倉儲中規劃業務持續性

這篇文章逐步說明在 SQL 資料倉儲中規劃業務持續性和災害復原的基本概念。資料倉儲是企業資訊的中央儲存機制，在組織的所有層級上，於分析和商務智慧的日常作業中扮演重要的角色。因此，資料倉儲必須很可靠，且支援復原和持續運作。特別的是，這篇文章將告訴您 SQL 資料倉儲如何讓您使用「還原時間點」和「異地還原」，從使用者錯誤和災害中復原。

## 概念

業務持續性和災害復原計畫必須將下列指標最佳化：

**復原時間目標 (RTO)：**資料庫在干擾性事件之後到完全復原之前，可接受的最長時間，亦即失敗期間可用性的最大損失。

**復原點目標 (RPO)：**當資料庫從干擾性事件復原時，可接受遺失更新的最長時間，亦即失敗期間最多的資料遺失。

**預估復原時間 (ERT)：**在收到還原要求之後，預估資料庫達到完全運作為止的持續時間。

## 業務續航力案例

**從基礎結構失敗中復原：**此案例是指從基礎結構問題中復原，例如磁碟故障等。客戶想要有容錯和高可用性基礎結構來確保業務持續性。

**從使用者錯誤中復原：**此案例是指從意外或偶發的資料損毀或刪除中復原。如果使用者不小心或偶然修改或刪除資料，客戶會想要將資料庫還原到較早的時間點，以確保業務持續性。

**從災害中復原 (DR)：**此案例是指從重大災難中復原。在天然災害或區域性停電等干擾性事件造成資料庫變成無法使用的情況下，客戶會想要復原不同區域的資料庫，讓業務得以持續運作。


## 業務續航力功能

讓我們看看 SQL 資料倉儲如何增強資料庫的可靠性，並於上述案例中支援復原和持續運作。


### 資料備援

由於 SQL 資料倉儲隔開計算和儲存體，您的資料會直接寫入異地備援的 Azure 儲存體 (RA-GRS)。異地備援儲存體會將資料複寫到與主要區域距離數百英哩的次要區域。在主要和次要區域中，您的資料都會複寫三次，且跨越不同的容錯網域和升級網域。即使是全面的區域性停電或災害，造成其中一個區域無法使用，這仍可確保資料的持久性。若要深入了解讀取存取異地備援儲存體，請參閱 [Azure 儲存體備援選項][]。

### 資料庫還原

資料庫還原的設計是為了將您的資料庫還原到較早的時間點。Azure SQL 資料倉儲服務每 8 小時以自動儲存體快照集來保護所有資料庫，並保留 7 天，為您提供一組獨立的還原點。這些備份儲存在 RA-GRS Azure 儲存體，因此，依預設為異地備援。自動備份和還原功能不需額外費用，並提供零成本和免管理的方式，避免資料庫遭到意外損毀或刪除。若要深入了解資料庫還原，請參閱[從使用者錯誤中復原][]。

### 異地還原

異地還原的設計是為了在資料庫因為干擾性事件而變成無法使用時復原資料庫。您可以連絡支援服務，從異地備援的備份還原資料庫，在任何 Azure 區域中建立新的資料庫。因為備份是異地備援，即使因為停電而無法存取資料庫，也能用來復原資料庫。異地還原功能不需要額外費用。


## 後續步驟
若要深入了解其他 SQL Database 版本的業務持續性功能，請閱讀 [SQL Database 業務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[business continuity overview]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Azure 儲存體備援選項]: storage-redundancy/#read-access-geo-redundant-storage-ra-grs.md
[SQL Database 業務持續性概觀]: sql-database-business-continuity.md
[從使用者錯誤中復原]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->