<properties
	pageTitle="逐步解說：啟用最新的 SQL Database Update V12"
	description="說明使用全新 Microsoft Azure 入口網站 UI 嘗試 Azure SQL Database V12 版本的步驟。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# 逐步解說：啟用最新的 SQL Database Update V12

本主題說明啟用 Microsoft 在 2014 年 12 月首度發行之 Azure SQL Database V12 選項的步驟。

若要嘗試最新的 V12，您首先需要 Microsoft Azure 訂用帳戶，或至少要有[免費試用](http://azure.microsoft.com/pricing/free-trial/)訂用帳戶。

您可以使用新的 Azure Preview 入口網站 [http://portal.azure.com/](http://portal.azure.com/)，而不是[較舊的 Azure 入口網站](http://manage.windowsazure.com/) 來啟用 V12。在您為您的訂用帳戶啟用 V12 之後，Azure Preview 入口網站中建立和升級 V12 的選項就會解除鎖定。然後，使用者就可以從伺服器刀鋒視窗或資料庫刀鋒視窗起始[建立](sql-database-create.md)工作流程。

> [AZURE.NOTE]測試資料庫、資料庫複本或新資料庫都適合升級至預覽。您的企業所依賴的實際執行資料庫應等到預覽期間後再升級。

如需升級至 V12 的詳細資訊，請參閱[規劃和準備升級至 Azure SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)。


## 答：安全性授權

第一個步驟是確定您有足夠的授權可為您的訂用帳戶啟用 V12。當您嘗試啟用 V12 選項時，系統會執行授權檢查，以確認您在訂用帳戶中有足夠的權限。

 若要嘗試 V12，您必須具有下列其中一項授權：

- 訂閱擁有者
- 訂閱的共同管理員

如需 Azure 帳戶的詳細資訊，請參閱[管理帳戶、訂用帳戶和系統管理角色](http://msdn.microsoft.com/library/hh531793.aspx)。

## B.Azure Preview 入口網站 UI 中的步驟

本節說明在 Azure Preview 入口網站 UI 中啟用 V12 選項的按滑鼠順序。啟用此選項後，該選項之後仍可使用。

所有啟用案例都使用相同的基本概念。當您第一次嘗試[建立新的 SQL Database 伺服器](sql-database-create.md)時，[最新更新 (預覽)] 刀鋒視窗會提供一個核取方塊供您選取，以啟用您的權限來使用 V12 版本。啟用權限之後，您就不會再看到此核取方塊。但是，您會看到 [是|否] 控制項，供您指定是否希望新的伺服器使用 V12。如果您選擇 [否]，您會建立 V11 伺服器 (如 SELECT @@VERSION; 所回報)。

### B.1 V12 版本的 [是|否] 控制項

啟用 V12 選項權限之後，您會看到下列 Azure Preview 入口網站螢幕擷取畫面中圈出來的 [是|否] 控制項。

![YesNoOptionForTheV12Preview][Image1]


## C.後續步驟

接著，下列主題將說明您可以使用 SQL Database V12 的方式。

- [在 SQL Database Update V12 中建立資料庫](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

 

<!---HONumber=July15_HO2-->