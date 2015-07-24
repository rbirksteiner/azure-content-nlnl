<properties 
	pageTitle="在 HDInsight 的 Hadoop 叢集上安裝和使用 Giraph | Microsoft Azure" 
	description="了解如何使用 Giraph 自訂 HDInsight 叢集。您將使用指令碼動作設定選項來使用指令碼安裝 Giraph。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# 在 HDInsight Hadoop 叢集上安裝 Giraph，以及使用 Giraph 來處理大規模圖形

您也可以使用**指令碼動作**叢集自訂，在 Azure HDInsight 上 Hadoop 中的任何一種叢集上安裝 Giraph。指令碼動作可讓您執行指令碼，只在建立叢集時才能自訂叢集。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。

在本主題中，您將學習如何使用指令碼動作來安裝 Giraph。一旦您已安裝 Giraph，您也將了解如何將 Giraph 用於大部分的一般應用程式，也就是處理大型的圖形。


## <a name="whatis"></a>什麼是 Giraph？

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。圖形可以模型化物件之間的關聯，例如大型網路 (像是網際網路) 上的路由器之間的連線，或社交網路上的人際關係 (有時稱為社交圖形)。圖形處理可讓您分析圖形中物件之間的關聯，例如：

- 根據目前的人際關係找出可能的朋友。
- 識別網路中兩台電腦之間的最短路線。
- 計算網頁的頁面排名。

   
## <a name="install"></a>如何安裝 Giraph？

您可以從一個唯讀的 Azure 儲存體 Blob 取得在 HDInsight 叢集上安裝 Giraph 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)。本節提供有關如何在使用 Azure 入口網站佈建叢集時使用範例指令碼的指示。

> [AZURE.NOTE]範例指令碼只能與 HDInsight 叢集版本 3.1 搭配使用。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

1. 依照[使用自訂選項佈建叢集](hdinsight-provision-clusters.md#portal)所述，使用**自訂建立**選項開始佈建叢集。 
2. 在精靈的 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 以提供有關指令碼動作的詳細資料，如下所示：

	![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "使用指令碼動作以自訂叢集")
	
	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱。例如，<b>安裝 Giraph</b>。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>指定統一資源識別元 (URI) 給為了自訂叢集所叫用的指令碼。例如，<i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>節點類型</td>
		<td>指定執行自訂指令碼的節點。您可以選擇 [<b>所有節點</b>]、[<b>僅限前端節點</b>] 或 [<b>僅限背景工作節點</b>]。
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。要安裝 Giraph 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
</table>您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，按一下核取記號以開始佈建叢集。

您也可以使用 Azure PowerShell 或 HDInsight.NET SDK，即可在 HDInsight 上使用指令碼安裝 Giraph。本主題稍後會提供這些程序的指示。

## <a name="usegiraph"></a>如何在 HDInsight 中使用 Giraph？

我們使用 SimpleShortestPathsComputation 範例來示範在圖形中的物件之間找出最短路徑的基本<a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 實作。請使用下列步驟來上傳範例資料及範例 jar，使用 SimpleShortestPathsComputation 範例執行工作，然後檢視結果。

1. 將範例資料檔案上傳至 Azure Blob 儲存體。在本機工作站上，建立名為 **tiny_graph.txt** 的新檔案。應該包含下列幾行：

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	將 tiny_graph.txt 檔案上傳至 HDInsight 叢集的主要儲存體。如需有關如何上傳資料的指示，請參閱[在 HDInsight 上將 Hadoop 工作的資料上傳](hdinsight-upload-data.md)。

	這項資料會使用 [source_id, source_value,[[dest_id], [edge_value],...]] 格式，描述一個有向圖形中物件之間的關聯性。每一行代表 **source_id** 物件和一或多個 **dest_id** 物件之間的關聯性。**edge_value** (或權數) 可以視為 **source_id** 和 **dest_id** 之間的連線強度或距離。

	如果使用值 (或權數) 當做物件之間的距離繪製出來，上述資料可能如下圖所示：

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. 執行 SimpleShortestPathsComputation 範例。使用 tiny_graph.txt 檔案做為輸入，即可使用下列 Azure PowerShell Cmdlet 來執行此範例。您必須已安裝和設定 [Azure PowerShell][powershell-install]。

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	在上述範例中，利用您已安裝 Giraph 的 HDInsight 叢集名稱取代 **clustername**。

5. 檢視結果。一旦工作完成，結果會儲存在 __wasb:///example/out/shotestpaths__ 資料夾中的兩個輸出檔中。這些檔案稱為 __part-m-00001__ 和 __part-m-00002__。執行下列步驟來下載和檢視輸出：

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	這會在工作站上目前的目錄中建立 __example/output/shortestpaths__ 目錄結構，並將兩個輸出檔案下載到該位置。

	使用 __Cat__ Cmdlet 顯示檔案的內容：

		Cat example/output/shortestpaths/part*

	輸出應該如下所示：


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation 範例已刻意設計成從物件識別碼 1 開始，尋找前往其他物件的最短路徑。因此，輸出應該會顯示 `destination_id distance`，其中 distance 是物件識別碼 1 與目標識別碼之間經過的邊緣的值 (或權數)。
	
	顯現為圖形後，您可以走過識別碼 1 與其他所有物件之間的最短路徑來驗證結果。請注意，識別碼 1 和識別碼 4 之間的最短路徑為 5。這是<span style="color:orange">識別碼 1 和 3 之間</span>加上<span style="color:red">識別碼 3 和 4 之間</span>的總距離。

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png)


