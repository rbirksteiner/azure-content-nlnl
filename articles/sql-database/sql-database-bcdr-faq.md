<properties 
   pageTitle="SQL Database 業務續航力常見問題集" 
   description="客戶經常詢問的問題和解答，與 Azure SQL Database 業務續航力和嚴重損壞修復的內建和選擇性功能相關。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

# 業務續航力常見問題集

## 1.當我依服務層降級/升級時，我的還原點保留期限會有什麼改變？
降級為較低的效能層之後，還原點的保留期限會立即縮短為目前資料庫效能層的保留期限。

如果升級資料庫服務層，只有在升級資料庫之後，保留期限才會開始延長。

例如，如果資料庫從 P1 降級為 S3，保留期限會立即從 35 天變更為 14 天，而且將無法再使用 14 天以前的所有還原點。之後，如果重新升級為 P1，保留期限一開始會是 14 天，再慢慢延長為 35 天。

## 2.已卸除資料庫的保留期限有多久？ 
保留期限取決於資料庫所在的服務層，或是資料庫存在的天數，取兩者中較少的天數。

## 3.如何還原已刪除的伺服器？

目前不支援還原已刪除的伺服器。

## 4.還原資料庫需要多久的時間？

還原資料庫所需的時間取決於多個因素，例如資料庫大小、交易記錄檔數目、網路頻寬等。大多數資料庫可在 12 小時內還原。

## 5.我可以變更資料庫的還原點保留期限嗎？

編號

## 6.如何找出資料庫的可用還原點？

若要從使用者錯誤復原，目前時間會是可用的最近還原點。若要找出可用的最早還原點，請使用[取得資料庫](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) 以取得最早的還原點 (非異地複寫的還原點)。

若要從中斷情況復原，請使用[取得可復原的資料庫](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) 以取得異地複寫的最近還原點。

## 7.如何大量還原伺服器下的所有資料庫？

沒有可執行大量還原的內建功能。您可以使用 [Azure SQL Database：完整伺服器復原](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)指令碼來完成這項工作。

## 8.標準異地複寫和主動式異地複寫有何不同？

若為標準異地複寫，次要資料庫是不可讀取的。它僅適用於在中斷期間進行容錯移轉。

若為主動式異地複寫，所有次要資料庫都是可讀取的 (最多 4 個次要資料庫)。

## 9.什麼是使用標準異地複寫或主動式異地複寫時的複寫延遲？

異地複寫使用連續複製。因此，使用 [sys.dm_continuous_copy_status](https://msdn.microsoft.com/library/azure/dn741329.aspx) 動態管理檢視 (DMV) 可取得上次複寫時間和其他資訊。




 

<!---HONumber=July15_HO2-->