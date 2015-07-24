<properties 
   pageTitle="SQL Database 嚴重損壞修復" 
   description="了解如何使用 Azure SQL Database 的異地複寫和異地還原功能，從區域資料中心中斷或失敗情況復原資料庫。" 
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

# 從中斷情況復原 Azure SQL Database

Azure SQL Database 提供幾項中斷復原功能：

- 主動式異地複寫 [(部落格)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- 標準異地複寫 [(部落格)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- 異地還原 [(部落格)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

若要了解如何為嚴重損壞情況做準備，以及何時可復原資料庫，請瀏覽我們的[業務續航力的設計](sql-database-business-continuity-design.md)頁面。

##何時起始復原 

復原作業會影響應用程式。它需要變更 SQL 連接字串，並且可能會導致永久的資料遺失。因此，只有在中斷情況可能持續超過應用程式的 RTO 時，才應該執行這項作業。將應用程式部署至生產環境之後，您應該定期監視應用程式健全狀況，並利用下列資料點判斷是否需要復原：

1. 從應用程式層到資料庫的連接發生永久性失敗。
2. 您的 Azure 入口網站顯示有關區域中影響廣泛之事件的警示。

## 容錯移轉至異地複寫的次要資料庫
> [AZURE.NOTE]您必須設定[標準異地複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx)或[主動式異地複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx)，以取得可用於容錯移轉的次要資料庫。異地複寫僅適用於標準和高階資料庫。

如果發生主要資料庫中斷的情況，您可以容錯移轉至次要資料庫，以還原可用性。若要執行這個動作，您需要強制終止連續複製關聯性。如需終止連續複製關聯性的完整說明，請至[這裡](https://msdn.microsoft.com/library/azure/dn741323.aspx)。



###Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 巡覽至您的資料庫，然後加以選取。 
4. 在資料庫刀鋒視窗底部選取 [異地複寫對應]。
4. 在 [次要] 下，以滑鼠右鍵按一下含有您要復原之資料庫名稱的資料列，然後選取 [停止]。

終止連續複製關聯性之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。
###PowerShell
使用 PowerShell 可以程式設計方式執行資料庫復原。

若要終止與次要資料庫的關聯性，請使用 [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223) Cmdlet。
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
終止連續複製關聯性之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。
###REST API 
使用 REST 可以程式設計方式執行資料庫復原。

1. 使用[取得資料庫複本](https://msdn.microsoft.com/library/azure/dn509570.aspx)作業來取得資料庫連續複製。
2. 使用[停止資料庫複製](https://msdn.microsoft.com/library/azure/dn509573.aspx)作業來停止資料庫連續複製。在停止資料庫複製要求 URI 中，使用次要伺服器名稱和資料庫名稱

 終止連續複製關聯性之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。
## 使用異地還原進行復原

如果發生資料庫中斷的情況，您可以使用異地還原，從資料庫最新的異地備援備份來復原資料庫。

###Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [新增]，然後依序選取 [資料和儲存體] 和 [SQL Database]。
2. 選取 [備份] 做為來源，然後選取您要從中復原的異地備援備份。
3. 指定其餘資料庫內容，然後按一下 [建立]。
4. 資料庫還原程序隨即開始，您可以使用畫面左側的 [通知] 來監視這個程序。

復原資料庫之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定資料庫以供使用。
###PowerShell 
使用 PowerShell 可以程式設計方式執行資料庫復原。

若要啟動異地還原要求，請使用 [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx) Cmdlet。如需詳細的逐步解說，請觀賞我們的[作法影片](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/)。

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

復原資料庫之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定資料庫以供使用。
###REST API 

使用 REST 可以程式設計方式執行資料庫復原。

1.	使用[列出可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800984.aspx)作業來取得可復原的資料庫清單。
	
2.	使用[取得可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800985.aspx)作業來取得您要復原的資料庫。
	
3.	使用[建立資料庫復原要求](http://msdn.microsoft.com/library/azure/dn800986.aspx)作業來建立復原要求。
	
4.	使用[資料庫作業狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)作業來追蹤復原狀態。

復原資料庫之後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定資料庫以供使用。
 

<!---HONumber=July15_HO2-->