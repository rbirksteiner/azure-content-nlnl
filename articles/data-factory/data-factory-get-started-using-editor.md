<properties 
	pageTitle="開始使用 Azure Data Factory" 
	description="此教學課程說明如何建立將資料從 Blob 複製到 Azure SQL 資料庫儲存個體的範例資料管線。" 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# 教學課程：使用 Data Factory 編輯器建立和監視 Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##本教學課程內容
本教學課程包含下列步驟：

步驟 | 說明
-----| -----------
[步驟 1：建立 Azure Data Factory](#CreateDataFactory) | 在此步驟中，您將建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。  
[步驟 2：建立連結服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 會連結 Azure 儲存體，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 ADFTutorialDataFactory。管線的輸入資料位於 Azure Blob 儲存體的 Blob 容器中，輸出資料則儲存在 Azure SQL Database 的資料表中。因此，您可以將這兩個資料存放區以連結服務的形式新增至 Data Factory。      
[步驟 3：建立輸入和輸出資料表](#CreateInputAndOutputDataSets) | 在上一個步驟中，您所建立的連結服務參照含有輸入/輸出資料的資料存放區。在此步驟中，您將定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表儲存在資料存放區的輸入/輸出資料。您將針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。您也將指定其他屬性 (例如資料結構、資料可用性等)。 
[步驟 4：建立和執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將在 ADFTutorialDataFactory 中建立名為 **ADFTutorialPipeline** 的管線。管線會有一個複製活動，將輸入資料從 Azure Blob 複製到輸出 Azure SQL 資料表。
[步驟 5：監視配量和管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將使用 Azure Preview 入口網站來監視輸入和輸出資料表的配量。
 

## <a name="CreateDataFactory"></a>步驟 1：建立 Azure Data Factory
在此步驟中，您會使用 Azure Preview 入口網站建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。

1.	登入 [Azure Preview 入口網站][azure-preview-portal]之後，請按一下左下角的 [新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。 

	![新增->DataFactory][image-data-factory-new-datafactory-menu]

6. 在 [新增 Data Factory] 刀鋒視窗中：
	1. 輸入 **ADFTutorialDataFactory** 做為名稱。 
	
  		![新增 Data Factory 刀鋒視窗][image-data-factory-getstarted-new-data-factory-blade]
	2. 按一下 [資源群組名稱]，然後執行下列動作：
		1. 按一下 [建立新的資源群組]。
		2. 在 [建立資源群組] 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為資源群組的名稱，然後按一下 [確定]。 

			![建立資源群組][image-data-factory-create-resource-group]

		本教學課程的某些步驟是假設您使用 **ADFTutorialResourceGroup** 做為資源群組名稱。若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](resource-group-overview.md)。  
7. 在 [新增 Data Factory] 刀鋒視窗中，請留意是否已選取 [新增至開始面板]。
8. 按一下 [新增 Data Factory] 刀鋒視窗中的 [建立]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialDataFactory” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。執行本教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialFactory。請參閱 [Data Factory - 命名規則][data-factory-naming-rules]主題，以了解 Data Factory 成品的命名規則。
	 
	![Data Factory 名稱無法使用][image-data-factory-name-not-available]

9. 按一下左側的 [通知] 中樞，然後從建立程序中尋找通知。按一下 **X** 關閉 [通知] 刀鋒視窗 (若已開啟)。
10. 建立完成之後，您會看到 [DATA FACTORY] 刀鋒視窗，如下所示。

    ![Data Factory 首頁][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>步驟 2：建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結服務會連結 Azure 儲存體帳戶，AzureSqlLinkedService 則會將 Azure SQL Database 連結至 **ADFTutorialDataFactory**。您稍後將在本教學課程中建立管線，以將資料從 StorageLinkedService 中的 Blob 容器複製到 AzureSqlLinkedService 中的 SQL 資料表。

### 建立 Azure 儲存體帳戶的連結服務
1.	在 [DATA FACTORY] 刀鋒視窗中，按一下 [製作和部署] 磚來啟動 Data Factory 的 [編輯器]。

	![[製作和部署] 磚][image-author-deploy-tile]

	如需 Data Factory 編輯器的詳細概觀，請參閱 [Data Factory 編輯器][data-factory-editor]主題。
	 
5. 在 [編輯器] 中，按一下工具列上的 [新增資料存放區] 按鈕，然後從下拉式功能表中選取 [Azure 儲存體]。在右窗格中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 範本。

	![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 將 **accountname** 和 **accountkey** 取代為 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰值。

	![編輯器 Blob 儲存體 JSON][image-editor-blob-storage-json]
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

6. 按一下工具列上的 [**部署**]，部署 StorageLinkedService。確認您在標題列看到 [已成功建立連結服務] 訊息。

	![編輯器 Blob 儲存體部署][image-editor-blob-storage-deploy]

### 建立 Azure SQL Database 的連結服務
1. 在 [Data Factory 編輯器] 中，按一下工具列上的 [新增資料存放區] 按鈕，然後從下拉式功能表中選取 [Azure SQL Database]。在右窗格中，您應該會看到用來建立 Azure SQL 連結服務的 JSON 範本。

	![編輯器 Azure SQL 設定][image-editor-azure-sql-settings]

2. 將 **servername**、**databasename**、**username@servername** 和 **password** 替換為您的 Azure SQL 伺服器名稱、資料庫名稱、使用者帳戶和密碼。
3. 按一下工具列上的 [部署]，建立並部署 AzureSqlLinkedService。 
   

## <a name="CreateInputAndOutputDataSets"></a>步驟 3：建立輸入和輸出資料表
在上一個步驟中，您已建立連結服務 **StorageLinkedService** 和 **AzureSqlLinkedService**，將 Azure 儲存體帳戶和 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactory**。在此步驟中，您將定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表StorageLinkedService 和 AzureSqlLinkedService 所參照資料存放區中所儲存的輸入/輸出資料。您將針對 EmpTableFromBlob 指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。

### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您將在 **StorageLinkedService** 連結服務所代表的 Azure 儲存體中，建立指向 Blob 容器的 **EmpBlobTable** 資料表。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增資料集] 按鈕，然後按下拉式功能表中的 [Blob 資料表]。 
2. 將右窗格中的 JSON 替換為以下 JSON 片段： 

        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     請注意：
	
	- 位置 **type** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService**。您已在步驟 2 中建立此連結服務。
	- **folderPath** 設為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。  
	- 格式 **type** 設為 **TextFormat**
	- 文字檔中有兩個欄位 (**FirstName** 和 **LastName**)，以逗號字元分隔 (**columnDelimiter**)	
	- **availability** 設為 **hourly** (**frequency** 設為 **hour** 且 **interval** 設為 **1**)，因此 Data Factory 服務會每隔一小時在您指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。 
	

	如果您未指定輸入資料表的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱此[教學課程][adf-tutorial]中的範例檔案。
 
	如果您未指定輸出資料表的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：Data.&lt;Guid&gt;.txt (範例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

	若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

2. 按一下工具列上的 [部署]，建立和部署 **EmpTableFromBlob** 資料表。確認您在編輯器的標題列看到 [已成功建立資料表] 訊息。

### 建立輸出資料表
在這部分的步驟中，您將在 **AzureSqlLinkedService** 連結服務所代表的 Azure SQL Database 中，建立指向 SQL 資料表的 **EmpSQLTable** 輸出資料表。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增資料集] 按鈕，然後按下拉式功能表中的 [Azure SQL 資料表]。 
2. 將右窗格中的 JSON 替換為以下 JSON 片段：

        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSqlTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     請注意：
	
	* 位置 **type** 設為 **AzureSQLTableLocation**。
	* **linkedServiceName** 設為 **AzureSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	* **tablename** 設為 **emp**。
	* 資料庫的 emp 資料表中有三個資料行 (**ID**、**FirstName** 和 **LastName**)，但 ID 是身分識別資料行，所以您在此處只需要指定 **FirstName** 和 **LastName**。
	* **availability** 設為 **hourly** (**frequency** 設為 **hour** 且 **interval** 設為 **1**)。Data Factory 服務會每隔一小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。


3. 按一下工具列上的 [部署]，建立並部署 **EmpSQLTable** 資料表。


## <a name="CreateAndRunAPipeline"></a>步驟 4：建立和執行管線
在此步驟中，您會建立管線，其中含有使用 **EmpTableFromBlob** 做為輸入和使用 **EmpSQLTable** 做為輸出的複製活動。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增管線] 按鈕。如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。或者，您也可以在樹狀檢視中，以滑鼠右鍵按一下 [**管線**]，再按一下 [**新增管線**]。

	![編輯器 [新增管線] 按鈕][image-editor-newpipeline-button]
 
2. 將右窗格中的 JSON 替換為以下 JSON 片段：

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	請注意：

	- 在活動區段中，只會有一個活動的 **type** 設為 **CopyActivity**。
	- 活動的輸入設為 **EmpTableFromBlob**，活動的輸出則設為 **EmpSQLTable**。
	- 在 **transformation** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。

	將 **start** 屬性的值替換為目前日期，並將 **end** 值替換為隔天的日期。在日期時間中，您只指定日期部分，並略過時間部分。例如，"2015-02-03"，這相當於 "2015-02-03T00:00:00Z"
	
	開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

4. 按一下工具列上的 [部署]，建立並部署 **ADFTutorialPipeline**。確認您看到 [已成功建立管線] 訊息。
5. 現在，按一下 **X** 關閉 [編輯器] 刀鋒視窗。再按一次 **X**，關閉內含工具列和樹狀檢視的 [ADFTutorialDataFactory] 刀鋒視窗。如果您看到 [將捨棄未儲存的編輯] 訊息，請按一下 [確定]。
6. 您應該會回到 **ADFTutorialDataFactory** 的 [DATA FACTORY] 刀鋒視窗。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。
 
### 在圖表檢視中檢視 Data Factory 
1. 在 [DATA FACTORY] 刀鋒視窗中，按一下 [圖表]。

	![Data Factory 刀鋒視窗：圖表磚][image-datafactoryblade-diagramtile]

2. 您應該會看到如下所示的圖表：

	![圖表檢視][image-data-factory-get-started-diagram-blade]

	您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。 
3. 在圖表檢視中，於 **ADFTutorialPipeline** 上按一下滑鼠右鍵，然後按一下 [開啟管線]。您應該會看到管線中的活動，以及活動的輸入和輸出資料集。在本教學課程中，管線中只能有一個活動 (複製活動) 將 EmpTableBlob 做為輸入資料集，並將 EmpSQLTable 做為輸出資料集。   

	![開啟管線](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 按一下左上角階層連結中的 [Data Factory]，回到圖表檢視。圖表檢視會顯示所有管線。在此範例中，您只建立了一個管線。
 

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5：監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需關於使用 Cmdlet 進行監視的詳細資料，請參閱[使用 PowerShell Cmdlet 監視和管理 Data Factory][monitor-manage-using-powershell]。

1. 如果尚未開啟 [Azure 入口網站 (預覽)][azure-preview-portal]，請瀏覽至該網站。 
2. 如果 **ADFTutorialDataFactory** 的刀鋒視窗未開啟，請按一下 [開始面板] 上的 [ADFTutorialDataFactory] 予以開啟。 
3. 您應該會看到您在此分頁上建立之資料表和管線的計數和名稱。

	![含名稱的首頁][image-data-factory-get-started-home-page-pipeline-tables]

4. 現在，按一下 [資料集] 磚。
5. 在 [資料集] 刀鋒視窗中，按一下 [EmpTableFromBlob]。這是 **ADFTutorialPipeline** 的輸入資料表。

	![已選取 EmpTableFromBlob 的資料集][image-data-factory-get-started-datasets-emptable-selected]   
5. 請注意，截至目前為止的資料配量都已產生，並已「就緒」，因為 **emp.txt** 檔案一直都存在於 Blob 容器中：**adftutorial\input**。確認下方的 [最近失敗的配量] 區段中沒有任何配量。

	[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更。
    

	-  您以手動方式更新配量的狀態，例如，使用 **Set-AzureDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
	-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。
 
	按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。
	
	若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。

	![資料配量 (依配量時間)][DataSlicesBySliceTime]

6. 現在，在 [資料集] 刀鋒視窗中，按一下 [EmpSQLTable]。這是 **ADFTutorialPipeline** 的輸出資料表。

	![資料集刀鋒視窗][image-data-factory-get-started-datasets-blade]



	 
6. 您應該會看到 **EmpSQLTable** 刀鋒視窗，如下所示：

	![資料表刀鋒視窗][image-data-factory-get-started-table-blade]
 
7. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [就緒]。底部的 [問題配量] 區段中沒有顯示任何配量。
8. 按一下 **…** (省略符號) 檢視所有配量。

	![資料配量刀鋒視窗][image-data-factory-get-started-dataslices-blade]

9. 按一下清單中的任何資料配量，您應該會看到 [資料配量] 刀鋒視窗。

	![資料配量刀鋒視窗][image-data-factory-get-started-dataslice-blade]
  
	如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

11. 在 [資料配量] 刀鋒視窗中，您應該會看到底部清單中的所有活動執行。按一下 [活動執行] 查看 [活動執行詳細資料] 刀鋒視窗。

	![活動執行詳細資料][image-data-factory-get-started-activity-run-details]

	
12. 按一下 **X** 關閉所有刀鋒視窗，直到您回到 **ADFTutorialDataFactory** 的起始刀鋒視窗。
14. (選擇性) 在 **ADFTutorialDataFactory** 的起始刀鋒視窗上按一下 [管線]，再按一下 [管線] 刀鋒視窗中的 **ADFTutorialPipeline**，然後深入檢視輸入資料表 (**已取用**) 或輸出資料表 (**已產生**)。
15. 啟動 **SQL Server Management Studio**，並連接到 Azure SQL Database，然後確認資料列已插入資料庫的 **emp** 資料表中。

	![SQL 查詢結果][image-data-factory-get-started-sql-query-results]


## 摘要 
在本教學課程中，您已建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。您已使用 Azure Preview 入口網站建立 Data Factory、連結服務、資料表和管線。以下是您在本教學課程中執行的高階步驟：

1.	建立 Azure **Data Factory**。
2.	建立將資料存放區和計算連結到 Data Factory 的連結服務 (稱為連結服務)。
3.	建立描述管線輸入資料和輸出資料的資料表。
4.	建立管線。管線由一或多個活動所組成，會處理輸入並產生輸出。指定管線的 [開始] 和 [結束] 時間，以設定管線的作用期間。作用中期間會定義將要產生資料配量的持續時間。 


如需所支援活動的清單，請參閱[管線和活動][msdn-activities]主題，如需所支援連結服務的清單，請參閱 MSDN Library 上的[連結服務][msdn-linkedservices]主題。
 
若要使用 Azure PowerShell 進行本教學課程，請參閱[使用 Azure PowerShell 建立和監視 Data Factory][monitor-manage-using-powershell]。

## 後續步驟

文章 | 說明
------ | ---------------
[使用 Azure Data Factory - 複製活動來複製資料][copy-activity] | 本文提供您在本教學課程中使用的複製活動詳細說明。 
[讓您的管線使用內部部署資料][use-onpremises-datasources] | 本文的逐步解說示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。 
[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，示範如何使用 Azure Data Factory 實作真實案例，藉此將記錄檔中的資料轉換成深入資訊。
[對 Data Factory 問題進行疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行疑難排解。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 Cmdlet、JSON 指令碼、函數等完整參考內容。 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 

<!---HONumber=62-->