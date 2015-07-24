<properties 
	pageTitle="使用 PowerShell 管理 HDInsight 中的 Hadoop 叢集 | Microsoft Azure" 
	description="了解如何使用 Azure PowerShell 對 HDInsight 中的 Hadoop 叢集執行管理工作。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="jgao"/>

# 使用 Azure PowerShell 管理 HDInsight 上的 Hadoop 叢集


Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。在本文中，您將了解如何使用本機 Azure PowerShell 主控台，透過使用 Windows PowerShell 來管理 Azure HDInsight 上的 Hadoop 叢集。如需 HDInsight PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]。

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。


##佈建 HDInsight 叢集
HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳號和儲存容器，您才能建立 HDInsight 叢集。

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**建立 Azure 儲存體帳戶**

匯入 publishsettings 檔案之後，您可以使用下列命令來建立儲存體帳戶：

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [資料中心清單](../../includes/hdinsight-pricing-data-centers-clusters.md)]


如需使用 Azure 入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱[建立、管理或刪除儲存體帳戶](../storage-create-storage-account/)。

如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列命令來擷取資訊：

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

如需使用 Azure 入口網站取得資訊的詳細資訊，請參閱[建立、管理或刪除儲存體帳戶](../storage-create-storage-account/)的「檢視、複製及重新產生儲存體存取金鑰」一節。

**建立 Azure 儲存容器**

Azure PowerShell 無法在 HDInsight 佈建程序期間建立 Blob 容器。您可以使用下列指令碼來建立 Blob 容器：

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**佈建叢集**

在儲存體帳號和 Blob 容器準備就緒後，您即可建立叢集。
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


下列螢幕擷取畫面顯示指令碼的執行：

![HDI.PS.Provision][image-hdi-ps-provision]




##列出叢集詳細資料
使用下列命令列出目前訂用帳戶中的所有叢集：

	Get-AzureHDInsightCluster 

使用下列命令顯示目前訂用帳戶中特定叢集的詳細資料：

	Get-AzureHDInsightCluster -Name <ClusterName> 

##刪除叢集
使用下列命令刪除叢集：

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##授與/撤銷 HTTP 服務存取

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


預設會授與這些服務的存取權。您可以撤銷/授與存取權。範例如下：

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

在範例中，<i>hdiv2</i> 是 HDInsight 叢集名稱。

>[AZURE.NOTE]透過授與/撤銷存取權，您將重設叢的使用者名稱和密碼。

這也可以透過 Azure 入口網站完成。請參閱[使用 Azure 入口網站管理 HDInsight][hdinsight-admin-portal]。

##調整叢集
請參閱[在 HDInsight 中調整 Hadoop 叢集](hdinsight-hadoop-cluster-scaling.md)。

##提交 MapReduce 工作
HDInsight 叢集配送提供一些 MapReduce 範例。其中一個範例是計算來源檔案中的文字出現率。

**提交 MapReduce 工作**

下列 Azure PowerShell 指令碼會提交字數統計範例工作：
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
如需 **wasb** 首碼的詳細資訊，請參閱[對於 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

**下載 MapReduce 工作輸出**

下列 Azure PowerShell 指令碼會從最後一個程序中擷取 MapReduce 工作輸出：

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

如需開發和執行 MapReduce 工作的詳細資訊，請參閱[將 MapReduce 與 HDInsight 搭配使用][hdinsight-use-mapreduce]。






































##提交 Hive 工作
HDInsight 叢集配送提供稱為 *hivesampletable* 的範例 Hive 資料表。您可以使用 HiveQL **SHOW TABLES** 命令，列出叢集上的 Hive 資料表。

**提交 Hive 工作**

下列指令碼會提交 Hive 工作，以列出 Hive 資料表：
	
	$clusterName = "<HDInsightClusterName>"               
	
	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable 
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

Hive 工作會先顯示叢集上所建立的 Hive 資料表，以及從 hivesampletable 資料表傳回的資料。

如需使用 Hive 的詳細資訊，請參閱[將 Hive 與 HDInsight 搭配使用][hdinsight-use-hive]。


##將資料上傳至 Azure Blob 儲存體
請參閱[將資料上傳至 HDInsight][hdinsight-upload-data]。

##從 Azure Blob 儲存體下載工作輸出
請參閱本文中的[提交 MapReduce 工作](#mapreduce)一節。

## 另請參閱
* [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]
* [使用 Azure 入口網站管理 HDInsight][hdinsight-admin-portal]
* [使用命令列介面管理 HDInsight][hdinsight-admin-cli]
* [佈建 HDInsight 叢集][hdinsight-provision]
* [將資料上傳到 HDInsight][hdinsight-upload-data]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [開始使用 Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


 

<!---HONumber=July15_HO2-->