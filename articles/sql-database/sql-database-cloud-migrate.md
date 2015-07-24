<properties 
   pageTitle="移轉至 Azure SQL Database" 
   description="Microsoft Azure SQL Database、資料庫部署、資料庫移轉、匯入資料庫，匯出資料庫、移轉精靈" 
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
   ms.date="04/15/2015"
   ms.author="pehteh"/>

# 概觀
Azure SQL Database V12 提供與 SQL Server 2014 幾乎完全的引擎相容性，因此可大幅簡化將大多數資料庫從 SQL Server 移轉至 Azure SQL Database 的工作。許多資料庫的移轉是直接移動作業，對結構描述的變更很少 (如果有的話)，而且重新設計應用程式的幅度也很小或甚至不需要。如果需要變更資料庫，這些變更的範圍會更侷限。

根據設計，SQL Server 的伺服器範圍功能不受 SQL Database 的支援，因此依賴這些功能的資料庫和應用程式仍然需要稍微重新設計，才能移轉。雖然 SQL Database V12 改善了與 SQL Server 的相容性，但是您仍然需要謹慎規劃及執行移轉，特別是針對較大且較複雜的資料庫。

## 速覽
將 SQL Server 資料庫移轉至 Azure 的方法有許多種，每種方法會使用一項或多項工具。有些方法快速且容易使用，有些方法則需要較長的準備時間。請注意，移轉大型複雜的資料庫可能需要好幾個小時的時間！

### 選項 #1
***使用 SQL Server Management Studio (SSMS) 移轉相容的資料庫***

您可以使用 SSMS 將資料庫部署至 Azure SQL Database。您可以將資料庫直接部署或匯出至 BACPAC，然後再匯入，以建立新的 Azure SQL Database。適用於來源資料庫完全與 Azure SQL Database 相容時。

### 選項 #2
***使用 SQL Azure 移轉精靈 (SAMW) 移轉幾乎相容的資料庫***

您可以使用 [SQL Azure 移轉精靈] 處理資料庫以產生移轉指令碼，其中包含結構描述或結構描述加上 BCP 格式的資料。適用於資料庫結構描述需要升級且可由精靈處理變更時。

### 選項 #3
***使用 Visual Studio (VS) 和 SAMW 離線更新資料庫結構描述，並使用 SSMS 進行部署***

您可以將來源資料庫匯入 Visual Studio 資料庫專案，以離線處理。[SQL Azure 移轉精靈] 會接著執行專案中的所有指令碼，以套用一連串的轉換和更正。專案是以 SQL Database V12 為目標所建置，並會回報任何其餘錯誤。接著可使用 Visual Studio 的 SQL Server 工具，手動解決這些錯誤。成功建置專案之後，會將專案發佈回來源資料庫的複本，並接著使用選項 #1，將這個更新過的資料庫部署至 Azure SQL Database。如果需要僅限結構描述的移轉，則可以將結構描述直接從 Visual Studio 發佈至 Azure SQL Database。適用於資料庫結構描述需要的變更超過 SAMW 單獨可處理的數量時。

## 決定要使用的選項
- 如果您預期資料庫可直接移轉而不需要變更，則應該使用快速且容易使用的選項 #1。如果您不確定，請依照選項 #1 的說明，一開始從資料庫匯出僅限結構描述的 BACPAC。如果匯出成功且未發生錯誤，則可以使選項 #1 移轉資料庫及其資料。  
- 如果在選項 #1 的匯出期間發生錯誤，請依照選項 #2 的說明，使用 [SQL Azure 移轉精靈] (SAMW) 在僅限結構描述模式中處理資料庫。如果 SAMW 未回報任何錯誤，則可以使用選項 #2。 
- 如果 SAMW 回報結構描述需要額外的工作，除非只需要簡單的修正，否則最好使用選項 #3，在 Visual Studio 中使用 SAMW 並手動套用結構描述變更，來離線更正資料庫結構描述。接著就地更新來源資料庫的複本，再使用選項 #1 移轉至 Azure。

## 移轉工具
使用的工具包括 SQL Server Management Studio (SSMS)、Visual Studio 的 SQL Server 工具 (VS、SSDT)、[SQL Azure 移轉精靈] (SAMW)，以及 Azure 入口網站。

> 由於舊版用戶端工具與 SQL Database V12 不相容，因此請務必安裝最新版本。

### SQL Server Management Studio (SSMS)
您可以使用 SSMS 將相容的資料庫直接部署至 Azure SQL Database，或將資料庫的邏輯備份匯出成 BACPAC，然後再使用 SSMS 匯入，以建立新的 Azure SQL Database。

