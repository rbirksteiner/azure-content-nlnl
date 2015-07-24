<properties
	pageTitle="從 HDFS 相容的 Blob 儲存體查詢資料 | Microsoft Azure"
	description="HDInsight 使用 Blob 儲存體作為 HDFS 的大量資料存放區。了解如何從 Blob 儲存體查詢資料並儲存分析的結果。"
	keywords="blob storage,hdfs,structured data,unstructured data"
	services="hdinsight,storage"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="jgao"/>


#在 HDInsight 上搭配 Hadoop 使用 HDFS 相容的 Azure Blob 儲存體

在本教學課程中，了解如何搭配 HDInsight 使用低成本的 Azure Blob 儲存體，建立 Azure 儲存體帳戶和 Blob 儲存體容器，然後處理其中的資料。

Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。

將資料儲存在 Blob 儲存體中，您便可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

> [AZURE.NOTE]在 HDInsight 3.0 版叢集中不支援 *asv://* 語法。這表示任何提交至 HDInsight 3.0 版叢集且明確使用 *asv://* 語法的工作都會失敗。請改用 *wasb://* 語法。另外，如果工作提交至任何以現有中繼存放區建立的 HDInsight 3.0 版叢集，且中繼存放區中使用 asv:// 語法來明確參考資源，也會失敗。必須使用 wasb:// 語法來定址資源，以重新建立這些中繼存放區。

> HDInsight 目前僅支援區塊 Blob。

> 大部分 HDFS 命令 (例如，<b>ls</b>、<b>copyFromLocal</b> 和 <b>mkdir</b>) 仍可正常運作。只有原生 HDFS 實作 (稱為 DFS) 的特定命令 (例如 <b>fschk</b> 和 <b>dfsadmin</b>) 才會在 Azure Blob 儲存體上出現不同的行為。

如需關於佈建 HDInsight 叢集的資訊，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。


##<a id="architecture"></a>HDInsight 儲存架構
下圖提供 HDInsight 儲存架構的摘要檢視：

