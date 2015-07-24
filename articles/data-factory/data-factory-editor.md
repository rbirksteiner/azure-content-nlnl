<properties 
	pageTitle="Azure Data Factory 編輯器" 
	description="描述 Azure Data Factory 編輯器，它可讓您使用簡易型 Web 使用者介面，建立、編輯和部署連結服務、資料表和管線的 JSON 定義。" 
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

# Azure Data Factory 編輯器
Azure Data Factory 編輯器是 Azure Preview 入口網站中的簡易型 Web 編輯器，可讓您建立、編輯和部署所有 Azure Data Factory 實體的 JSON 檔案。這可讓您使用 Data Factory 服務隨附的 JSON 範本，建立連結服務、資料集和管線。如果您未使用過 PowerShell，這讓您不需要安裝和熟悉 Azure PowerShell，也能建立 Azure Data Factory。

## 啟動 Data Factory 編輯器
若要啟動 Data Factory 編輯器，請按一下 Azure Data Factory 的 [**Data Factory**] 分頁上的 [**製作和部署**] 磚。

![[製作和部署] 磚][author-and-deploy-tile]

您會看到如下圖所示的 Data Factory 編輯器：
 
![Data Factory 編輯器][data=factory-editor]
 
## 建立新的連結服務、資料集和管線
工具列有四個按鈕可用來建立 Azure Data Factory 實體。
 
- [**新增資料存放區**] 可建立資料存放區連結服務。按一下此按鈕會出現含有下列選項的功能表：[Azure 儲存體]、[Azure SQL Database]、[內部部署 SQL Server 資料庫]。
- [**新增計算**] 可建立計算連結服務。按一下此按鈕會出現含有下列選項的功能表：[隨選 HDInsight 叢集]、[HDInsight 叢集]、[AzureML 連結服務]。      
- [**新增資料集**] 可建立資料集。按一下此按鈕會出現下列選項：[Blob 資料表、[Azure SQL 資料表]、[內部部署資料表]。  
- [**新增管線**] 可建立管線。如果在工具列上看不到此按鈕，請按一下工具列的 **... (省略符號)**。
 
### 建立儲存體連結服務
1. 按一下 [**新增資料存放區**]，然後按一下功能表的其中一個選項。
 
	![[新增資料存放區] 功能表][new-data-store-menu] 
2. 右側的**編輯器畫布**會顯示 JSON 範本，讓您建立儲存體連結服務。您在 [**草稿**] 下也會看到草稿節點。執行下列動作：
	1. **Azure 儲存體**：將 **<accountname>** 和 **<accountkey>** 取代為 Azure 儲存體帳戶的名稱和金鑰。
	2. **Azure SQL Database**：將 **<servername>** 取代為 Azure SQL Server 的名稱、將 **<databasename>** 取代為資料庫的名稱、將 **<username>@<servername>** 取代為使用者的名稱、將 **<password>** 取代為使用者帳戶的密碼。 
	3. **內部部署 SQL Server 資料庫**：將 **<servername>** 取代為內部部署 SQL Server 的名稱、將 **<databasename>** 取代為資料庫的名稱、將 **<username>** 取代為使用者的名稱、將 **<password>** 取代為使用者帳戶的密碼。
4. 按一下工具列的 [**部署**]，部署連結服務。您可以按一下 [**捨棄**]，捨棄您建立的 JSON 草稿。
 
	![[部署] 按鈕][deploy-button]

1. 標題列應該會顯示部署作業的狀態。

	![部署成功訊息][deploy-success-message]
2. 如果部署作業成功，左側的樹狀檢視中會出現已建立的連結服務。
  
	![樹狀檢視中的 StorageLinkedService][storagelinkedservice-in-treview]

### 建立計算連結服務
1. 按一下 [**新增計算**]，然後按一下功能表的其中一個選項。
 
	![[新增計算] 功能表][new-compute-menu] 
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立計算連結服務。執行下列動作：
	1. 如果是**隨需 HDInsight 叢集**，請指定下列屬性的值： 
		1. 在 **clusterSize** 屬性中，指定您希望 Data Factory 服務在執行階段建立的 HDInsight 叢集大小。 
		2. 在 **jobsContainer** 屬性中，指定您想用來儲存叢集記錄檔的預設 Blob 容器名稱。
		3. 在 **timeToLive** 屬性中，指定刪除 HDInsight 叢集之前允許的閒置時間。例如：00:05:00 表示叢集閒置 5 分鐘後，就應該刪除叢集。
		4. 在 **version** 屬性中，指定叢集的 HDInsight 版本 (預設值：version 3.1)。
		5. 在 **linkedServiceName** 屬性中，指定要與 HDInsight 叢集相關聯的 Azure 儲存體連結服務。 
	6. 如果是 **HDInsight 叢集** (自備)，請指定下列屬性的值：
		1. 在 **clusterUri** 屬性中，指定您自己的 HDInsight 叢集的 URL。 
		2. 在 **userName** 屬性中，指定使用者帳戶，供 Data Factory 服務用來連接至您的 HDInsight 叢集。 
		3. 在 **password** 屬性中，指定使用者帳戶的密碼。 
		4. 在 **linkedServiceName** 屬性中，指定與您的 HDInsight 叢集相關聯的 Azure 儲存體連結服務。
	5. 如果是 **AzureML 連結服務**，請指定下列屬性：
		1. 在 **mlEndPoint** 屬性中，指定 Azure Machine Learning 批次計分 URL。
		2. 在 **apiKey** 屬性中，指定發行的工作區模型的 API 金鑰。
3. 按一下工具列的 [**部署**]，部署連結服務。

> [AZURE.NOTE]如需有關用來定義 Azure Data Factory 連結服務的 JSON 元素的說明，請參閱 MSDN Library 中的[連結的服務][msdn-linkedservices-reference]主題。

### 建立新的資料集
1. 按一下 [**新增資料集**]，然後按一下功能表的其中一個選項。
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立資料集。執行下列動作： 
	1. 如果是 **Blob 資料表**，請指定下列屬性的值：
	2. 如果是 **Azure SQL 資料表**或**內部部署資料表**，請指定下列屬性的值： 
		1. 在 **location** 區段中： 
			2. 在 **linkedServiceName** 屬性中，指定參考到您的 Azure SQL/內部部署 SQL Server 資料庫的連結服務名稱。
			2. 在 **tableName** 屬性中，指定連結服務參考到 Azure SQL Database 執行個體/內部部署 SQL Server 中的資料表名稱。
		3. 在 **availability** 區段中：
			1. 在 **frequency** 屬性中，指定資料配量生產的時間單位。支援的頻率值：Minute、Hour、Day、Week、Month。
			2. 在 **interval** 屬性中，指定所定義頻率內的間隔。例如，**frequency** 設為 **Hour**，**interval** 設為 **1**，即表示每小時產生一次新的資料配量。 
		3. 在 **structure** 區段中： 
			1. 指定資料行的名稱和類型，如下列範例所示：
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]如需有關用來定義 Azure Data Factory 資料表的 JSON 元素的說明，請參閱 MSDN Library 中的[資料表][msdn-tables-reference]主題。
 		           
