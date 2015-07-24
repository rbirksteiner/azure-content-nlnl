<properties 
	pageTitle="使用 Azure 管理入口網站管理 Azure SQL Database" 
	description="了解如何使用 Azure 管理入口網站來管理雲端中的關聯式資料庫。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# 使用 Azure 管理入口網站管理 Azure SQL Database

[Azure 管理入口網站][Management Portal]可讓您建立、監視和管理 Azure SQL Database 與伺服器。本文會強調顯示可使用管理入口網站來完成的資料庫作業。您可以在[這裡][AzureDb management overview]進一步了解其他 Azure SQL Database 管理工具。

>[AZURE.NOTE]如果您不熟悉 Azure 管理入口網站，[此視訊教學課程提供快速概觀][Azure Portal Tour]，可讓您了解入口網站的一般功能和概念。

![資料庫概觀](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1.資料庫管理動作
![資料庫管理動作](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure 管理入口網站提供一組常用的資料庫動作，您可以從資料庫刀鋒視窗頂端存取這些動作。您可以將資料庫還原至先前的時間點、在 Visual Studio 中開啟資料庫、將資料庫複製到新的伺服器，以及將資料庫匯出至 Azure 儲存體帳戶。

## 2.資料庫監視
![資料庫監視](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL Database 根據預設採用的監視圖表有資料庫輸送量單位 (DTU)、資料庫大小和連接的健康情況。您可以自訂這些監視圖表，並延伸成其他圖表，例如 CPU 百分比、資料 IO 百分比、死結數、記錄 IO 百分比，甚至是遭防火牆封鎖之要求的百分比。如需如何自訂監視圖表的詳細資訊，請參閱[這裡][Azure part monitoring]。

此外，您還可以設定警示規則以監視指定的度量，而且當達到預設的臨界值時，會對指定的管理員和共同管理員發出警示。如需如何在 Azure 管理入口網站中設定警示規則的詳細資訊，請參閱[這裡][Azure part monitoring]。

## 3.資料庫安全性與稽核
![資料庫安全性](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL Database 可設為追蹤所有資料庫事件，然後將事件寫入您 Azure 儲存體帳戶中的稽核記錄。此功能有助於維持符合法規、了解資料庫活動，以及深入洞察可能代表業務考量或疑似違反安全性的不一致之處。如需 Azure SQL Database 稽核的詳細資訊，請參閱[這裡][AzureDb Auditing]。

Azure SQL Database 也可以設定為對沒有權限的使用者遮罩處理機密資料。如需 Azure SQL Database 動態資料遮罩功能的詳細資訊，請參閱[這裡][AzureDb datamasking]。

## 4.異地複寫
![異地複寫](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL Database 可以設定為以非同步方式，將已認可的交易複寫至次要資料庫。在管理入口網站上的異地複寫部分，可讓您選取想要放置次要資料庫的 Azure 區域。如需 Azure 中資料庫異地複寫的詳細資訊，請參閱[這裡][Database geo-replication]。

##其他資源
* [SQL Database 簡介][]   
* [使用 SQL Server Management Studio 管理 Azure SQL Database][]   
* [使用動態管理檢視監視 SQL Database][]   
* [Transact-SQL 參考 (SQL Database)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [SQL Database 簡介]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [使用 SQL Server Management Studio 管理 Azure SQL Database]: sql-database-manage-azure-ssms.md
  [使用動態管理檢視監視 SQL Database]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Transact-SQL 參考 (SQL Database)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 
 

<!---HONumber=July15_HO2-->