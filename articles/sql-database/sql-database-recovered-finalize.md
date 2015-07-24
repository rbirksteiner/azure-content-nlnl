<properties
   pageTitle="完成復原的 Azure SQL Database"
   description="還原時間點, Microsoft Azure SQL Database, 還原資料庫, 復原資料庫, Azure 管理入口網站, Azure 入口網站"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="04/13/2015"
   ms.author="elfish"/>

# 完成復原的 Azure SQL Database

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md)
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)

## 概觀

您必須逐一執行本文提供的檢查清單工作，才能將剛復原的 Azure SQL Database 放回生產環境。這份檢查清單適用於從異地複寫容錯移轉復原的資料庫、將刪除的資料庫還原、時間點還原或異地還原。

## 更新連接字串

請確認您應用程式的連接字串是指向剛復原的資料庫。如果有以下其中一種情況，請更新連接字串：

  + 復原的資料庫使用的名稱與來源資料庫不同
  + 復原的資料庫和來源伺服器位於不同的伺服器

如需關於變更連接字串的詳細資訊，請參閱[以程式設計方式連接至 Azure SQL Database 的指導方針](https://msdn.microsoft.com/library/azure/ee336282.aspx)和[連接至 Azure SQL Database：重要的建議](sql-database-connect-central-recommendations.md)。
 
## 修改防火牆規則
請確認伺服器層級和資料庫層級的防火牆規則，並確定已啟用您用戶端電腦或 Azure 與伺服器以及剛復原之資料庫的連接。如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)和[作法：進行防火牆設定 (Azure SQL Database)](https://msdn.microsoft.com/library/azure/jj553530.aspx)。

## 確認伺服器登入和資料庫使用者

確認應用程式使用的所有登入，是否都在主控您已復原資料庫的伺服器上。重新建立缺少的登入，並將適當的已復原資料庫之權限授與登入。如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

請確認已復原資料庫中的每位資料庫使用者，是否都和有效的伺服器登入相關聯。使用 ALTER USER 陳述式，將使用者對應至有效的伺服器登入。如需詳細資訊，請參閱 [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486)。


## 設定遙測警示

確認現有的警示規則設定是否對應至復原的資料庫。如果有以下其中一種情況，請更新設定：

  + 復原的資料庫使用的名稱與來源資料庫不同
  + 復原的資料庫和來源伺服器位於不同的伺服器

如需關於資料庫警示規則的詳細資訊，請參閱[作法：在 Azure 中接收警示通知及管理警示規則](https://msdn.microsoft.com/library/azure/dn306638.aspx)。


## 啟用稽核

如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。用戶端應用程式必須在 *.database.secure.windows.net 的模式中使用安全連接字串，才能有良好的稽核指標。如需詳細資訊，請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)。
 

<!---HONumber=July15_HO2-->