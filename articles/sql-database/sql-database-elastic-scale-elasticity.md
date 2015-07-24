<properties 
	pageTitle="分區彈性" 
	description="說明概念並提供分區彈性的範例，此功能可用來輕鬆擴充 Azure SQL 資料庫。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# 分區彈性 

**分區彈性**可讓應用程式開發人員根據需求，動態地放大和縮小資料庫資源，以最佳化其應用程式的效能並降低成本。Azure SQL Database 的**彈性資料庫工具**結合 [Basic、Standard 和 Premium 服務層](http://msdn.microsoft.com/library/azure/dn741340.aspx)，提供令人信服的彈性案例。彈性資料庫工具支援水平縮放 - 一種設計模式，從分區集加入或移除資料庫 (也稱為[分區](sql-database-elastic-scale-glossary.md))，以放大或縮小容量。同樣地，SQL Database 服務層提供**垂直縮放**功能，單一資料庫的資源可以相應增加或減少，以適當地配合需求。單一分區的垂直縮放和許多分區的水平縮放結合起來，為應用程式開發人員提供非常有彈性的環境，可以調整以符合效能、容量和成本最佳化的需求。

搭配新引進的**彈性資料庫集區**功能，更容易達成垂直縮放。集區允許在整個集區共享的預算內，讓個別資料庫的資源耗用*自動*擴大或縮小。對於選擇不利用彈性資料庫集區的應用程式，這篇文章將說明實作以原則為基礎的機制來管理垂直縮放的其他技術，以及水平縮放作業自動化的一些常見案例。

## 水平縮放的範例：演唱會尖峰

處理音樂會門票交易的應用程式是水平縮放的典型案例。在正常的客戶數量下，應用程式使用最少的資料庫資源來處理購票交易。不過，在銷售熱門音樂會的門票時，單一資料庫無法處理大量湧現的客戶需求。

為了處理大幅增加的交易，應用程式進行水平縮放。應用程式現在可以將交易負載分散在許多分區。當不再需要額外的資源時，資料庫層就縮小回到正常使用量。在此，水平縮放可讓應用程式相應放大以符合客戶需求，並於不再需要資源時相應縮小。

## 垂直縮放的範例：遙測

使用**分區集**來儲存作業遙測的應用程式是垂直縮放的典型案例。在此案例中，最好是將一天內的所有遙測資料放在單一分區上。在這個應用程式中，當天的資料會擷取到一個分區中，並針對後續的日子佈建新的分區。然後，作業資料可存檔，供需要時查詢。

若要擷取高負載的遙測資料，最好採用較高服務層。換句話說，Premium 資料庫比 Basic 資料庫更好。一旦資料庫到達容量上限，就從擷取角色切換為分析和報告。就此而言，標準層的效能可滿足工作。因此，您可以在分區上 (不是最近建立的) 垂直地相應減少服務層，以配合較舊資料的較低效能需求。

垂直縮放也可用來增進單一資料庫的效能，以達到更高的效能。例如報稅應用程式。在收件期間，最好將單一客戶的資料都保留在相同資料庫中，並增加該分區的效能。視應用程式而定，垂直地相應增加和減少資源有利於最佳化成本和效能需求。

資料庫層的水平和垂直縮放結合起來，形成應用程式延展性和分區彈性的核心。

本文件提供分區彈性案例的背景，並參考垂直和水平縮放的範例實作。

## 分區彈性的機制 

垂直和水平縮放取決於三個基本要素：

1. **遙測**
2. **規則**
3. **動作**   

## 遙測

**資料導向彈性**是彈性擴縮應用程式的核心。根據效能的需求，使用遙測來制訂資料導向決策，以決定垂直或水平縮放。

#### 遙測資料來源
就 Azure SQL DB 來說，有很多重要來源都可以作為分區彈性的資料來源。

1. **效能遙測**在 **sys.resource_stats** 檢視表中以五分鐘的持續時間呈現 
2. 每小時**資料庫容量遙測**是透過 **sys.resource_usage** 檢視表來呈現。  

您可以使用下列查詢來查詢 master DB，以分析效能資源使用狀況，其中 ‘Shard_20140623’ 是目標資料庫的名稱。

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

**效能遙測**可以彙總一段時間 (下列查詢中為七天)，以移除暫時性的行為。

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

**資料庫容量**可以透過對 **sys.resource_usage** 檢視表執行類似的查詢來測量。**storage_in_megabytes** 資料行的最大值決定目前的資料庫大小。當特定分區達到容量上限時，這類遙測適用於水平縮放應用程式。

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

隨著資料擷取到特定的分區，最好提前一天進行預測，並判斷分區是否有足夠的容量來處理未來的工作負載。雖然不是真正的線性迴歸實作，但下列查詢會傳回資料庫容量在連續兩天之間的最大差異。這類遙測可套用至規則，進而形成可採取的動作 (或無動作)。

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## 規則  

規則是決策引擎，可決定是否採取動作。有些規則非常明確，而有些則較為複雜。如以下的程式碼片段所示，以容量為主的規則可設定為當分區達到其容量上限的 $SafetyMargin 時 (例如 80%)，就佈建新的分區。

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

根據上述的資料來源，可以制訂一些規則以完成許多分區彈性案例。

## 動作  

根據規則的結果，動作 (或無動作) 就是答案。兩個最常見的動作是：

* 增加或減少分區的服務層或效能層級 
* 在分區集新增或移除分區。

請注意，在水平及垂直縮放解決方案中，動作不會立即產生效果。例如，垂直縮放時，發出 ALTER DATABASE 命令將 Basic 資料庫的服務層增加為 Premium 資料庫需要花費長短不一的時間。持續時間多半取決於資料庫的大小 (如需詳細資訊，請參閱[變更服務層和效能層級](http://msdn.microsoft.com/library/azure/dn369872.aspx)。同樣地，配置或佈建新的分區也並非瞬間完成。因此，針對垂直和水平縮放兩者，應用程式應該考量改變或佈建新資料庫的非零時間。

## 範例分區彈性案例 

下圖所示的範例強調兩個彈性擴縮案例：1. 分區對應的水平縮放 2. 個別分區的垂直縮放。

![作業資料擷取][1]

為了水平縮放，使用規則 (根據日期或資料庫大小) 來佈建新的分區，並將它註冊到分區對應，從而水平放大資料庫層。其次，為了垂直縮放，實作第二個規則，將任何超過一天的分區從 Premium Edition 降級到 Standard 或 Basic Edition。

請再次考量遙測案例： 依日期的應用程式分區。它會持續收集遙測資料，在載入時需要高效能版本，但隨著資料過時，只需要較低效能。當天的資料 [Tnow] 寫入至高效能資料庫 (Premium)。午夜過後，前一天的分區 (現在是 [T-1]) 就不再用於擷取。目前的資料由目前的 [Tnow] 擷取。在隔天之前，必須佈建新的分區並向分區對應註冊 ([T+1])。

在作法上，可以在每一天之前佈建新的分區，或在目前的分區 ([Tnow]) 接近容量上限時佈建新的分區。使用任一種方法都會保留特定一天寫入的所有遙測的資料位置。也可套用每小時分區化以達到更精細的程度。佈建新的分區之後，因為 [T-1] 用於查詢和報告，這時就可降低資料庫的效能層級來減少成本。隨著 DB 中的內容過時，可以進一步降低效能層，及/或將 DB 的內容保存到 Azure 儲存體或刪除，視應用程式而定。

## 執行分區彈性案例  

為了協助水平和垂直縮放案例的實際實作，有許多[分區彈性範例指令碼](http://go.microsoft.com/?linkid=9862617)已建立並張貼在指令碼中心。這些 PowerShell Runbook 是針對在 Azure 自動化服務中執行而撰寫，提供一些與彈性資料庫用戶端程式庫和 Azure SQL Database 進行互動的方法。在建置時，或從這些程式碼範例中擷取時，您可以編寫必要的指令碼，將應用程式的水平、垂直或兩者的縮放案例自動化。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-elasticity/data-ingestion.png

<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=July15_HO2-->