<properties 
	pageTitle="Azure Data Factory - 術語" 
	description="本文介紹使用 Azure Data Factory 服務建立 Data Factory 時所使用的術語" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Azure Data Factory - 術語

## 資料處理站
**Azure Data Factory** 有一或多個管線可以使用連結的計算服務 (例如 Azure HDInsight)，以處理連結的資料存放區 (Azure 儲存體、Azure SQL Database、內部部署 SQL Server 等) 中的資料。Azure Data Factory 本身不包含資料；資料是儲存在上面所述的資料存放區中。

## 連結服務
**連結服務**是連結至 Azure Data Factory 的服務。連結服務可以是下列其中一種：

- **資料存放區**服務，例如 Azure 儲存體、Azure SQL Database 或內部部署 SQL Server 資料庫。資料存放區是輸入/輸出資料集的容器。    
- 「運算」服務 (例如 Azure HDInsight、Azure Machine Learning 和 Azure Batch)。運算服務會處理輸入資料，並產生輸出資料。  

## 資料集
**資料集**是具名的資料檢視。所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於關聯式資料表甚或模型。Data Factory「資料表」是具有結構描述的資料集，並且是矩形的。在資料存放區中建立連結服務之後，可以定義代表儲存在資料存放區中的輸入/輸出資料的資料集。


##管線
Azure Data Factory 中的「管線」會使用連結的運算服務，處理連結的儲存體服務中的資料。其中包含一系列活動，其中，每個活動各執行特定的處理作業。例如，**複製活動**會將資料從來源儲存體複製到目的地儲存體，而 **HDInsight 活動**會使用 Azure HDInsight 叢集透過 Hive 查詢或 Pig 指令碼來處理資料。資料處理站可以有一或多個管線。

建立 Azure Data Factory 執行個體的一般步驟如下：

1. 建立 **Data Factory**。
2. 建立每個資料存放區或運算服務的「連結服務」。
3. 建立輸入和輸出**資料集**。
4. 建立**管線**。 

##活動
取用一或多個輸入資料集並產生一或多個輸出資料集之管線中的資料處理步驟。活動執行於執行環境中 (例如：Azure HDInsight 叢集)，並且會對與 Azure Data Factory 相關聯/連結的資料存放區讀取/寫入資料。

Azure Data Factory 服務支援管線中的下列活動：

- **複製活動**會將資料從一個資料存放區複製到另一個資料存放區。如需有關「複製活動」所支援資料存放區的詳細資料，請參閱[使用 Azure Data Factory 複製資料][copy-data-with-adf]。 
- **HDInsight 活動**會在 HDInsight 叢集上執行 Hive/Pig 指令碼或 MapReduce 程式來處理資料。如需詳細資料，請參閱[搭配 Data Factory 使用 Pig 和 Hive][use-pig-hive] 和[從 Data Factory 叫用 MapReduce 程式][run-map-reduce]。 
- **Azure Machine Learning 批次計分活動**會叫用 Azure Machine Learning 批次計分 API。如需詳細資料，請參閱[使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線][azure-ml-adf]。 
- **預存程序活動**會在 Azure SQL Database 中叫用預存程序。如需詳細資料，請參閱 MSDN Library 上的[預存程序活動][msdn-stored-procedure-activity]。   

##配量
Azure Data Factory 中的資料表由配量所組成。配量的寬度取決於排程 – 每小時/每日。如果排程是「每小時」，將會依照管線的開始時間和結束時間每小時產生配量。例如，如果管線開始日期時間是 03/03/2015 06:00:00 (早上 6 點) 而結束日期時間是 03-03/2015 09:00:00 (同一天的早上 9 點)，則會產生三個資料配量，一個配量表示 1 個小時間隔：早上 6-7、早上 7-8，以及早上 8-9。

配量可讓您處理特定時段的整體資料子集 (例如：在 下午 1:00 至下午 2:00 期間 (小時) 內產生的配量)。

## 配量的活動執行
**執行**或活動執行是配量的處理單位。如果您重新嘗試，或是在失敗時重新執行配量，可能執行一次或多次配量。配量是由其開始時間識別。

##資料管理閘道
Microsoft **資料管理閘道**是將內部部署資料來源連接至雲端服務以供取用的軟體。您至少要有一個閘道安裝在公司環境中，並使用 Azure Data Factory 入口網站加以註冊，才能將內部部署資料來源新增為連結服務。
 
##資料中樞
**資料中樞**是資料儲存和計算服務的邏輯容器。例如，以 HDFS 做為儲存體，並以 Hive/Pig 做為計算 (處理) 服務的 Hadoop 叢集，即為資料中樞。同樣地，企業資料倉儲 (EDW) 也可以模型化為資料中樞 (以資料庫做為儲存體，以預存程序和 (或) ETL 工具做為計算服務)。管線會使用資料存放區，並執行於資料中樞內的計算資源上。目前僅支援 HDInsight 中樞。

資料中樞可讓 Data Factory 分成邏輯或網域特定群組，例如，以「美西 Azure 中樞」管理專門處理美西資料中心的所有連結服務 (資料存放區和計算) 和管線，或以「銷售 EDW 中樞」管理所有與填入和處理「銷售企業資料倉儲」的資料相關聯的連結服務和管線。

中樞的重要特性之一是管線執行於單一中樞上。這表示在定義管線時，該管線內的資料表或活動所參考的所有連結服務，都必須具有與管線本身相同的中樞名稱。如果未指定連結服務的 HubName 屬性，連結服務將會置於「預設」中樞內。

## 另請參閱

1. [Azure Data Factory 簡介][adf-intro]。本文提供 Azure Data Factory 服務，它提供的值和所支援之案例的概觀。
2. [開始使用Data Factory][datafactory-getstarted]。本文提供端對端教學課程，說明如何建立將資料從 Azure Blob 複製到 Azure SQL 資料庫的範例 Azure Data Factory。


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=62-->