<properties 
	pageTitle="在 HDInsight 中使用 R 來自訂叢集| Microsoft Azure" 
	description="了解如何安裝和使用 R 來自訂 Hadoop 叢集。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# 在 HDInsight Hadoop 叢集上安裝和使用 R

您可以使用**指令碼動作**叢集自訂，在 HDInsight 上 Hadoop 中的任何一種叢集上安裝 R。這可讓資料科學家和分析師使用 R 來部署強大的 MapReduce/YARN 程式設計架構，以處理部署在 HDInsight 中之 Hadoop 叢集上的大量資料。

指令碼動作可讓您執行指令碼來自訂叢集，但只能在建立叢集時進行。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。


## <a name="whatIs"></a>什麼是 R？

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> 是一個用於統計計算的開放原始碼語言和環境。R 提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。R 是廣泛各種不同領域中，大多數專業統計人員和科學家慣用的程式設計環境。

R 指令碼可以在 HDInsight 中的 Hadoop 叢集上執行，這些叢集是在建立時已藉由使用「指令碼動作」來自訂安裝 R 環境的叢集。R 與 Azure Blob 儲存體 (WASB) 相容，因此便可在 HDInsight 上使用 R 來處理儲存在該處的資料。

## <a name="install"></a>如何安裝 R？

您可以從 Azure 儲存體中的唯讀 Blob，取得用以在 HDInsight 叢集上安裝 R 的[範例指令碼](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)。本節提供有關如何在使用 Azure 入口網站佈建叢集時使用範例指令碼的指示。

> [AZURE.NOTE]範例指令碼是在 HDInsight 叢集版本 3.1 中所推出。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](../hdinsight-component-versioning/)。

1. 依照[使用自訂選項佈建叢集](../hdinsight-provision-clusters/#portal)所述，使用**自訂建立**選項開始佈建叢集。 
2. 在精靈的 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 以提供有關指令碼動作的詳細資料，如下所示：

	![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "使用指令碼動作以自訂叢集")
	
	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱，例如 [<b>安裝 R</b>]。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>指定為了自訂叢集所叫用之指令碼的 URI，例如 <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
	<tr><td>節點類型</td>
		<td>指定執行自訂指令碼的節點。您可以選擇 [<b>所有節點</b>]、[<b>僅限前端節點</b>] 或 [<b>僅限背景工作節點</b>]。
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。不過，用來安裝 R 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
</table>您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，請按一下核取記號以開始佈建叢集。

您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上使用指令碼安裝 R。本文稍後會提供這些程序的指示。

## <a name="useR"></a>如何使用 HDInsight 執行 R 指令碼？
本節說明如何使用 HDInsight 在 Hadoop 叢集上執行 R 指令碼。

1. **建立與叢集的遠端桌面連線**：從 Azure 入口網站，針對您所建立且已安裝 R 的叢集啟用遠端桌面，然後連線到叢集。如需指示，請參閱<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連線到 HDInsight 叢集</a>。

2. **開啟 R 主控台**：R 安裝會在前端節點的桌面上放置 R 主控台的連結。按一下該連結以開啟 R 主控台。

3. **執行 R 指令碼**：您可以透過貼上、選取然後按 Enter 鍵的方式，直接從 R 主控台執行 R 指令碼。以下是一個簡單的範例指令碼，此指令碼會產生數字 1 到 100，然後將它們乘以 2。

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

前兩行會呼叫與 R 一起安裝的 RHadoop 程式庫。最後一行會將結果列印到主控台。輸出應該會看起來如下：

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>使用 Azure PowerShell 在 HDInsight 上安裝 R

本節中，我們使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** Cmdlet，使用指令碼動作叫用指令碼以自訂叢集。在繼續之前，請確認您已安裝和設定 Azure PowerShell。如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

執行下列步驟：

1. 開啟 Azure PowerShell 主控台，並宣告下列變數：

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. 指定組態值 (例如叢集中的節點) 和要使用的預設儲存體。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. 使用 **Add-AzureHDInsightScriptAction** Cmdlet 叫用用來安裝 R 的範例指令碼，例如：

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行自訂指定碼的節點。有效值為 **HeadNode** (在前端節點上安裝) 或 **DataNode** (在所有資料節點上安裝)。您可以使用其中一個或兩個值。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼所需的參數。 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行指令碼的 URI。</td></tr>
</table>
	
4. 最後，佈建經自訂而已安裝 R 的叢集。
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出現提示時，請輸入叢集的認證。建立叢集可能需要幾分鐘的時間。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight 上安裝 R

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。

執行下列程序即可使用 SDK 佈建 HDInsight 叢集：

- [安裝 HDInsight .NET SDK](#installSDK)
- [建立自我簽署憑證](#createCert)
- [在 Visual Studio 中建立 .NET 應用程式](#createApp)
- [執行應用程式](#runApp)

下列各節示範如何執行這些程序。

### <a name="installSDK"></a>安裝 HDInsight .NET SDK

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

### <a name="createCert"></a>建立自我簽署憑證

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


### <a name="createApp"></a>在 Visual Studio 中建立 .NET 應用程式

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會從目前的 Visual Studio 專案新增 .NET 程式庫及其參考。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 **using** 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 在 **Main()** 函數中，貼上下列程式碼，並提供變數的值：
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
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

10. 將下列程式碼附加至 **Main()** 函數中，以使用 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 類別叫用用來安裝 R 的自訂指令碼。

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. 最後，建立叢集：

		client.CreateCluster(clusterInfo);

11. 儲存對應用程式所做的變更，然後建置方案。

### <a name="runApp"></a>執行應用程式

開啟 Azure PowerShell 主控台、瀏覽至您儲存專案的位置、瀏覽至專案內的 \\bin\\debug 目錄，然後執行下列命令：

	.\CreateRCluster <cluster-name>

請提供叢集名稱，然後按 ENTER 以佈建已安裝 R 的叢集。

## <a name="seeAlso"></a>另請參閱

- [在 HDInsight 叢集上安裝和使用 Spark][hdinsight-install-spark] 的指示，其中說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 Spark。Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。
- [在 HDInsight 叢集上安裝 Giraph](../hdinsight-hadoop-giraph-install)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。
- [在 HDInsight 叢集上安裝 Solr](../hdinsight-hadoop-solr-install)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您對儲存的資料執行功能強大的搜尋作業。

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
 

<!---HONumber=July15_HO2-->