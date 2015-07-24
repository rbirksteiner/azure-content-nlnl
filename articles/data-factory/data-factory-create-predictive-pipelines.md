<properties 
	pageTitle="Data Factory - 使用 Data Factory 和 Machine Learning 來建立預測管線 | Azure" 
	description="說明如何使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線。" 
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
	ms.date="06/09/2015" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 和 Azure Machine Learning 來建立預測管線 
## 概觀
您可以在 Azure Data Factory 管線內將已發佈的 [Azure Machine Learning][azure-machine-learning] 模型實作化。這些管線稱為預測管線。若要建立預測管線，您將需要：

-	已發佈之工作區模型的「API 金鑰」和批次評分 URL (請參閱下圖)。
-	一個 Azure Blob 儲存體，用來存放輸入 CSV 檔案 (或) 含有要評分之輸入資料的 Azure SQL Database。 
-	一個 Azure Blob 儲存體，將存放評分結果 CSV 檔案 (或) 存放輸出資料的 Azure SQL Database。 

	![機器學習服務儀表板][machine-learning-dashboard]

	請依上圖所示取得 AzureMLLinkedService 的批次評分 URL，但要去掉 ‘**help**’：https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

**預測管線**包含下列部分：

-	輸入和輸出資料表
-	Azure 儲存體/Azure SQL 和 Azure ML 連結服務
-	具有「Azure ML 批次評分活動」的管線

> [AZURE.NOTE]您可以使用 Azure Data Factory (ADF) 管線中已發佈 Azure Machine Learning Web 服務所公開的 Web 服務參數。如需詳細資訊，請參閱本文中的＜Web 服務參數＞一節。

## 範例
此範例使用 Azure 儲存體來存放輸入和輸出資料。您也可以使用 Azure SQL Database，不用 Azure 儲存體。

建議您在瀏覽此範例之前，先瀏覽[開始使用 Azure Data Factory][adf-getstarted]教學課程，並在此範例中使用 Data Factory 編輯器建立 Data Factory 成品 (連結服務、資料表、管線)。
 

1. 為您的 Azure 儲存體建立連結的服務如果評分輸入和輸出檔案會在不同的儲存體帳戶中，您將需要兩個連結的服務。以下是 JSON 範例：

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 建立輸入和輸出 Azure Data Factory 資料表。請注意，與其他 Data Factory 資料表不同的是，這些資料表必須同時包含 **folderPath** 和 **fileName** 值。您可以使用資料分割，讓每個批次執行 (每一個資料配量) 處理或產生唯一的輸入和輸出檔案。您將可能需要包含某個上游活動，以將輸入轉換成 CSV 檔案格式，並將它放在每個配量的儲存體帳戶中。在該情況下，您將不會包含下面範例所示的 “waitOnExternal” 設定，而您的 ScoringInputBlob 將會是不同「活動」的輸出資料表。

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}
	
	您的批次評分 csv 檔案必須有資料行標頭資料列。如果使用 [複製活動] 建立 csv 或將其移至 Blob 儲存體，則接收屬性 **blobWriterAddHeader** 應該設為 **true**。例如：
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	如果 csv 檔案沒有標頭資料列，您可能會看到下列錯誤：**活動中的錯誤：讀取字串時發生錯誤。非預期的權杖：StartObject。路徑 ''、行 1、位置 1**。
3. 此輸出範例使用資料分割來為每一個配量執行建立一個唯一輸出路徑。如果沒有此唯一輸出路徑，活動就會覆寫檔案。

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. 建立下列類型的連結服務：**AzureMLLinkedService**，並提供 API 金鑰和模型批次評分 URL。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 最後，撰寫一個包含 **AzureMLBatchScoringActivity** 的管線。它將會從輸入資料表取得輸入檔案的位置、呼叫 AzureML 批次評分 API，然後將批次評分輸出複製到輸出資料表中指定的 Blob。不同於一些其他 Data Factory 活動，AzureMLBatchScoringActivity 只能有一個輸入和一個輸出資料表。

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}


