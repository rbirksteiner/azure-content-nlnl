<properties
   pageTitle="搭配使用 MapReduce 和 PowerShell 與 Hadoop | Microsoft Azure"
   description="了解如何使用 PowerShell 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#使用 PowerShell 搭配執行 Hive 查詢與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [MapReduce 選取器](../../includes/hdinsight-selector-use-mapreduce.md)]

本文件提供使用 Azure PowerShell 在 HDInsight 叢集的 Hadoop 中執行 MapReduce 工作的範例。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

- **Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 型或 Linux 型)**

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

##<a id="powershell"></a>使用 Azure PowerShell 執行 MapReduce 工作

Azure PowerShell 提供 *Cmdlet*，可讓您從遠端在 HDInsight 上執行 MapReduce 工作。在內部，您可以使用在 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集中執行 MapReduce 工作時，會使用下列 Cmdlet。

* **Add-AzureAccount**：驗證您 Azure 訂閱的 PowerShell

* **New-AzureHDInsightMapReduceJobDefinition**：使用指定的 MapReduce 資訊建立新的*工作定義*

* **Start-AzureHDInsightJob**：將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的*工作*物件

* **Wait-AzureHDInsightJob**：使用工作物件來檢查工作的狀態。它會等到工作完成，或等到等候時間超過。

* **Get-AzureHDInsightJobOutput**：用來擷取工作的輸出

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作。

1. 使用編輯器，將下列程式碼儲存為 **mapreducejob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. 開啟新的 **Azure PowerShell** 命令提示字元。將目錄變更至 **mapreducejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

		.\mapreducejob.ps1

3. 工作完成之後，您應該會收到與下列類似的輸出：

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	此輸出表示工作已順利完成。

	> [AZURE.NOTE]如果 **ExitCode** 的值不是 0，請參閱[疑難排解](#troubleshooting)。

##<a id="results"></a>檢視工作輸出

MapReduce 工作已將作業結果儲存至 Azure Blob 儲存體 (位於指定為工作之引數的 **wasb:///example/data/WordCountOutput** 路徑中)。Azure Blob 儲存體可以透過 Azure PowerShell 存取，但是您必須知道儲存體帳戶名稱、金鑰，以及 HDInsight 叢集用來直接存取檔案的容器。

幸運的是，您可以使用下列 Azure PowerShell Cmdlet 取得這項資訊：

* **Get-AzureHDInsightCluster**：傳回 HDInsight 叢集的相關資訊 (包括任何相關聯的儲存體帳戶)。一律會有與叢集相關聯的預設儲存體帳戶。
* **New-AzureStorageContext**：如果指定使用 **Get-AzureHDInsightCluster** 所擷取的儲存體帳戶名稱和金鑰，則會傳回可用來存取儲存體帳戶的內容物件。
* **Get-AzureStorageBlob**：如果指定內容物件和容器名稱，則會傳回容器內的 blob 清單。
* **Get-AzureStorageBlobContent**：如果指定內容物件、檔案路徑和名稱以及容器名稱 (從 **Get-AzureHDinsightCluster** 傳回)，則會從 Azure Blob 儲存體下載檔案。

下列範例會擷取儲存體資訊，然後從 **wasb:///example/data/WordCountOutput** 下載輸出。將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]此範例會將已下載的檔案儲存到您執行指令碼所在目錄的 **example/data/WordCountOutput** 資料夾中。

MapReduce 工作的輸出會儲存在名稱為 *part-r-#####* 的檔案中。使用文字編輯器開啟 **example/data/WordCountOutput/part-r-00000** 檔案，以查看工作所產生的單字和計數。

> [AZURE.NOTE]MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列命令新增至 **mapreducejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

<!---HONumber=July15_HO2-->