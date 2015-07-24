<properties 
	pageTitle="安裝彈性資料庫工作" 
	description="逐步解說如何安裝彈性工作功能。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# 安裝彈性資料庫工作元件

[彈性資料庫集區 (預覽)](sql-database-elastic-pool-portal.md) 提供一個用於部署大量資料庫的可預測模型。當您建立彈性資料庫集區之後，可以使用**彈性資料庫工作**跨彈性資料庫集區中的所有資料庫執行管理工作。例如，您可以部署新的結構描述，像是在每個資料庫上設定 RLS 原則，將資料限制為僅具有正確認證的人員才能檢視機密資料。以下說明如何安裝**彈性資料庫工作**。

**預估完成時間：**10 分鐘。

## 必要條件
* Azure 訂閱。如需免費試用，請參閱[免費試用一個月](http://azure.microsoft.com/pricing/free-trial/)。
* 彈性資料庫集區。請參閱[建立 Azure SQL Database 彈性集區 (預覽)](sql-database-elastic-pool-portal.md)。

## 安裝服務元件
請先登入 [Azure 預覽入口網站](https://ms.portal.azure.com/#)。


1. 從彈性資料庫工作集區的儀表板檢視，按一下 [建立工作]。
2. 如果您是第一次建立工作，則必須安裝**彈性資料庫工作**。請按一下 [預覽條款]， 
3. 然後按一下核取方塊接受條款。
4. 在 [安裝服務] 檢視中，按一下 [工作認證]。

	![安裝服務][1]

5. 輸入資料庫管理員的使用者名稱和密碼。如果已經有可跨彈性資料庫集區中所有資料庫執行指令碼的一般使用者，請考慮使用同一個使用者，如此則不需要將新的使用者加入每個資料庫以執行指令碼。在安裝過程中，會建立新的 Azure SQL Database 伺服器。在這個新的伺服器中，會建立稱為控制資料庫的新資料庫，以用來包含彈性資料庫工作的中繼資料。此處所建立的使用者名稱和密碼有兩個用途：(1) 用來登入控制資料庫，以及 (2) 做為每次執行 (Run) 指令碼執行 (Exeution) 工作時，用來登入彈性集區中每個資料庫的認證。
 
	![建立使用者名稱和密碼][2]
6. 按一下 [確定] 按鈕。幾分鐘後會在新的[資源群組](../resource-group-portal.md)中為您建立元件。新的資源群組已釘選到「開始面板」，如下所示。建立後，會在群組中建立所有彈性資料庫工作 (雲端服務、SQL Database、服務匯流排和儲存體)。

	![「開始面板」中的資源群組][3]


7. 如果您嘗試在安裝彈性資料庫工作時建立或管理工作，您會在提供**認證**時看到下列訊息。

	![部署仍在進行中][4]

8. 如果安裝因某些原因而失敗，請刪除資源群組。請參閱[如何解除安裝彈性資料庫工作元件](sql-database-elastic-jobs-uninstall.md)。


## 後續步驟

如果彈性資料庫集區中具有指令碼執行之適當權限的每個資料庫，尚不存在安裝彈性資料庫工作時所提供的新認證，則必須在每個資料庫上建立認證。請參閱[如何將使用者加入彈性資料庫集區](sql-database-elastic-jobs-add-logins-to-dbs.md)。若要了解如何建立工作，請參閱[建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=62-->