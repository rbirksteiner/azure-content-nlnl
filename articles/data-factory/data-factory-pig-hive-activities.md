<properties 
	pageTitle="使用 Pig 和 Hive 搭配 Azure Data Factory" 
	description="了解如何從 Azure Data Factory 在 Azure HDInsight 叢集執行 Pig 和 Hive 指令碼來處理資料。" 
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
	ms.date="06/19/2015" 
	ms.author="spelluru"/>

# 使用 Pig 和 Hive 搭配 Data Factory
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明在 Azure Data Factory 管線中搭配 Pig/Hive 轉換使用 HDInsight 活動。如需關於在 HDInsight 叢集上從 Azure Data Factory 管線執行 MapReduce 程式的詳細資訊，請參閱[從 Data Factory 叫用 MapReduce 程式][data-factory-map-reduce]。

## 逐步解說：搭配 Azure Data Factory 使用 Hive
本逐步解說提供在 Azure Data Factory 管線中搭配 Hive 轉換使用 HDInsight 活動的逐步指示。

### 必要條件
1. 完成[開始使用 Azure Data Factory][adfgetstarted] 文章中的教學課程。
2. 在 **C:\ADFGetStarted** 下名為 **Hive** 的子資料夾中，使用下列內容建立 **hivequery.hql** 檔案。
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]若要在 HQL 檔案中使用 **Tez** 引擎執行 Hive 查詢，請在檔案頂端加入 "**set hive.execution.engine=tez**;"。
		
3.  將 **hivequery.hql** 上傳至 Blob 儲存體中的 **adftutorial** 容器


### 逐步介紹

#### 建立輸出資料表
        
1. 在 [Data Factory 編輯器] 中，按一下 [新增資料集]，然後按一下命令列中的 [Azure Blob 儲存體]。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON 指令碼：

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 按一下命令列上的 [部署]，部署資料表。