## Web 服務參數
您可以使用 Azure Data Factory (ADF) 管線中已發佈 Azure Machine Learning Web 服務所公開的 Web 服務參數。您可以在 Azure Machine Learning 中建立實驗，並將它發佈為 Web 服務，然後透過 Web 服務參數傳入不同的輸入，以在多個 ADF 管線或活動中使用該 Web 服務。

### 傳遞 Web 服務參數的值
將 **transformation** 區段加入管線 JSON 中的 **AzureMLBatchScoringActivty** 區段，以指定該區段中 Web 服務參數的值，如下列範例所示：

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


您也可以使用 [Data Factory 函式](https://msdn.microsoft.com/library/dn835056.aspx)傳遞 Web 服務參數的值，如下列範例所示：

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web 服務參數區分大小寫，因此，請確定您在活動 JSON 中所指定的名稱符合 Web 服務所公開的名稱。

### Azure SQL 讀取器和寫入器
使用 Azure SQL 讀取器和寫入器，是常見的 Web 服務參數使用案例。讀取器模組將資料從 Azure Machine Learning Studio 外部的資料管理服務載入至實驗，而寫入器模組則是將資料從實驗儲存至 Azure Machine Learning Studio 外部的資料管理服務。如需 Azure Blob/Azure SQL 讀取器/寫入器的詳細資料，請參閱 MSDN Library 上的[讀取器](https://msdn.microsoft.com/library/azure/dn905997.aspx)和[寫入器](https://msdn.microsoft.com/library/azure/dn905984.aspx)主題。上一節中的範例已使用 Azure Blob 讀取器與 Azure Blob 寫入器。本節討論如何使用 Azure SQL 讀取器和 Azure SQL 寫入器。

#### Azure SQL 讀取器
在 Azure ML Studio 中，您可以建置實驗，並發佈使用 Azure SQL 讀取器進行輸入的 Web 服務。Azure SQL 讀取器的連線屬性可以公開為 Web 服務參數，允許在批次評分要求中於執行階段傳遞連線屬性的值。

在執行階段，Data Factory 服務將使用輸入 Data Factory 資料表中的詳細資料來填入 Web 服務參數。請注意，您必須使用 Web 服務參數的預設名稱 (資料庫伺服器名稱、資料庫名稱、伺服器使用者帳戶名稱、伺服器使用者帳戶密碼)，才能與 Data Factory 服務整合。

如有任何其他 Web 服務參數，請使用活動 JSON 的 **webServiceParameters** 區段。如果您在此區段指定 Azure SQL 讀取器參數的值，則這些值會覆寫從輸入 Azure SQL 連結服務中挑選的值。不建議您直接在 webServiceParameters 區段中指定 Azure SQL 讀取器的值。請使用該區段傳遞任何其他參數值。

若要透過 Azure Data Factory 管線使用 Azure SQL 讀取器，請執行下列步驟：

- 建立 **Azure SQL 連結服務**。 
- 建立使用 **AzureSqlTableLocation** 的 Data Factory **資料表**。
- 將該 Data Factory **資料表**設定為管線 JSON 中 **AzureMLBatchScoringActivity** 的**輸入**。 



#### Azure SQL 寫入器
與 Azure SQL 讀取器相同，Azure SQL 寫入器的屬性也可以公開為 Web 服務參數。Azure SQL 寫入器使用與輸入資料表或輸出資料表相關聯之連結服務中的設定。下表描述何時使用輸入連結服務與輸出連結服務。

<table>
<tr>
<td>輸出/輸入</td>
<td><b>輸入是 Azure SQL</b></td>
<td><b>輸入是 Azure Blob</b></td>
</tr>
<tr>
<td><b>輸出是 Azure SQL</b></td>
<td><p>Data Factory 服務會使用 INPUT 連結服務中的連接字串資訊，產生具有下列名稱的 Web 服務參數：「資料庫伺服器名稱」、「資料庫名稱」、「伺服器使用者帳戶名稱」、「伺服器使用者帳戶密碼」。請注意，在 Azure ML Studio 中必須使用這些 Web 服務參數的預設名稱。</p>
<p>如果 Azure ML 模型中的 Azure SQL 讀取器和 Azure SQL 寫入器共用上述的相同 Web 服務參數，則是正確的。如果它們未共用相同的 Web 服務參數 (例如，如果 Azure SQL 寫入器使用參數名稱：資料庫伺服器名稱1、資料庫名稱1、伺服器使用者帳戶名稱1、伺服器使用者帳戶密碼1 (結尾為 '1' ))，您必須在活動 JSON 的 webServiceParameters 區段中傳遞這些 OUTPUT Web 服務參數的值。</p>
<p>
您可以使用活動 JSON 的 webServiceParameters 區段來傳遞任何其他 Web 服務參數的值。 
</p>

</td>
<td>
<p>Data Factory 服務會使用 OUTPUT 連結服務中的連接字串資訊，產生具有下列名稱的 Web 服務參數：「資料庫伺服器名稱」、「資料庫名稱」、「伺服器使用者帳戶名稱」、「伺服器使用者帳戶密碼」。請注意，在 Azure ML Studio 中必須使用這些 Web 服務參數的預設名稱。</p>
<p>您可以使用活動 JSON 的 webServiceParameters 區段來傳遞任何其他 Web 服務參數的值。<p>輸入 Blob 會用做輸入位置。</p>
</td>
</tr>
<tr>
<td><b>輸出是 Azure Blob</b></td>
<td>Data Factory 服務會使用 INPUT 連結服務中的連接字串資訊，產生具有下列名稱的 Web 服務參數：「資料庫伺服器名稱」、「資料庫名稱」、「伺服器使用者帳戶名稱」、「伺服器使用者帳戶密碼」。請注意，在 Azure ML Studio 中必須使用這些 Web 服務參數的預設名稱。
</td>
<td>
<p>您必須使用活動 JSON 的 WebServiceParameters 區段來傳遞任何 Web 服務參數的值。</p> 

<p>Blob 會用做輸入和輸出位置。</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Azure SQL 寫入器可能會在覆寫身分識別資料行時發生索引鍵違規。您應該確定輸出資料表的結構，以避免這種情況。
> 
> 您可以搭配使用暫存資料表與預存程序活動，來合併資料列，或在評分前截斷資料。如果您使用這種方法，請將 executionPolicy 的並行設定設為 1。

### 使用 Web 服務參數的範例
#### 使用 Web 服務參數且含 AzureMLBatchScoringActivity 的管線

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	],

			"start": "2015-02-13T00:00:00Z",
        	"end": "2015-02-14T00:00:00Z"
		}
	}
 
