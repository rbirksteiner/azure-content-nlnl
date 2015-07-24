<properties 
	pageTitle="從 Azure Data Factory 叫用 MapReduce 程式" 
	description="了解如何從 Azure Data Factory，在 Azure HDInsight 叢集上執行 MapReduce 程式以處理資料。" 
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

# 從 Data Factory 叫用 MapReduce 程式
本文說明如何搭配 MapReduce 轉換使用 HDInsight 活動，從 Azure Data Factory 管線叫用 **MapReduce** 程式。

## 簡介 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明如何使用「HDInsight 活動」的 MapReduce 轉換。
 
若要了解如何使用 HDInsight 活動的 Pig/Hive 轉換，在 HDInsight 叢集上從 Azure Data Factory 管線執行 Pig/Hive 指令碼，請參閱[搭配 Data Factory 使用 Pig 和 Hive][data-factory-pig-hive-activities]。

## 使用 MapReduce 轉換之 HDInsight 活動的 JSON 

在 HDInsight 活動的 JSON 定義中：
 
1. 將 **activity** 的 **type** 設為 **HDInsightActivity**。
2. 將 **transformation** 的 **type** 設為 **MapReduce**。
3. 指定 **className** 屬性的類別名稱。
4. 為 **jarFilePath** 屬性指定包含檔案名稱的 JAR 檔案路徑。
5. 為 **jarLinkedService** 屬性指定連結服務，此連結服務參考包含 JAR 檔案的 Azure Blob 儲存體。   
6. 在 **arguments** 區段中，為 MapReduce 程式指定所有引數。 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

您可以使用 MapReduce 轉換，在 HDInsight 叢集上執行任何 MapReduce JAR 檔案。在下列管線的範例 JSON 定義中，已設定讓「HDInsight 活動」執行 Mahout JAR 檔案。

## 範例
您可以從 [GitHub 上的 Data Factory 範例][data-factory-samples]下載搭配 MapReduce 轉換使用 HDInsight 活動的範例。

## 另請參閱

文章 | 說明
------ | ---------------
[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，示範如何使用 Azure Data Factory 實作接近真實的案例，藉此將記錄檔中的資料轉換成深入資訊。在此教學課程中，您將使用 Pig 和 Hive 轉換處理資料。 
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 Cmdlet、JSON 指令碼、函數等完整參考內容。 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=62-->