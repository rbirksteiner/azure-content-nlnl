<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Hive 與 PowerShell | Microsoft Azure"
   description="使用 PowerShell 在 HDInsight 的 Hadoop 中執行 Hive 查詢"
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

#使用 PowerShell 執行 Hive 查詢

[AZURE.INCLUDE [Hive 選取器](../../includes/hdinsight-selector-use-hive.md)]

本文件提供使用 Azure PowerShell 在 HDInsight 叢集的 Hadoop 中執行 Hive 查詢的範例。

> [AZURE.NOTE]本文件不提供範例中使用的 HiveQL 陳述式所執行的工作詳細的描述。如需此範例中使用的 HiveQL 的相關資訊，請參閱[在 HDInsight 上搭配 Hadoop 使用 Hive](hdinsight-use-hive.md)。


##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

- **Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 型或 Linux 型)**
- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。

##<a id="powershell"></a>使用 Azure PowerShell 執行 Hive 查詢

Azure PowerShell 提供 *Cmdlet*，可讓您從遠端在 HDInsight 上執行 Hive 查詢。在內部，您可以使用在 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集中執行 Hive 查詢時，會使用下列 Cmdlet：

* **Add-AzureAccount**：驗證您 Azure 訂閱的 PowerShell

* **New-AzureHDInsightHiveJobDefinition**：使用指定的 HiveQL 陳述式建立新的*工作定義*

* **Start-AzureHDInsightJob**：將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的*工作*物件

* **Wait-AzureHDInsightJob**：使用工作物件來檢查工作的狀態。它會等到工作完成，或等到等候時間超過。

* **Get-AzureHDInsightJobOutput**：用來擷取工作的輸出

* **Invoke-Hive**：用來執行 HiveQL 陳述式並封鎖其完成。這會阻止查詢完成，然後傳回結果

* **Use-AzureHDInsightCluster**：設定要用於 **Invoke-Hive** 命令的目前叢集

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作：

1. 使用編輯器，將下列程式碼儲存為 **hivejob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. 開啟新的 **Azure PowerShell** 命令提示字元。將目錄變更至 **hivejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

		.\hivejob.ps1

7. 工作完成時，應該會傳回與下面類似的資訊：

		Display the standard output...
		[ERROR]	3

4. 如前所述，**Invoke-Hive** 可以用來執行查詢，並等候回應。使用下列命令，並將 **CLUSTERNAME** 取代為您叢集的名稱：

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	輸出顯示如下：

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]如果 HiveQL 查詢的時間很長，您可以使用 Azure PowerShell **Here-Strings** Cmdlet 或 HiveQL 指令碼檔案。下列程式碼片段說明如何使用 **Invoke-Hive** Cmdlet 來執行 HiveQL 指令碼檔案。必須將 HiveQL 指令碼檔案上傳至 wasb://。
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> 如需 **Here-Strings** 的詳細資訊，請參閱<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>。

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列內容新增至 **hivejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->