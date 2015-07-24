<properties 
	pageTitle="升級至 SQL Database V12" 
	description="說明如何從較早版本的 Azure SQL Database 升級為 Azure SQL Database V12。" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# 就地升級至 SQL Database V12


[註冊](https://portal.azure.com) SQL Database V12，在 Microsoft Azure 上充分運用新一代的 SQL Database。首先，您需要 Microsoft Azure 訂用帳戶。註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial)並檢閱[定價](http://azure.microsoft.com/pricing/details/sql-database)資訊。


## 升級至 SQL Database V12 的步驟


| 升級步驟 | 螢幕擷取畫面 |
| :--- | :--- |
| 1.登入 [http://portal.azure.com/](http://portal.azure.com/)。 | ![新的 Azure 入口網站][1] |
| 2.按一下 [瀏覽]。 | ![瀏覽服務][2] |
| 3.按一下 [SQL Server]。隨即顯示 SQL Server 名稱清單。 | ![選取 SQL Server 服務][3] |
| 4.選取您想複製到啟用 SQL Database Update 之新伺服器的伺服器。 | ![顯示 SQL Server 的清單][4] |
| 5.按一下 [設定] 或 [伺服器版本]，將您的伺服器升級到 V12。 | ![最新的預覽功能][5] |
| 6.按一下 [升級此伺服器]。 | ![將 SQL Server 升級至預覽][6] |
| 7.一旦按一下 [**升級此伺服器**]，升級程序就會開始。在 [**伺服器版本**] 下，通知會從 [**V2**] 變更為 [**升級排程的...**]。如果您按一下 [**升級排程的...**] 通知，則會開啟刀鋒視窗，頂端有 [**取消升級**] 按鈕。如果您因為任何原因而決定不要升級伺服器，請按一下 [**取消升級**]。請**注意**，取消作業不會結束升級程序，升級仍會完成。|![取消升級][9] 


> [AZURE.NOTE]一旦您選取升級選項，您的伺服器和該伺服器內的資料庫將會啟用 SQL Database V12 功能，並且無法回復。若要將伺服器升級至 SQL Database V12，您需要「基本」、「標準」或「高階」服務層。如需服務層的詳細資訊，請參閱[將 SQL Database Web/商務資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md)。


> [AZURE.IMPORTANT]SQL Database V12 (預覽) 不支援異地複寫。如需詳細資訊，請參閱[規劃和準備升級至 Azure SQL Database V12 預覽](sql-database-v12-plan-prepare-upgrade.md)。


按一下 [升級此伺服器] 選項後，開啟的刀鋒視窗會顯示有關驗證程序的訊息。


- 驗證程序會檢查您資料庫的服務層，並檢查是否已啟用異地複寫。驗證完成後，刀鋒視窗會顯示結果。 
- 驗證程序完成後，您會看到一份需要您採取動作以符合升級至 SQL Database V12 之需求的資料庫名稱清單。
 - **您必須個別為那些資料庫完成動作，才能升級至 SQL Database V12**。
- 當您按一下每個資料庫名稱時，新的刀鋒視窗會根據您目前的使用量提供服務定價層建議。您也可以瀏覽各種定價層，選取最符合您環境的選項。所有已設定異地複寫的資料庫都必須重新設定才能停止複寫。 
- 請注意，若找不到足夠資料，將不會顯示定價層建議。 


| 動作 | 螢幕擷取畫面 |
| :--- | :--- |
| 7.在您完成伺服器升級的準備動作後，請輸入要升級的伺服器名稱，然後按一下 [確定]。 | ![確認要升級的伺服器名稱][7] |
| 8.升級程序即會啟動。升級最久可能需要 24 小時。此伺服器上的所有資料庫在這段期間將維持線上狀態，但伺服器和資料庫管理動作將受到限制。一旦程序完成，伺服器刀鋒視窗上會顯示 [已啟用] 狀態。 | ![確認已啟用預覽功能][8] |


## PowerShell Cmdlet


PowerShell Cmdlet 可用來啟動、停止或監視從 V11 或任何其他 V12 以前版本升級至 Azure SQL Database V12。


如需這些 PowerShell Cmdlet 的參考文件，請參閱：


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Stop- Cmdlet 表示取消，不是暫停。升級一旦停止就沒有任何方法可以繼續，只能再次從頭開始。Stop- Cmdlet 會清除並釋放所有適當的資源。


## 相關連結

-  [SQL Database V12 新功能](sql-database-v12-whats-new.md) 
- [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[2]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[3]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[4]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[5]: ./media/sql-database-v12-upgrade/latestprview.png
[6]: ./media/sql-database-v12-upgrade/upgrade.png
[7]: ./media/sql-database-v12-upgrade/typeservername.png
[8]: ./media/sql-database-v12-upgrade/enabled.png
[9]: ./media/sql-database-v12-upgrade/cancel.PNG
 

<!---HONumber=July15_HO2-->