您必須使用最新版 SSMS (SQL Server 2013 的 CU6 和以上版本)，或下載工具的[最新版本](http://msdn.microsoft.com/evalcenter/dn434042.aspx)。

### SQL Azure 移轉精靈 (SAMW)
SAMW 可用來分析現有資料庫的結構描述是否與 Azure SQL Database 相容，而且在許多情況下，可用來產生含有結構描述和資料的 T-SQL 指令碼，再進行部署。這個精靈會在轉換期間遇到無法轉換的結構描述內容時回報錯誤。如果發生這種情況，產生的指令碼會要求進一步的編輯，部署才能成功。SAMW 會處理 Visual Studio 的 SQL Server 工具執行驗證時一般會排除的函式或預存程序主體 (如下所示)，因此可能會找到單獨使用 Visual Studio 驗證時不會回報的問題。搭配使用 SAMW 和 Visual Studio 的 SQL Server 工具可大幅減少移轉複雜結構描述所需的工作量。

請務必使用來自 CodePlex 的最新版 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)。

### Visual Studio 的 SQL Server 工具 (VS、SSDT)
Visual Studio 的 SQL Server 工具可用來建立及管理資料庫專案，該專案針對結構描述中的每個物件各包含一組 T-SQL 檔案。您可以從資料庫或指令碼檔案匯入這個專案。專案建立之後，可以是 Azure SQL Database V12；建置專案，再驗證結構描述相容性。按一下錯誤會開啟對應的 T-SQL 檔案，以供編輯並更正錯誤。修正所有錯誤之後，可以將專案直接發佈至 SQL Database，以建立空的資料庫；或發佈回原始 SQL Server 資料庫，以更新其結構描述，如此一來，便可使用上述的 SSMS 透過其資料來部署資料庫。

您必須針對 Azure SQL Database V12 最新更新，使用 Visual Studio 的最新版 SQL Server 資料庫工具。請確定您已安裝 Visual Studio 2013 Update 4。

## 比較
| 選項 #1 | 選項 #2 | 選項 #3 |
| ------------ | ------------ | ------------ |
| 將相容的資料庫部署至 Azure SQL Database | 產生含有變更的移轉指令碼，並在 Azure SQL Database 上執行 | 就地更新資料庫，然後部署至 Azure SQL Database |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png) | ![離線編輯](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| 使用 SSMS | 使用 SAMW | 使用 SAMW、VS、SSMS |
|這個簡單程序要求結構描述必須相容。移轉後的結構描述會保持不變。 | SAMW 會產生 T-SQL 指令碼，其中包含確保相容性所需的變更。某些不支援的功能將從結構描述卸除，大部分會標示為錯誤。 | 結構描述會匯入 Visual Studio 中的資料庫專案，並 (選擇性) 使用 SAMW 進行轉換。使用 Visual Studio 的 SQL Server 工具進行其他更新，然後使用最終結構描述就地更新資料庫。 |
| 如果匯出 BACPAC，則可以選擇僅限結構描述移轉。 | 可設定精靈撰寫結構描述指令碼，或撰寫結構描述加上資料的指令碼。 | 可將僅限結構描述直接從 Visual Studio 發佈至 Azure。資料庫會就地更新任何必要的變更，以允許部署/匯出結構描述和資料。 |
| 一律會部署或匯出整個資料庫。 | 可選擇排除特定物件不進行移轉。 | 完全控制要包含在移轉中的物件。 |
| 如果發生錯誤，無法變更輸出；來源結構描述必須相容。 | 產生的單一指令碼很龐大，因此在需要編輯時可能很難進行。這個指令碼可在 SSMS 中，或在 Visual Studio 中透過 SQL Server 資料庫工具來開啟及編輯。必須修正所有錯誤，才能將這個指令碼部署至 Azure SQL Database。| 可使用 Visual Studio 之 SQL Server 工具的完整功能。結構描述會離線變更。 |
| 應用程式驗證會在 Azure 中進行。由於結構描述會直接移轉而不需要變更，因此為基本驗證。 | 應用程式驗證會在移轉後於 Azure 中進行。也可能在內部安裝產生的指令碼，以進行初始應用程式驗證。 | 應用程式驗證會在將資料庫部署至 Azure 之前，於 SQL Server 中完成。 |
| Microsoft 支援的工具。 | 從 CodePlex 下載的社群支援工具。 | Microsoft 支援的工具，並選擇性使用從 CodePlex 下載的社群支援工具。 |
| 簡單且容易設定的一個或兩個步驟程序。 | 結構描述的轉換、產生和部署至雲端，都是透過單一且容易使用的精靈來協調。 | 較複雜的多步驟程序 (如果只要部署結構描述則比較簡單)。 |


 

<!---HONumber=July15_HO2-->