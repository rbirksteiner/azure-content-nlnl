<properties 
	title="How to add a users to an elastic database pool" 
	pageTitle="如何將使用者加入彈性資料庫集區" 
	description="您必須將具有權限的使用者加入集區中的每個資料庫" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh" />

# 如何將使用者加入彈性資料庫集區

彈性資料庫工作可讓您對[彈性資料庫集區](sql-database-elastic-pool.md)中的每個資料庫執行相同的指令碼。若要執行指令碼，您必須將具有適當權限的使用者加入集區中的每個資料庫。如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396)或[將使用者加入 SQL Azure Database](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## 必要條件
* [建立彈性資料庫集區 (預覽)](sql-database-elastic-pool-portal.md)
* 安裝[彈性工作元件](sql-database-elastic-jobs-service-installation.md)。 

## 如何將使用者加入資料庫

1.	首先連接到 Azure SQL Database 伺服器的**主要**，這是彈性資料庫集區中的資料庫所在的位置，然後使用安裝**彈性資料庫工作**時所提供的相同認證來建立新的登入。

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. 登入集區中的每個資料庫，並使用相同的名稱和密碼建立使用者。使用者必須具有足夠的權限，才能執行這項工作。這個程式碼必須在每個資料庫上執行。

		CREATE USER admin1 FROM LOGIN login1;
		
3. 使用者也必須具有足夠的權限，才能執行為工作所指定的指令碼。使用 **sp_addrolemember** 程序提供使用者最小必要權限，以順利執行指令碼。

## 後續步驟

對資料庫集區執行工作。請參閱[建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=62-->