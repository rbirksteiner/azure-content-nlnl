<properties
   pageTitle="從 SQL 資料倉儲中的使用者錯誤復原資料庫 | Microsoft Azure"
   description="從 SQL 資料倉儲中的使用者錯誤復原資料庫的步驟"
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
   ms.date="06/26/2015"
   ms.author="sahajs"/>

# 從 SQL 資料倉儲中的使用者錯誤復原資料庫

SQL 資料倉儲提供兩個核心功能，可從造成非預期之資料損毀或刪除的使用者錯誤復原。

- 還原即時資料庫
- 還原已刪除的資料庫

這兩個功能會在相同的伺服器上還原至新的資料庫。

## 復原即時資料庫
當使用者錯誤造成非預期的資料修改時，您可以在保留期限內將資料庫還原到任何還原點。即時資料庫的資料庫快照集每 8 小時會作用一次，並且保留 7 天。

### PowerShell

使用 PowerShell 可以程式設計方式執行資料庫還原。若要還原資料庫，請使用 [Start-AzureSqlDatabaseRestore][] Cmdlet。

1. 在包含要還原之資料庫的帳戶下選取訂用帳戶。
2. 列出資料庫的還原點 (需要 Azure 資源管理模式)
3. 使用 RestorePointCreationDate 挑選出想要的還原點。
3. 將資料庫還原到想要的還原點。
4. 監視還原的進度。

```
Select-AzureSubscription -SubscriptionId <Subscription_GUID>

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

還原完成後，您可以設定為遵循[完成復原的資料庫][]指南來使用復原的資料庫。

## 復原已刪除的資料庫
若資料庫已刪除，您可以將已刪除的資料庫還原至刪除的時間點。Azure SQL 資料倉儲會在卸除資料庫前採用資料庫快照集，並保留 7 天。

### PowerShell
使用 PowerShell 以程式設計方式還原已刪除的資料庫。若要還原已刪除的資料庫，請使用 [Start-AzureSqlDatabaseRestore][] Cmdlet。

1. 從已刪除的資料庫清單中尋找已刪除的資料庫及其刪除日期。

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

2. 取得特定已刪除的資料庫，並開始進行還原。

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

還原完成後，您可以設定為遵循[完成復原的資料庫][]指南來使用復原的資料庫。


## 後續步驟
若要深入了解其他 Azure SQL Database 版本的業務持續性功能，請閱讀 [Azure SQL Database 業務持續性概觀][]。


<!--Image references-->

<!--Article references-->
[Azure SQL Database 業務持續性概觀]: sql-database/sql-database-business-continuity/,d
[完成復原的資料庫]: sql-database/sql-database-recovered-finalize/

<!--MSDN references-->
[建立資料庫還原要求]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/zh-tw/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->