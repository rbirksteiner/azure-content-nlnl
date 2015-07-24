<properties 
	pageTitle="教學課程：使用 Azure PowerShell 建立和監視 Azure Data Factory" 
	description="了解如何使用 Azure PowerShell 建立和監視 Azure Data Factory。" 
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

# 教學課程：使用 Azure PowerShell 建立和監視 Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

[開始使用 Azure Data Factory][adf-get-started] 教學課程示範如何使用 [Azure Preview 入口網站][azure-preview-portal]建立和監視 Azure Data Factory。在本教學課程中，您將使用 Azure PowerShell Cmdlet 來建立和監視 Azure Data Factory。在本教學課程中，您於 Data Factory 中建立的管線會將資料從 Azure Blob 複製到 Azure SQL Database。

> [AZURE.NOTE]這篇文章並未涵蓋所有的 Data Factory Cmdlet。如需 Data Factory Cmdlet 的完整文件，請參閱 [Data Factory Cmdlet 參考][cmdlet-reference]。

##必要條件
除了「教學課程概觀」主題中所列的必要條件，您還需要在電腦上安裝 Azure PowerShell。如果還沒有安裝，請在電腦上下載並安裝 [Azure PowerShell][download-azure-powershell]。

##本教學課程內容
下表列出您在本教學課程中將執行的步驟及說明。

