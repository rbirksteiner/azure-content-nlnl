<properties 
	pageTitle="使用 Azure Data Factory 的常見案例" 
	description="了解使用 Azure Data Factory 服務的一些常見案例" 
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

# 使用 Azure Data Factory 的常見案例
本節將說明 Azure Data Factory 目前可支援、且將持續演化為中樞案例的幾個範例案例。

> [AZURE.NOTE]閱讀本文之前，請先看完一遍 [Azure Data Factory 簡介][datafactory-introduction]文章。

##案例 #1：資料中樞的資料來源
![資料中樞的資料來源][image-data-factory-introduction-secenario1-source-datahub]

企業會在有不同類型的資料位於不同的來源中。建置資訊生產系統的第一步，就是連線到所有必要的資料和處理來源 (例如 SaaS 服務、檔案共用、FTP、Web 服務)，然後視需要移動資料以進行後續處理。

沒有 Data Factory，企業必須建置自訂的資料移動元件或撰寫自訂服務，以整合這些資料來源和處理。此類系統不僅昂貴，而且難以整合和維護，且通常缺乏企業等級的監視和警示功能，以及完全受管理的服務所能提供的控制力。
  
透過 Azure Data Factory，資料儲存和處理服務將會收集到中樞容器，而可加速和最佳化計算和儲存活動、達到統一的資源使用管理，並視需要提供資料移動的服務。

##案例 #2：實作資訊生產
實作案例是資料來源案例之後的下一個邏輯步驟。在資料存在於中樞之後，您想要編寫和實作資料管線，在可維護且可控制的排程中可靠地產生轉換的資料，以將信任的資料饋送至生產環境。Azure Data Factory 中的資料轉換是透過 Hive、Pig 和執行於 Hadoop (Azure HDInsight) 的自訂 C# 處理來執行的。這些轉換可以用來清除資料、遮罩重要的資料欄位，以及以各種不同的複雜方法對資料執行其他操作。通常，實作化是藉由既複雜又難以維護的基礎結構和自訂服務來達成，為這類解決方案的實作、管理、調整、疑難排解及版本控制帶來一些挑戰。
  
以 Data Factory 做為完全受管理的服務，可讓透過一次性或複雜的週期性排程定義這些管道而加以實作，且協調流程工作會直接由 Data Factory 服務來處理。使用「中樞」時，將會代表使用者處理「中樞」內所有資料和處理的叢集管理，讓使用者可以專注在具變化性的分析上，而不是專注在基礎結構管理上。Azure Data Factory 排除了在使用脆弱的自訂服務上的挑戰，讓企業能夠以可靠且能夠重現的方式產生受信任的資訊。


##案例 3#：透過資料探索整合資訊生產
傳統 BI 方法和技術在提供「權威性事實來源」的同時，也幾乎都有嚴重的副作用：由於有一個謹慎控制的變更要求程序，因此經常會有待處理的要求項目。為了因應快速變化的商業問題，企業必須要有更大的彈性，以將其資訊生產系統與資訊使用系統相連結。Azure Data Factory 可協助您透過資訊生產的精簡化資料管線來解決連結這些系統的挑戰，並以可輕鬆取用的形式提供最新的可信任資料，來解決資訊取用方面的挑戰。
  
Azure Data Factory 支援下列可讓產生的資料易於取用的功能：

- 使用現有的 BI 工具 (Excel、Tableau 等)，輕鬆地將產生的資料資產移至 (一次性或排程) 關聯式資料超市，以供取用。
- 取用 Data Factory 直接在 Excel 中使用 Power Query 產生的資料資產。

請參閱下列主題，以了解如何使用 Power Query 取用資料：

- [Power Query：連線到 Microsoft Azure 資料表儲存體][Power-Query-Azure-Table]
- [Power Query：連接至 Windows Azure Blob 儲存體][Power-Query-Azure-Blob]
- [Power Query：連線到 Microsoft Azure SQL 資料庫][Power-Query-Azure-SQL]
- [Power Query：連線至 Microsoft 內部部署 SQL Server][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png



 

<!---HONumber=62-->