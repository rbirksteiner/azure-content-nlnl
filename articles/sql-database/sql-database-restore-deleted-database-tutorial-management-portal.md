<properties 
   pageTitle="還原 Azure 入口網站中已刪除的 Azure SQL Database" 
   description="Microsoft Azure SQL Database, restore deleted database, recover deleted database, Azure Management Portal, Azure portal, 還原資料庫, 還原已刪除的資料庫, Azure 管理入口網站, Azure 入口網站," 
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
   ms.author="elfish; v-romcal"/>

# 還原 Azure 入口網站中已刪除的 Azure SQL Database

> [AZURE.SELECTOR]
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## 概觀

本教學課程會向您示範如何還原 [Azure 入口網站](http://manage.windowsazure.com)中已刪除的 Azure SQL Database。您可以將在其保留期間內被刪除的資料庫還原至刪除時的時間點。保留期間是由資料庫的服務層決定。

還原已刪除的 Azure SQL Database 會建立新的資料庫。服務會依據還原點使用的備份自動選擇服務層。請確定您在邏輯伺服器上擁有可用額度，以建立另一個資料庫。如果您想要求增加額度，請洽詢 [Azure 技術支援](http://azure.microsoft.com/support/options/)。

## 限制與安全性

* 所有 Basic、Standard 及 Premium 服務層皆支援還原已刪除的 Azure SQL Database。 

* 備份保留期限如下：Basic 為 7 天、Standard 為 14 天，及 Premium 為 35 天。

* 只有系統管理員或訂用的共同管理員可以提交還原要求。

* 伺服器層級主體登入將成為還原之資料庫的擁有者。
 
* Web 與 Business Edition 服務層不支援還原已刪除的 SQL Database。
 
	* 如果您有 Web 或 Business Edition 資料庫，您可以使用資料庫複本來取得交易一致的資料庫複本，然後將複製的資料庫匯出至 Microsoft Azure 儲存體帳戶。如需詳細資訊，請參閱[作法：使用資料庫複本 (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff951631.aspx) 以及[作法：在 Azure SQL Database 中使用匯入和匯出服務](http://msdn.microsoft.com/library/azure/hh335292.aspx)。
	* Web 與 Business Edition 將會在 2015 年 9 月淘汰。如需詳細資訊，請參閱 [Web 和 Business Edition 終止常見問題集](http://msdn.microsoft.com/library/azure/dn741330.aspx)。

## 作法：還原 Azure 入口網站中已刪除的 Azure SQL Database

> [AZURE.VIDEO restore-a-deleted-sql-database]

1. 使用您的 Microsoft 帳戶，登入 [Azure 入口網站](http://manage.windowsazure.com)。

2. 在左側導覽中，按一下 [SQL DATABASES]。

3. 在頁面頂端按一下 [已刪除的資料庫]。就會顯示 [可還原的已刪除資料庫] 的清單。

4. 按一下您想要還原的資料庫。

6. 在頁面底部的命令列中按一下 [還原]。這會啟動 [指定還原設定] 對話方塊。

7. 選擇 [DATABASE NAME]。依照預設，系統已經為您選擇資料庫名稱，但是您可以視需要變更。

	* 請注意，已刪除的資料庫只能還原至其被刪除時所在的來源伺服器，以及還原至其被刪除時的時間點。   

8. 按一下核取記號來提交還原要求。

還原可能需要一些時間來完成。若要監視還原狀態，請按一下頁面底部右側命令列中的狀態圖示，然後按一下 [詳細資料]。

## 後續步驟

如需詳細資訊，請參閱下列主題：

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

<!---HONumber=62-->