### 建立及啟動管線 
1. 按一下工具列的 [**新增管線**]。如果沒看到 [**新增管線**] 按鈕，按一下 **...(省略符號)** 就能看到。   
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立管線。執行下列動作： 
	1. 在 **description** 屬性中，指定管線的描述。
	2. 在 **activities** 區段中，將活動加入至管線。範例：
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
					"type": "CopyActivity",
					"inputs": [ {"name": "EmpTableFromBlob"} ],
					"outputs": [ {"name": "EmpSQLTable"} ],		
					"transformation":
					{
						"source":
						{                               
							"type": "BlobSource"
						},
						"sink":
						{
							"type": "SqlSink"
						}	
					},
					"Policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"style": "StartOfInterval",
						"retry": 0,
						"timeout": "01:00:00"
					}		
				}
    		]
	3. 在 **start** 屬性中，指定資料處理開始或處理資料配量的時間。範例：2014-05-01T00:00:00Z。
	4. 在 **end** 屬性中，指定資料處理結束的時間。範例：2014-05-01T00:00:00Z。       

> [AZURE.NOTE]如需有關用來定義 Azure Data Factory 管線的 JSON 元素的說明，請參閱 MSDN Library 中的[管線和活動][msdn-pipelines-reference]主題。

## 將活動定義加入至管線 JSON
您可以按一下工具列的 [**加入活動**]，將活動定義加入至管線 JSON。按一下此按鈕時，您可以選擇想要加入至管線的活動類型。

![[加入活動] 選項][add-activity-options]

如果您要將資料從 Azure SQL Database 複製到 Azure Blob 儲存體，並在 HDInsight 叢集上使用 Pig 指令碼來處理 Blob 儲存體中的資料，請先將**複製活動**加入至管線，再將 **Pig 活動**加入至管線。這樣會在管線 JSON 的 [活動] 區段中建立兩個區段。Pig 活動只是 HDInsight 活動再加上 Pig 轉換。

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## 啟動管線
您可以在 JSON 中指定 start 和 end 屬性的值，以指定管線的開始日期時間和結束日期時間。

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## 編輯器中的草稿
當您在 Data Factory 中切換環境或瀏覽至不同實體時，草稿可讓您暫時儲存工作。「草稿」的存留期間與瀏覽器工作階段有關。如果您關閉瀏覽器或使用另一部電腦，就會失去草稿。

## 捨棄 Data Factory 實體的 JSON 草稿
您可以按一下工具列的 [**捨棄**] 按鈕，捨棄 Azure Data Factory 實體的 JSON 定義。

## 複製 Data Factory 實體
您可以在樹狀檢視中選取實體，再按一下工具列的 [**複製**] 按鈕，以複製現有的 Azure Data Factory 實體 (連結服務、資料表或管線)。

![複製 Data Factory 實體][clone-datafactory-entity]

您會看到樹狀檢視的 [**草稿**] 節點下建立新的草稿。

## 刪除 Data Factory 實體
您可以在樹狀檢視中選取實體，再按一下工具列的 [**刪除**]，或以滑鼠右鍵按一下實體，再按一下 [**刪除**]，以刪除 Azure Data Factory 實體 (連結服務、資料表或管線)。

![刪除 Data Factory 實體][delete-datafactory-entity]

## 另請參閱
如需有關使用 Data Factory 編輯器建立 Azure Data Factory 的逐步指示，請參閱[開始使用 Azure Data Factory][data-factory-get-started] 主題。

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=62-->