![Hadoop 叢集會使用 HDFS API 來存取和儲存 Blob 儲存體中的結構化和非結構化資料。](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight 儲存架構")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。可使用完整 URI 來存取此檔案系統，例如：

	hdfs://<namenodehost>/<path>

此外，HDInsight 也能夠存取儲存在 Azure Blob 儲存體中的資料。語法為：

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop 支援預設檔案系統的概念。預設檔案系統意指預設配置和授權。也可用來解析相對路徑。HDInsight 佈建過程中會指定 Azure 儲存體帳戶和該帳戶中的特定 Azure Blob 儲存容器，做為預設檔案系統。

除了此儲存體帳戶，您也可以在佈建過程中，從相同 Azure 訂用帳戶或不同 Azure 訂用帳戶中新增其他儲存體帳戶。如需關於新增其他儲存體帳戶的指示，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

- **儲存體帳戶中連線至叢集的容器：**因為在佈建期間帳戶名稱和金鑰會與叢集相關聯，所以您對這些容器中的 Blob 具有完整存取權。

- **儲存體帳戶中未連線至叢集的公用容器或公用 Blob：**您對容器中的 Blob 只有唯讀權限。

	> [AZURE.NOTE]> 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">限制對容器和 Blob 的存取</a>。

- **儲存體帳戶中未連線至叢集的私人容器：**除非在提交 WebHCat 工作時定義儲存體帳戶，否則不能存取容器中的 Blob。稍後在本文中會加以說明。


佈建程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。HDInsight 的預設行為是使用 core-site.xml 檔案中定義的儲存體帳戶。因為叢集前端節點 (主要) 有可能會隨時重新安裝映像或進行移轉，屆時將會遺失對這些檔案所做的任何變更，所以我們不建議您編輯 core-site.xml 檔案。

多個 WebHCat 工作 (包括 Hive、MapReduce、Hadoop 資料流和 Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料(目前適用於含儲存體帳戶的 Pig，但不適用於中繼資料)。 在本文的[使用 Azure PowerShell 存取 Blob](#powershell) 一節中，提供了此功能的範例。如需詳細資訊，請參閱[在其他儲存體帳戶和 Metastores 上使用 HDInsight 叢集](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 儲存體可使用於結構化和非結構化資料。Blob 儲存容器以機碼/值組來儲存資料，沒有目錄階層。但是，機碼名稱中可使用 ( / ) 斜線字元，使檔案變成好像儲存在目錄結構中一樣。例如，Blob 的機碼可能是 *input/log1.txt*。實際上，*input* 目錄並不存在，只是因為機碼名稱中有斜線字元，才形成檔案路徑的樣子。

###<a id="benefits"></a>Blob 儲存體的優點
運算叢集和儲存體叢集未並存於同處所隱含的效能損失，可經由將運算叢集佈建到靠近 Azure 資料中心內的儲存體帳戶資源來彌補，其中的高速網路可讓運算節點非常有效率地存取 Azure Blob 儲存體內的資料。

將資料儲存在 Azure Blob 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於運算叢集內。只有可存取運算叢集的應用程式，才能利用 HDFS API 來使用資料。可透過 HDFS API 或 [Blob 儲存體 REST API][blob-storage-restAPI] 來存取 Azure Blob 儲存體中的資料。因此，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。
* **資料封存：**將資料儲存在 Azure Blob 儲存體中，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。
* **資料儲存成本：**長期將資料儲存在 DFS 中的成本高於將資料儲存在 Azure Blob 儲存體中，因為運算叢集的成本高於 Azure Blob 儲存容器的成本。此外，因為不需要每次產生運算叢集時都重新載入資料，也能節省資料載入成本。
* **彈性向外延展：**雖然HDFS 提供向外延展的檔案系統，但延展程度取決於您佈建給叢集的節點數目。變更延展程度較為複雜，可改用 Azure Blob 儲存體自動提供的彈性延展功能。
* **異地複寫：**Azure Blob 儲存容器可以透過 Azure 入口網站在異地複寫。雖然這樣可支援地理位置復原和資料備援，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。因此，只有在資料的價值大於額外成本時，才建議您明智地選擇異地複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Azure Blob 儲存體中。在此情況下，您仍可選擇將資料儲存在本機 HDFS。事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。



##<a id="preparingblobstorage"></a>建立 Blob 容器

若要使用 Blob，您必須先建立 [Azure 儲存體帳戶][azure-storage-create]。在這過程中，需要指定 Azure 資料中心來儲存您以此帳戶所建立的物件。叢集與儲存體帳戶必須在相同資料中心內託管。Hive 中繼存放區 SQL Server 資料庫和 Oozie 中繼存放區 SQL Server 資料庫也必須位在相同的資料中心內。

您所建立的每個 Blob 不論位於何處，都屬於 Azure 儲存體帳戶中的某個容器。此容器可能是在 HDInsight 外建立的現有 Blob，也可能是為 HDInsight 叢集建立的容器。

不要與多個 HDInsight 叢集共用預設儲存容器。如果需要使用共用容器來提供多個 HDInsight 叢集的資料存取權限，應將共用容器新增為叢集組態中的其他儲存體帳戶。如需詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。不過，在刪除原始的 HDInsight 叢集後，您可以重複使用預設儲存容器。至於 HBase 叢集，您可以利用被刪除的 HBase 叢集使用的預設 Blob 儲存容器來佈建一個新的 HBase 叢集，藉此實際保留 HBase 資料表結構描述和資料。


###使用 Azure 入口網站

從 Azure 入口網站佈建 HDInsight 叢集時，有兩種選項：**快速建立**和**自訂建立**。要使用 [快速建立] 選項，需要事先建立 Azure 儲存體帳戶。相關指示請參閱[如何建立儲存體帳戶][azure-storage-create]。

使用 [快速建立] 選項時，您可以選擇現有的儲存體帳戶。佈建程序會建立與 HDInsight 叢集名稱同名的新容器。如果已有相同名稱的容器存在，便會使用 <clusterName>-<x>。例如，*myHDIcluster-1*。此容器做為預設檔案系統。

![使用快速建立在 Azure 入口網站中的 HDInsight 建立新的 Hadoop 叢集。][img-hdi-quick-create]

使用 [自訂建立] 時，您可以對預設儲存體帳戶使用下列其中一個選項：

- 使用現有儲存體
- 建立新的儲存體
- 使用其他訂用帳戶的儲存體

您也可以選擇建立自己專屬的容器或使用現有容器。

![使用現有的儲存體帳戶以用於 HDInsight 叢集的選項。][img-hdi-custom-create-storage-account]

###使用 Azure CLI

如果您已[安裝和設定 Azure CLI](../xplat-cli.md)，下列命令即可用於儲存體帳戶和容器。

	azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE]`--type` 參數表示儲存體帳戶的複寫方式。如需詳細資訊，請參閱 [Azure 儲存體複寫](../storage-redundancy.md)

系統會提示您指定將放置儲存體帳戶的地理區域。您應該在您計劃建立 HDInsight 叢集的相同區域中建立儲存體帳戶。

建立儲存體帳戶後，使用下列命令來擷取儲存體帳戶金鑰：

	azure storage account keys list <storageaccountname>

若要建立容器，請使用下列命令：

	azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

###使用 Azure PowerShell

如果您已[安裝和設定 Azure PowerShell][powershell-install]，您可以從 Azure PowerShell 提示字元使用下列程式碼來建立儲存體帳戶和容器：

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext


##<a id="addressing"></a>定址 Blob 儲存體中的檔案

從 HDInsight 存取 Blob 儲存體中的檔案的 URI 配置如下：

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE]在儲存體模擬器 (在 HDInsight 模擬器上執行) 上定址檔案的語法是 <i>wasb://&lt;ContainerName&gt;@storageemulator</i>。



URI 配置提供未加密存取 (使用*wasb:* 首碼) 和 SSL 加密存取 (使用 *wasbs*)。建議盡可能使用 *wasbs*，即使存取相同 Azure 資料中心內的資料也一樣。

&lt;BlobStorageContainerName&gt; 是指 Azure Blob 儲存體中的容器名稱。&lt;StorageAccountName&gt; 是指 Azure 儲存體帳戶名稱。需要使用完整網域名稱 (FQDN)。

如果 &lt;BlobStorageContainerName&gt; 和 &lt;StorageAccountName&gt; 都未指定，則會使用預設檔案系統。對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。例如，可使用下列語法來參考 HDInsight 叢集隨附的 *hadoop-mapreduce-examples.jar* 檔案：

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]在 HDInsight 2.1 和 1.6 版叢集中的檔案名稱是 <i>hadoop-examples.jar</i>。


