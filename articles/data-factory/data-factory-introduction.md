<properties 
	pageTitle="Azure Data Factory 簡介" 
	description="了解如何使用 Azure Data Factory 服務撰寫資料處理、資料儲存和資料移動服務，以建立產生可信資訊的管線。" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 服務簡介
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


**Azure Data Factory** 服務是完全受管理的服務，可將資料儲存、資料處理及資料移動服務組合成有效率、可調整且可靠的資料生產管線。Data Factory 服務可讓您：

- 建置資料導向的工作流程 (管線)，以聯結、彙總和轉換來自內部部署、雲端型和網際網路資料存放區的資料。 
- 將半結構化、非結構化和結構化資料從不同的資料來源轉換為受信任的資訊。
- 使用商業智慧 (BI)、分析工具和其他應用程式，產生可以輕鬆取用的資料。 
- 透過簡單的 JSON 指令碼來設定複雜資料處理。
- 透過 Azure 預覽入口網站所提供的豐富視覺體驗，快速監視和管理這些管線。  

以下影片提供 Azure Data Factory 服務的快速概觀。


- [影片：Azure Data Factory 簡介](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## 概觀
傳統上，資料整合專案牽涉到建立從組織內的各種資料來源擷取資料的「擷取、轉換和載入 (ETL)」程序、轉換資料以符合 Enterprise Data Warehouse (EDW) 的目標結構描述，以及將資料載入至 EDW。接著，EDW 會受到存取，做為 BI 分析解決方案的單一事實來源。

![傳統 ETL][image-data-factory-introduction-traditional-ETL]

企業現今的資料型態，在數量、多樣性與複雜性方面都持續呈現急速成長的態勢。不同形式和速度的內部部署與雲端架構資料，都比以往更為多元化。資料處理必須跨地理位置執行，並且包含開放原始碼軟體、商業解決方案和自訂處理服務的組合，其成本昂貴，而且難以整合和維護。因應現今不斷變動的巨量資料型態所需的靈活性，是結合傳統 EDW 與現代資訊生產系統所需功能的良機。

![當今的各種處理型態][image-data-factory-introduction-todays-diverse-processing-landspace]

**Azure Data Factory** 服務是可跨越傳統 EDW 和變動的資料型態而運作的編撰平台，可讓企業利用所有的可用資料做出資料導向的決策。它可讓企業善用這些多樣性，因為它所提供的平台可將資料處理、儲存和移動服務組合到資訊生產管線中，以及管理受信任的資料資產。

Azure Data Factory 服務可讓您：

- 輕鬆地使用不同的資料儲存和處理系統。 Data Factory 服務可讓您建立資訊生產管線，以移動和處理內部部署資料 (例如 SQL Server) 和雲端資料來源 (例如 Azure SQL Database、Azure 資料表和 Blob)。 
- 將資料轉換為受信任的資訊。 Data Factory 服務支援 Hive、Pig 和 C# 處理以及重要處理功能，例如自動 Hadoop (HDInsight) 叢集管理、暫時性失敗重試、可設定的逾時原則和警示。  
- 在同一處監視資料管線。 Data Factory 服務提供一個可靠且完整的儲存、處理和資料移動服務檢視。它可協助您快速評估端對端資料管線健康情況、指出問題所在，並視需要採取修正動作。您也可以透過視覺化方式追蹤跨任何來源的資料之間的資料歷程和關聯，並從單一監視儀表板查看工作執行、系統健全狀況和相依性的完整歷程記錄處理。
- **從轉換的資料取得豐富的深入資訊** Data Factory 服務可讓您建立資料管線，以產生受信任的資料，供商業智慧和分析工具以及其他應用程式使用。

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## 應用程式模型
下圖說明 Azure Data Factory 服務所支援的應用程式模型。

![應用程式模型][image-data-factory-application-model]

Azure Data Factory 有三個資訊生產階段：

- 連接和收集。在這個階段中，資料會從各種資料來源匯入至資料中樞。資料處理站中的管線可以有一或多個活動。您可以使用資料管線中的一或多個複製活動，將來源資料存放區中的資料收集到資料中樞內的目的地資料存放區，以供進一步處理。HDInsight 叢集 (運算) 和其相關聯的 Azure Blob 儲存體 (儲存) 一起構成資料中樞 (HDInsight 資料中樞)。若要使用 HDInsight 資料中樞，您可以將所有來源資料複製到與 HDInsight 相關聯的 Azure Blob 存放區，以讓 HDInsight 叢集處理資料。管線會在資料中樞 (例如 HDInsight 叢集) 的運算資源上執行。      
- 轉換和擴充。在這個階段中，會處理所收集的資料。例如，管線中的 HDInsight 活動可以使用 Hive/Pig 指令碼執行轉換，以產生受信任的資訊，藉此處理相關聯 Azure Blob 存放區中所儲存的資料。管線可以鏈結 (如圖所示)，因此，管線的輸出資料集可以是相同資料中樞或另一個資料中樞內另一個管線的輸入資料集。  
- 發行。在這個階段中，會發行資料，讓資料可供 BI 工具、分析工具及其他應用程式取用。例如，管線中的複製活動可以將輸出資料從轉換和擴充階段中所執行的處理複製到資料存放區 (例如：內部部署 SQL Server)，而在其上可建置商業智慧解決方案。   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##後續步驟
1. [開始使用 Data Factory][datafactory-getstarted]。本文提供端對端教學課程，說明如何建立將資料從 Azure Blob 複製到 Azure SQL 資料庫的範例 Azure Data Factory。
2. [教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial]。本文提供端對端逐步解說，示範如何使用 Azure Data Factory 實作真實案例，藉此將記錄檔中的資料轉換成深入資訊。

## 另請參閱
- [Data Factory - 術語][adf-terminology]。本文介紹使用 Azure Data Factory 服務建立 Data Factory 時所使用的術語。 
- [Data Factory - 常見問題集][adf-faq]。本文提供常見問題與解答的清單。
- [使用 Azure Data Factory 的常見案例][adf-common-scenarios]。本文說明使用 Azure Data Factory 服務的一些常見案例。 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
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