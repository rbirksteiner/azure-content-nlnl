<properties 
	pageTitle="Azure SQL Database 連接問題" 
	description="識別及判斷 SQL Database 連接失敗。" 
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


# SQL Database 連接問題

本文提供當您無法連接到 Azure SQL Database 時，如何使用各種疑難排解工具的概觀。


## 判斷連接問題是針對個別應用程式，還是您只是無法連接到資料庫？

請使用 SQL Server Management Studio 或 SQLCMD，驗證是否可以連接到資料庫。

- 如需使用 SQL Server Management Studio (SSMS) 連接到 SQL Database 的指示，請參閱[如何使用 SSMS 連接到 Azure SQL Database](sql-database-connect-to-database.md)。
- 如需使用 SQLCMD 連接到 SQL Database 的指示，請參閱[作法：使用 sqlcmd 連接到 Azure SQL Database](https://msdn.microsoft.com/library/azure/ee336280.aspx)。



## 應用程式是否嘗試連接到在 Azure 中執行的 SQL Database (例如，無法連接到資料庫的應用程式是雲端服務或 Web 應用程式)？

請確定防火牆規則允許為伺服器/資料庫啟用所有 Azure 服務。

- 如需防火牆規則及啟用來自 Azure 的連接的相關資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。



## 應用程式是否嘗試從私人網路連接到 SQL Database？

請確定防火牆規則允許為伺服器/資料庫啟用來自特定網路的存取。

- 如需防火牆規則的一般資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。
- 如需設定防火牆規則的指示，請參閱[作法：進行防火牆設定 (Azure SQL Database)](https://msdn.microsoft.com/library/azure/jj553530.aspx)。


## 如果未正確設定防火牆規則，則嘗試[疑難排解 Microsoft Azure SQL Database 連接](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1)引導式逐步解說。

上述支援文章提供下列 SQL Database 連接問題的說明：

- 由於防火牆設定，無法開啟伺服器連接 
- 找不到伺服器或無法存取 
- 無法登入伺服器 
- 連接逾時錯誤 
- 暫時性錯誤 
- 由於達到某些系統定義的限制，連接已終止 
- 無法從 SQL Server Management Studio (SSMS) 連接 


## 其他資訊

- 如需連接到 SQL Database 的其他資訊，請參閱[以程式設計方式連接到 Azure SQL Database 的方針](https://msdn.microsoft.com/library/azure/ee336282.aspx)。   

- 如需特定連接錯誤的詳細資訊，請參閱[錯誤訊息 (Azure SQL Database)](https://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors) 中的**連接遺失錯誤**一節。

- 連接事件資料可透過使用 [**sys.event_log (Azure SQL Database)**](https://msdn.microsoft.com/library/dn270018.aspx) 檢視查詢連接事件來存取。

- 資料庫連接事件的度量可透過查詢 [**sys.database_connection_stats (Azure SQL Database)**](https://msdn.microsoft.com/library/dn269986.aspx) 檢視來存取。

 

<!---HONumber=July15_HO2-->