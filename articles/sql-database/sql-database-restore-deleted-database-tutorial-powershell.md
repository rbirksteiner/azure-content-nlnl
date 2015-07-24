<properties 
   pageTitle="還原 Azure PowerShell 中已刪除的 Azure SQL Database" 
   description="Microsoft Azure SQL Database, restore deleted database, recover deleted database, Azure PowerShell, 還原已刪除的資料庫, 復原已刪除的資料庫," 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# 還原 Azure PowerShell 中已刪除的 Azure SQL Database

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## 概觀

本教學課程會向您示範如何還原 [Azure PowerShell](../powershell-install-configure.md) 中已刪除的 Azure SQL Database。您可以將在其保留期間內被刪除的資料庫還原至其被刪除時的時間點。保留期間是由資料庫的服務層決定。

還原已刪除的 Azure SQL Database 會建立新的資料庫。服務會依據還原點使用的備份自動選擇服務層。請確定您在邏輯伺服器上擁有可用額度，以建立另一個資料庫。如果您想要求增加額度，請洽詢 [Azure 技術支援](http://azure.microsoft.com/support/options/)。

## 限制與安全性

請參閱[還原 Azure 入口網站中已刪除的 Azure SQL Database](sql-database-restore-deleted-database-tutorial-management-portal.md)。

## 作法：還原 Azure PowerShell 中已刪除的 Azure SQL Database

> [AZURE.VIDEO restore-a-deleted-sql-database-with-microsoft-azure-powershell]

您必須使用憑證式認證來執行下列 Cmdlet。如需詳細資訊，請參閱*如何安裝及設定 Azure PowerShell* 中的[使用憑證方法](../powershell-install-configure.md#use-the-certificate-method)一節。

1. 使用 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) Cmdlet 取得可還原之資料庫的清單。
	* 使用 **RestorableDropped** 切換及指定 **ServerName** (要從中刪除資料庫之伺服器的名稱)。
	* 執行以下命令會將結果儲存在稱為 **$RecoverableDBs** 的變數中。
	
	`PS C:\>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" –RestorableDropped`

2. 從已刪除之資料庫的清單中選擇您想要還原的已刪除資料庫。

	* 輸入 **$RecoverableDBs** 清單中已刪除之資料庫的編號。  

	`PS C:\>$Database = $RecoverableDBs[<deleted database number>]`

	* 如需如何取得可還原之已刪除資料庫物件的相關詳細資訊，請參閱 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx)。

3. 使用 [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) Cmdlet 來開始還原。指定以下參數：
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName**：您還原資料庫後之目標資料庫的名稱。

	將傳回的內容儲存至稱為 **$RestoreRequest** 的變數中。此變數包含用來監視還原狀態的還原要求識別碼。
	
	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “myrestoredDB”`

還原可能需要一些時間來完成。若要監視還原狀態，請使用 [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) Cmdlet 並指定以下參數：

* **ServerName**：您還原資料庫後之目標伺服器的名稱。
* **OperationGuid**：於步驟 3 中儲存在 **$RestoreRequest** 變數中的還原要求識別碼。

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

**State** 與 **PercentComplete** 欄位會顯示還原狀態。

## 後續步驟

如需詳細資訊，請參閱下列主題：

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)

<!---HONumber=62-->