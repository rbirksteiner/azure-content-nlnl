<properties 
	pageTitle="將 SQL Database Web 或 Business 資料庫升級至新的服務層" 
	description="將 Azure SQL Database Web 或 Business 資料庫升級到新的 Azure SQL Database「基本」、「標準」，與「高階」服務層和效能層級。" 
	services="sql-database" 
	documentationCenter="" 
	authors="jenniehubbard" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/18/2015" 
	ms.author="jhubbard; sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# 將 SQL Database Web 或 Business 資料庫升級至新的服務層

Azure SQL Web 和 Business 資料庫即將在 [2015 年 9 月淘汰](http://msdn.microsoft.com/library/azure/dn741330.aspx)，所以現在是開始規劃將現有的 Web 或 Business 資料庫升級到基本、標準或高階服務層的時候。

下載 [Web and Business Database Migration Guidance Cookbook](http://download.microsoft.com/download/3/C/9/3C9FF3D8-E702-4F5D-A38C-5EFA1DBA73E6/Azure_SQL_Database_Migration_Cookbook.pdf) (Web 和 Business 資料庫移轉指引操作手冊)。

> [AZURE.NOTE] [Pricing tier recommendations](sql-database-service-tier-advisor.md) 的 Web 和 Business 資料庫現在已可使用。

## 概觀

<p>Azure Web 與 Business SQL 資料庫是在一個共用的多租用戶環境中執行，資料庫沒有任何保留的資源容量。此共用資源環境中其他資料庫的活動會影響您的效能。任何指定時間點的資源可用性主要取決於系統中執行的其他並行工作負載。這會導致有極大差異且無法預期的資料庫應用程式效能。客戶反應這樣無法預期的效能在管理上有困難，他們偏好更能預期的效能。

為了因應這項反應，Azure SQL Database 服務導入新的資料庫服務層 [(基本、標準和高階)](http://msdn.microsoft.com/library/dn741340.aspx)，以提供更能預期的效能及豐富的商務持續性和安全性新功能。這些新服務層可為某個資料庫工作負載提供指定層級的資源，不論該環境中執行的其他客戶工作負載為何。如此一來，便能有效地預期效能行為。

客戶對這些變更會產生疑問，不知道應如何評估和決定最適合目前的 Web 和Business (W/B) 資料庫的新服務層，以及實際升級程序是如何。

最終，最好的選擇就是可提供功能、效能和成本間最佳平衡，但又完全符合商務需求和應用程式需求的服務層和效能層級組合。

本白皮書會引導您將 Web 或 Business 資料庫升級至其中一個新服務層/效能層級。

請特別注意，Azure SQL Database 不會鎖定至任何特定的服務層或效能層級，所以當您的資料庫需求變更時，您就可以輕易變更可用的服務層和效能層級。事實上，基本、標準和高階 SQL Database 是以小時計費，您可以在 24 小時內向上或向下調整每個資料庫 4 次 (使用 [Azure 入口網站或以程式設計方式](http://msdn.microsoft.com/library/azure/ff394116.aspx))。這表示您可以調整服務層和效能層級，根據您的應用程式需求和各種工作負載，充分發揮資料庫的效能需求、功能集和成本。這也表示，評估及變更資料庫的服務層和效能層級 (向上和向下調整) 是持續不斷的過程，應該是您排定的維護與效能微調例行工作的一部分。


## 升級 Web 和 Business 資料庫

將 Web 或 Business 資料庫升級到新的服務層/效能層級，並不會讓資料庫離線。資料庫會繼續進行升級作業。在實際轉換到新的效能層級時，資料庫連線可能會暫時中斷一小段時間 (通常以秒計算)。若應用程式對於連線終止有暫時性的錯誤處理，在升級結束時連線就不會中斷。

將 Web 或 Business 資料庫升級至新的服務層包含下列步驟：


1. 依據功能可用性判斷服務層
2. 依據歷史資源使用量判斷可接受的效能層級
3. 為什麼 Web 或 Business 資料庫的現有效能對應到較高階層級？
4. 微調您的工作負載以符合較低的效能層級
5. 升級至新的服務層/效能層級
6. 監視新服務層/效能層級的升級
7. 在升級後監視資料庫



## 1.依據功能可用性判斷服務層

基本、標準和高階服務層提供不同的功能集，所以選取適當層的第一個步驟就是判斷可提供您的應用程式和業務所需最低功能層級的服務層。

例如，請考慮備份需要保留多久，或是否需要[標準或作用中異地複寫](http://msdn.microsoft.com/library/azure/dn783447.aspx)功能，或所需的整體最大資料庫大小等等。這些需求會決定您最低的服務層選擇。

「基本」層主要用於極小型、活動少的資料庫。因此，您通常應依據所需功能的最低層級，優先選擇升級至「標準」或「高階」層。

下表彙總及比較新服務層的功能和效能層級︰

![服務層功能比較][1]


**比較服務層和效能層級的其他資源：**

| 文章 | 說明 |
|:--|:--|
|[Azure SQL Database 服務層 (版本)](http://msdn.microsoft.com/library/azure/dn741340.aspx)| 基本、標準和高階服務層的概觀。|
|[Azure SQL Database 服務層和效能層級](http://msdn.microsoft.com/library/dn741336.aspx)| 各服務層的度量和功能 (以及如何在管理入口網站中使用 DMV 監視資料庫使用量)。 |
|[服務層有什麼不同？](http://msdn.microsoft.com/library/dn369873.aspx#Different)| 不同服務層的詳細資訊，包括您為何一層一層選取的原因。 |
|[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)|不同服務層可用的商務持續性及災害復原功能 (時間點還原、異地還原、異地複寫) 的詳細資料。|
|[SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)|不同服務層和效能層級的詳細定價資訊。|

<br>

選取符合您的資料庫需求的適當服務層之後, 下一個步驟就是對您實際的資料庫工作負載選取可接受的效能層級。



## 2.依據歷史資源使用量判斷可接受的效能層級

Azure SQL Database 服務會在管理入口網站和 [系統檢視] 中公開資訊，以針對您現有的 Web 或 Business 資料庫建議相當的新服務層和效能層級。

由於 Web 和 Business 資料庫沒有關聯之任何保證的 DTU/資源限制，我們是以 S2 效能層級資料庫可用的資源數量將百分比值標準化。任何特定間隔的資料庫平均 DTU 百分比耗用量，均可計算為該間隔內最高的 CPU、IO 和記錄檔使用量百分比值。


使用管理入口網站，取得高階的 DTU 百分比使用量概觀，然後使用系統檢視向下切入至詳細資料。

當您將伺服器升級至 Azure SQL Database V12 ([某些地區為預覽版](sql-database-preview-whats-new.md#V12AzureSqlDbPreviewGaTable)) 時，您也可以使用新的 Azure 管理入口網站來檢視針對您的 Web 或 Business 資料庫所建議的服務層。

### 如何在新的 Azure 管理入口網站中檢視建議的服務層
管理入口網站會在將伺服器升級至 Azure SQL Database V12 的程序中，針對您的 Web 或 Business 資料庫建議適當的服務層。此建議是以資料庫資源耗用量之歷程記錄分析為依據。

**新的管理入口網站**

1. 登入[新的管理入口網站](https://portal.azure.com)並瀏覽至包含 Web 或 Business 資料庫的伺服器。
2. 按一下伺服器刀鋒視窗中的 [最新更新] 部分。
3. 按一下 [升級此伺服器]。

[升級此伺服器] 刀鋒視窗現在會顯示伺服器上 Web 或 Business 資料庫的清單，以及建議的服務層。



### 如何在管理入口網站中檢視 DTU 耗用量
管理入口網站可讓您深入了解現有 Web 或 Business 資料庫的 DTU 消耗。在目前的 Azure 入口網站中可取得 DTU 資訊。


**管理入口網站**

1. 登入[管理入口網站](https://manage.windowsazure.com)並瀏覽至現有的 Web 或 Business 資料庫。
2. 按一下 [監視] 索引標籤。
3. 按一下 [新增度量]。
4. 選取 [DTU 百分比]，然後按一下底部的核取記號進行確認。

確認之後，您應在資料表中看到 [DTU 百分比] 資料。請記住，這是相較於「標準 (S2)」層級資料庫中 DTU (50 個 DTU) 的百分比。

此外，值得注意的是此資料是每 5 分鐘取樣一次的平均值，所以這些度量可能未反映樣本之間的簡短活動暴增。。

![DTU 百分比資料][2]

請注意，上述範例中的資料顯示大約 10 DTD 的平均使用量 (50 的 19.23%) 和最多 28 DTU 的最大 DTU 百分比 (55.83% x 50)。假設此資料表示我的典型工作負載，我可能會在初次升級時選取「標準 (S1)」。「標準 (S0)」提供 10 DTU，這是我的平均使用量，不過那就表示我的資料庫會平均執行 100%容量，絕對不是很好的計畫。雖然 S1 可能是很適合於我的平均使用量，而我達到最大值的次數又如何？ 或許我知道暴增來自於某些夜間維護程序，而實際的客戶使用量並不受影響，所以該時間範圍內的效能降低可能無妨。但是，或許我不知道何時達到最大值，所以 DTU 百分比耗用量可能需要進一步分析。

若要向下切入到資料庫資源耗用狀況的詳細資料，您可以使用提供的系統檢視。


### 系統檢視


在目前資料庫所在的邏輯伺服器的 master 資料庫中，Web 和 Business 資料庫資源耗用資料是透過 [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) 檢視存取。它是以效能層級限制的百分比顯示資源耗用量資料。此檢視最早可提供 14 天前的資料，間隔是 5 分鐘。

> [AZURE.NOTE]您現在可在 Web 和 Business 資料庫中使用 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 檢視，以取得資源耗用量資料的較高粒度檢視 (每 15 秒)。因為 sys.dm_db_resource_stats 只會將歷程記錄資料保留一小時，所以您可以每小時查詢此 DMV，並儲存資料以供進一步分析。

在 master 資料庫上執行下列查詢可擷取資料庫的平均 DTU 耗用量：

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

由 Web 和 Business 層的 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 與 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 傳回的資料會指示標準 S2 效能層的百分比。例如，當針對 Web 或 Business 資料庫執行時，如果值傳回 70%，表示 S2 層限制的 70%。此外，針對 Web 和 Business，百分比可能反映超出 100% 的數字，這也是以 S2 層的限制為依據。

以 S2 資料庫層級為依據的 DTU 耗用量資訊可將您的 Web 和 Business 資料庫目前的耗用量以新服務層資料庫的方式標準化，進而找出較合適的服務層。例如，如果您的平均 DTU 百分比耗用量顯示值為 80%，表示資料庫耗用 DTU 的速率是 S2 效能層級之資料庫限制的 80%。如果您在 **sys.resource_stats** 檢視中看到大於 100% 的值，就表示您需要大於 S2 的效能層。舉例來說，假設您看到值為 300% 的尖峰 DTU 百分比值。這說明您使用的資源比 S2 中可用的資源多三倍。若要判斷合理的起始大小，請比較 S2 中可用的 DTU (50 DTU) 與下一個較高的大小 (S3/P1 = 100 DTU 或 S2 的 200 %，P2 = 200 DTU 或 S2 的 400 %)。因為您的速率是 S2 的 300%，您可以從 P2 開始再重新測試。

根據 DTU 使用量百分比和符合您工作負載所需的最大版本，您可以判斷哪個服務層和效能層級最適合您的資料庫工作負載 (如同透過 DTU 百分比和各種[效能層級](http://msdn.microsoft.com/library/azure/dn741336.aspx)相對的 DTU 功率所示)。下表提供 Web/商務資源耗用量百分比和對等之新服務層效能層級的對應：

![資源耗用量][4]

> **注意：**各種效能層級間相對之 DTU 數的依據是 [Azure SQL Database 基準測試結果](http://msdn.microsoft.com/library/azure/dn741327.aspx)工作負載。由於您的資料庫工作負載很可能不同於基準測試結果，您應使用上述計算做為指導方針來判斷最初適合您的 Web/Business 資料庫的新服務層。在將資料庫移至新服務層後，請使用上一節中的程序來驗證/微調符合您工作負載需求的正確服務層。
> 
> 雖然建議的新版服務層/效能層級會將過去 14 天的資料庫活動列入考量，但此資料的依據是平均超過 5 分鐘的資源耗用量資料範例。它可能會因此漏掉持續時間少於 5 分鐘的短期活動高載。因此，升級資料庫時應先參考本指南。當您將資料庫升級至建議的服務層後，會需要更多的監視、測試和驗證，以視需要將資料庫升級或降級至不同的服務層/效能層級。

這是在 master 資料庫上執行的查詢，它會為您的 Web/Business 服務層資料庫執行計算，並針對這些 5 分鐘的資料範例間隔各別建議何種版本可能適合您的工作負載。

> **注意：**這個查詢僅適用於 Web/Business 資料庫，而且「不會」為新層級中的資料庫提供正確結果。

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**範例結果：**

![範例結果](media/sql-database-upgrade-new-service-tiers/sample_result.png)

圖示為一段時間的平均 DTU 百分比耗用趨勢。這裡提供多數時間處於 S2 層級之資料庫的範例圖表，而其中的某個尖峰活動暴衝至 P1 資料庫層級。一段時間後的 DTU 耗用量從「基本」限制攀升至 ‘P1’ 限制。為使這個資料庫完全適應新的服務層，您需要 ‘P1’ 效能層級的「高階」服務層資料庫。另一方面，如果這些暴衝至 P1 層級的突發高載情況並不常見，使用 S2 層級資料庫就已足夠。

![DTU 使用量](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**記憶體對效能的影響：**雖然記憶體是構成 DTU 評等的其中一項資源層面，但 SQL Database 的設計是要將所有可用的記憶體運用在資料庫作業上。基於這個原因，記憶體耗用量並未包含在上述查詢的平均 DTU 耗用量中。另一方面，如果您要降級至較低的效能層級，資料庫可用的記憶體就會減少。這會導致較高的 IO 耗用量，進而影響耗用的 DTU。因此，在降級至較低的效能層級時，請確定 IO 百分比中有足夠的空餘空間。您可以使用上述的 [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV 來監視。



## 3.為什麼 Web 或 Business 資料庫的現有效能對應到較高階層級？

Web 和 Business 資料庫沒有針對任何個別資料庫保留特定數量的資源容量。此外，沒有任何機制可供客戶向上或向下調整 Web 或 Business 資料庫的效能。這會導致 Web 和 Business 資料庫效能不一，範圍從相當緩慢以至高階層級。此多變的效能範圍「不公平地」取決於在多租用戶環境中共用資源的其他資料庫在任何時間點的整體資源耗用量層級。

當然 Azure SQL Database 服務的目標是讓所有 Web 和 Business 資料庫儘可能以接近 100% 最佳狀況執行。此外，這些服務還讓 Web 和 Business 資料庫的平均效能層級提升至高階層級。這就是為什麼您現有資料庫的效能可能對應至目前的高階層級。不幸的是，在評估要升級到哪個服務層時，對照 Web 和 Business 資料庫與新的服務層/效能層級時，這導致了有點不切實際的預期。

若要更清楚地了解 Web/Business 與基本、標準和高階服務層之間的差異，請看下圖。相較於在基本、標準和高階服務層模型中執行的 9 個 SQL Database，請考慮在共用資源 Web/Business 模型中執行的 9 個 SQL Database。在 Web/Business 模型中，您可以清楚看到「吵雜鄰居」對於此共用資源集區中其他資料庫的影響。當 1 個資料庫正在執行資源密集的工作負載時，集區中的所有其他資料庫都會受影響，且可用的資源會開始減少。在基本、標準和高階服務層中，每個資料庫「都會」配置特定數量的資源，所以共用環境中的其他資料庫基本上不受吵雜鄰居的問題影響，並受限於為資料庫選取的效能層級。

![新服務層的可預測效能][3]

如果整體 DTU 百分比極高，您應該開始探究構成 DTU 的詳細度量；請特別深入探索資料庫的記錄檔 I/O 和記憶體使用量細節。有可能會發現您可最佳化並減少 DTU 耗用量的潛在區域。


## 4.微調您的資料庫工作負載以符合較低的效能層級
如果資料庫的過去資源使用量分析指出您應該升級至成本超出您想要的效能層級，您可以研究一下額外效能微調有所幫助的區域。

考量您對於應用程式詳細資料的瞭解，如果相較於您所預期的典型工作負載應有使用量，資源使用量似乎非常高，您或許有些機會透過效能微調讓您的應用程式獲得好處。

事實上，所有資料庫均可受惠於另一回合的效能微調。

除了一般微調維護，例如分析索引、執行計劃等，您應該最佳化您的資料存取常式，以 Azure SQL Database 為目標。

| 文章 | 說明 |
|:--|:--|
| [Azure SQL Database 效能指引](http://msdn.microsoft.com/library/dn369873.aspx) | ＜微調您的應用程式＞一節提供微調 Azure SQL Database 效能的詳細建議和技術。|
|[效能監視和微調工具](https://msdn.microsoft.com/library/ms179428.aspx)| 可用於在 SQL Server 中監視事件及微調實體資料庫設計的工具連結和說明。|
|[效能的監視與微調](https://msdn.microsoft.com/library/ms189081.aspx)|MSDN 上的 SQL Server 2014 效能微調一節。|
| [疑難排解 SQL Server 2008 的效能問題](https://msdn.microsoft.com/library/dd672789.aspx) | 較舊但仍提供疑難排解常見效能問題指引的相關白皮書，包括有關疑難排解記憶體和 CPU 瓶頸的絕佳資訊。|
|[使用 Azure SQL Database 疑難排解和最佳化查詢](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|較舊但仍與動態管理檢視以及如何將其用來進行疑難排解有關的主題。|



## 5.升級至新的服務層/效能層級
為 Web 或 Business 資料庫判斷適當的服務層和效能層級之後，可透過多種方式將資料庫升級至新的服務層：

| 管理工具 | 變更資料庫的服務層和效能層級|
| :---| :---|
| [Azure 管理入口網站](https://manage.windowsazure.com) | 按一下資料庫 [儀表板] 頁面上的 [調整] 索引標籤。 |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | 使用 [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx) Cmdlet。 |
| [服務管理 REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) | 使用[更新資料庫](http://msdn.microsoft.com/library/dn505718.aspx)命令。|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | 使用 [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) 陳述式。 |

如需詳細資訊，請參閱[變更資料庫服務層和效能層級](http://msdn.microsoft.com/library/dn369872.aspx)


## 6.監視新服務層/效能層級的升級
Azure SQL Database 在您目前資料庫所在邏輯伺服器的 master 資料庫的 sys.dm_operation_status 動態管理檢視中，提供關於資料庫上執行之管理作業 (如 CREATE、ALTER、DROP) 的進度資訊，[請參閱 sys.dm _operation_ status 文件。](http://msdn.microsoft.com/library/azure/dn270022.aspx) 請使用作業狀態 DMV 判斷資料庫的升級作業進度。這個範例查詢會顯示資料庫上執行的所有管理作業：

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

如果您是使用管理入口網站升級，入口網站中也會有關於作業的通知。

### 當資料庫工作負載在升級後達到資源限制時會發生什麼事？
效能層級會受校正和管理，以提供所需資源來將您的資料庫工作負載執行到您所選服務層/效能層級允許的上限 (亦即資源耗用量是 100%)。如果您的工作負載達到 CPU/資料 IO/記錄檔 IO 限制的其中一項限制，您仍然可繼續接收允許之最大層級的資源，但您可能會經歷較長的查詢延遲。達到這些限制的其中一項並不會導致任何錯誤 (除非是速度慢到使查詢開始逾時)，否則僅會使您的工作負載速度變慢。如果您到達最大允許並行使用者工作階段/要求 (背景工作執行緒) 的限制，您會看到 [錯誤 10928 或 10929](http://msdn.microsoft.com/library/azure/dn338078.aspx)。


## 7.在升級後監視資料庫
將 Web/Business 資料庫升級至新服務層後，建議您主動監視資料庫，以確保應用程式達到所需的執行效能，並且視需要最佳化使用方式。建議您使用下列的額外步驟來監視資料庫。


**資源耗用量資料：**對於「基本」、「標準」和「高階」資料庫，使用者資料庫中一個名為 [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) 的新 DMV 會提供更詳細的資源耗用量資料。此 DMV 以 15 秒的間隔提供幾乎即時的前一小時作業資源耗用量資訊。某一間隔的 DTU 百分比耗用量會計算為 CPU、IO 和記錄檔方面的最大百分比耗用量。下列是計算前一小時之平均 DTU 百分比耗用量的查詢：

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
其他[文件](http://msdn.microsoft.com/library/dn800981.aspx)包含如何使用此 DMV 的詳細資料。[Azure SQL Database 效能指南](http://msdn.microsoft.com/library/azure/dn369873.aspx)涵蓋如何監視和調整您的應用程式。


- **警示：**在 Azure 管理入口網站設定「警示」，即可在已升級之資料庫的 DTU 耗用量接近特定的高層級時通知您。在 Azure 管理入口網站中，可以為各種效能度量 (如 DTU、CPU、IO 和記錄檔) 設定資料庫警示。 

	例如，您可以設定若過去 5 分鐘的平均 DTU 百分比值超出 75% 則發出「 DTU 百分比 」電子郵件警示。請參閱[作法：在 Azure 中接收警示通知和管理警示規則](http://msdn.microsoft.com/library/azure/dn306638.aspx)，以了解如何設定警示通知的詳細資訊。


- **排定的效能層級升級/降級：**如果應用程式有特殊情況，只有在某天/週的特定時間需要更多效能，您可以使用 [Azure 自動化](http://msdn.microsoft.com/library/azure/dn643629.aspx)，以計劃的作業方式將資料庫升級或降級為較高/較低的效能層級。

	例如，每週的批次/維護工作期間將資料庫升級至較高的效能層級，然後在工作完成後將它降級。這種排程也適用於任何大型的資源密集作業，像是資料載入和索引重建等。請注意，Azure SQL Database 計費模型基礎是每小時的服務層/效能層級使用量。這種彈性可讓您以更符合成本效益的方式規劃排程或計劃的升級。



## 摘要
Azure SQL Database 服務提供遙測資料和工具，可用於評估您的 Web/Business 資料庫工作負載，並判斷適合升級的最佳服務層。升級程序相當簡單，不需將資料庫離線即可完成，而且不會遺失資料。升級之資料庫的好處是效能將可預期，還有新服務層所提供的額外功能。




<!--Image references-->
[1]: ./media/sql-database-upgrade-new-service-tiers/service-tier-features.png
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png

 

<!---HONumber=62-->