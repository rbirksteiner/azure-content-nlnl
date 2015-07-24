<properties 
   pageTitle="Azure SQL Database 定價層建議" 
   description="在 Azure 入口網站中變更定價層時，提供的各種定價層建議所建議的層是最適合執行現有 Azure SQL Database 的工作負載。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="04/29/2015"
   ms.author="sstein"/>

# SQL Database 定價層建議

 在 Azure 入口網站中變更定價層時，提供的各種定價層建議所建議的層是最適合執行現有 Azure SQL Database 的工作負載。

> [AZURE.NOTE]定價層建議僅適用於 Web 和 Business 資料庫，而且只有在 [Azure 入口網站](https://portal.azure.com/)中可看到建議。


## 概觀

Azure 會評估 SQL Database 的歷程記錄資源使用狀況，藉此分析目前的效能和功能需求。此外，還會根據資料庫的大小決定至少可接受的服務層，並且啟用[商務持續性](https://msdn.microsoft.com/library/azure/hh852669.aspx)功能。

然後 Azure 會分析這項資訊，並且建議最適合的服務層和效能等級，以用於執行資料庫典型工作負載和維護其目前功能集。

- 服務會檢查歷程記錄前 15 到 30 天的資料 (資源使用量、資料庫大小和資料庫活動)，並且在耗用的資源量和目前可用服務層和效能等級的實際限制之間進行比較。
- 資料會以 15 秒鐘的間隔進行分析，每個間隔的結果集會分成最適合用來處理該結果集之工作量的現有服務層和效能等級。
- 接著這些 15 秒鐘的樣本會彙總成更大的 15-30 天分析，然後建議可以最佳化處理 95% 的歷程記錄工作負載的服務層和效能等級。

### 建議

根據您的資料庫使用方式，目前可能會有 2 種建議：


| 建議 | 說明 |
| :--- | :--- |
| 升級 | 升級至新的層。 |
| 無法使用 | 資料庫需要最小的工作負載或大約 14 天的活動。沒有足夠的資料可提供有效的建議。 |

## 取得定價層建議

選取現有的 Web 或商務資料庫，然後按一下 [定價層] 磚，可取得定價層建議。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左側功能表中的 [瀏覽]。
3. 按一下 [瀏覽] 刀鋒視窗中的 [SQL Database]。
4. 在 [SQL Database] 刀鋒視窗中，按一下您想讓服務分析的資料庫。

    ![選取資料庫][1]

5. 在資料庫刀鋒視窗中，選取 [定價層] 磚。

    ![定價層][2]


7. 按一下 [定價層] 磚之後，會看到 [建議的定價層] 刀鋒視窗，您可以在此按一下建議的層，然後按一下 [選取] 按鈕變更為該層。

    ![註冊預覽版][4]

8. (選擇性) 按一下 [檢視使用量詳細資料] 以開啟 [定價層建議詳細資料] 刀鋒視窗，您可以在此檢視建議用於資料庫的層、目前層和建議層之間的功能比較，以及歷程記錄資源使用狀況分析的圖表。

    ![註冊預覽版][5]



## 摘要

定價層建議能夠自動收集每個 SQL Database 的遙測資料，並且根據資料庫的實際效能需求和功能需求建議最佳的服務層/效能等級組合。按一下資料庫刀鋒視窗中的 [定價層] 磚，以查看定價層建議。



## 後續步驟

視特定資料庫的詳細資料而定，系統通常不會立即執行升級或降級。管理入口網站會在資料庫轉換到新層時提供通知，或者您可以藉由查詢 SQL Database Server 的主資料庫中的 [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) 檢視，來監視升級狀態。


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[2]: ./media/sql-database-service-tier-advisor/pricing-tier.png
[3]: ./media/sql-database-pricing-tier-recommendations/preview-sign-up.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=62-->