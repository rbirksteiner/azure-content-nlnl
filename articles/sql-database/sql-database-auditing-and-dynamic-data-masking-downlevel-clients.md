<properties 
	pageTitle="稽核和動態資料遮罩的 SQL Database 舊版用戶端支援 | Azure" 
	description="稽核和動態資料遮罩的 SQL Database 舊版用戶端支援" 
	services="sql-database" 
	documentationCenter="" 
	authors="nadavhelfman" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="nadavhelfman"/>
 
# SQL Database - 稽核和動態資料遮罩的舊版用戶端支援 


[稽核](sql-database-auditing-get-started.md)和[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)與支援 TDS 重新導向的 SQL 用戶端搭配使用。

實作 TDS 7.4 的任何用戶端應該也支援重新導向。例外包括其中未完全支援重新導向功能的 JDBC 4.0，和其中未實作重新導向的 Tedious for Node.JS。

對於「舊版用戶端」，也就是支援 TDS 7.3 版和以下版本 - 應該修改連接字串中的伺服器 FQDN：

連接字串中的原始伺服器 FQDN：<*伺服器名稱*>.database.windows.net

修改的連接字串中的伺服器 FQDN：<*伺服器名稱*>.database.**secure**.windows.net

「舊版用戶端」的部分清單包括：

- .NET 4.0 和以下版本，
- ODBC 10.0 和以下版本。
- JDBC 4.0 和以下版本 (雖然 JDBC 4.0 支援 TDS 7.4，但是未完整支援 TDS 重新導向功能)
- Tedious (適用於 Node.JS)

**備註：**上述伺服器 FDQN 修改可能會對於套用 SQL Server 層級稽核原則有所助益，不需要每個資料庫中的組態步驟 (暫存緩和)。

 

<!---HONumber=July15_HO2-->