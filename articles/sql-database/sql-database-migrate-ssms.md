<properties
   pageTitle="使用 SSMS 移轉至 SQL Database "
   description="Microsoft Azure SQL Database, 移轉 SQL Database, 使用 SSMS 移轉"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#使用 SSMS 移轉相容資料庫 

![SSMS 移轉圖表](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

如果資料庫結構描述已與 Azure SQL Database 相容，那麼您將能輕鬆進行移轉作業。由於不需進行轉換，因此您只需將資料庫匯入 Azure 即可進行移轉作業。這只需要一個步驟即可完成，亦即使用 SSMS 將資料庫「部署」至 Azure SQL Database；或者也可分為兩個步驟，先將 BACPAC 匯出，然後再匯入 Azure SQL Database 以建立新的資料庫。

您可以將匯出的 BACPAC 上傳至 Azure 儲存體，然後使用入口網站將其匯入。在雲端中執行匯入作業可減少匯入過程中的延遲，藉此改善大型資料庫移轉作業的效能及可靠性。

直接從 SSMS 部署將會一律部署結構描述和資料；而匯出及匯入則會一律部署結構描述並提供一個選項，讓您部署所有資料表或資料表子集的資料。不論您是從 SSMS 部署，還是從 SSMS (或稍後從入口網站) 匯出再匯入，基本上都會使用相同的 DAC 技術，且結果都是一樣的。

在資料庫更新並與 Azure SQL Database 相容後，此選項也會被當作選項 3 中用於移轉資料庫的最後一個步驟。

##使用 SSMS 部署至 Azure SQL Database
1.	使用上述的 Azure 入口網站佈建一部伺服器，接著為移轉的資料庫建立目標伺服器。
2. 在 SSMS 物件總管中找出來源資料庫，然後執行 [**將資料庫部署到 Azure SQL Database...**] 工作。

	![從 [工作] 功能表部署至 Azure](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	在部署精靈中，設定與上述步驟中佈建之目標 Azure SQL Database 的連接。
4.	輸入資料庫 [名稱]，並設定 [版本] (服務層) 和 [服務目標] (效能層級)。如需關於進行這些設定的詳細資訊，請參閱＜選擇資料庫效能層級/定價層以進行移轉＞。 

	![匯出設定](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	完成精靈以執行資料庫的移轉。移轉時間取決資料庫部署的大小及複雜性，可能需要數分鐘到數小時。如果移轉過程中發生錯誤，指出資料庫結構描述與 SQL Database 不相容，則必須使用不同選項。

##使用 SSMS 將 BACPAC 匯出，再將其匯入 SQL Database。
部署程序可以分成兩個步驟：匯出和匯入。第一個步驟會先建立 BACPAC 檔案，接著在第二個步驟將其做為輸入使用。

1.	使用上述最新 SQL Database 更新佈建一部伺服器，接著為移轉的資料庫建立目標伺服器。
2.	在 SSMS 物件總管中找出來源資料庫，然後選取 [**將資料庫部署到 Azure SQL Database...**] 工作。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. 在匯出精靈中設定匯出，將 BACPAC 檔案儲存在本機上。匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。比方說，您可能會選擇只匯出參考資料表的資料。
	>[AZURE.NOTE]注意：一旦 Azure 管理入口網站支援在 Azure 中執行匯入後，您便可以選擇將匯出的 BACPAC 檔案儲存至 Azure 儲存體，並在雲端中執行匯入。

	![匯出設定](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	一旦建立 BACPAC 檔案後，請連接至步驟 1 中建立的伺服器，以滑鼠右鍵按一下 [資料庫] 資料夾，然後選取 [匯入資料層應用程式...]。

	![匯入資料層應用程式功能表項目](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	在匯入精靈中，選取剛才匯出的 BACPAC 檔案，即可在 Azure SQL Database 中建立新的資料庫 。

	![匯入設定](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	輸入資料庫名稱，然後設定 [版本] (服務層) 和 [服務目標] (效能層級)。
	 
7.	完成精靈以匯入 BACPAC 檔案，然後在 Azure SQL Database 中建立資料庫。

	![資料庫設定](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##替代項目
您也可以使用命令列公用程式 sqlpackage.exe 部署資料庫或匯出和匯入 BACPAC。Sqlpackage.exe 和 SSMS 同樣使用 DAC 技術，因此結果會是一樣的。如需詳細資訊，請參閱 [MSDN 上的 SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)。

<!---HONumber=July15_HO2-->