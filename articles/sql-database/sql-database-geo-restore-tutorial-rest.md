<properties 
   pageTitle="搭配 REST API 使用地理還原來復原 Azure SQL Database" 
   description="Geo-Restore, Microsoft Azure SQL Database, restore database, recover database, REST API, 地理還原, 還原資料庫, 復原資料庫" 
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

# 搭配 REST API 使用地理還原來復原 Azure SQL Database

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)

## 概觀

本指南說明如何使用 REST API 復原 Azure SQL Database。已提供更多詳細操作方式的連結。地理還原是所有 Basic、Standard 及 Premium Azure SQL Databases 服務層皆包含的核心災害復原保護技術。

## 限制與安全性

請參閱[使用 Azure 入口網站中的地理還原來復原 Azure SQL Database](sql-database-geo-restore-tutorial-management-portal.md)。

## 作法：使用 REST API 來復原 Azure SQL Database

1.	使用[列出可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800984.aspx)作業來取得可復原的資料庫清單。
	
2.	使用[取得可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800985.aspx)作業來取得您要復原的資料庫。
	
3.	使用[建立資料庫復原要求](http://msdn.microsoft.com/library/azure/dn800986.aspx)作業來建立復原要求。
	
4.	使用[資料庫作業狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)作業來追蹤復原狀態。

## 後續步驟

如需詳細資訊，請參閱下列主題：

[使用 Azure 入口網站中的還原時間點來還原 Azure SQL Database](sql-database-point-in-time-restore-tutorial-management-portal.md)

[還原 Azure 入口網站中已刪除的 Azure SQL Database](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database 地理還原 (部落格)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->