在上述 JSON 範例中：

- Azure ML 模型同時使用 Azure SQL 讀取器和 Azure SQL 寫入器
- 透過 Web 服務公開時，參數會使用預設名稱
	- **讀取器**為：資料庫伺服器名稱、資料庫名稱、伺服器使用者帳戶名稱和伺服器使用者帳戶密碼。
	- **寫入器**為：資料庫伺服器名稱1、資料庫名稱1、伺服器使用者帳戶名稱1 和伺服器使用者帳戶密碼1。
	
		請注意，在此情況下，讀取器和寫入器不會共用參數。  
- Data Factory 服務會自動產生具有下列名稱的 Web 服務參數值：**資料庫伺服器名稱**、**資料庫名稱**、**伺服器使用者帳戶名稱**和**伺服器使用者帳戶密碼**，與輸入讀取器的名稱相符合。因此，您不需要透過下面活動 JSON 中的 **webServiceParameters** 明確傳遞這些參數的值。  
- Data Factory 服務不會自動填入寫入器的參數 (尾碼為 '1' 的參數)。因此，您需要在活動 JSON 的 **webServiceParameters** 區段中指定這些參數的值。  
- **客戶識別碼**、**評分標籤**和**評分機率**會儲存為以逗號分隔的資料行。 
- 在此範例中，**資料表名稱**對應到輸出資料庫中的資料表。
- **開始**和**結束**日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](https://msdn.microsoft.com/library/dn835050.aspx)。




## 另請參閱

文章 | 說明
------ | ---------------
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 Cmdlet、JSON 指令碼、.NET 類別庫、函式等完整參考內容。 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=62-->