<properties 
   pageTitle="使用 SQL Azure 移轉精靈 | Microsoft" 
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
   ms.date="07/01/2015"
   ms.author="pehteh"/>


# 如何：使用 SQL Azure 移轉精靈


![替代文字](./media/sql-database-migration-wizard/01SAMWDiagram.png)


此選項會使用 SQL Azure 移轉精靈，從來源資料庫產生 T-SQL 指令碼。此時精靈會轉換此資料庫使其與 SQL Database 相容，然後將此資料庫連接至 Azure SQL Database 以便在空的 Azure SQL Database 中執行指令碼。只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。此精靈也可讓您從指令碼中的資料庫包含或排除特定物件。


請注意，並非精靈可偵測到之所有不相容的結構描述都可由內建轉換加以處理。無法處理的不相容指令碼將會報告為錯誤，並在產生的指令碼中插入註解。如果發生這種情況，就必須儲存指令碼，並在提交至 Azure SQL Database 之前以手動方式加以編輯。如果需要變更，可以使用 SSMS 或 VS 中的 SQL Server 工具儲存並編輯指令碼。一旦指令碼相容後，就會在 Azure SQL Database 中以頻外方式執行。


> **注意：**如果偵測到許多錯誤，且不容易修正錯誤，您可以將產生的指令碼檔案匯入 Visual Studio 中的資料庫專案 (這是此選項的延伸功能)。如果您將專案的目標設為 SQL Database V12，就可以建置專案，並使用 VS 中的 SQL Server 工具以漸進方式修正錯誤。一旦結構描述沒有任何錯誤後，您便可以將結構描述發佈至來源資料庫的複本，然後使用 SSMS 部署或將資料庫匯出/匯入到選項 1 中所述的 Azure SQL Database。


## 下載 SQLAzureMW.exe


您可以從 CodePlex 下載 SQL Azure 移轉精靈：


[下載 SQLAzureMW.exe](http://sqlazuremw.codeplex.com/)


## 移轉步驟


&nbsp;1.請在 SQL Database 中的新伺服器上佈建新的資料庫，或是在前述升級後的現有伺服器上進行佈建。在最後一個步驟中，您將在此資料庫中執行在此選項中建立的移轉指令碼。


&nbsp;2.開啟移轉精靈，然後選取 [分析/移轉資料庫] 選項，並將 [目標伺服器] 設為 Azure SQL Database V12，然後按 [下一步]。


![替代文字](./media/sql-database-migration-wizard/02MigrationWizard.png)


&nbsp;3.在下一個頁面中，按一下 [連接到伺服器]，然後提供來源伺服器的連接資訊。在 [連接] 對話方塊中指定來源資料庫或連接到伺服器，然後從資料庫清單中選取來源資料庫。


![替代文字](./media/sql-database-migration-wizard/03MigrationWizard.png)


&nbsp;4.在下一個頁面的 [選擇物件] 中，您可以選擇編寫所有資料庫物件的指令碼 (預設值)，或選取特定資料庫物件以編寫指令碼。您可能會發現，一開始最好就先在第一次傳遞中編寫所有物件的指令碼，然後在精靈報告編寫或轉換錯誤時回到此步驟並排除物件。此精靈的運作方式是，先在資料庫中編寫物件的指令碼 (使用 SMO)，接著再後處理產生的指令碼來套用一系列以 RegEx 為基礎的驗證和轉換規則。


![替代文字](./media/sql-database-migration-wizard/04MigrationWizard.png)


&nbsp;5.選取 [進階]，並檢閱精靈所使用的組態選項。在第一次傳遞中，特別是針對大型資料庫，您應該將 [指令碼資料表/資料] 設為 [僅限資料表結構描述]，確保 [目標伺服器] 設為 Microsoft Azure SQL Database V12，且 [相容性檢查] 設為 [覆寫: 執行所有相容性檢查]。


![替代文字](./media/sql-database-migration-wizard/05MigrationWizard.png)


&nbsp;6.按 [下一步] 檢閱選項，然後再按 [下一步] 產生並轉換指令碼。您可以檢閱 [SQL 指令碼] 索引標籤上的指令碼。


&nbsp;7.如果結構描述與 SQL Database 不相容，且精靈無法轉換結構描述，則指令碼產生作業將會報告錯誤。如下方的螢幕擷取畫面所示，轉換程序中發現一個預存程序出現問題。在此情況下，程序已被寫入以使用全文檢索搜尋，但 V12 中尚未支援此作業 (將會在新版本中支援)。


![替代文字](./media/sql-database-migration-wizard/06MigrationWizard.png)


- 視您的錯誤評估而定，您可以在精靈中選擇返回並排除造成問題的物件並重新產生指令碼。當您思考打算如何解決錯誤時，請考慮對資料庫中其他物件及使用該資料庫的應用程式所造成的影響。
- 如果指令碼有需要加以更正的錯誤，您可以在 [SQL 指令碼] 索引標籤中儲存僅限結構描述的指令碼檔案，然後在您慣用的編輯器中開啟並編輯指令碼以修正錯誤，但必須在步驟 1 中所建立的新資料庫中於 SAMW 外部執行指令碼之前完成。如果指令碼過大或錯誤數量過多，您應該使用選項 3。請注意，雖然您可以將產生的指令碼檔案匯入 Visual Studio，但如選項 3 所述，從 SQL 檔案匯入所需要的時間，會比從資料庫匯入長很多。 


&nbsp;8.如果已經沒有錯誤，或您已去除因產生作業所造成的錯誤來源，可以按 [下一步]，接著在下一頁中按一下 [連接到伺服器]，以連接至在步驟 1 中所建立的 Azure SQL Database 伺服器。


![替代文字](./media/sql-database-migration-wizard/07MigrationWizard.png)


&nbsp;9.下一步是選取將在其中執行指令碼的資料庫。伺服器上所有現有資料庫將會列於清單中。選取您在步驟 1 中建立的資料庫。資料庫應該是空的，而且有適用於此作業的正確定價層。如果需要，您可以在此時使用精靈建立一個資料庫。若要建立資料庫，請按一下 [建立資料庫]，即可進行設定及建立。如需關於在移轉程序期間選擇要使用之效能層級的指引，請參閱＜快速入門＞一節。一旦建立資料庫後，選取資料庫並繼續。


&nbsp;10.選取空的資料庫後，按 [下一步] 並確認您想執行該指令碼以完成移轉作業。

 

<!---HONumber=July15_HO2-->