## <a name="usingPS"></a>使用 Azure PowerShell 在 HDInsight Hadoop 叢集上安裝 Giraph

本節中，我們使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** Cmdlet，使用「指令碼動作」叫用指令碼以自訂叢集。在繼續之前，請確認您已安裝和設定 Azure PowerShell。如需設定工作站以執行 HDInsight 的 Azure Powershell cmdlet 之相關資訊，請參閱[安裝和設定 Azure PowerShell][powershell-install]。

執行下列步驟：

1. 開啟 Azure PowerShell 視窗，並宣告下列變數：

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. 指定設定值，例如要使用的叢集中節點和預設儲存體：

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. 使用 **Add-AzureHDInsightScriptAction** Cmdlet 將指令碼動作加入叢集設定。稍後在建立叢集時，將會執行指令碼動作。

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** Cmdlet 可接受下列參數：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">參數</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">設定</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">要在其中新增指令碼動作資訊的組態物件。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">自訂指令碼執行所在的節點。有效值為 HeadNode (在前端節點上安裝) 或 DataNode (在所有資料節點上安裝)。您可以使用其中一個或兩個值。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">所執行之指令碼的 URI。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼所需的參數。本主題中使用的範例指令碼不需要任何參數，因此您在上述程式碼片段中看不到此參數。
</td></tr>
</table>
	
4. 最後，開始佈建已安裝 Giraph 的自訂叢集。
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出現提示時，請輸入叢集的認證。建立叢集可能需要幾分鐘的時間。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Giraph

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET Framework 應用程式使用 HDInsight。本節說明如何使用來自 SDK 的「指令碼動作」來佈建已安裝 Giraph 的叢集。必須執行下列程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 應用程式**

1. 開啟 Visual Studio 2013。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會從目前的 Visual Studio 專案新增 .NET 程式庫及其參考。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 在 Main() 函數中，複製及貼上下列程式碼，並提供變數的值：
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. 將下列程式碼附加至 Main() 函數中，以使用 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 類別來叫用自訂指令碼安裝 Giraph：

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. 最後，建立叢集：

		client.CreateCluster(clusterInfo);

12. 儲存對應用程式所做的變更，然後建置方案。

**執行應用程式**

開啟 Azure PowerShell 主控台、瀏覽至您儲存 Visual Studio 專案的位置、瀏覽至專案內的 \\bin\\debug 目錄，然後執行下列命令：

	.\CreateGiraphCluster <cluster-name>

請提供叢集名稱，然後按 ENTER 以佈建已安裝 Giraph 的叢集。


## 另請參閱##
- [在 HDInsight 叢集上安裝和使用 Spark][hdinsight-install-spark] 的指示，其中說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 Spark。Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。
- [在 HDInsight 叢集上安裝 R][hdinsight-install-r] 說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 R。R 是一個用於統計計算的開放原始碼語言和環境。它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。
- [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您在儲存的資料上執行功能強大的搜尋作業。



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=July15_HO2-->