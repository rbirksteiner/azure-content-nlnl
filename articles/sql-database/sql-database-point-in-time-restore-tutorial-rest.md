<properties 
   pageTitle="搭配 REST API 使用還原時間點來還原 Azure SQL Database" 
   description="Point in Time Restore, Microsoft Azure SQL Database, restore database, recover database, REST API, 還原時間點, 還原資料庫," 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# 搭配 REST API 使用還原時間點來還原 Azure SQL Database

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## 概觀

本指南會向您示範如何搭配 REST API 使用還原時間點來還原 Azure SQL Database。已提供更多詳細操作方式的連結。

還原時間點會建立新的資料庫。服務會依據還原點使用的備份自動選擇服務層。請確定您在邏輯伺服器上擁有可用額度，以建立另一個資料庫。如果您想要求增加額度，請洽詢 [Azure 技術支援](http://azure.microsoft.com/support/options/)。

## 限制與安全性

請參閱[使用 Azure 入口網站中的還原時間點來還原 Azure SQL Database](sql-database-point-in-time-restore-tutorial-management-portal.md)。

## 作法：使用 REST API 來還原 Azure SQL Database

1.	使用[取得資料庫](http://msdn.microsoft.com/library/azure/dn505708.aspx)操作取得您想要還原的資料庫。

2.	使用[建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx)操作來建立還原要求。
	
3.	使用[資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)操作來追蹤還原要求。

## 後續步驟

如需詳細資訊，請參閱下列主題：

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database 還原時間點 (部落格)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->