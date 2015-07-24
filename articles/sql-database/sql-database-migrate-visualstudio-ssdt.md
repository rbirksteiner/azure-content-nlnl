<properties 
   pageTitle="使用 Visual Studio 和 SSDT 移轉" 
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯出資料庫, 移轉精靈" 
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

#就地更新資料庫，接著部署至 Azure SQL Database

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

將資料庫移轉至 Azure SQL Database V12，需要使用 SQL Azure 移轉精靈 (SAMW) 無法處理的結構描述變更時，請使用此選項。如果資料庫使用 Azure SQL Database 不支援或尚未支援的 SQL Server 功能時，便會發生這樣的狀況。在此選項中，Visual Studio 會先用來從來源資料庫建立資料庫專案。專案的目標平台會設為 Azure SQL Database V12，且會建置專案以找出所有相容性問題。SAMW 能夠修正絕大部分的相容性問題，因此會用來處理專案中所有指令碼做為第一次傳遞。您可選擇是否使用 SAMW，但我們強烈建議您使用。在使用 SAMW 處理指令碼檔案後建置專案，將會找出剩下的問題，而這些問題必須隨後使用 Visual Studio 的 T-SQL 編輯工具來手動解決。一旦專案成功建置後，結構描述會發佈回來源資料庫複本 (建議選項)，以更新其結構描述和在原位上的資料。更新的資料庫接著會使用選項 1 中所述的技術部署至 Azure (直接部署或透過匯出和匯入 BACPAC 檔案進行)。
 
因為此選項涉及在將資料庫部署至 Azure 之前更新原位資料庫的結構描述，所以我們強烈建議在資料庫中的複本執行此選項。Visual Studio 結構描述比較工具可用來檢閱完整變更集合，此變更集會在發佈專案前套用至資料庫。

您可選擇是否使用 SQL Azure 移轉精靈 (SAMW)，但我們建議您使用。SAMW 會先偵測函數主體、預存程序和觸發程序中的相容性問題，否則其在部署完成前將不會進行偵測。如果需要僅限結構描述的部署，則可以將更新的結構描述直接從 Visual Studio 發佈至 Azure SQL Database。

## 移轉步驟

1.	開啟 Visual Studio 中的 [SQL Server 物件總管]。使用 [加入 SQL Server] 連接到包含移轉之資料庫的 SQL Server 執行個體。在總管中找出資料庫，以滑鼠右鍵按一下該資料庫，然後選取 [建立新專案...]。 

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	將匯入設定設為 [僅匯入應用程式範圍的物件]。取消選取該選項，以匯入參考登入、權限和資料庫設定。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	按一下 [開始] 匯入資料庫並建立專案，其中會包含資料庫中每個物件的 T-SQL 指令碼檔案。專案內資料夾中的指令碼檔案為巢狀。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下資料庫專案，然後選取 [屬性]。這會開啟 [專案設定] 頁面，您應該在其中將 [目標平台] 設為 [Microsoft Azure SQL Database V12]。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	選擇性：以滑鼠右鍵按一下專案，然後選取 [建置] 建置專案 (不一定要在此時執行此動作，但現在執行可提供您專案中相容性問題的數目基準，讓接下來的步驟更有效益)。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	選擇性：以滑鼠右鍵按一下專案，然後選取 [快照集專案]。藉由在轉換期間的開始及稍後階段建立快照集，您便可以在每個階段使用結構描述比較工具來比較結構描述。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)快照專案會建立具有時間戳記的 .dacpac 檔案，其中包含完整的專案結構描述。您可以修改檔案名稱，以指出在程序中的哪個階段產生此快照集。![替代文字](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

7.	使用 SQL Azure 移轉精靈 (SAMW) 處理匯入的指令碼檔案。使用資料夾選項，並選取根專案目錄資料夾。![替代文字](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)

8.	精靈會處理此資料夾和所有子資料夾中的每個指令碼檔案。在下一個頁面中將會顯示結果摘要。![替代文字](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)
9.	按 [下一步] 即可查看已變更的檔案摘要清單。 

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

>請注意，暫存複本是由處理之前的原始檔案，以及在頁面頂端指出之位置處理之後受到影響的檔案所組成。

10.	按一下 [確認] 對話方塊中的 [覆寫] 及 [確定]，變更的檔案就會覆寫原始檔案。請注意，只有實際變更的檔案會被覆寫。
11.	選用。使用結構描述比較工具，將先前建立的快照集或原始資料庫與該專案做比較，即可了解精靈所做的變更。建議您也在此時建立另一個快照集。 

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

12.	再一次建置專案 (請參閱稍早步驟) 來判斷哪些錯誤仍會發生。

13.	逐步有系統地檢查錯誤以解決每個問題。評估使用資料庫的應用程式上每個變更的影響。

14.	當資料庫沒有任何錯誤時，以滑鼠右鍵按一下專案，然後選取 [發佈]，以建置和發佈該資料庫至來源資料庫的複本 (強烈建議至少在一開始使用複本)。這個步驟的目標是更新來源資料庫結構描述，並對資料庫中的資料進行任何後續變更。
- 在您發佈之前，根據來源 SQL Server 版本，您可能需要重設專案目標平台以啟用部署。如果您正在移轉舊版 SQL Server 資料庫，除非您先將資料庫移轉至新版 SQL Server，否則請勿將任何功能導入來源 SQL Server 不支援的專案。 
- 您必須設定發佈步驟以啟用適當的 [發佈] 選項。例如，如果您已刪除或標記專案中不支援的物件，且必須從資料庫刪除這些物件，您便必須允許 [發佈] 刪除目標資料庫中的資料。 
- 如果您預期發佈作業會重複進行 

15.	請使用選項 1 中所述的技巧，將資料庫複本部署至 Azure SQL Database。

## 驗證移轉

一旦您完成移轉後，建議您將移轉資料庫中的結構描述，與來源資料庫中的結構描述進行比較，以清楚了解所做的任何變更是否滿足您當初的預期，且不會在將應用程式移轉至新資料庫時產生任何問題。您可以使用 Visual Studio 中隨附於 SQL Server 工具的結構描述比較工具來進行比較。您可以將 Azure SQL Database 中的資料庫與原始 SQL Server Database，或與資料庫首次匯入專案時所建立的快照集進行比較。

1.	連接至 Azure SQL Database 中包含移轉之資料庫的伺服器，並找出該資料庫。 

2.	以滑鼠右鍵按一下資料庫，然後選取 [結構描述比較...]。這會開啟新的結構描述比較，而左側的 Azure 的資料庫會選取做為來源。使用右側的 [選取目標] 下拉式清單，以選取目標資料庫或快照集檔案進行比較。

3.	選取來源和目標後，按一下 [比較] 觸發比較。從 Azure SQL Database 中的複雜資料庫載入結構描述可能需要相當長的時間。載入結構描述及執行 Azure SQL Database 上其他中繼資料工作的時間較短，且具有較高的定價層。

4.	當比較作業完成後，請檢閱其中差異。除非您有任何考量，否則不應該按照規則將變更套用至任何結構描述。

在左側 Azure SQL Database V12 中的 Adventure Works 2014 資料庫下的結構描述比較 (由 SQL Azure 移轉精靈轉換和移轉)，會與右側的 SQL Server 中的來源資料庫進行比較。

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

 

<!---HONumber=July15_HO2-->