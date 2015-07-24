<properties 
	pageTitle="Data Factory - 版本資訊 | Azure" 
	description="Data Factory 版本資訊" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 版本資訊

## Data Factory 2015/04/10 版本的注意事項
現在，您會在 [資料表] 刀鋒視窗中看到 [最近更新的配量] 和 [最近失敗的配量] 清單。這些清單會依配量的更新時間排序。在下列情況中，配量的更新時間會變更。

-  您以手動方式更新配量的狀態，例如，使用 **Set-AzureDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。

按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。
 
您仍然可以按一下 [資料配量 (依配量時間)] 磚，檢視依配量時間排序的配量。這些集合中的配量會依配量時間排序。例如，如果是以小時排程，則配量為：- 4/4/2015 5pm 進行中 - 4/4/2015 4pm 失敗 - 4/4/2015 3pm 失敗

但是，如果重新執行較舊的配量，即使這可能是使用者最感興趣的配量，也不會顯示在此清單頂端。

## Data Factory 2015/3/31 版本的注意事項
- [Microsoft 下載中心][adf-gateway-download]已公佈更新的「資料管理閘道」安裝套件。
- 現在支援從內部部署檔案系統至 Azure Blob 的複製作業。如需詳細資訊，請參閱下列主題。
	-  [內部部署檔案系統連結服務](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [資料表 JSON 中的 OnPremisesFileSystemLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [支援的來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。請參閱更新的複製對照表和 **FileSystemSource** 屬性。 
-  現在支援從內部部署 Oracle 資料庫至 Azure Blob 的複製作業。如需詳細資訊，請參閱下列主題。 
	-  [內部部署 Oracle 連結服務](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [資料表 JSON 中的 OnPremisesOracleTableLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [支援的來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。請參閱更新的複製對照表和 **OracleSource** 屬性。
-  您可以對 Azure Blob 中的文字檔案指定編碼。請參閱新的 [encodingName 屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。 
- 複製到 SQL 接收器時，您可以搭配其他參數來叫用預存程序。如需詳細資訊，請參閱[叫用 SQL 接收器的預存程序][adf-copy-advanced]。   

如需詳細資訊，包括範例，請參閱 Blob 文章：[Azure Data Factory 更新 - 新的資料存放區](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/)。

## Data Factory 2015/2/27 版本的注意事項

### 新的改進功能
- **Azure Data Factory 編輯器**。Data Factory 編輯器是 Azure Preview 入口網站的一部分，可讓您建立、編輯及部署 JSON 檔案，此檔案定義連結服務、資料集和管線。編輯器的主要目標是提供快速又輕巧的使用者介面 (UI)，讓您不需要安裝 Azure PowerShell 或學習使用 PowerShell Cmdlet，即可建立 Azure Data Factory 成品。如需 Data Factory 編輯器的快速概觀和影片，請參閱 [Azure Data Factory 編輯器 - 輕量型 Web 編輯器][adf-editor-blog]部落格文章。如需編輯器的詳細概觀，請參閱 [Data Factory 編輯器][adf-editor]文章。          

### 變更

## Data Factory 2015/1/26 版本的注意事項 ##

### 變更
- [Microsoft 下載中心][adf-gateway-download]已公佈更新的「資料管理閘道」安裝套件。從這個版本開始，您可以在此下載位置找到搭配 Azure Data Factory 使用的最新「資料管理閘道」。此安裝套件支援 Azure Data Factory 和 Power BI for Office 365 服務。如果您同時使用這兩種服務，請注意，Data Factory 和 Power BI 的閘道必須安裝在不同電腦上，並根據 Data Factory 或 Power BI 文件的指導方針，以不同方式加以設定。
- 複製活動現在支援在內部部署 SQL Server 和 Azure SQL Database 之間複製資料。如需詳細資訊，請參閱[複製活動][adf-copy-activity]，如需 JSON 範例，請瀏覽 [GitHub][adf-github-samples]。
- **SqlSink** 支援新的屬性：**WriteBatchTimeout**。這個屬性可讓您彈性地設定在批次插入作業逾時之前，要等候多久讓作業完成。若要執行混合式複製 (複製作業涉及內部部署資料來源和雲端資料來源)，您必須有 1.4 版或更新版本的閘道，才能使用這個屬性。 
- SQL Server 連結服務現在支援 Windows 驗證。 
	- 現在使用入口網站建立 SQL Server 連結服務時，可以選擇使用 Windows 驗證，並設定適當的認證。您需要具有 1.4 版或更新版本的閘道。 
	- 使用 Azure PowerShell 建立 SQL Server 連結服務時，您可以用純文字指定連接資訊，或使用更新的 [New-AzureDataFactoryEncryptValue Cmdlet][adf-encrypt-value-cmdlet] 來加密連接資訊；然後，在連結服務 JSON 承載的 Connection String 屬性中使用加密的字串。如需關於在 JSON 中定義連結服務的詳細資訊，請參閱[連結服務][adf-msdn-linked-services]。New-AzureDataFactoryEncryptValue Cmdlet 尚不支援加密功能。 

## Data Factory 2014/12/11 版本的注意事項 ##

### 新的改進功能

- Azure Machine Learning 整合
	- 這一版的 Azure Data Factory 服務可讓您使用 **AzureMLLinkedService** 和 **AzureMLBatchScoringActivity** 將 Azure Data Factory 與 Azure Machine Learning (ML) 整合。如需詳細資訊，請參閱[使用 Data Factory 和 Azure Machine Learning 建立預測管線][adf-azure-ml]。 
- 提供閘道版本狀態
	- 如果有比目前安裝之版本還要新的閘道，「Azure Preview 入口網站」和 Get-AzureDataFactoryGateway Cmdlet 的輸出中就會顯示 "NewVersionAvailable" 狀態。。您可以接著依照入口網站程序下載新的安裝檔案 (.msi)，然後執行它來安裝最新的閘道。不需要進行其他設定。

### 變更

- 已移除 HdInsightOnDemandLinkedService 中的 JobsContainer。
	- 在 HDInsightOnDemandLinkedService 的 JSON 定義中，您不再需要指定 **jobsContainer** 屬性。如果您有為隨選連結服務指定的屬性，則會忽略此屬性。您可以從連結服務的 JSON 定義中移除屬性，然後使用 New-AzureDataFactoryLinkedService Cmdlet 來更新連結服務的定義。
- HDInsightOnDemandLinkedService 的選擇性組態參數
	- 此版本針對 HDInsightOnDemandLinked (隨選 HDInsight 叢集) 導入一些選擇性組態參數支援。如需詳細資訊，請參閱 [ClusterCreateParameters 屬性][on-demand-hdi-parameters]。
- 已移除閘道位置
	- 透過入口網站或 PowerShell (New-AzureDataFactoryGateway) 建立 Azure Data Factory 閘道時，您不再需要指定閘道的位置。將會繼承 Data Factory 地區。同樣地，若要使用 JSON 來設定 SQL Server 連結服務，也不再需要 "gatewayLocation" 屬性。Data Factory .NET SDK 也已更新來反映這些變更。
	- 如果您使用較舊版的 SDK 和 Azure PowerShell，則仍然必須提供位置設定。
 
     

#### 重大變更
	
- CustomActivity 變更為 DotNetActivity
	- **ICustomActivity** 介面已重新命名為 **IDotNetActivity**。您將必須更新 Data Factory NuGet 封裝，將您自訂活動之原始程式碼中的 ICustomActivity 變更為 IDotNetActivity。  
	- 您在 JSON 定義中的自訂活動類型必須從 **CustomActivity** 變更為 **DotNetActivity**。 
	- **CustomActivity** 和 **CustomActivityProperties** 類別已重新命名為具有一組相同屬性的 **DotNetActivity** 和 **DotNetActivityProperties**。

		如果您使用舊版 SDK 和 Azure PowerShell，則可以繼續使用 CustomActivity，而不需改成 DotNetActivity。
    
  		如需如何建立自訂活動，並在 Azure Data Factory 管線中使用此活動的逐步解說，請參閱[在 Azure Data Factory 管線中使用自訂活動][adf-custom-activities]。

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=62-->