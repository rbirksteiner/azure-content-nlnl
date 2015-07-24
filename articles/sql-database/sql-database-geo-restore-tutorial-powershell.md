<properties 
   pageTitle="使用 Azure PowerShell 中的地理還原來復原 Azure SQL Database" 
   description="Geo-Restore, Microsoft Azure SQL Database, restore database, recover database, Azure PowerShell, 地理還原, 還原資料庫, 復原資料庫" 
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

# 使用 Azure PowerShell 中的地理還原來復原 Azure SQL Database

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## 概觀

本教學課程會向您示範如何使用 [Azure PowerShell](../powershell-install-configure.md) 中的地理還原來復原 Azure SQL Database。地理還原是所有 Basic、Standard 及 Premium Azure SQL Databases 服務層皆包含的核心災害復原保護技術。

## 限制與安全性

請參閱[使用 Azure 入口網站中的地理還原來復原 Azure SQL Database](sql-database-geo-restore-tutorial-management-portal.md)。

## 作法：使用 Azure PowerShell 中的地理還原來復原 Azure SQL Database

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell]

您必須使用憑證式認證來執行下列 Cmdlet。如需詳細資訊，請參閱*如何安裝及設定 Azure PowerShell* 中的[使用憑證方法](../powershell-install-configure.md#use-the-certificate-method)一節。

1. 使用 [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) Cmdlet 取得可還原之資料庫的清單。指定以下參數：
	* **ServerName**：資料庫所在之伺服器的名稱。	

	`PS C:\>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. 使用 [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) Cmdlet 選擇您想要復原的來源資料庫。指定以下參數：
	* **ServerName**：資料庫所在之伺服器的名稱。
	* **DatabaseName**：您要復原之來源資料庫的名稱。

	`PS C:\>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" –DatabaseName “mydb”`
	 
3. 使用 [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx) Cmdlet 來開始復原。指定以下參數：
	* **SourceDatabase**：您想復原的來源資料庫。
	* **TargetDatabaseName**：您復原資料庫後之目標資料庫的名稱。
	* **TargetServerName**：您復原資料庫後之目標伺服器的名稱。

	將傳回的內容儲存至稱為 **$RestoreRequest** 的變數中。此變數包含用來監視還原狀態的還原要求識別碼。

	`PS C:\>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “myrecoveredDB” –TargetServerName “mytargetserver”`
	
資料庫還原可能需要一些時間來完成。若要監視復原狀態，請使用 [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) Cmdlet 並指定以下參數：

* **ServerName**：您還原資料庫後之目標伺服器的名稱。
* **OperationGuid**：於步驟 3 中儲存在 **$RecoveryRequest** 變數中的還原要求識別碼。

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName “mytargetserver” –OperationGuid $RecoveryRequest.ID`

**State** 與 **PercentComplete** 欄位會顯示還原狀態。

## 後續步驟

如需詳細資訊，請參閱下列主題：

[使用 Azure 入口網站中的還原時間點來還原 Azure SQL Database](sql-database-point-in-time-restore-tutorial-management-portal.md)

[還原 Azure 入口網站中已刪除的 Azure SQL Database](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database 地理還原 (部落格)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=62-->