步驟 | 說明
-----| -----------
[步驟 1：建立 Azure Data Factory](#CreateDataFactory) | 在此步驟中，您將建立名為 **ADFTutorialDataFactoryPSH** 的 Azure Data Factory。 
[步驟 2：建立連結服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 會連結 Azure 儲存體，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 ADFTutorialDataFactoryPSH。
[步驟 3：建立輸入和輸出資料集](#CreateInputAndOutputDataSets) | 在此步驟中，您將定義兩個資料集 (**EmpTableFromBlob** 和 **EmpSQLTable**)，當您在下一個步驟中建立 ADFTutorialPipeline 時，可將這兩個資料集做為複製活動的輸入和輸出資料集。
[步驟 4：建立和執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將在 Data Factory **ADFTutorialDataFactoryPSH** 中建立名為 **ADFTutorialPipeline** 的管線。此管線的複製活動會將資料從 Azure Blob 複製到輸出 Azure 資料庫資料表。
[步驟 5：監視資料集和管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將使用 Azure PowerShell 來監視資料集和管線。

## <a name="CreateDataFactory"></a>步驟 1：建立 Azure Data Factory
在此步驟中，您會使用 Azure PowerShell 建立名為 **ADFTutorialDataFactoryPSH** 的 Azure Data Factory。

1. 啟動 **Azure PowerShell** 並執行下列命令。將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。如果關閉後再重新開啟，則需要重新執行這些命令。
	- 執行 **Add-AzureAccount**，並輸入您用來登入 Azure Preview 入口網站的使用者名稱和密碼。  
	- 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	- 執行 **Select-AzureSubscription** 以選取您想要使用的訂用帳戶。此訂用帳戶應該與您在「Azure Preview 入口網站」中使用的相同。 
2. 切換至 **AzureResourceManager** 模式，因為此模式中可以使用 Azure Data Factory Cmdlet。

		Switch-AzureMode AzureResourceManager 
3. 執行以下命令，建立名為 **ADFTutorialResourceGroup** 的 Azure 資源群組。
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	本教學課程的某些步驟會假設您使用名為 **ADFTutorialResourceGroup** 的資源群組。如果使用不同的資源群組，您必須以該群組取代本教學課程中的 ADFTutorialResourceGroup。 
4. 執行 **New-AzureDataFactory** Cmdlet，建立名為 **ADFTutorialDataFactoryPSH** 的 Data Factory。  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：「Data Factory 名稱 “ADFTutorialDataFactoryPSH” 無法使用」，請變更名稱 (例如 yournameADFTutorialDataFactoryPSH)。執行本教學課程中的步驟時，請使用此名稱來取代 ADFTutorialFactoryPSH。

## <a name="CreateLinkedServices"></a>步驟 2：建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或內部部署 SQL Server 資料庫，且內含 Data Factory 管線的輸入資料或儲存其輸出資料。計算服務是處理輸入資料並產生輸出資料的服務。

在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結服務會連結 Azure 儲存體帳戶，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactoryPSH**。您稍後將在本教學課程中建立管線，以將資料從 StorageLinkedService 中的 Blob 容器複製到 AzureSqlLinkedService 中的 SQL 資料表。

### 建立 Azure 儲存體帳戶的連結服務
1.	在 **C:\ADFGetStartedPSH** 中，使用以下內容建立名為 **StorageLinkedService.json** 的 JSON 檔案。建立 ADFGetStartedPSH 資料夾 (如果不存在)。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	在 **Azure PowerShell** 中，切換到 **ADFGetStartedPSH** 資料夾。 
3.	您可以使用 **New-AzureDataFactoryLinkedService** Cmdlet 建立連結服務。此 Cmdlet 和您在本教學課程中使用的其他 Data Factory Cmdlet，皆需要您將值傳給 **ResourceGroupName** 和 **DataFactoryName** 參數。或者，您可以使用 **Get-AzureDataFactory** 取得 DataFactory 物件，並傳遞此物件，就不需要在每次執行 Cmdlet 時輸入 ResourceGroupName 和 DataFactoryName。執行以下命令，將 **Get-AzureDataFactory** Cmdlet 的輸出指派給變數：**$df**。 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	現在，執行 **New-AzureDataFactoryLinkedService** Cmdlet 建立連結服務：**StorageLinkedService**。

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	如果您還沒執行 **Get-AzureDataFactory** Cmdlet 和指派輸出給 **$df** 變數，您就必須指定 ResourceGroupName 和 DataFactoryName 參數的值，如下所示。
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	如果您在教學課程的中途關閉 Azure PowerShell，則下次啟動 Azure PowerShell 時必須執行 Get-AzureDataFactory Cmdlet，才能完成教學課程。

### 建立 Azure SQL Database 的連結服務
1.	使用以下內容建立名為 AzureSqlLinkedService.json 的 JSON 檔案。

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	執行以下命令建立連結服務。 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	確認 Azure SQL Server 已開啟 [允許存取 Azure 服務] 設定。若要確認並開啟此設定，請執行下列動作：

	1. 按一下左邊的 [**瀏覽**] 中樞，然後按一下 [**SQL Server**]。
	2. 選取您的伺服器，然後按一下 [SQL SERVER] 刀鋒視窗上的 [**設定**]。
	3. 在 [**設定**] 刀鋒視窗中，按一下 [**防火牆**]。
	4. 在 [防火牆設定] 刀鋒視窗中，按一下 [允許存取 Azure 服務] 的 [開啟]。
	5. 按一下左邊的 [作用中] 中樞，切換至原先的 [Data Factory] 刀鋒視窗。
	

## <a name="CreateInputAndOutputDataSets"></a>步驟 3：建立輸入和輸出資料表

在上一個步驟中，您已建立連結服務 **StorageLinkedService** 和 **AzureSqlLinkedService**，將 Azure 儲存體帳戶和 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactoryPSH**。在此步驟中，您將建立資料表，這些資料表在下一個步驟所建立的管線中，會代表複製活動的輸入和輸出資料。

資料表是矩形的資料集，而且是目前唯一受支援的資料集類型。本教學課程中的輸入資料表是指 StorageLinkedService 所指向之 Azure 儲存體中的 Blob 容器，輸出資料表則是指 AzureSqlLinkedService 所指向之 Azure SQL Database 中的 SQL 資料表。

### 準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫
如果您已完成[開始使用 Azure Data Factory][adf-get-started] 文章中的教學課程，請略過此步驟。

您需要執行下列步驟來準備本教學課程中的 Azure Blob 儲存體和 Azure SQL Database。
 
* 在 **StorageLinkedService** 所指向的 Azure Blob 儲存體中，建立名為 **adftutorial** 的 Blob 容器。 
* 建立文字檔 **emp.txt**，並以 Blob 形式上傳至 **adftutorial** 容器。 
* 在 **AzureSqlLinkedService** 所指向的 Azure SQL Database中，建立名為 **emp** 的資料表。


1. 啟動 [記事本]，貼上以下文字，並命名為 **emp.txt**，然後儲存至您硬碟上的 **C:\ADFGetStartedPSH** 資料夾。 

        John, Doe
		Jane, Doe
				
2. 使用 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)這類的工具建立 **adftutorial** 容器，以及將 **emp.txt** 檔案上傳至該容器。

    ![Azure 儲存體總管][image-data-factory-get-started-storage-explorer]
3. 使用以下 SQL 指令碼，在您的 Azure SQL Database 中建立 **emp** 資料表。  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	如果您的電腦上已安裝 SQL Server 2014：請遵循[步驟 2：使用 SQL Server Management Studio 連接到管理 Azure SQL Database 的 SQL Database][sql-management-studio] 文章中的指示，連接到您的 Azure SQL Server，並執行 SQL 指令碼。

	如果您的電腦上已安裝 Visual Studio 2013：請在 Azure Preview 入口網站中 ([http://portal.azure.com](http://portal.sazure.com))，依序按一下左側的 [瀏覽] 中樞、[SQL Server]，選取您的資料庫，然後按一下工具列上的 [在 Visual Studio 中開啟] 按鈕，以連接到您的 Azure SQL Server 並執行指令碼。如果不允許您的用戶端存取 Azure SQL Server，則必須將 Azure SQL Server 的防火牆設定成允許從您的電腦 (IP 位址) 存取。請參閱上文的步驟，設定 Azure SQL Server 的防火牆。
		
### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您將在 **StorageLinkedService** 連結服務所代表的 Azure 儲存體中，建立指向 Blob 容器的 **EmpBlobTable** 資料表。此 Blob 容器 (**adftutorial**) 包含的輸入資料位於 **emp.txt** 檔案中。

1.	在 **C:\ADFGetStartedPSH** 資料夾中，使用下列內容建立名為 **EmpBlobTable.json** 的 JSON 檔案：

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
	                "waitonexternal": {}
	                }
	        }
		}
	
	請注意：
	
	- 位置 **type** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService**。 
	- **folderPath** 設為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。  
	- 格式 **type** 設為 **TextFormat**
	- 文字檔中有兩個欄位 (**FirstName** 和 **LastName**)，以逗號字元分隔 (**columnDelimiter**)	
	- **availability** 設為 **hourly** (**frequency** 設為 **hour** 且 **interval** 設為 **1**)，因此 Data Factory 服務會每隔一小時在您指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。

	如果您未指定輸入資料表的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱此[教學課程][adf-tutorial]中的範例檔案。
 
	如果您未指定輸出資料表的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：Data.<Guid>.txt (範例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

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

2.	執行以下命令建立 Data Factory 資料表。

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### 建立輸出資料表
在這部分的步驟中，您將在 **AzureSqlLinkedService** 連結服務所代表的 Azure SQL Database中，建立指向 SQL 資料表 (**emp**) 的輸出資料表 **EmpSQLTable**。管線會將輸入 Blob 中的資料複製到 **emp** 資料表。

1.	在 **C:\ADFGetStartedPSH** 資料夾中，使用下列內容建立名為 **EmpSQLTable.json** 的 JSON 檔案。
		
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
		            "type": "AzureSQLTableLocation",
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
	* **linkedServiceName** 設為 **AzureSqlLinkedService**。
	* **tablename** 設為 **emp**。
	* 資料庫的 emp 資料表中有三個資料行 (**ID**、**FirstName** 和 **LastName**)，但 ID 是身分識別資料行，所以您在此處只需要指定 **FirstName** 和 **LastName**。
	* **availability** 設為 **hourly** (**frequency** 設為 **hour** 且 **interval** 設為 **1**)。Data Factory 服務會每小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。

2.	執行以下命令建立 Data Factory 資料表。
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>步驟 4：建立和執行管線
在此步驟中，您會建立管線，其中含有使用 **EmpTableFromBlob** 做為輸入和使用 **EmpSQLTable** 做為輸出的複製活動。

1.	在 **C:\ADFGetStartedPSH** 資料夾中，使用下列內容建立名為 **ADFTutorialPipeline.json** 的 JSON 檔案。 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
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
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	請注意：

	- 在活動區段中，只會有一個活動的 **type** 設為 **CopyActivity**。
	- 活動的輸入設為 **EmpTableFromBlob**，活動的輸出則設為 **EmpSQLTable**。
	- 在 **transformation** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。

	將 **start** 屬性的值取代為目前日期，並將 **end** 值取代為隔天的日期。開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9/9/9999** 做為 **end** 屬性的值。
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。
2.	執行以下命令建立 Data Factory 資料表。 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5：監視資料集和管線
在此步驟中，您將使用 Azure PowerShell 來監視 Azure Data Factory 的運作情形。

1.	執行 **Get-AzureDataFactory** 並指派輸出給變數 $df。

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	執行 **Get-AzureDataFactorySlice**，以取得 **EmpSQLTable** (管線的輸出資料表) 所有配量的詳細資料。

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	以目前的年、月、日，取代 **StartDateTime** 參數的年、月、日部分。這應該要符合管線 JSON 中的 **Start** 值。

	您應該會看到 24 個配量，從今天 12 AM 到隔天 12 AM，每小時各一個。
	
	第一個配量：

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	最後一個配量：

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	執行 **Get-AzureDataFactoryRun**，以取得特定配量的活動執行詳細資料。變更 **StartDateTime** 參數的值，以符合上述輸出中配量的 **Start** 時間。**StartDateTime** 的值必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-03-03T22:00:00Z。

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	您應該會看到如下所示的輸出：

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

如需 Data Factory Cmdlet 的完整文件，請參閱 [Data Factory Cmdlet 參考][cmdlet-reference]。

## 後續步驟

文章 | 說明
------ | ---------------
[使用 Azure Data Factory - 複製活動來複製資料][copy-activity] | 本文提供您在本教學課程中使用的複製活動詳細說明。 
[讓您的管線使用內部部署資料][use-onpremises-datasources] | 本文的逐步解說示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。 
[搭配 Data Factory 使用 Pig 和 Hive][use-pig-and-hive-with-data-factory] | 本文的逐步解說示範如何使用 HDInsight 活動執行 **hive/pig** 指令碼，以處理輸入資料並產生輸出資料。
[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，示範如何使用 Azure Data Factory 實作真實案例，藉此將記錄檔中的資料轉換成深入資訊。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，以逐步指示說明如何建立自訂活動，並將此活動用於管線中。 
[對 Data Factory 問題進行疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行疑難排解。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 此參考內容提供所有 **Data Factory Cmdlet** 的詳細資料。
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 Cmdlet、JSON 指令碼、函數等完整參考內容。 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2
 

<!---HONumber=62-->