### 建立 HDInsight 叢集的連結服務
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。隨選的 HDInsight 叢集會自動建立，並由處理資料的 Azure Data Factory 服務所管理。請參閱 [HDInishgt 隨選連結服務](https://msdn.microsoft.com/library/dn893526.aspx)，以了解隨選 HDInsight 連結服務的詳細資料。基於範例的目的，讓我們使用隨選叢集。請注意，系統花費了 15 分鐘以上的時間來建立隨選的 HDInsight 叢集，但只會針對 HDInsight 叢集啟動並執行工作的時間來向您收取費用。

#### 若要使用隨選 HDInsight 叢集
1. 按一下命令列中的 [新增計算]，然後選取功能表中的 [隨選 HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作： 
	1. 在 **clusterSize** 屬性中，指定 HDInsight 叢集的大小。
	2. 在 **jobsContainer** 屬性中，指定要儲存叢集記錄檔的預設容器名稱。在本教學課程中，請指定 **adfjobscontainer**。
	3. 在 **timeToLive** 屬性中，指定叢集閒置多久之後會被刪除。 
	4. 在 **version** 屬性中，指定您要使用的 HDInsight 版本。如果您排除此屬性，則會使用最新版本。  
	5. 在 **linkedServiceName** 中，指定您在「開始使用」教學課程中建立的 **StorageLinkedService**。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 按一下命令列上的 [部署]，部署連結服務。
   
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 按一下命令列中的 [新增計算]，然後選取功能表中的 [HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作： 
	1. 在 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如：https://<clustername>.azurehdinsight.net/     
	2. 在 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
	3. 在 **Password** 屬性中，輸入使用者的密碼。 
	4. 在 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您在「開始使用」教學課程中建立的連結服務。 

2. 按一下命令列上的 [部署]，部署連結服務。

### 建立和排程管線
   
1. 按一下命令列上的 [新增管線]。如果看不到此命令，請按一下 **...** (省略符號) 就可看到。 
2. 使用下列 JSON 指令碼取代右窗格中的 JSON。如果您想要使用自己的叢集，且已遵循步驟建立 **HDInsightLinkedService** 連結服務，請在下列 JSON 中以 **HDInsightLinkedService** 取代 **HDInsightOnDemandLinkedService**。 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]以目前日期的前三天取代 **StartDateTime** 值，並以目前日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。將輸出資料表排定為每天產生，因此將產生三個配量。
	> 
	> 使用您的儲存體帳戶名稱取代 JSON 中的 **your storage account**。
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。
2. 按一下命令列上的 [部署]，部署管線。
4. 請參閱 [開始使用 Data Factory][adfgetstarted] 文章的[監視資料集和管線][adfgetstartedmonitoring]一節。 

	> [AZURE.NOTE]在輸出資料表配量的 [活動執行詳細資料] 刀鋒視窗中 (在入口網站中依序選取輸出資料表 -> 選取配量 -> 選取活動執行)，您會看到 HDInsight 叢集所建立的記錄檔連結。您可以直接在入口網站中檢閱記錄檔，或下載到電腦。
  

## Pig JSON 範例
在管線 JSON 中定義 Pig 或 Hive 活動時，**type** 屬性必須設為 **HDInsightActivity**。

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**請注意：**
	
- 活動 **type** 設為 **HDInsightActivity**。
- **linkedServiceName** 設為 **MyHDInsightLinkedService**。如需建立 HDInsight 連結服務的詳細資訊，請參閱下方的 HDInsight 連結服務區段。
- **transformation** 的 **type** 設為 **Pig**。
- 您可以為 **script** 屬性指定 Pig 內嵌指令碼，或將指令碼檔案儲存在 Azure Blob 儲存體，然後使用 **scriptPath** 屬性來參考該檔案，本文後面會加以說明。 
- 您可以使用 **extendedProperties** 指定 Pig 指令碼的參數。本文後面提供更多的詳細資料。 


## Hive JSON 範例


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**請注意：**
	
- 活動 **type** 設為 **HDInsightActivity**。
- **linkedServiceName** 設為 **MyHDInsightLinkedService**。 
- **transformation** 的 **type** 設為 **Hive**。
- 您可以為 **script** 屬性指定 Hive 內嵌指令碼，或將指令碼檔案儲存在 Azure Blob 儲存體，然後使用 **scriptPath** 屬性來參考該檔案，本文後面會加以說明。 
- 您可以使用 **extendedProperties** 指定 Hive 指令碼的參數。本文後面提供更多的詳細資料。 

> [AZURE.NOTE]如需關於 Cmdlet、JSON 結構描述和結構描述中之屬性的詳細資訊，請參閱[開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908)。


## 在 HDInsight 活動中使用 Pig 及 Hive 指令碼
您可以將 Pig/Hive 指令碼儲存在與 HDInsight 叢集相關聯的 Azure Blob 儲存體中，並在 JSON 中使用下列屬性，從 Pig/Hive 活動參考它們：

* **scriptPath**：Pig 或 Hive 指令碼檔案的路徑
* **scriptLinkedService**：包含指令碼檔案的 Azure 儲存體帳戶

以下範例管線的 JSON 範例使用參考 **transformdata.hql** 檔案的 Hive 活動，而該檔案儲存於 **adfwalkthrough** 容器 (位於 **StorageLinkedService** 所代表的 Azure Blob 儲存體中) 的 **scripts** 資料夾中。

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}


> [AZURE.NOTE]若要使用 **Tez** 引擎執行 Hive 查詢，請在執行 Hive 查詢之前，先執行 "**set hive.execution.engine=tez**;"。
> 
> 如需關於 Cmdlet、JSON 結構描述和結構描述中之屬性的詳細資訊，請參閱[開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908)。

## 參數化的 Pig 和 Hive 查詢
Data Factory Pig 和 Hive 活動可讓您使用 **extendedProperties**，指定 Pig 和 Hive 指令碼中所使用的參數值。ExtendedProperties 區段包含參數的名稱和參數的值。

請參閱以下範例，了解如何使用 **extendedProperties** 指定 Hive 指令碼的參數。若要使用參數化的 Hive 指令碼，請執行下列動作：

1.	定義 **extendedProperties** 中的參數。
2.	在內嵌 Hive 指令碼 (或) 儲存在 Blog 儲存體中的 Hive 指令碼檔案中，請使用 **${hiveconf:parameterName}** 來參考參數。

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## 另請參閱

文章 | 說明
------ | ---------------
[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，示範如何使用 Azure Data Factory 實作接近真實的案例，藉此將記錄檔中的資料轉換成深入資訊。
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 Cmdlet、JSON 指令碼、函數等完整參考內容。 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=62-->