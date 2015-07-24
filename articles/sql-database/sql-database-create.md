<properties 
	pageTitle="在 SQL Database Update V12 中建立資料庫" 
	description="示範如何在 Azure SQL Database Update V12 中建立資料庫" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# 在 SQL Database V12 中建立資料庫


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[註冊](https://portal.azure.com) SQL Database V12 [(某些區域為 Preview)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)，以在 Microsoft Azure 上利用新一代的 SQL Database。首先，您需要 Microsoft Azure 訂用帳戶。註冊 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial)並檢閱[定價](http://azure.microsoft.com/pricing/details/sql-database)資訊。


| 建立資料庫 | 螢幕擷取畫面 |
| :--- | :--- |
| 1.登入 [http://portal.azure.com/](http://portal.azure.com/)。 | ![新的 Azure 入口網站][1] |
| 2.在頁面下方的左側，按一下 [新增]。 | ![起始新的服務][2]|
| 3.按一下 [SQL Database]。| ![可選取的不同服務][3] |
| 4.[SQL Database] 刀鋒視窗隨即開啟。在 [名稱] 欄位中，指定資料庫名稱。 | ![為資料庫命名][4] |
| 5.在 [SQL Database] 刀鋒視窗中，按一下 [伺服器]。[伺服器] 刀鋒視窗隨即開啟，其中包含兩個選項：您可以建立新的伺服器，或使用現有的伺服器。| ![選取伺服器類型][4] |
|5a.如果您選取 [使用現有的伺服器] 選項，請選取您所選的伺服器，並按一下 [選取]。然後，完成步驟 6 以後的所有動作。| ![從清單中選取伺服器][5]| 
|5b.如果您選取 [建立新的伺服器]，則 [新增伺服器] 刀鋒視窗隨即開啟。指定伺服器名稱、伺服器系統管理員登入和密碼。按一下 [位置] 以選取伺服器位置。 | ![完成 [建立新的伺服器] 選項][9]| 
|5c.[新增伺服器] 刀鋒視窗可讓您選擇使用 V12 更新建立新的伺服器。若要深入了解 V12 伺服器的功能，請檢閱 [SQL Database V12 新功能](sql-database-v12-whats-new.md)。| ![選取 V12 伺服器][6]|
|5d.在 [新增伺服器] 刀鋒視窗上進行選取，然後按一下 [確定]。您將會回到 [SQL Database] 刀鋒視窗，以完成建立資料庫的其餘動作。 | ![完成 [新增伺服器] 刀鋒視窗動作][8]|
|6.按一下 [選取來源]。建立資料庫時，您可以選取的各種來源類型： 空白資料庫、範例資料庫或從資料庫的備份。| ![選取資料庫的來源][10]|
|7.接著，在 [SQL Database] 刀鋒視窗中，按一下 [定價層]。您可以選取其中一個建議的定價層，或選取 [全部檢視] 以檢視所有可用的定價層。進行選擇之後，按一下 [選取]。<p> 如需定價層的詳細資訊，請參閱[將 SQL Database Web/Business 資料庫升級至新的服務層](./sql-database-upgrade-new-service-tiers/)和 [Azure SQL Database 服務層和效能層級](http://msdn.microsoft.com/library/azure/dn741336.aspx)。 |![選取定價層][7]
| 8.接下來，在 [SQL Database] 刀鋒視窗中，按一下 [選擇性組態]，進行選擇並按一下 [確定]。 
| 9.當您選取現有的伺服器時，系統已為您選擇 [資源群組] 和 [訂用帳戶]。在 [SQL Database] 刀鋒視窗中，[資源群組] 和 [訂用帳戶] 旁會顯示鎖定圖示。如果您建立新的伺服器，則可以選取或建立資源群組。如需詳細資訊，請檢閱[使用資源群組管理您的 Azure 資源](resource-group-overview.md)。|![指定資源群組][11]
| 10.按一下 [建立]。即會建立含有 SQL Database V12 功能的新資料庫。 |![建立新的資料庫][12]

## 相關連結

- [SQL Database V12 新功能](sql-database-v12-whats-new.md)
- [規劃和準備升級至 Azure SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=July15_HO2-->