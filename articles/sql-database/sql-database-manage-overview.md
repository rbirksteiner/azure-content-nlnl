<properties 
	pageTitle="概觀：SQL Database 的管理工具" 
	description="比較管理 Azure SQL Database 的工具和選項" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="vinsonyu"/>

# 概觀：SQL Database 的管理工具

本主題將探索並比較管理 SQL Database 的工具和選項，讓您可以挑選適合您工作、業務和您自身的正確工具。要選擇正確的工具就必須考量到您管理多少資料庫、工作本身以及執行工作的頻率。



## Azure 入口網站


[Azure 入口網站](http://portal.azure.com)是網頁式管理入口網站，您可以在其中建立、更新和刪除 Azure SQL Database 和邏輯伺服器，以及監視資料庫資源。如果您剛開始使用 Azure，或管理少量的資料庫，或是需要快速做某件事，那這正是十分適合您的工具。

如需關於使用入口網站的詳細深入資訊，請參閱[使用 Azure 入口網站管理 SQL Database](sql-database-manage-portal.md)。

## Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools


Visual Studio 中的 SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 都是可在您電腦上執行的用戶端工具，並可讓您連接、管理和開發雲端中的資料庫。如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員，請嘗試使用 Visual Studio 中的 SSDT。如果您需要 SSDT 中尚未提供的進階 SQL 功能，例如在混合式環境中管理 SQL Server 資料庫，請使用 SSMS。

如需使用 SSMS 和 SSDT 管理 Azure SQL Database 的詳細資訊，請參閱[使用 SSMS 管理 SQL Database](sql-database-manage-azure-ssms.md)


## 命令列工具。

您可以使用命令列工具 (例如 PowerShell) 來管理 Azure SQL Database 以及自動化 Azure 資源部署工作。當您在生產環境中管理大量 Azure SQL Database 以及部署資源變更時，Microsoft 建議使用這項工具。

如需使用命令列工具管理 Azure SQL Database 的詳細資訊，請[按一下這裡](sql-database-command-line-tools.md)
 

<!---HONumber=July15_HO2-->