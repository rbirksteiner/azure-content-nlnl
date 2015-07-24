<properties 
   pageTitle="SQL Database 使用者錯誤復原" 
   description="了解如何使用 Azure SQL Database 的時間點還原 (PITR) 功能，從使用者錯誤、意外的資料損毀或已刪除的資料庫復原。" 
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

# 從使用者錯誤復原 Azure SQL Database

Azure SQL Database 提供兩個核心功能，從使用者錯誤或非預期的資料修改復原。

- 時間點還原 
- 還原已刪除的資料庫

您可以在此[部落格文章](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)進一步了解這些功能。

Azure SQL Database 一律會還原至新的資料庫。所有「基本」、「標準」和「高階」資料庫都提供這些還原功能。
##使用時間點還原復原
發生使用者錯誤或非預期的資料修改時，時間點還原可用來將資料庫還原至資料庫保留期限內的任何時間點。

「基本」資料庫有 7 天的保留期，「標準」資料庫有 14 天的「保留期」，「高階」資料庫則有 35 天的保留期。若要深入了解資料庫保留，請閱讀我們的[商務持續性概觀](sql-database-business-continuity.md)。

###Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 瀏覽至您的資料庫，然後選取它。
4. 在資料庫刀鋒視窗的頂端，選取 [還原]。
5. 指定資料庫名稱、時間點，然後按一下 [建立]。
6. 資料庫還原程序就會開始，您可以使用畫面左側的 [通知] 監視程序。

還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。
###PowerShell
使用 PowerShell 可以程式設計方式執行資料庫還原。

若要使用時間點還原來還原資料庫，請使用 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) Cmdlet。如需詳細的逐步執行，請參閱我們的[作法視訊](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/)。

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。

1. 使用[取得資料庫](http://msdn.microsoft.com/library/azure/dn505708.aspx)操作取得您想要還原的資料庫。

2.	使用[建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx)操作來建立還原要求。
	
3.	使用[資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)操作來追蹤還原要求。

還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。

##復原已刪除的資料庫
若資料庫被刪除，Azure SQL Database 可讓您將已刪除的資料庫還原至刪除的時間點。Azure SQL Database 會將已刪除的資料庫備份，儲存一段資料庫保留期間的時間。

已刪除資料庫的保留期間取決於資料庫所在的服務層，或是資料庫存在的天數，兩者中較少的天數。若要深入了解資料庫保留，請閱讀我們的[商務持續性概觀](sql-database-business-continuity.md)。

###Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]。
3. 瀏覽至您的伺服器，然後選取它。
4. 在伺服器刀鋒視窗的 [作業] 下，選取 [已刪除的資料庫]。
5. 按一下您想要還原的已刪除資料庫。
6. 指定資料庫名稱，然後按一下 [建立]。
7. 資料庫還原程序就會開始，您可以使用畫面左側的 [通知] 監視程序。

還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。

###PowerShell
使用 PowerShell 可以程式設計方式執行資料庫還原。

若要還原已刪除的資料庫，請使用 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) Cmdlet。如需詳細的逐步執行，請參閱我們的[作法視訊](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/)。

1. 從已刪除資料庫的清單中找出已刪除的資料庫及其刪除日期。
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. 取得特定已刪除的資料庫，並開始進行還原。

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。

1.	使用[列出可還原的已刪除資料庫](http://msdn.microsoft.com/library/azure/dn509562.aspx)操作來列出所有可還原的已刪除資料庫。
	
2.	使用[取得可還原的已刪除資料庫](http://msdn.microsoft.com/library/azure/dn509574.aspx)操作來取得您想要還原之已刪除資料庫的詳細資訊。

3.	使用[建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx)操作來開始還原。
	
4.	使用[資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)操作來追蹤還原狀態。

還原完成後，您可以遵循[完成復原的資料庫](sql-database-recovered-finalize.md)指南，設定復原的資料庫。
 

<!---HONumber=62-->