&lt;path&gt; 是檔案或目錄 HDFS 路徑名稱。因為 Azure Blob 儲存體中的容器僅是機碼值存放區，所以並沒有真正的階層式檔案系統。Blob 機碼內的斜線字元 ( / ) 會被解釋為目錄分隔符號。例如，*hadoop-mapreduce-examples.jar* 的 Blob 名稱為：

	example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]在 HDInsight 外部使用 Blob 時，大部分的公用程式無法辨識 WASB 格式而改為預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

##<a id="azurecli"></a>使用 Azure CLI 存取 Blob

請使用下列命令來列出 Blob 相關的命令：

	azure storage blob

**使用 Azure CLI 上傳檔案的範例**

	azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 下載檔案的範例**

	azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 刪除檔案的範例**

	azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 列出檔案的範例**

	azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

##<a id="powershell"></a>使用 Azure PowerShell 存取 Blob

> [AZURE.NOTE]本章中的命令會提供使用 PowerShell 來存取儲存在 Blob 中的資料的基本範例。如需使用 HDInsight 的更完整範例，請參閱 [HDInsight 工具](https://github.com/Blackmist/hdinsight-tools)。

請使用下列命令來列出 Blob 相關的 Cmdlet：

	Get-Command *blob*

![與 Blob 相關的 Power Shell Cmdlet 清單。][img-hdi-powershell-blobcommands]


**使用 Azure PowerShell 上傳檔案的範例**

請參閱[將資料上傳至 HDInsight][hdinsight-upload-data]。

**使用 Azure PowerShell 下載檔案的範例**

下列指令碼將區塊 Blob 下載至目前的資料夾。執行指令碼之前，請將目錄變更為您具有寫入權限的資料夾。


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**使用 Azure PowerShell 刪除檔案的範例**

下列指令碼說明如何刪除檔案。

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob


**使用 Azure PowerShell 列出資料夾內檔案的範例**

下列指令碼說明如何列出資料夾內的檔案。(下個範例說明如何使用 **Invoke-Hive** Cmdlet 來執行 **dfs ls** 命令，以列出資料夾。)

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"

	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours

	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"
	#Select-AzureSubscription $subscriptionName

	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**使用未定義的儲存體帳戶利用 Azure PowerShell 執行 Hive 查詢的範例**

本範例說明如何從未在佈建程序期間定義的儲存體帳戶列出資料夾。$clusterName = "<HDInsightClusterName>"

	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

	Use-AzureHDInsightCluster $clusterName

	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

##<a id="nextsteps"></a>接續步驟

在本文中，您學到如何搭配 HDInsight 使用 HDFS 相容的 Azure Blob 儲存體，也了解 Azure Blob 儲存體是 HDInsight 的基本元件。這可讓您以 Azure Blob 儲存體建立可擴充、長期封存的資料取得解決方案，並利用 HDInsight 來揭開儲存的結構化和非結構化資料內的資訊。

若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]

[powershell-install]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png
 

<!---HONumber=July15_HO2-->